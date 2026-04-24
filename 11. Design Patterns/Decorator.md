### Part 1: Problem Statement (Class Explosion)

Sochiye aapke paas **Coffee** hai. Coffee ke saath Milk, Sugar, Caramel, Whipped Cream jaise add-ons ho sakte hain. Agar aap har combination ke liye alag class banaoge to **Class Explosion** ho jayegi.

#### ❌ BAD PRACTICE: Inheritance se Class Explosion

```java
// Bina Decorator ke - Class Explosion!
class Coffee { 
    int cost() { return 100; } 
    String desc() { return "Coffee"; }
}
class CoffeeWithMilk extends Coffee { 
    int cost() { return super.cost() + 20; } 
    String desc() { return super.desc() + ", Milk"; }
}
class CoffeeWithMilkAndSugar extends CoffeeWithMilk { 
    int cost() { return super.cost() + 5; } 
    String desc() { return super.desc() + ", Sugar"; }
}
class CoffeeWithMilkSugarAndCaramel extends CoffeeWithMilkAndSugar {
    // Har combination ke liye nayi class! 😱
}
// ... What about Coffee with only Caramel? Coffee with Double Milk? Class explosion!
```

**Dikkatein:**
1. **Class Explosion:** n toppings ke liye 2^n classes ban sakti hain.
2. **Static:** Compile time pe decide karna padta hai, runtime pe add-ons change nahi kar sakte.
3. **Inflexible:** Agar aaj Milk chahiye kal nahi chahiye to naya object banana padega.

---

### Part 2: Decorator Pattern Solution (Good Example)

Hum ek **Core Component** banayenge aur uske upar **Decorators** lapetenge. Har decorator apne aap mein ek component hai.

#### Step 1: Component Interface (Base Coffee)

```java
// 1. Component Interface: Sabhi coffees aur decorators isko implement karenge
interface Beverage {
    String getDescription();
    double getCost();
}
```

#### Step 2: Concrete Component (Core Coffee)

```java
// 2. Concrete Component: Simple Coffee (Base Object)
class SimpleCoffee implements Beverage {
    @Override
    public String getDescription() {
        return "☕ Simple Coffee";
    }
    
    @Override
    public double getCost() {
        return 100.0;
    }
}

// Another base beverage
class DecafCoffee implements Beverage {
    @Override
    public String getDescription() {
        return "☕ Decaf Coffee";
    }
    
    @Override
    public double getCost() {
        return 120.0;
    }
}
```

#### Step 3: Abstract Decorator (Wrapper Base)

```java
// 3. Abstract Decorator: Ye wrapper ka base hai
abstract class CoffeeDecorator implements Beverage {
    protected Beverage beverage;  // Composition: Iske andar ek aur Beverage hai
    
    public CoffeeDecorator(Beverage beverage) {
        this.beverage = beverage;
    }
    
    // Abstract methods ko override karna padega
    public abstract String getDescription();
    public abstract double getCost();
}
```

#### Step 4: Concrete Decorators (Toppings)

```java
// 4. Concrete Decorators: Actual add-ons
class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + " 🥛 + Milk";
    }
    
    @Override
    public double getCost() {
        return beverage.getCost() + 20.0;
    }
}

class SugarDecorator extends CoffeeDecorator {
    public SugarDecorator(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + " 🧂 + Sugar";
    }
    
    @Override
    public double getCost() {
        return beverage.getCost() + 5.0;
    }
}

class CaramelDecorator extends CoffeeDecorator {
    public CaramelDecorator(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + " 🍯 + Caramel";
    }
    
    @Override
    public double getCost() {
        return beverage.getCost() + 15.0;
    }
}

class WhippedCreamDecorator extends CoffeeDecorator {
    public WhippedCreamDecorator(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + " 🍦 + Whipped Cream";
    }
    
    @Override
    public double getCost() {
        return beverage.getCost() + 25.0;
    }
}

// Double add-on bana sakte hain
class ExtraShotDecorator extends CoffeeDecorator {
    public ExtraShotDecorator(Beverage beverage) {
        super(beverage);
    }
    
    @Override
    public String getDescription() {
        return beverage.getDescription() + " ⚡ + Extra Shot";
    }
    
    @Override
    public double getCost() {
        return beverage.getCost() + 30.0;
    }
}
```

