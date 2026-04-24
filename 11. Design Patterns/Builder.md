### Part 1: Problem Statement (Telescoping Constructor Horror)

Sochiye aap ek **Computer** object bana rahe ho. Kuch parts compulsory hain (CPU, RAM), kuch optional hain (Graphics Card, Bluetooth, WiFi).

Bina Builder ke aap kya karoge?

#### ❌ BAD PRACTICE: Telescoping Constructors (The Ugly Code)

```java
class Computer {
    // Required
    private String cpu;
    private String ram;
    // Optional (Bahut saare hain)
    private String gpu;
    private boolean bluetooth;
    private boolean wifi;
    private String os;

    // Constructor 1: Sirf Required
    public Computer(String cpu, String ram) {
        this(cpu, ram, null); // null pass karna pada
    }

    // Constructor 2: Required + GPU
    public Computer(String cpu, String ram, String gpu) {
        this(cpu, ram, gpu, false); // false pass karna pada
    }

    // Constructor 3: Saara kuch... ye list badhti jaati hai!
    public Computer(String cpu, String ram, String gpu, boolean bluetooth) {
        this.cpu = cpu;
        this.ram = ram;
        this.gpu = gpu;
        this.bluetooth = bluetooth;
        // abhi wifi set nahi, OS set nahi...
    }
    
    // Problem 1: Kitne constructors banaoge?
    // Problem 2: Sequence yaad rakhna mushkil (boolean vs String)
    // Problem 3: new Computer("i7", "16GB", true, "RTX 4060") -> Compile error, sequence galat hai
}
```

**Dikkatein:**
1. **Telescoping Constructor Anti-Pattern:** Constructors ki lambi list.
2. **Readability Zero:** `new Computer("i7", "16GB", null, true, false, "Windows")` padh ke samajh nahi aata kis cheez ka kya value hai.
3. **Immutability Tough:** Setters use karoge to object mutable ho jayega.

---

### Part 2: Builder Pattern Solution (Good Example)

Builder Pattern ek **inner static class** use karta hai jo step-by-step values set karta hai aur finally `build()` method se main object return karta hai.

#### Step 1: Computer Class with Builder

```java
// ✅ GOOD PRACTICE: Computer class with Builder
class Computer {
    // Required parameters (final for immutability)
    private final String cpu;
    private final String ram;
    
    // Optional parameters
    private final String gpu;
    private final boolean bluetooth;
    private final boolean wifi;
    private final String os;

    // PRIVATE Constructor: Sirf Builder hi isko call kar sakta hai
    private Computer(Builder builder) {
        this.cpu = builder.cpu;
        this.ram = builder.ram;
        this.gpu = builder.gpu;
        this.bluetooth = builder.bluetooth;
        this.wifi = builder.wifi;
        this.os = builder.os;
    }

    // Getters (Setters nahi honge agar Immutable chahiye)
    public String getCpu() { return cpu; }
    public String getRam() { return ram; }
    // ... other getters

    @Override
    public String toString() {
        return "Computer [cpu=" + cpu + ", ram=" + ram + ", gpu=" + gpu + 
               ", bluetooth=" + bluetooth + ", wifi=" + wifi + ", os=" + os + "]";
    }

    // **************** INNER STATIC BUILDER CLASS ****************
    public static class Builder {
        // Required parameters
        private final String cpu;
        private final String ram;
        
        // Optional parameters - initialized to default values
        private String gpu = "Integrated";
        private boolean bluetooth = false;
        private boolean wifi = false;
        private String os = "DOS";

        // Builder Constructor (Sirf Required fields leta hai)
        public Builder(String cpu, String ram) {
            this.cpu = cpu;
            this.ram = ram;
        }

        // Methods for Optional fields (Yehi to Magic hai)
        // Har method "this" return karta hai for Method Chaining
        public Builder setGpu(String gpu) {
            this.gpu = gpu;
            return this;
        }

        public Builder setBluetooth(boolean bluetooth) {
            this.bluetooth = bluetooth;
            return this;
        }

        public Builder setWifi(boolean wifi) {
            this.wifi = wifi;
            return this;
        }

        public Builder setOs(String os) {
            this.os = os;
            return this;
        }

        // Final build method
        public Computer build() {
            // Yahan validation laga sakte ho
            // e.g., if(cpu == null) throw new Exception...
            return new Computer(this);
        }
    }
}
```

