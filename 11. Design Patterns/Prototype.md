### Part 1: Problem Statement (Expensive Object Creation)

Sochiye aapke paas ek **DatabaseConnection** object hai jisko create karne mein 5 seconds lagte hain (Loading Driver, TCP Handshake, Authentication, etc.). Ab aapko 10 jagah connection chahiye.

#### ❌ BAD PRACTICE: Har Baar Naya Object Banana

```java
// Bina Prototype ke - Har baar naya object banane mein time waste
class DatabaseConnection {
    private String connectionString;
    private String username;
    private String password;
    
    public DatabaseConnection(String connString, String user, String pass) {
        this.connectionString = connString;
        this.username = user;
        this.password = pass;
        // Expensive Operation Simulation
        System.out.println("⏳ Loading Database Driver... (2 sec)");
        System.out.println("🔗 Establishing TCP Handshake... (2 sec)");
        System.out.println("🔐 Authenticating... (1 sec)");
        System.out.println("✅ Connected to " + connString);
    }
    
    public void executeQuery(String query) {
        System.out.println("Executing: " + query);
    }
}

// Client Code (BAD)
public class BadApp {
    public static void main(String[] args) {
        // Har baar 5 sec lagenge!
        DatabaseConnection conn1 = new DatabaseConnection("prod-db", "admin", "pass1");
        DatabaseConnection conn2 = new DatabaseConnection("prod-db", "admin", "pass1");
        DatabaseConnection conn3 = new DatabaseConnection("prod-db", "admin", "pass1");
        // Total Time: 15 seconds wasted
    }
}
```

**Dikkatein:**
1. **Performance Hit:** Har baar naya object banane mein time aur resources waste.
2. **Redundant Initialization:** Same state ke liye baar baar same steps.
3. **Complex Object Creation:** Agar object mein 20 fields set karne hain to baar baar setter call karna padega.

---

### Part 2: Prototype Pattern Solution (Good Example)

Java mein prototype implement karne ke 2 tarike hain:
1. **Cloneable Interface** (Java Native)
2. **Custom Copy Method** (Better Approach)

#### Approach 1: Using Java's Cloneable Interface (Shallow Copy)

```java
// ✅ GOOD PRACTICE: Cloneable Interface use karke

// Step 1: Implement Cloneable
class DatabaseConnectionProto implements Cloneable {
    private String connectionString;
    private String username;
    private String password;
    private boolean isConnected;
    
    // Expensive Constructor (Sirf ek baar call hoga)
    public DatabaseConnectionProto(String connString, String user, String pass) {
        this.connectionString = connString;
        this.username = user;
        this.password = pass;
        this.isConnected = false;
        
        // Simulate expensive operation
        System.out.println("🔥 [EXPENSIVE] Creating FIRST connection...");
        System.out.println("   - Loading drivers...");
        System.out.println("   - Handshaking...");
        System.out.println("   - Authenticating...");
        System.out.println("✅ Master Connection Created!\n");
        this.isConnected = true;
    }
    
    // Step 2: Override clone() method
    @Override
    protected Object clone() throws CloneNotSupportedException {
        System.out.println("📋 [CHEAP] Cloning existing connection...");
        // Shallow Copy (Primitives copy ho jate hain, Objects reference share karte hain)
        return super.clone();
    }
    
    public void executeQuery(String query) {
        System.out.println("Executing '" + query + "' on " + connectionString);
    }
    
    @Override
    public String toString() {
        return "DB Connection [" + connectionString + "] - " + (isConnected ? "Active" : "Inactive");
    }
}

public class PrototypeExample1 {
    public static void main(String[] args) throws CloneNotSupportedException {
        // Step 1: Pehla object expensive way se banao
        System.out.println("=== Creating Master Object (5 seconds) ===");
        DatabaseConnectionProto master = new DatabaseConnectionProto("jdbc:mysql://prod-db:3306/mydb", "admin", "secret");
        
        // Step 2: Baaki objects clone karo (milliseconds mein banenge)
        System.out.println("\n=== Creating 3 Clones (Milliseconds) ===");
        DatabaseConnectionProto clone1 = (DatabaseConnectionProto) master.clone();
        DatabaseConnectionProto clone2 = (DatabaseConnectionProto) master.clone();
        DatabaseConnectionProto clone3 = (DatabaseConnectionProto) master.clone();
        
        System.out.println("\n=== Using Connections ===");
        master.executeQuery("SELECT * FROM users");
        clone1.executeQuery("SELECT * FROM orders");
        clone2.executeQuery("SELECT * FROM products");
        
        System.out.println("\n✅ Total 4 connections created with only ONE expensive operation!");
    }
}
```