#### Step 5: Client Code (Maza aa gaya!)

```java
public class CoffeeShop {
    public static void main(String[] args) {
        System.out.println("=== ☕ Coffee Shop - Decorator Pattern ===\n");
        
        // Order 1: Simple Coffee
        Beverage coffee1 = new SimpleCoffee();
        printOrder(coffee1);
        
        // Order 2: Coffee with Milk and Sugar
        Beverage coffee2 = new SimpleCoffee();
        coffee2 = new MilkDecorator(coffee2);
        coffee2 = new SugarDecorator(coffee2);
        printOrder(coffee2);
        
        // Order 3: Decaf with Caramel and Whipped Cream
        Beverage coffee3 = new DecafCoffee();
        coffee3 = new CaramelDecorator(coffee3);
        coffee3 = new WhippedCreamDecorator(coffee3);
        printOrder(coffee3);
        
        // Order 4: Ultimate Coffee - Everything!
        Beverage coffee4 = new SimpleCoffee();
        coffee4 = new MilkDecorator(coffee4);
        coffee4 = new SugarDecorator(coffee4);
        coffee4 = new CaramelDecorator(coffee4);
        coffee4 = new WhippedCreamDecorator(coffee4);
        coffee4 = new ExtraShotDecorator(coffee4);
        printOrder(coffee4);
        
        // Order 5: Runtime pe dynamic add
        Beverage coffee5 = new SimpleCoffee();
        printOrder(coffee5);
        
        System.out.println("Customer: \"Milk add kar do please\"");
        coffee5 = new MilkDecorator(coffee5);
        printOrder(coffee5);
        
        System.out.println("Customer: \"Aur Caramel bhi\"");
        coffee5 = new CaramelDecorator(coffee5);
        printOrder(coffee5);
    }
    
    private static void printOrder(Beverage beverage) {
        System.out.println("📋 Order: " + beverage.getDescription());
        System.out.println("💰 Cost: ₹" + beverage.getCost());
        System.out.println("--------------------------------");
    }
}
```

---

### Part 3: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: Pizza Topping System (Zomato/Swiggy Style)

**Scenario:** Base pizza hai, toppings dynamically add karte jaao.

