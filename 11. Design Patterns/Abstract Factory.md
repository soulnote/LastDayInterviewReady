### Part 1: The Problem Statement (Theme Family)

Sochiye aap Windows bana rahe ho. Ek button, ek checkbox chahiye.
**Dikkat:** Kabhi Light Theme dikhani hai, Kabhi Dark Theme.
**Rule:** Agar Theme Dark hai to **Saare** components (Button, Checkbox) Dark hone chahiye. Aap Light Button aur Dark Checkbox mix nahi kar sakte.

Agar aap Simple Factory use karoge to client ko do alag factory pass karni padegi (ButtonFactory, CheckboxFactory) aur ye risk hai ki Client galti se Light Button aur Dark Checkbox mix kar dega.

**Solution:** Abstract Factory. Ye guarantee karta hai ki poori family ek saath banegi.

---

### Part 2: Bad Example (Without Abstract Factory)

```java
// ❌ BAD PRACTICE: Client ko alag-alag object banane pad rahe hain, Theme mismatch ka risk hai

// Interfaces
interface Button { void render(); }
interface Checkbox { void render(); }

// Light Theme Components
class LightButton implements Button {
    public void render() { System.out.println("[White Button]"); }
}
class LightCheckbox implements Checkbox {
    public void render() { System.out.println("[White Checkbox]"); }
}

// Dark Theme Components
class DarkButton implements Button {
    public void render() { System.out.println("[Black Button]"); }
}
class DarkCheckbox implements Checkbox {
    public void render() { System.out.println("[Black Checkbox]"); }
}

// Client Code (BAD)
public class BadUIApp {
    public static void main(String[] args) {
        String theme = "Dark";
        
        Button btn;
        Checkbox chk;
        
        // Problem: Ye if-else har jagah likhna padega
        // Problem: Galti se Dark Button aur Light Checkbox bana diya to UI kharab ho jayega
        if(theme.equals("Dark")) {
            btn = new DarkButton();
            chk = new DarkCheckbox();
        } else {
            btn = new LightButton();
            chk = new LightCheckbox();
        }
        
        btn.render();
        chk.render();
    }
}
```

---

### Part 3: Abstract Factory Pattern (Good Example)

Yahan hum pehle ek **Abstract Factory** interface banayenge, jiske andar har product banane ka method hoga.

#### Step 1: Define Products (Components)
```java
// Product Families ke Interfaces
interface Button {
    void paint();
}

interface Checkbox {
    void paint();
}
```

#### Step 2: Concrete Products (Light & Dark)
```java
// ---- LIGHT THEME Products ----
class LightButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering Light Button (White)");
    }
}
class LightCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering Light Checkbox (White)");
    }
}

// ---- DARK THEME Products ----
class DarkButton implements Button {
    @Override
    public void paint() {
        System.out.println("Rendering Dark Button (Black)");
    }
}
class DarkCheckbox implements Checkbox {
    @Override
    public void paint() {
        System.out.println("Rendering Dark Checkbox (Black)");
    }
}
```

#### Step 3: Abstract Factory (The God Contract)
```java
// Yeh Abstract Factory hai. Ye guarantee deti hai ki "jo bhi factory aayegi, Button aur Checkbox dono degi"
interface GUIFactory {
    Button createButton();
    Checkbox createCheckbox();
}
```

#### Step 4: Concrete Factories (Theme Factories)
```java
// Yeh Light Theme ki poori family banati hai
class LightThemeFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new LightButton();
    }
    @Override
    public Checkbox createCheckbox() {
        return new LightCheckbox();
    }
}

// Yeh Dark Theme ki poori family banati hai
class DarkThemeFactory implements GUIFactory {
    @Override
    public Button createButton() {
        return new DarkButton();
    }
    @Override
    public Checkbox createCheckbox() {
        return new DarkCheckbox();
    }
}
```

