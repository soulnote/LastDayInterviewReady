
### Part 1: Bad Example (Bina Factory Ke) - "Jugaad Code"

Sochiye aap Zomato/Swiggy jaisa app bana rahe ho. Aapko alag-alag jagah Notification bhejne hain (Push, SMS, Email).

Bina Factory ke aap ye karoge:

```java
// ❌ BAD PRACTICE - Client ko sab banana pad raha hai, tight coupling hai
public class NotificationServiceBad {

    public static void main(String[] args) {
        String type = "SMS"; // Man lo UI se aaya

        // Problem 1: Har jagah if-else lagega
        // Problem 2: Agar kal naya type "WhatsApp" aaya to YEH CODE TODNA PADEGA (Violates Open/Closed Principle)
        // Problem 3: Client ko `new SMSNotification()` ka pata hona zaroori hai (Tight Coupling)

        if (type.equals("SMS")) {
            SMSNotification sms = new SMSNotification();
            sms.send("OTP is 1234");
        } else if (type.equals("Email")) {
            EmailNotification email = new EmailNotification();
            email.send("OTP is 1234");
        } else {
            System.out.println("Invalid Type");
        }
    }
}

class SMSNotification {
    public void send(String msg) { System.out.println("SMS: " + msg); }
}
class EmailNotification {
    public void send(String msg) { System.out.println("Email: " + msg); }
}
```

**Dikkat Kya Hai?**
- Jaise hi WhatsApp add karna hai, `main()` method mein ghus ke `else if` lagana padega.
- 10 jagah notification bhejni hai? 10 jagah ye `if-else` copy-paste karna padega.
- Testing mushkil hai.

---

### Part 2: Factory Pattern se Solution (Good Example)

Ab hum ek "Factory" class banayenge jiska kaam sirf objects banana hai.

#### Step 1: Common Interface (Contract)
```java
// 1. Sabhi Notifications is tarah dikhenge
interface Notification {
    void send(String message);
}
```

#### Step 2: Concrete Classes (Asli Implementations)
```java
// 2. Specific Classes
class SMSNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("📱 SMS Sent: " + message);
    }
}

class EmailNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("📧 Email Sent: " + message);
    }
}

class PushNotification implements Notification {
    @Override
    public void send(String message) {
        System.out.println("🔔 Push Notification: " + message);
    }
}
```

#### Step 3: The MAGIC FACTORY
```java
// 3. Yeh Factory hai - Saara object creation logic yahin centralized hai
class NotificationFactory {

    // Yeh method object banake dega
    public Notification createNotification(String channel) {
        if (channel == null || channel.isEmpty())
            return null;

        // Ab agar kal ko logic badlega to sirf yahan change hoga, client code untouched rahega
        switch (channel.toUpperCase()) {
            case "SMS":
                return new SMSNotification();
            case "EMAIL":
                return new EmailNotification();
            case "PUSH":
                return new PushNotification();
            default:
                throw new IllegalArgumentException("Unknown channel " + channel);
        }
    }
}
```

#### Step 4: Client Code (Ab Kitna Clean hai!)
```java
// ✅ GOOD PRACTICE - Client loose coupled hai
public class NotificationServiceGood {
    public static void main(String[] args) {
        NotificationFactory factory = new NotificationFactory();

        // Client ko sirf naam pata hai, "new" keyword use nahi kiya
        Notification notification = factory.createNotification("SMS");
        notification.send("Aapka OTP 9999 hai!");

        // Kal ko WhatsApp aaya to yahan kuch nahi badlega, sirf Factory class badlegi
    }
}
```

---

### Part 3: 5 Real-World Scenarios with Examples

Ab aate hain asli mazedar hisse pe. 5 alag-alag situations jahan Factory Pattern devta ki tarah pujta hai.

#### Scenario 1: Payment Gateway Integration (Most Common)
**Problem:** Customer Paytm se pay kare ya Google Pay se ya Card se, aapko bas `pay()` call karna hai.