---

### Part 3: Deep Copy vs Shallow Copy (Critical Concept)

**Problem:** `Cloneable` sirf **Shallow Copy** karta hai. Agar object ke andar **mutable objects** hain (List, Date, Custom Objects), to wo share honge. Isliye hum **Deep Copy** ka custom method likhte hain.

#### Example 2: Deep Copy with Custom Prototype

```java
import java.util.ArrayList;
import java.util.List;

// Prototype Interface (Custom)
interface Prototype {
    Prototype deepCopy(); // Custom method
}

// Complex Object with Mutable Fields
class Employee implements Prototype {
    private String name;
    private String department;
    private List<String> skills; // Mutable List
    private Address address;     // Custom Mutable Object
    
    // Expensive Constructor (Database se data lao)
    public Employee(String name, String dept) {
        this.name = name;
        this.department = dept;
        this.skills = new ArrayList<>();
        this.address = new Address();
        
        // Simulate DB Call
        System.out.println("💾 [DB CALL] Fetching employee data from database for: " + name);
        System.out.println("   - Querying employee table...");
        System.out.println("   - Joining with skills table...");
        System.out.println("   - Fetching address details...");
        System.out.println("✅ Data loaded for " + name + "\n");
    }
    
    // Getters and Setters
    public void addSkill(String skill) { skills.add(skill); }
    public void setAddress(String city, String country) { 
        this.address.city = city;
        this.address.country = country;
    }
    public List<String> getSkills() { return skills; }
    public void setName(String name) { this.name = name; }
    
    // Custom Deep Copy Method (The MAGIC)
    @Override
    public Prototype deepCopy() {
        System.out.println("📋 [DEEP COPY] Creating clone of " + this.name);
        
        // Step 1: Naya Employee object (lekin constructor call nahi karenge, naya banana hai)
        Employee clone = new Employee(this.name, this.department); // Constructor call se DB hit hoga - AVOID THIS!
        // Actually humein new Employee() nahi karna chahiye, isliye better hai alag constructor ya setter use karein
        
        // Correct way: Copy using setters ya naya constructor
        Employee properClone = new Employee();
        properClone.name = this.name;
        properClone.department = this.department;
        
        // Step 2: Deep copy of List (Nayi List banao)
        properClone.skills = new ArrayList<>(this.skills);
        
        // Step 3: Deep copy of Address (Naya Address object)
        properClone.address = new Address();
        properClone.address.city = this.address.city;
        properClone.address.country = this.address.country;
        
        return properClone;
    }
    
    // Default constructor for cloning (avoids DB call)
    private Employee() {}
    
    public void display() {
        System.out.println("👤 Employee: " + name);
        System.out.println("   Dept: " + department);
        System.out.println("   Skills: " + skills);
        System.out.println("   Address: " + address.city + ", " + address.country);
    }
}

// Mutable Address Class
class Address {
    String city;
    String country;
}

public class DeepCopyExample {
    public static void main(String[] args) {
        System.out.println("=== Creating MASTER Employee (Expensive DB Call) ===\n");
        Employee emp1 = new Employee("Rahul Kumar", "Engineering");
        emp1.addSkill("Java");
        emp1.addSkill("Spring Boot");
        emp1.addSkill("AWS");
        emp1.setAddress("Bangalore", "India");
        emp1.display();
        
        System.out.println("\n=== Creating CLONES (No DB Call) ===\n");
        Employee emp2 = (Employee) emp1.deepCopy();
        Employee emp3 = (Employee) emp1.deepCopy();
        
        System.out.println("\n=== Modifying Clones (Should NOT affect Original) ===\n");
        emp2.setName("Priya Sharma");
        emp2.addSkill("Python");
        emp2.setAddress("Mumbai", "India");
        
        System.out.println("--- Original ---");
        emp1.display();
        
        System.out.println("\n--- Clone 1 (Modified) ---");
        emp2.display();
        
        System.out.println("\n--- Clone 2 (Unmodified) ---");
        emp3.display();
    }
}
```