```java
// File: PizzaDecorator.java

// Component
interface Pizza {
    String getDescription();
    double getPrice();
}

// Concrete Component
class MargheritaPizza implements Pizza {
    @Override
    public String getDescription() {
        return "🍕 Margherita Pizza (Tomato Sauce, Cheese)";
    }
    
    @Override
    public double getPrice() {
        return 199.0;
    }
}

class FarmhousePizza implements Pizza {
    @Override
    public String getDescription() {
        return "🍕 Farmhouse Pizza (Onion, Capsicum, Tomato)";
    }
    
    @Override
    public double getPrice() {
        return 299.0;
    }
}

// Abstract Decorator
abstract class ToppingDecorator implements Pizza {
    protected Pizza pizza;
    
    public ToppingDecorator(Pizza pizza) {
        this.pizza = pizza;
    }
    
    public abstract String getDescription();
    public abstract double getPrice();
}

// Concrete Toppings
class CheeseBurstDecorator extends ToppingDecorator {
    public CheeseBurstDecorator(Pizza pizza) {
        super(pizza);
    }
    
    @Override
    public String getDescription() {
        return pizza.getDescription() + " 🧀 + Cheese Burst";
    }
    
    @Override
    public double getPrice() {
        return pizza.getPrice() + 79.0;
    }
}

class PepperoniDecorator extends ToppingDecorator {
    public PepperoniDecorator(Pizza pizza) {
        super(pizza);
    }
    
    @Override
    public String getDescription() {
        return pizza.getDescription() + " 🍖 + Pepperoni";
    }
    
    @Override
    public double getPrice() {
        return pizza.getPrice() + 99.0;
    }
}

class JalapenoDecorator extends ToppingDecorator {
    public JalapenoDecorator(Pizza pizza) {
        super(pizza);
    }
    
    @Override
    public String getDescription() {
        return pizza.getDescription() + " 🌶️ + Jalapeno";
    }
    
    @Override
    public double getPrice() {
        return pizza.getPrice() + 49.0;
    }
}

class OliveDecorator extends ToppingDecorator {
    public OliveDecorator(Pizza pizza) {
        super(pizza);
    }
    
    @Override
    public String getDescription() {
        return pizza.getDescription() + " 🫒 + Black Olives";
    }
    
    @Override
    public double getPrice() {
        return pizza.getPrice() + 59.0;
    }
}

public class PizzaShop {
    public static void main(String[] args) {
        System.out.println("=== 🍕 Pizza Builder - Decorator Pattern ===\n");
        
        // Create your own pizza
        Pizza myPizza = new FarmhousePizza();
        System.out.println("Base: " + myPizza.getDescription());
        
        myPizza = new CheeseBurstDecorator(myPizza);
        myPizza = new PepperoniDecorator(myPizza);
        myPizza = new JalapenoDecorator(myPizza);
        
        System.out.println("\n📋 Final Order:");
        System.out.println(myPizza.getDescription());
        System.out.println("💰 Total: ₹" + myPizza.getPrice());
        
        // Another pizza
        Pizza vegPizza = new MargheritaPizza();
        vegPizza = new OliveDecorator(vegPizza);
        vegPizza = new JalapenoDecorator(vegPizza);
        
        System.out.println("\n📋 Veg Pizza Order:");
        System.out.println(vegPizza.getDescription());
        System.out.println("💰 Total: ₹" + vegPizza.getPrice());
    }
}
```

---

#### Example 2: Notification System (Email + SMS + Push)

**Scenario:** User ko ek hi notification multiple channels pe bhejni hai.

```java
// File: NotificationDecorator.java

// Component
interface Notification {
    void send(String message);
    String getChannels();
}

// Concrete Component
class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("📧 Sending Email: " + message);
    }
    
    @Override
    public String getChannels() {
        return "Email";
    }
}

// Abstract Decorator
abstract class NotificationDecorator implements Notification {
    protected Notification notification;
    
    public NotificationDecorator(Notification notification) {
        this.notification = notification;
    }
    
    public abstract void send(String message);
    public abstract String getChannels();
}

// Concrete Decorators
class SMSNotificationDecorator extends NotificationDecorator {
    public SMSNotificationDecorator(Notification notification) {
        super(notification);
    }
    
    @Override
    public void send(String message) {
        notification.send(message);  // Pehle wrapped notification bhejo
        System.out.println("📱 Sending SMS: " + message);
    }
    
    @Override
    public String getChannels() {
        return notification.getChannels() + " + SMS";
    }
}

class PushNotificationDecorator extends NotificationDecorator {
    public PushNotificationDecorator(Notification notification) {
        super(notification);
    }
    
    @Override
    public void send(String message) {
        notification.send(message);
        System.out.println("🔔 Sending Push Notification: " + message);
    }
    
    @Override
    public String getChannels() {
        return notification.getChannels() + " + Push";
    }
}

class SlackNotificationDecorator extends NotificationDecorator {
    public SlackNotificationDecorator(Notification notification) {
        super(notification);
    }
    
    @Override
    public void send(String message) {
        notification.send(message);
        System.out.println("💬 Sending Slack Message: " + message);
    }
    
    @Override
    public String getChannels() {
        return notification.getChannels() + " + Slack";
    }
}

class WhatsAppNotificationDecorator extends NotificationDecorator {
    public WhatsAppNotificationDecorator(Notification notification) {
        super(notification);
    }
    
    @Override
    public void send(String message) {
        notification.send(message);
        System.out.println("💚 Sending WhatsApp: " + message);
    }
    
    @Override
    public String getChannels() {
        return notification.getChannels() + " + WhatsApp";
    }
}

public class NotificationService {
    public static void main(String[] args) {
        System.out.println("=== 📢 Notification Service ===\n");
        
        // User preference ke hisaab se channels add karo
        Notification notifier = new EmailNotification();
        System.out.println("Base channels: " + notifier.getChannels());
        
        // User ne SMS bhi enable kiya
        notifier = new SMSNotificationDecorator(notifier);
        
        // User ne Push bhi enable kiya
        notifier = new PushNotificationDecorator(notifier);
        
        // Admin ke liye Slack bhi
        notifier = new SlackNotificationDecorator(notifier);
        
        System.out.println("Final channels: " + notifier.getChannels() + "\n");
        
        // Send notification (sab channels pe jayega)
        notifier.send("Your order #12345 has been shipped!");
        
        // Different user with different preferences
        System.out.println("\n=== Another User ===\n");
        Notification user2Notifier = new EmailNotification();
        user2Notifier = new WhatsAppNotificationDecorator(user2Notifier);
        System.out.println("Channels: " + user2Notifier.getChannels() + "\n");
        user2Notifier.send("Your OTP is 987654");
    }
}
```