#### Step 5: Client Code (Maza aa gaya!)
```java
// ✅ GOOD PRACTICE: Client ko sirf ek Factory pass karo, poori family guaranteed hai
public class Application {
    private Button button;
    private Checkbox checkbox;

    // Constructor: Bas Factory inject karo
    public Application(GUIFactory factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    public void renderUI() {
        button.paint();
        checkbox.paint();
    }

    public static void main(String[] args) {
        // Application start hone pe decide karo Light ya Dark
        GUIFactory factory;
        String osTheme = "Dark"; // Suppose config file se aaya
        
        if (osTheme.equals("Dark")) {
            factory = new DarkThemeFactory();
        } else {
            factory = new LightThemeFactory();
        }

        Application app = new Application(factory);
        app.renderUI();
        // Output automatically dono Dark honge. Koi mismatch nahi.
    }
}
```

---

### Part 4: 5 Real-World Scenarios for Abstract Factory

Ab aate hain scenarios pe jahan Abstract Factory asli hero hai.

#### Scenario 1: Cross-Platform UI Toolkit (Flutter/React Native Jaisa)
**Problem:** Aapka code Android pe bhi chalna chahiye (Material Design) aur iOS pe bhi (Cupertino). Components alag dikhte hain.
```java
// Factories: AndroidUIFactory, IosUIFactory
// Products: Button, Slider, NavBar
// Client: Bas factory badlo, poora app theme badal jayega.
```

#### Scenario 2: Database Driver with Connection & Command
**Problem:** Jab aap SQL Server use karte ho to Connection bhi SQL Server ka aur Command bhi SQL Server ka. Oracle mein mix nahi kar sakte.
```java
interface DatabaseFactory {
    Connection createConnection();
    Command createCommand();
}
// Concrete: MySQLFactory, OracleFactory
```

#### Scenario 3: Game Level Generation (Terrain & Obstacles)
**Problem:** Jungle Level mein Palm Tree aur Crocodile. Snow Level mein Pine Tree aur Polar Bear. Agar aap Palm Tree aur Polar Bear mix kar doge to game unrealistic lagega.
```java
interface LevelFactory {
    Tree createTree();
    Animal createAnimal();
}
// Concrete: JungleLevelFactory, SnowLevelFactory
```

#### Scenario 4: Document Creator (Resume Builder)
**Problem:** User ne Modern Template select kiya to Header bhi modern aur Footer bhi modern. Old School Template select kiya to dono old school.
```java
interface DocumentFactory {
    Header createHeader();
    Footer createFooter();
}
```

#### Scenario 5: Vehicle Factory (Car Parts)
**Problem:** Tata Car mein Tata Engine aur Tata Headlight. Maruti Car mein Maruti Engine aur Maruti Headlight. Parts cross-compatible nahi hote.
```java
interface CarFactory {
    Engine assembleEngine();
    Headlight fitHeadlight();
}
```

---

### Part 5: Difference Table (Simple vs Abstract)

| Feature | Simple Factory | Abstract Factory |
| :--- | :--- | :--- |
| **Kya Banata Hai?** | Single Object (Sirf Button) | Family of Objects (Button + Checkbox) |
| **Method Count** | 1 method `getProduct()` | Multiple methods `createA()`, `createB()` |
| **Use Case** | Logging, Notifications, Parsing | UI Themes, Cross-DB Support, Game Levels |
| **Complexity** | Easy | Medium |

### Part 6: Diagram Analogy (Dimaag mein set karne ke liye)

**Simple Factory:** Ek **"Pani Puri Wala"** hai. Aap bole "Bhaiya Teekha" to wo sirf **Teekha Pani** banayega. Aap bole "Meetha" to sirf **Meetha Pani**.

**Abstract Factory:** Ek **"Combo Meal Restaurant"** hai. Aap bole "Veg Thali". Factory guarantee karegi ki aapko **Veg Curry, Veg Rice, Veg Salad** hi milega. Non-Veg mix nahi hoga. Aap bole "Non-Veg Thali" to saara kuchh Non-Veg aayega.

### Part 7: Interview Tip

**Question:** Difference between Factory and Abstract Factory?

**Answer:** *"Sir, Simple Factory sirf **Single Product** ke creation ko encapsulate karta hai. Abstract Factory **Families of related products** ke creation ko encapsulate karta hai. Jaise agar humein GUI library banana hai to Abstract Factory guarantee karta hai ki saare widgets (Button, Textbox) ek hi theme (Windows ya Mac) ke honge. Simple Factory mein ye guarantee nahi hoti. Abstract Factory mein hum **Factory of Factories** ki tarah treat karte hain."*