---

### Part 4: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: Document Template System (MS Word Templates)

**Scenario:** Legal documents mein same format baar baar use hota hai. Template clone karke fill karo.

```java
// File: DocumentTemplate.java
import java.util.HashMap;
import java.util.Map;

interface DocumentPrototype extends Cloneable {
    DocumentPrototype clone();
    void customize(Map<String, String> placeholders);
    void print();
}

class LegalAgreement implements DocumentPrototype {
    private String templateContent;
    private String templateName;
    private Map<String, String> fields;
    
    // Expensive: Template database se load hoti hai
    public LegalAgreement(String name) {
        this.templateName = name;
        this.fields = new HashMap<>();
        System.out.println("📄 [LOADING] Fetching template '" + name + "' from Document DB...");
        System.out.println("   - Loading clauses...");
        System.out.println("   - Applying standard formatting...");
        System.out.println("✅ Template loaded!\n");
        
        // Sample template
        this.templateContent = "This Agreement is made between [PARTY_A] and [PARTY_B] on [DATE]. " +
                               "The purpose is [PURPOSE].";
    }
    
    @Override
    public DocumentPrototype clone() {
        try {
            LegalAgreement clone = (LegalAgreement) super.clone();
            // Deep copy of Map
            clone.fields = new HashMap<>(this.fields);
            System.out.println("📋 [CLONED] Template copied for new agreement");
            return clone;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
    
    @Override
    public void customize(Map<String, String> placeholders) {
        this.fields.putAll(placeholders);
    }
    
    @Override
    public void print() {
        String content = this.templateContent;
        for (Map.Entry<String, String> entry : fields.entrySet()) {
            content = content.replace("[" + entry.getKey() + "]", entry.getValue());
        }
        System.out.println("\n📜 " + templateName);
        System.out.println("--------------------------------");
        System.out.println(content);
        System.out.println("--------------------------------\n");
    }
}

public class DocumentGenerator {
    public static void main(String[] args) {
        // Sirf ek baar template load karo
        LegalAgreement masterTemplate = new LegalAgreement("Non-Disclosure Agreement (NDA)");
        
        // Agreement 1 ke liye clone
        LegalAgreement agreement1 = (LegalAgreement) masterTemplate.clone();
        Map<String, String> data1 = new HashMap<>();
        data1.put("PARTY_A", "Google Inc.");
        data1.put("PARTY_B", "Startup XYZ");
        data1.put("DATE", "22-Apr-2026");
        data1.put("PURPOSE", "Discussing potential acquisition");
        agreement1.customize(data1);
        agreement1.print();
        
        // Agreement 2 ke liye clone
        LegalAgreement agreement2 = (LegalAgreement) masterTemplate.clone();
        Map<String, String> data2 = new HashMap<>();
        data2.put("PARTY_A", "Microsoft Corp.");
        data2.put("PARTY_B", "App Developer Ltd.");
        data2.put("DATE", "23-Apr-2026");
        data2.put("PURPOSE", "API integration partnership");
        agreement2.customize(data2);
        agreement2.print();
    }
}
```

---

#### Example 2: Game Character Spawner (Enemy Cloning)

**Scenario:** Game mein ek hi type ke 100 enemies spawn karne hain. Har baar 3D model load karna expensive hai.