---

#### Example 3: Text Editor with Formatting (Bold, Italic, Underline)

**Scenario:** MS Word jaisa editor jahan text pe multiple formatting apply kar sakte ho.

```java
// File: TextEditorDecorator.java

// Component
interface TextComponent {
    String getText();
    String getFormatted();
}

// Concrete Component
class PlainText implements TextComponent {
    private String text;
    
    public PlainText(String text) {
        this.text = text;
    }
    
    @Override
    public String getText() {
        return text;
    }
    
    @Override
    public String getFormatted() {
        return text;
    }
}

// Abstract Decorator
abstract class TextDecorator implements TextComponent {
    protected TextComponent textComponent;
    
    public TextDecorator(TextComponent textComponent) {
        this.textComponent = textComponent;
    }
    
    public abstract String getText();
    public abstract String getFormatted();
}

// Concrete Formatting Decorators
class BoldDecorator extends TextDecorator {
    public BoldDecorator(TextComponent textComponent) {
        super(textComponent);
    }
    
    @Override
    public String getText() {
        return textComponent.getText();
    }
    
    @Override
    public String getFormatted() {
        return "<b>" + textComponent.getFormatted() + "</b>";
    }
}

class ItalicDecorator extends TextDecorator {
    public ItalicDecorator(TextComponent textComponent) {
        super(textComponent);
    }
    
    @Override
    public String getText() {
        return textComponent.getText();
    }
    
    @Override
    public String getFormatted() {
        return "<i>" + textComponent.getFormatted() + "</i>";
    }
}

class UnderlineDecorator extends TextDecorator {
    public UnderlineDecorator(TextComponent textComponent) {
        super(textComponent);
    }
    
    @Override
    public String getText() {
        return textComponent.getText();
    }
    
    @Override
    public String getFormatted() {
        return "<u>" + textComponent.getFormatted() + "</u>";
    }
}

class ColorDecorator extends TextDecorator {
    private String color;
    
    public ColorDecorator(TextComponent textComponent, String color) {
        super(textComponent);
        this.color = color;
    }
    
    @Override
    public String getText() {
        return textComponent.getText();
    }
    
    @Override
    public String getFormatted() {
        return "<span style='color:" + color + "'>" + textComponent.getFormatted() + "</span>";
    }
}

class FontSizeDecorator extends TextDecorator {
    private int size;
    
    public FontSizeDecorator(TextComponent textComponent, int size) {
        super(textComponent);
        this.size = size;
    }
    
    @Override
    public String getText() {
        return textComponent.getText();
    }
    
    @Override
    public String getFormatted() {
        return "<span style='font-size:" + size + "px'>" + textComponent.getFormatted() + "</span>";
    }
}

public class TextEditor {
    public static void main(String[] args) {
        System.out.println("=== 📝 Text Editor - Decorator Pattern ===\n");
        
        // Simple text
        TextComponent text = new PlainText("Hello World!");
        System.out.println("Original: " + text.getFormatted());
        
        // Bold only
        TextComponent boldText = new BoldDecorator(new PlainText("Bold Text"));
        System.out.println("\nBold: " + boldText.getFormatted());
        
        // Bold + Italic
        TextComponent boldItalic = new ItalicDecorator(new BoldDecorator(new PlainText("Bold and Italic")));
        System.out.println("Bold+Italic: " + boldItalic.getFormatted());
        
        // Complex formatting: Bold + Italic + Underline + Red Color + Size 20
        TextComponent complex = new PlainText("Fancy Text");
        complex = new BoldDecorator(complex);
        complex = new ItalicDecorator(complex);
        complex = new UnderlineDecorator(complex);
        complex = new ColorDecorator(complex, "red");
        complex = new FontSizeDecorator(complex, 20);
        
        System.out.println("\nComplex Formatting:");
        System.out.println("Raw Text: " + complex.getText());
        System.out.println("Formatted: " + complex.getFormatted());
        
        // Dynamically remove formatting (by not wrapping)
        System.out.println("\n=== Runtime Formatting Toggle ===\n");
        TextComponent dynamic = new PlainText("Dynamic Formatting");
        System.out.println("Step 1: " + dynamic.getFormatted());
        
        dynamic = new BoldDecorator(dynamic);
        System.out.println("Step 2 (Add Bold): " + dynamic.getFormatted());
        
        dynamic = new ItalicDecorator(dynamic);
        System.out.println("Step 3 (Add Italic): " + dynamic.getFormatted());
        
        dynamic = new UnderlineDecorator(dynamic);
        System.out.println("Step 4 (Add Underline): " + dynamic.getFormatted());
    }
}
```