#### Step 2: Client Code (Maza aa gaya!)

```java
public class ComputerShop {
    public static void main(String[] args) {
        // Dekho Kitna Clean aur Readable hai!
        // Method Chaining ki wajah se pata chal raha hai kya set ho raha hai
        
        Computer gamingPC = new Computer.Builder("Intel i9", "32GB")
                                .setGpu("Nvidia RTX 4090")
                                .setBluetooth(true)
                                .setWifi(true)
                                .setOs("Windows 11 Pro")
                                .build();

        Computer officePC = new Computer.Builder("Intel i5", "16GB")
                                .setOs("Ubuntu Linux")
                                .setWifi(true)
                                // GPU set nahi kiya to default Integrated rahega
                                .build();

        Computer basicPC = new Computer.Builder("Celeron", "4GB")
                                .build(); // Sab default values

        System.out.println(gamingPC);
        System.out.println(officePC);
        System.out.println(basicPC);
    }
}
```

---

### Part 3: 5 Real-World Scenarios (Where Builder Shines)

Yahan 5 aise scenarios hain jahan Builder ke bina life jahannum hai.

#### Scenario 1: API Request Payload (HTTP Client)
**Problem:** Ek POST request mein 10 headers, 5 query params, aur body hoti hai.
```java
HttpRequest request = new HttpRequest.Builder("https://api.example.com")
                        .method("POST")
                        .header("Authorization", "Bearer token")
                        .header("Content-Type", "application/json")
                        .body("{'name':'John'}")
                        .timeout(5000)
                        .build();
```

#### Scenario 2: Pizza Ordering System (Bahut Saare Toppings)
**Problem:** Pizza mein crust compulsory hai, baaki 20 toppings optional hain.
```java
Pizza pizza = new Pizza.Builder("Thin Crust", "Medium")
                .addCheese(true)
                .addPepperoni(true)
                .addMushrooms(false)
                .addOlives(true)
                .build();
```

#### Scenario 3: Lombok Library (Java Magic)
**Problem:** Java mein har class ke liye Builder likhna boring hai.
**Solution:** Lombok ki `@Builder` annotation.
```java
@Builder
public class User {
    private String name;
    private int age;
}
// Usage: User.builder().name("Rahul").age(25).build();
```
*(Lombok internally yahi sab code generate karta hai jo humne upar likha)*

#### Scenario 4: Configuration Objects (Database Connection)
**Problem:** Database connection ke 15 parameters hote hain (Pool Size, Timeout, SSL, etc.).
```java
DatabaseConfig config = new DatabaseConfig.Builder("jdbc:mysql://localhost")
                        .setUsername("admin")
                        .setPassword("secret")
                        .setMaxPoolSize(50)
                        .setConnectionTimeout(30000)
                        .enableSSL(true)
                        .build();
```

#### Scenario 5: PDF Document Generator
**Problem:** PDF ke liye Margin, Font, Header, Footer, Watermark sab optional hain.
```java
PDFDocument doc = new PDFDocument.Builder("Report.pdf")
                    .setMargins(10, 10, 10, 10)
                    .setFont("Arial", 12)
                    .addHeader("Company Confidential")
                    .addWatermark("DRAFT")
                    .build();
```

---

### Part 4: Builder Pattern ka Director (Advanced Concept)

Kabhi kabhi Builder ko alag-alag tarike se call karne ke liye ek **Director** class banate hain.

**Use Case:** Aapke paas predefined configurations hain. Jaise "Gaming PC" ka ek fixed template hai.