```java
// File: GameCharacter.java
import java.util.Random;

interface GameCharacter extends Cloneable {
    GameCharacter clone();
    void setPosition(int x, int y);
    void attack();
}

class OrcWarrior implements GameCharacter {
    private String model3D;
    private String texture;
    private int health;
    private int x, y;
    private static int cloneCount = 0;
    
    // Expensive: 3D Model aur Texture load karna
    public OrcWarrior() {
        System.out.println("🐉 [HEAVY] Loading 3D Orc Model from disk... (2 seconds)");
        System.out.println("   - Loading mesh vertices...");
        System.out.println("   - Loading high-res textures...");
        System.out.println("   - Applying shaders...");
        this.model3D = "Orc_Berserker.fbx";
        this.texture = "Orc_Skin_Red.png";
        this.health = 100;
        this.cloneCount = 0;
        System.out.println("✅ Orc Warrior Model Ready!\n");
    }
    
    @Override
    public GameCharacter clone() {
        try {
            OrcWarrior clone = (OrcWarrior) super.clone();
            cloneCount++;
            System.out.println("👥 [CLONE #" + cloneCount + "] Orc Warrior copied (Cheap operation)");
            return clone;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
    
    @Override
    public void setPosition(int x, int y) {
        this.x = x;
        this.y = y;
    }
    
    @Override
    public void attack() {
        System.out.println("⚔️ Orc at (" + x + "," + y + ") attacks for 25 damage! [Health: " + health + "]");
    }
    
    public void takeDamage(int damage) {
        this.health -= damage;
    }
}

public class GameLevel {
    public static void main(String[] args) {
        System.out.println("=== LOADING LEVEL 1 ===\n");
        
        // Sirf ek baar model load karo
        OrcWarrior prototype = new OrcWarrior();
        
        System.out.println("=== SPAWNING ENEMY HORDE ===\n");
        
        // 100 Orcs spawn karo (Bina kisi performance hit ke)
        Random rand = new Random();
        GameCharacter[] enemies = new GameCharacter[5]; // Demo ke liye 5
        
        for (int i = 0; i < enemies.length; i++) {
            enemies[i] = prototype.clone();
            enemies[i].setPosition(rand.nextInt(100), rand.nextInt(100));
        }
        
        System.out.println("\n=== ENEMIES ATTACKING ===\n");
        for (GameCharacter enemy : enemies) {
            enemy.attack();
        }
    }
}
```

---

#### Example 3: Configuration Manager (Environment Configs)

**Scenario:** Dev, QA, Prod environments ke liye base config clone karni hai.

```java
// File: AppConfig.java
import java.util.Properties;

class Configuration implements Cloneable {
    private Properties properties;
    private String environment;
    
    // Expensive: Config file ya remote server se load
    public Configuration() {
        System.out.println("⚙️ [LOADING] Reading base configuration from config-server...");
        System.out.println("   - Connecting to http://config-server:8888");
        System.out.println("   - Fetching application.yml");
        System.out.println("   - Parsing 150+ properties...");
        
        this.properties = new Properties();
        // Default values
        properties.setProperty("app.name", "MyApp");
        properties.setProperty("app.version", "3.2.1");
        properties.setProperty("db.pool.size", "10");
        properties.setProperty("cache.enabled", "true");
        properties.setProperty("log.level", "INFO");
        
        System.out.println("✅ Base configuration loaded!\n");
    }
    
    @Override
    protected Configuration clone() {
        try {
            Configuration clone = (Configuration) super.clone();
            // Deep copy of Properties
            clone.properties = new Properties();
            clone.properties.putAll(this.properties);
            System.out.println("📋 [CLONED] Configuration copied for environment customization");
            return clone;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
    
    public void setEnvironment(String env) {
        this.environment = env;
    }
    
    public void override(String key, String value) {
        properties.setProperty(key, value);
        System.out.println("   ↳ Overriding " + key + " = " + value);
    }
    
    public void display() {
        System.out.println("\n🔧 Configuration for " + environment.toUpperCase());
        System.out.println("--------------------------------");
        properties.forEach((k, v) -> System.out.println("  " + k + " = " + v));
        System.out.println("--------------------------------\n");
    }
}

public class ConfigManager {
    public static void main(String[] args) {
        // Base config ek baar load karo
        Configuration baseConfig = new Configuration();
        
        // Dev environment config
        Configuration devConfig = baseConfig.clone();
        devConfig.setEnvironment("Development");
        devConfig.override("db.url", "jdbc:mysql://localhost:3306/devdb");
        devConfig.override("cache.enabled", "false");
        devConfig.override("log.level", "DEBUG");
        devConfig.display();
        
        // Prod environment config
        Configuration prodConfig = baseConfig.clone();
        prodConfig.setEnvironment("Production");
        prodConfig.override("db.url", "jdbc:mysql://prod-cluster:3306/proddb");
        prodConfig.override("db.pool.size", "100");
        prodConfig.override("cache.enabled", "true");
        prodConfig.override("log.level", "ERROR");
        prodConfig.display();
    }
}
```