---

#### Example 4: Data Stream Processing (Compression + Encryption)

**Scenario:** File save karte waqt pehle compress karo, fir encrypt karo.

```java
// File: DataStreamDecorator.java
import java.util.Base64;

// Component
interface DataStream {
    String readData();
    void writeData(String data);
    String getData();
}

// Concrete Component
class FileDataStream implements DataStream {
    private String data;
    
    @Override
    public String readData() {
        System.out.println("📁 Reading data from file...");
        return data;
    }
    
    @Override
    public void writeData(String data) {
        System.out.println("📁 Writing data to file...");
        this.data = data;
    }
    
    @Override
    public String getData() {
        return data;
    }
}

// Abstract Decorator
abstract class DataStreamDecorator implements DataStream {
    protected DataStream stream;
    
    public DataStreamDecorator(DataStream stream) {
        this.stream = stream;
    }
    
    public abstract String readData();
    public abstract void writeData(String data);
    public abstract String getData();
}

// Compression Decorator
class CompressionDecorator extends DataStreamDecorator {
    public CompressionDecorator(DataStream stream) {
        super(stream);
    }
    
    @Override
    public String readData() {
        String compressed = stream.readData();
        String decompressed = decompress(compressed);
        System.out.println("🗜️ [DECOMPRESS] " + compressed + " -> " + decompressed);
        return decompressed;
    }
    
    @Override
    public void writeData(String data) {
        String compressed = compress(data);
        System.out.println("🗜️ [COMPRESS] " + data + " -> " + compressed);
        stream.writeData(compressed);
    }
    
    @Override
    public String getData() {
        return stream.getData();
    }
    
    private String compress(String data) {
        // Simple simulation: Repeat count compression
        if (data == null || data.isEmpty()) return data;
        
        StringBuilder compressed = new StringBuilder();
        int count = 1;
        for (int i = 1; i < data.length(); i++) {
            if (data.charAt(i) == data.charAt(i - 1)) {
                count++;
            } else {
                compressed.append(data.charAt(i - 1));
                if (count > 1) compressed.append(count);
                count = 1;
            }
        }
        compressed.append(data.charAt(data.length() - 1));
        if (count > 1) compressed.append(count);
        
        return compressed.toString();
    }
    
    private String decompress(String data) {
        // Simple simulation
        if (data == null || data.isEmpty()) return data;
        
        StringBuilder decompressed = new StringBuilder();
        for (int i = 0; i < data.length(); i++) {
            char ch = data.charAt(i);
            if (i + 1 < data.length() && Character.isDigit(data.charAt(i + 1))) {
                int count = data.charAt(i + 1) - '0';
                for (int j = 0; j < count; j++) {
                    decompressed.append(ch);
                }
                i++; // Skip the digit
            } else {
                decompressed.append(ch);
            }
        }
        return decompressed.toString();
    }
}

// Encryption Decorator
class EncryptionDecorator extends DataStreamDecorator {
    public EncryptionDecorator(DataStream stream) {
        super(stream);
    }
    
    @Override
    public String readData() {
        String encrypted = stream.readData();
        String decrypted = decrypt(encrypted);
        System.out.println("🔓 [DECRYPT] " + encrypted + " -> " + decrypted);
        return decrypted;
    }
    
    @Override
    public void writeData(String data) {
        String encrypted = encrypt(data);
        System.out.println("🔒 [ENCRYPT] " + data + " -> " + encrypted);
        stream.writeData(encrypted);
    }
    
    @Override
    public String getData() {
        return stream.getData();
    }
    
    private String encrypt(String data) {
        // Simple Base64 encoding (simulation)
        return Base64.getEncoder().encodeToString(data.getBytes());
    }
    
    private String decrypt(String data) {
        // Simple Base64 decoding
        byte[] decoded = Base64.getDecoder().decode(data);
        return new String(decoded);
    }
}

// Logging Decorator (Audit Trail)
class LoggingDecorator extends DataStreamDecorator {
    public LoggingDecorator(DataStream stream) {
        super(stream);
    }
    
    @Override
    public String readData() {
        System.out.println("📋 [LOG] Reading data at " + System.currentTimeMillis());
        return stream.readData();
    }
    
    @Override
    public void writeData(String data) {
        System.out.println("📋 [LOG] Writing data '" + data + "' at " + System.currentTimeMillis());
        stream.writeData(data);
    }
    
    @Override
    public String getData() {
        return stream.getData();
    }
}

public class SecureFileStorage {
    public static void main(String[] args) {
        System.out.println("=== 🔐 Secure File Storage - Decorator Pattern ===\n");
        
        String originalData = "HELLOOO WORLD";  // Notice repeated O's for compression demo
        
        // Setup: File -> Compression -> Encryption -> Logging
        DataStream stream = new FileDataStream();
        stream = new CompressionDecorator(stream);
        stream = new EncryptionDecorator(stream);
        stream = new LoggingDecorator(stream);
        
        System.out.println("--- Writing Data ---");
        stream.writeData(originalData);
        System.out.println("Stored data: " + stream.getData());
        
        System.out.println("\n--- Reading Data ---");
        String readData = stream.readData();
        System.out.println("Retrieved data: " + readData);
        
        // Verification
        System.out.println("\n--- Verification ---");
        System.out.println("Original:  " + originalData);
        System.out.println("Retrieved: " + readData);
        System.out.println("Match: " + originalData.equals(readData));
        
        // Different stack: Only Encryption (No Compression)
        System.out.println("\n=== Different Configuration ===\n");
        DataStream simpleStream = new FileDataStream();
        simpleStream = new EncryptionDecorator(simpleStream);
        simpleStream.writeData("Simple Secret");
        System.out.println("Stored: " + simpleStream.getData());
        System.out.println("Read: " + simpleStream.readData());
    }
}
```