```java
class ComputerDirector {
    private Computer.Builder builder;

    public ComputerDirector(Computer.Builder builder) {
        this.builder = builder;
    }

    // Predefined Templates
    public Computer constructGamingPC() {
        return builder.setGpu("RTX 4080")
                      .setBluetooth(true)
                      .setWifi(true)
                      .setOs("Windows 11")
                      .build();
    }

    public Computer constructOfficePC() {
        return builder.setGpu("Integrated")
                      .setWifi(true)
                      .setOs("Windows 10 Pro")
                      .build();
    }
}

// Usage:
Computer.Builder builder = new Computer.Builder("i7", "16GB");
ComputerDirector director = new ComputerDirector(builder);
Computer pc = director.constructGamingPC(); // Ready-made PC mil gaya
```

---

### Part 5: Builder vs Other Patterns (Comparison)

| Pattern | Use Case | Key Point |
| :--- | :--- | :--- |
| **Factory** | Polymorphic creation (Payment types) | "Konsa object" decide karna |
| **Abstract Factory** | Families of objects (UI Themes) | "Combo pack" guarantee karna |
| **Builder** | Complex object with many params | "Step by step" banana, Readability |

### Part 6: Interview Tip (Must Know)

**Question:** Why use Builder over Setters?

**Answer:**
1. **Immutability:** Builder se bana object **Immutable** hota hai (fields `final` hote hain). Setters se object mutable hota hai jo Multi-threaded environment mein problem de sakta hai.
2. **Consistency:** Builder `build()` method mein saari validation ek saath kar sakta hai. Setters se object kabhi bhi incomplete state mein ho sakta hai.
3. **Fluent API:** Method chaining se code readable aur maintainable banta hai.

**Question:** What is the disadvantage of Builder Pattern?
**Answer:** *"Sir, Boilerplate code badh jaata hai. Har class ke liye Builder inner class likhni padti hai. Lekin Lombok jaise tools is problem ko solve kar dete hain."*

---

### Part 7: Code Without Lombok vs With Lombok

| Without Lombok (50+ Lines) | With Lombok (5 Lines) |
| :--- | :--- |
| `private static class Builder {...}` | `@Builder` |
| `public Builder setX(X x) {...}` | `@Singular` for collections |
| Tedious to write | Focus on Business Logic |

**Final Tip:** Agar parameter 4 se kam hain to Builder mat use karo (Overkill hai). 5+ parameters ho ya future mein badhne wale ho to turant Builder daal do.


---

### Example 1: Pizza Ordering System (Restaurant App)

**Scenario:** Zomato/Swiggy jaisa app. Pizza mein size aur crust to compulsory hai, lekin 20 toppings optional hain.

```java
// File: Pizza.java
import java.util.ArrayList;
import java.util.List;

class Pizza {
    // Required
    private final String size;
    private final String crust;
    
    // Optional
    private final boolean extraCheese;
    private final List<String> toppings;
    private final String specialInstruction;

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.crust = builder.crust;
        this.extraCheese = builder.extraCheese;
        this.toppings = builder.toppings;
        this.specialInstruction = builder.specialInstruction;
    }

    public void serve() {
        System.out.println("🍕 Pizza Ready!");
        System.out.println("Size: " + size);
        System.out.println("Crust: " + crust);
        System.out.println("Extra Cheese: " + (extraCheese ? "Yes" : "No"));
        System.out.println("Toppings: " + (toppings.isEmpty() ? "None" : toppings));
        System.out.println("Note: " + (specialInstruction != null ? specialInstruction : "None"));
        System.out.println("------------------------");
    }

    // BUILDER CLASS
    public static class Builder {
        // Required
        private final String size;
        private final String crust;
        
        // Optional (with defaults)
        private boolean extraCheese = false;
        private List<String> toppings = new ArrayList<>();
        private String specialInstruction;

        public Builder(String size, String crust) {
            this.size = size;
            this.crust = crust;
        }

        public Builder extraCheese(boolean val) {
            this.extraCheese = val;
            return this;
        }

        public Builder addTopping(String topping) {
            this.toppings.add(topping);
            return this;
        }

        public Builder specialInstruction(String msg) {
            this.specialInstruction = msg;
            return this;
        }

        public Pizza build() {
            // Validation
            if (size == null || crust == null) {
                throw new IllegalStateException("Size and Crust are mandatory!");
            }
            return new Pizza(this);
        }
    }
}

// Usage (Main Method)
public class PizzaShop {
    public static void main(String[] args) {
        Pizza vegDelight = new Pizza.Builder("Large", "Thin Crust")
                            .extraCheese(true)
                            .addTopping("Mushrooms")
                            .addTopping("Olives")
                            .addTopping("Onions")
                            .specialInstruction("Less spicy please")
                            .build();
        vegDelight.serve();

        Pizza simpleMargherita = new Pizza.Builder("Medium", "Cheese Burst")
                                 .build(); // No extras
        simpleMargherita.serve();
    }
}
```