```java
// Interface
interface PaymentProcessor {
    void pay(int amount);
}

// Concrete Classes
class Paytm implements PaymentProcessor {
    public void pay(int amount) { System.out.println("Paytm: Rs." + amount); }
}
class GooglePay implements PaymentProcessor {
    public void pay(int amount) { System.out.println("GPay: Rs." + amount); }
}
class CreditCard implements PaymentProcessor {
    public void pay(int amount) { System.out.println("Card: Rs." + amount); }
}

// Factory
class PaymentFactory {
    public static PaymentProcessor getProcessor(String method) {
        return switch (method.toLowerCase()) {
            case "paytm" -> new Paytm();
            case "gpay" -> new GooglePay();
            case "card" -> new CreditCard();
            default -> null;
        };
    }
}
// Use: PaymentFactory.getProcessor("gpay").pay(500);
```

#### Scenario 2: Database Connection (Environment Based)
**Problem:** Local pe MySQL chalana hai, Server pe Oracle. Har baar URL yaad rakhna mushkil.

```java
interface Database {
    void connect();
}
class MySQL implements Database {
    public void connect() { System.out.println("Connected to Local MySQL"); }
}
class Oracle implements Database {
    public void connect() { System.out.println("Connected to Prod Oracle"); }
}

class DBFactory {
    public Database getDatabase(String env) {
        if(env.equals("DEV")) return new MySQL();
        else return new Oracle();
    }
}
```

#### Scenario 3: Logging Framework (Slf4j jaisa)
**Problem:** Kabhi Console pe log karna hai, kabhi File mein, kabhi Cloud pe.

```java
interface Logger {
    void log(String msg);
}
class ConsoleLogger implements Logger {
    public void log(String msg) { System.out.println("Console: " + msg); }
}
class FileLogger implements Logger {
    public void log(String msg) { /* Write to file logic */ }
}

class LoggerFactory {
    public Logger getLogger(String type) { ... }
}
```

#### Scenario 4: Vehicle Manufacturing (Car/Bike Rental App)
**Problem:** Uber app ko pata nahi user ne "Go" ya "XL" select kiya, bas gadi chahiye.

```java
abstract class Vehicle {
    abstract void drive();
}
class Car extends Vehicle { void drive() { System.out.println("Car driving"); } }
class Bike extends Vehicle { void drive() { System.out.println("Bike riding"); } }

class VehicleFactory {
    public Vehicle getVehicle(String type) { ... }
}
```

#### Scenario 5: Parser Factory (JSON / XML Reader)
**Problem:** API se JSON bhi aa sakta hai, XML bhi. Parser automatically decide ho jaye.

```java
interface Parser {
    void parse(String data);
}
class JsonParser implements Parser { ... }
class XmlParser implements Parser { ... }
```

---

### Part 4: Kab Use Karna Hai aur Kab Nahi? (Decision Matrix)

| **Use Karo Jab...** | **Mat Karo Agar...** |
| :--- | :--- |
| Object creation logic complex hai (Bahut saare if-else) | Sirf 2-3 hi types hain jo kabhi badlenge nahi |
| Future mein naye types add hone wale hain | Simple `new ClassName()` kaam kar raha hai |
| Client code ko implementation se alag rakhna hai | Performance critical hai (Factory thoda sa overhead laati hai) |

### Part 5: Interview Tip (Smart Answer)
Agar Interviewer puche "What is Factory Pattern?"

**Answer:** *"Sir, Factory pattern ek Creational Design Pattern hai jisme hum object creation logic ko client se hata kar ek alag Factory class mein encapsulate karte hain. Isse **Loose Coupling** achieve hoti hai aur **Open/Closed Principle** follow hota hai. Example ke taur pe agar humein Payment Gateway integrate karna ho, to client ko sirf gateway ka naam batana hai, new Paytm() ya new GPay() ka burden Factory utha legi."*