---

#### Example 4: UI Component Cache (Button Factory)

**Scenario:** Swing/JavaFX mein same styled button baar baar banana.

```java
// File: UIComponentCache.java
import java.util.HashMap;
import java.util.Map;

interface UIComponent extends Cloneable {
    UIComponent clone();
    void render();
    void setText(String text);
}

class StyledButton implements UIComponent {
    private String text;
    private String backgroundColor;
    private String fontFamily;
    private int fontSize;
    private int width;
    private int height;
    
    // Expensive: CSS parsing aur font loading
    public StyledButton(String styleName) {
        System.out.println("🎨 [PARSING] Loading style '" + styleName + "' from CSS...");
        System.out.println("   - Parsing gradients...");
        System.out.println("   - Loading custom fonts...");
        System.out.println("   - Calculating dimensions...");
        
        // Simulate different styles
        if (styleName.equals("Primary")) {
            this.backgroundColor = "#007bff";
            this.fontFamily = "Arial";
            this.fontSize = 14;
            this.width = 120;
            this.height = 40;
        } else if (styleName.equals("Secondary")) {
            this.backgroundColor = "#6c757d";
            this.fontFamily = "Helvetica";
            this.fontSize = 14;
            this.width = 120;
            this.height = 40;
        }
        System.out.println("✅ Button style ready!\n");
    }
    
    @Override
    public UIComponent clone() {
        try {
            System.out.println("🔄 [CLONED] Button cloned with same styling");
            return (UIComponent) super.clone();
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
    
    @Override
    public void setText(String text) {
        this.text = text;
    }
    
    @Override
    public void render() {
        System.out.println("🖱️ Rendered Button: '" + text + "' [" + backgroundColor + "] " + width + "x" + height);
    }
}

// Prototype Registry (Factory + Prototype Pattern Combined)
class ButtonRegistry {
    private Map<String, UIComponent> prototypes = new HashMap<>();
    
    public void registerPrototype(String key, UIComponent prototype) {
        prototypes.put(key, prototype);
    }
    
    public UIComponent createButton(String type) {
        return prototypes.get(type).clone();
    }
}

public class UIApplication {
    public static void main(String[] args) {
        // Styles ek baar load karo (Expensive)
        System.out.println("=== INITIALIZING UI STYLES ===\n");
        StyledButton primaryProto = new StyledButton("Primary");
        StyledButton secondaryProto = new StyledButton("Secondary");
        
        // Registry mein register karo
        ButtonRegistry registry = new ButtonRegistry();
        registry.registerPrototype("PRIMARY", primaryProto);
        registry.registerPrototype("SECONDARY", secondaryProto);
        
        System.out.println("\n=== BUILDING UI ===\n");
        
        // Ab buttons clone karke banate jao
        UIComponent btn1 = registry.createButton("PRIMARY");
        btn1.setText("Submit");
        btn1.render();
        
        UIComponent btn2 = registry.createButton("SECONDARY");
        btn2.setText("Cancel");
        btn2.render();
        
        UIComponent btn3 = registry.createButton("PRIMARY");
        btn3.setText("Save");
        btn3.render();
        
        UIComponent btn4 = registry.createButton("PRIMARY");
        btn4.setText("Update");
        btn4.render();
    }
}
```

---

#### Example 5: Report Generator (Cached Queries)

**Scenario:** Reporting tool mein same complex query structure baar baar use hoti hai.