---

### Example 2: API Request Builder (HTTP Client)

**Scenario:** Microservices communication. Ek HTTP Request mein Headers, Body, Timeout sab set karna hai.

```java
// File: HttpRequest.java
import java.util.HashMap;
import java.util.Map;

class HttpRequest {
    private final String url;
    private final String method;
    private final Map<String, String> headers;
    private final String body;
    private final int timeout;

    private HttpRequest(Builder builder) {
        this.url = builder.url;
        this.method = builder.method;
        this.headers = builder.headers;
        this.body = builder.body;
        this.timeout = builder.timeout;
    }

    public void execute() {
        System.out.println("🌐 Executing Request:");
        System.out.println("URL: " + url);
        System.out.println("Method: " + method);
        System.out.println("Headers: " + headers);
        System.out.println("Body: " + (body != null ? body : "Empty"));
        System.out.println("Timeout: " + timeout + "ms");
        System.out.println("------------------------");
    }

    public static class Builder {
        private final String url;
        private String method = "GET"; // Default
        private Map<String, String> headers = new HashMap<>();
        private String body;
        private int timeout = 5000; // Default 5 sec

        public Builder(String url) {
            this.url = url;
        }

        public Builder method(String method) {
            this.method = method;
            return this;
        }

        public Builder addHeader(String key, String value) {
            this.headers.put(key, value);
            return this;
        }

        public Builder body(String body) {
            this.body = body;
            return this;
        }

        public Builder timeout(int timeout) {
            this.timeout = timeout;
            return this;
        }

        public HttpRequest build() {
            return new HttpRequest(this);
        }
    }
}

public class ApiClient {
    public static void main(String[] args) {
        HttpRequest postRequest = new HttpRequest.Builder("https://api.example.com/users")
                                    .method("POST")
                                    .addHeader("Authorization", "Bearer xyz123")
                                    .addHeader("Content-Type", "application/json")
                                    .body("{\"name\": \"Rahul\", \"age\": 30}")
                                    .timeout(10000)
                                    .build();
        postRequest.execute();

        HttpRequest getRequest = new HttpRequest.Builder("https://api.example.com/health")
                                    .build();
        getRequest.execute();
    }
}
```

---

### Example 3: Database Connection Pool Config

**Scenario:** Production level app jahan DB connection ke 10-15 parameters hote hain.