---

#### Example 5: Web API Response Builder (Middleware Pattern)

**Scenario:** API response mein headers, pagination, metadata dynamically add karna.

```java
// File: APIResponseDecorator.java
import java.util.HashMap;
import java.util.Map;

// Component
interface APIResponse {
    Map<String, Object> getResponse();
    String toJSON();
}

// Concrete Component
class BaseResponse implements APIResponse {
    private Object data;
    
    public BaseResponse(Object data) {
        this.data = data;
    }
    
    @Override
    public Map<String, Object> getResponse() {
        Map<String, Object> response = new HashMap<>();
        response.put("data", data);
        response.put("timestamp", System.currentTimeMillis());
        return response;
    }
    
    @Override
    public String toJSON() {
        return mapToJSON(getResponse());
    }
    
    private String mapToJSON(Map<String, Object> map) {
        StringBuilder json = new StringBuilder("{");
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            json.append("\"").append(entry.getKey()).append("\": ");
            if (entry.getValue() instanceof String) {
                json.append("\"").append(entry.getValue()).append("\"");
            } else if (entry.getValue() instanceof Map) {
                json.append(mapToJSON((Map<String, Object>) entry.getValue()));
            } else {
                json.append(entry.getValue());
            }
            json.append(", ");
        }
        if (json.length() > 1) json.setLength(json.length() - 2);
        json.append("}");
        return json.toString();
    }
}

// Abstract Decorator
abstract class ResponseDecorator implements APIResponse {
    protected APIResponse response;
    
    public ResponseDecorator(APIResponse response) {
        this.response = response;
    }
    
    public abstract Map<String, Object> getResponse();
    
    @Override
    public String toJSON() {
        return mapToJSON(getResponse());
    }
    
    private String mapToJSON(Map<String, Object> map) {
        StringBuilder json = new StringBuilder("{");
        for (Map.Entry<String, Object> entry : map.entrySet()) {
            json.append("\"").append(entry.getKey()).append("\": ");
            if (entry.getValue() instanceof String) {
                json.append("\"").append(entry.getValue()).append("\"");
            } else if (entry.getValue() instanceof Map) {
                json.append(mapToJSON((Map<String, Object>) entry.getValue()));
            } else {
                json.append(entry.getValue());
            }
            json.append(", ");
        }
        if (json.length() > 1) json.setLength(json.length() - 2);
        json.append("}");
        return json.toString();
    }
}

// Pagination Decorator
class PaginationDecorator extends ResponseDecorator {
    private int page;
    private int limit;
    private long total;
    
    public PaginationDecorator(APIResponse response, int page, int limit, long total) {
        super(response);
        this.page = page;
        this.limit = limit;
        this.total = total;
    }
    
    @Override
    public Map<String, Object> getResponse() {
        Map<String, Object> resp = response.getResponse();
        Map<String, Object> pagination = new HashMap<>();
        pagination.put("page", page);
        pagination.put("limit", limit);
        pagination.put("total", total);
        pagination.put("totalPages", (int) Math.ceil((double) total / limit));
        resp.put("pagination", pagination);
        return resp;
    }
}

// Metadata Decorator
class MetadataDecorator extends ResponseDecorator {
    private Map<String, Object> metadata;
    
    public MetadataDecorator(APIResponse response) {
        super(response);
        this.metadata = new HashMap<>();
    }
    
    public void addMeta(String key, Object value) {
        metadata.put(key, value);
    }
    
    @Override
    public Map<String, Object> getResponse() {
        Map<String, Object> resp = response.getResponse();
        resp.put("metadata", metadata);
        return resp;
    }
}

// Status Decorator
class StatusDecorator extends ResponseDecorator {
    private boolean success;
    private String message;
    
    public StatusDecorator(APIResponse response, boolean success, String message) {
        super(response);
        this.success = success;
        this.message = message;
    }
    
    @Override
    public Map<String, Object> getResponse() {
        Map<String, Object> resp = response.getResponse();
        resp.put("success", success);
        resp.put("message", message);
        return resp;
    }
}

// HATEOAS Links Decorator
class LinksDecorator extends ResponseDecorator {
    private Map<String, String> links;
    
    public LinksDecorator(APIResponse response) {
        super(response);
        this.links = new HashMap<>();
    }
    
    public void addLink(String rel, String href) {
        links.put(rel, href);
    }
    
    @Override
    public Map<String, Object> getResponse() {
        Map<String, Object> resp = response.getResponse();
        resp.put("_links", links);
        return resp;
    }
}

public class RESTAPIBuilder {
    public static void main(String[] args) {
        System.out.println("=== 🌐 REST API Response Builder ===\n");
        
        // Sample user data
        Map<String, Object> userData = new HashMap<>();
        userData.put("id", 123);
        userData.put("name", "Rahul Kumar");
        userData.put("email", "rahul@example.com");
        
        // Build response dynamically
        APIResponse response = new BaseResponse(userData);
        
        // Add status
        response = new StatusDecorator(response, true, "User fetched successfully");
        
        // Add pagination (if list)
        response = new PaginationDecorator(response, 1, 10, 1);
        
        // Add metadata
        MetadataDecorator metaDecorator = new MetadataDecorator(response);
        metaDecorator.addMeta("version", "v1.0");
        metaDecorator.addMeta("responseTime", "45ms");
        response = metaDecorator;
        
        // Add HATEOAS links
        LinksDecorator linksDecorator = new LinksDecorator(response);
        linksDecorator.addLink("self", "/api/users/123");
        linksDecorator.addLink("update", "/api/users/123");
        linksDecorator.addLink("delete", "/api/users/123");
        response = linksDecorator;
        
        System.out.println("📦 Final Response:");
        System.out.println(response.toJSON());
        
        // Different configuration for error response
        System.out.println("\n=== Error Response ===\n");
        APIResponse errorResponse = new BaseResponse(null);
        errorResponse = new StatusDecorator(errorResponse, false, "User not found");
        
        MetadataDecorator errorMeta = new MetadataDecorator(errorResponse);
        errorMeta.addMeta("errorCode", "404");
        errorMeta.addMeta("timestamp", System.currentTimeMillis());
        errorResponse = errorMeta;
        
        System.out.println(errorResponse.toJSON());
    }
}
```