```java
// File: ReportTemplate.java
import java.util.ArrayList;
import java.util.List;

class ReportQuery implements Cloneable {
    private String baseQuery;
    private List<String> columns;
    private List<String> joins;
    private List<String> groupBy;
    private String orderBy;
    
    // Expensive: Query optimizer se guzarna
    public ReportQuery(String reportType) {
        System.out.println("📊 [BUILDING] Creating optimized query for '" + reportType + "' report...");
        System.out.println("   - Analyzing table statistics...");
        System.out.println("   - Determining optimal join order...");
        System.out.println("   - Creating execution plan...");
        
        this.columns = new ArrayList<>();
        this.joins = new ArrayList<>();
        this.groupBy = new ArrayList<>();
        
        if (reportType.equals("Sales")) {
            this.baseQuery = "SELECT {COLUMNS} FROM sales_fact";
            this.columns.add("SUM(amount) as total_sales");
            this.columns.add("COUNT(*) as transaction_count");
            this.joins.add("INNER JOIN dim_product ON sales_fact.product_id = dim_product.id");
            this.joins.add("INNER JOIN dim_date ON sales_fact.date_id = dim_date.id");
            this.groupBy.add("dim_product.category");
            this.groupBy.add("dim_date.month");
            this.orderBy = "dim_date.month DESC";
        }
        System.out.println("✅ Query plan cached!\n");
    }
    
    @Override
    protected ReportQuery clone() {
        try {
            ReportQuery clone = (ReportQuery) super.clone();
            // Deep copy of lists
            clone.columns = new ArrayList<>(this.columns);
            clone.joins = new ArrayList<>(this.joins);
            clone.groupBy = new ArrayList<>(this.groupBy);
            System.out.println("📋 [CLONED] Query structure copied for customization");
            return clone;
        } catch (CloneNotSupportedException e) {
            return null;
        }
    }
    
    public void addFilter(String filter) {
        // Custom filter add karna
        System.out.println("   ↳ Adding filter: " + filter);
    }
    
    public void execute() {
        String query = baseQuery.replace("{COLUMNS}", String.join(", ", columns));
        System.out.println("\n🔍 Executing Query:");
        System.out.println(query);
        joins.forEach(j -> System.out.println(j));
        if (!groupBy.isEmpty()) System.out.println("GROUP BY " + String.join(", ", groupBy));
        if (orderBy != null) System.out.println("ORDER BY " + orderBy);
        System.out.println("---");
        System.out.println("✅ Report generated successfully!\n");
    }
}

public class ReportingEngine {
    public static void main(String[] args) {
        // Complex query structure ek baar banao
        ReportQuery salesTemplate = new ReportQuery("Sales");
        
        // Different reports ke liye clone karo
        System.out.println("=== GENERATING REPORTS ===\n");
        
        // Monthly Sales Report
        ReportQuery monthlyReport = salesTemplate.clone();
        monthlyReport.addFilter("dim_date.month = 'January'");
        monthlyReport.execute();
        
        // Product Category Report
        ReportQuery categoryReport = salesTemplate.clone();
        categoryReport.addFilter("dim_product.category = 'Electronics'");
        categoryReport.execute();
        
        // Regional Report
        ReportQuery regionReport = salesTemplate.clone();
        regionReport.addFilter("dim_store.region = 'North'");
        regionReport.execute();
    }
}
```

---

### Part 5: Prototype Pattern - Key Points & Interview Answers

| Aspect | Details |
|:---|:---|
| **Use When** | Object creation is expensive (DB calls, File I/O, Network calls, Complex calculations) |
| **Java Implementation** | `Cloneable` interface + `clone()` method override |
| **Shallow Copy** | Primitives copy hote hain, Objects share hote hain |
| **Deep Copy** | Sab kuch naya banta hai (Custom logic required) |
| **Prototype Registry** | Factory pattern ke saath combine karke cache maintain karna |

**Interview Question:** *What is the difference between Prototype and Factory Pattern?*

**Answer:** *"Sir, Factory pattern **naya object banata hai** from scratch har baar. Prototype pattern **existing object ko clone karta hai** jo pehle se initialized hai. Jab object creation expensive ho (jaise DB connection, 3D model loading) tab Prototype use karte hain. Factory mein `new` keyword use hota hai, Prototype mein `clone()`."*

**Interview Question:** *What is the disadvantage of Prototype Pattern?*

**Answer:** *"Sir, deep copy implement karna complex ho sakta hai specially agar circular references ho. Java ka default `clone()` shallow copy karta hai jo bugs create kar sakta hai agar hum careful nahi hain."*