```java
// File: DatabaseConfig.java
class DatabaseConfig {
    private final String url;
    private final String username;
    private final String password;
    private final int maxPoolSize;
    private final int minIdle;
    private final long connectionTimeout;
    private final boolean enableSSL;
    private final String driverClass;

    private DatabaseConfig(Builder builder) {
        this.url = builder.url;
        this.username = builder.username;
        this.password = builder.password;
        this.maxPoolSize = builder.maxPoolSize;
        this.minIdle = builder.minIdle;
        this.connectionTimeout = builder.connectionTimeout;
        this.enableSSL = builder.enableSSL;
        this.driverClass = builder.driverClass;
    }

    public void connect() {
        System.out.println("💾 Database Connection Established!");
        System.out.println("URL: " + url);
        System.out.println("User: " + username);
        System.out.println("Pool Size: " + maxPoolSize);
        System.out.println("SSL: " + (enableSSL ? "Enabled" : "Disabled"));
        System.out.println("------------------------");
    }

    public static class Builder {
        // Required
        private final String url;
        private final String username;
        private final String password;
        
        // Optional (Production defaults)
        private int maxPoolSize = 10;
        private int minIdle = 2;
        private long connectionTimeout = 30000;
        private boolean enableSSL = true;
        private String driverClass = "com.mysql.cj.jdbc.Driver";

        public Builder(String url, String username, String password) {
            this.url = url;
            this.username = username;
            this.password = password;
        }

        public Builder maxPoolSize(int val) {
            this.maxPoolSize = val;
            return this;
        }

        public Builder minIdle(int val) {
            this.minIdle = val;
            return this;
        }

        public Builder connectionTimeout(long val) {
            this.connectionTimeout = val;
            return this;
        }

        public Builder enableSSL(boolean val) {
            this.enableSSL = val;
            return this;
        }

        public Builder driverClass(String val) {
            this.driverClass = val;
            return this;
        }

        public DatabaseConfig build() {
            // Validation
            if (url == null || username == null) {
                throw new IllegalStateException("URL and Username are mandatory!");
            }
            return new DatabaseConfig(this);
        }
    }
}

public class DatabaseManager {
    public static void main(String[] args) {
        // Production Setup
        DatabaseConfig prodConfig = new DatabaseConfig.Builder(
                                        "jdbc:mysql://prod-db:3306/mydb", 
                                        "admin", 
                                        "SuperSecret123")
                                    .maxPoolSize(50)
                                    .minIdle(5)
                                    .enableSSL(true)
                                    .build();
        prodConfig.connect();

        // Local Dev Setup
        DatabaseConfig devConfig = new DatabaseConfig.Builder(
                                        "jdbc:mysql://localhost:3306/devdb", 
                                        "root", 
                                        "")
                                    .maxPoolSize(5)
                                    .enableSSL(false)
                                    .build();
        devConfig.connect();
    }
}
```

---

### Example 4: Resume/CV Generator

**Scenario:** Naukri.com jaisa portal. User ke paas bahut saari optional fields hain (Projects, Skills, Certifications).

```java
// File: Resume.java
import java.util.ArrayList;
import java.util.List;

class Resume {
    // Required
    private final String name;
    private final String email;
    
    // Optional Sections
    private final String summary;
    private final List<String> skills;
    private final List<String> experience;
    private final String template; // Modern, Classic

    private Resume(Builder builder) {
        this.name = builder.name;
        this.email = builder.email;
        this.summary = builder.summary;
        this.skills = builder.skills;
        this.experience = builder.experience;
        this.template = builder.template;
    }

    public void preview() {
        System.out.println("📄 Resume Preview (" + template + " Template)");
        System.out.println("Name: " + name);
        System.out.println("Email: " + email);
        if (summary != null) System.out.println("Summary: " + summary);
        if (!skills.isEmpty()) System.out.println("Skills: " + skills);
        if (!experience.isEmpty()) System.out.println("Experience: " + experience);
        System.out.println("------------------------");
    }

    public static class Builder {
        // Required
        private final String name;
        private final String email;
        
        // Optional
        private String summary;
        private List<String> skills = new ArrayList<>();
        private List<String> experience = new ArrayList<>();
        private String template = "Classic"; // Default

        public Builder(String name, String email) {
            this.name = name;
            this.email = email;
        }

        public Builder summary(String val) {
            this.summary = val;
            return this;
        }

        public Builder addSkill(String skill) {
            this.skills.add(skill);
            return this;
        }

        public Builder addExperience(String exp) {
            this.experience.add(exp);
            return this;
        }

        public Builder template(String val) {
            this.template = val;
            return this;
        }

        public Resume build() {
            return new Resume(this);
        }
    }
}

public class ResumeBuilderApp {
    public static void main(String[] args) {
        Resume fresherResume = new Resume.Builder("Priya Sharma", "priya@email.com")
                                .summary("Enthusiastic CS Graduate")
                                .addSkill("Java")
                                .addSkill("Spring Boot")
                                .addSkill("SQL")
                                .template("Modern")
                                .build();
        fresherResume.preview();

        Resume expertResume = new Resume.Builder("Vikram Singh", "vikram@email.com")
                                .addExperience("5 years at Google")
                                .addExperience("2 years at Startup")
                                .addSkill("Leadership")
                                .build();
        expertResume.preview();
    }
}
```