---

### Part 6: Decorator Pattern - Summary & Interview Points

| Aspect | Details |
|:---|:---|
| **Problem Solved** | Class explosion, dynamic feature addition |
| **Use When** | Need to add/remove responsibilities at runtime |
| **Key Components** | Component (Interface), ConcreteComponent, Decorator (Abstract), ConcreteDecorator |
| **Real Examples** | Java I/O Streams (`BufferedInputStream`), Collections (`Collections.synchronizedList()`) |

**Interview Question:** *What is Decorator Pattern and how is it different from Inheritance?*

**Answer:** *"Sir, Decorator pattern allows **dynamic addition of responsibilities** to objects at runtime through composition, while inheritance adds responsibilities **statically at compile time**. Decorator follows **Open/Closed Principle** - open for extension, closed for modification. Java I/O streams are perfect example: `new BufferedInputStream(new FileInputStream("file.txt"))`."*

**Interview Question:** *Difference between Decorator and Proxy Pattern?*

| Decorator | Proxy |
|:---|:---|
| Adds **behavior/functionality** | Controls **access** |
| Client explicitly wraps object | Proxy is transparent to client |
| Multiple decorators can be stacked | Usually single proxy |

**Interview Question:** *Difference between Decorator and Adapter Pattern?*

| Decorator | Adapter |
|:---|:---|
| Interface **same** rakhta hai | Interface **change** karta hai |
| Functionality add karta hai | Compatibility provide karta hai |
| Recursive composition | Simple wrapping |

**Java I/O Example (Decorator in Action):**
```java
// Real Java I/O Decorator Chain
InputStream is = new FileInputStream("data.txt");           // Core
is = new BufferedInputStream(is);                          // + Buffering
is = new DataInputStream(is);                              // + Data reading
is = new CipherInputStream(is, cipher);                    // + Encryption
// Every decorator adds functionality without changing interface!
```