---

### Example 5: Computer Assembler (Gaming PC Builder)

**Scenario:** Custom PC build karna jahan motherboard, RAM, Storage sab alag alag combination mein ho sakte hain.

```java
// File: GamingPC.java
class GamingPC {
    // Required
    private final String cpu;
    private final String motherboard;
    
    // Optional
    private final String gpu;
    private final int ramGB;
    private final int storageGB;
    private final boolean liquidCooling;
    private final String rgbColor;

    private GamingPC(Builder builder) {
        this.cpu = builder.cpu;
        this.motherboard = builder.motherboard;
        this.gpu = builder.gpu;
        this.ramGB = builder.ramGB;
        this.storageGB = builder.storageGB;
        this.liquidCooling = builder.liquidCooling;
        this.rgbColor = builder.rgbColor;
    }

    public void bootUp() {
        System.out.println("💻 Gaming PC Booting...");
        System.out.println("CPU: " + cpu);
        System.out.println("Motherboard: " + motherboard);
        System.out.println("GPU: " + (gpu != null ? gpu : "Integrated Graphics"));
        System.out.println("RAM: " + ramGB + "GB");
        System.out.println("Storage: " + storageGB + "GB SSD");
        System.out.println("Cooling: " + (liquidCooling ? "Liquid AIO" : "Air Cooler"));
        System.out.println("RGB: " + (rgbColor != null ? rgbColor : "Off"));
        System.out.println("------------------------");
    }

    public static class Builder {
        // Required
        private final String cpu;
        private final String motherboard;
        
        // Optional (Sensible Defaults)
        private String gpu = null; // Integrated by default
        private int ramGB = 8;
        private int storageGB = 256;
        private boolean liquidCooling = false;
        private String rgbColor = null;

        public Builder(String cpu, String motherboard) {
            this.cpu = cpu;
            this.motherboard = motherboard;
        }

        public Builder gpu(String val) {
            this.gpu = val;
            return this;
        }

        public Builder ramGB(int val) {
            this.ramGB = val;
            return this;
        }

        public Builder storageGB(int val) {
            this.storageGB = val;
            return this;
        }

        public Builder liquidCooling(boolean val) {
            this.liquidCooling = val;
            return this;
        }

        public Builder rgbColor(String val) {
            this.rgbColor = val;
            return this;
        }

        public GamingPC build() {
            // Complex validation example
            if (gpu != null && gpu.contains("RTX 4090") && ramGB < 16) {
                System.out.println("⚠️ Warning: RTX 4090 recommended with at least 16GB RAM!");
            }
            return new GamingPC(this);
        }
    }
}

public class PcAssembler {
    public static void main(String[] args) {
        GamingPC budgetPC = new GamingPC.Builder("Intel i3", "H610M")
                            .ramGB(8)
                            .storageGB(512)
                            .build();
        budgetPC.bootUp();

        GamingPC ultimatePC = new GamingPC.Builder("AMD Ryzen 9", "X670E")
                            .gpu("Nvidia RTX 4090")
                            .ramGB(64)
                            .storageGB(2000)
                            .liquidCooling(true)
                            .rgbColor("Rainbow Wave")
                            .build();
        ultimatePC.bootUp();
    }
}
```
