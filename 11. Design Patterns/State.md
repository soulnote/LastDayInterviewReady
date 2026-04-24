### Part 1: Problem Statement (State-Based If-Else Hell)

Sochiye aapke paas **Traffic Light System** hai. Light RED, YELLOW, GREEN state change hoti rehti hai.

#### ❌ BAD PRACTICE: If-Else Based State Machine

```java
// Bina State Pattern ke - If-Else Hell!
class TrafficLightBad {
    private String currentState = "RED";
    
    public void change() {
        if (currentState.equals("RED")) {
            System.out.println("🔴 RED → 🟡 YELLOW");
            currentState = "YELLOW";
        } 
        else if (currentState.equals("YELLOW")) {
            System.out.println("🟡 YELLOW → 🟢 GREEN");
            currentState = "GREEN";
        }
        else if (currentState.equals("GREEN")) {
            System.out.println("🟢 GREEN → 🔴 RED");
            currentState = "RED";
        }
    }
    
    public void emergencyMode() {
        if (currentState.equals("RED")) {
            // Emergency behavior for RED
        } else if (currentState.equals("YELLOW")) {
            // Emergency behavior for YELLOW
        } // ... and so on
    }
}
```

**Dikkatein:**
1. **If-Else Spaghetti:** Har method mein state checking ka if-else.
2. **New State Add Tough:** Naya state add karna hai to saare methods mein if-else modify karo.
3. **Violates Open/Closed Principle:** Existing code modify karna padta hai.

---

### Part 2: State Pattern Solution (Good Example)

Hum har state ko **alag class** banayenge. Context (TrafficLight) current state ko hold karega aur state change hone pe behavior automatically change ho jayega.

#### Step 1: State Interface

```java
// 1. State Interface: Sabhi states isko implement karenge
interface TrafficLightState {
    void handle(TrafficLight context);
    String getColor();
    int getDurationSeconds();
}
```

#### Step 2: Concrete States

```java
// 2. Concrete State: RED
class RedState implements TrafficLightState {
    @Override
    public void handle(TrafficLight context) {
        System.out.println("🔴 STOP! Light is RED");
        System.out.println("   Duration: " + getDurationSeconds() + " seconds");
        System.out.println("   Next: YELLOW (Get Ready)");
        
        // After duration, change to next state
        context.setState(new YellowState());
    }
    
    @Override
    public String getColor() {
        return "RED";
    }
    
    @Override
    public int getDurationSeconds() {
        return 30;
    }
}

// Concrete State: YELLOW
class YellowState implements TrafficLightState {
    @Override
    public void handle(TrafficLight context) {
        System.out.println("🟡 CAUTION! Light is YELLOW");
        System.out.println("   Duration: " + getDurationSeconds() + " seconds");
        System.out.println("   Next: GREEN (Go)");
        
        // After duration, change to next state
        context.setState(new GreenState());
    }
    
    @Override
    public String getColor() {
        return "YELLOW";
    }
    
    @Override
    public int getDurationSeconds() {
        return 5;
    }
}

// Concrete State: GREEN
class GreenState implements TrafficLightState {
    @Override
    public void handle(TrafficLight context) {
        System.out.println("🟢 GO! Light is GREEN");
        System.out.println("   Duration: " + getDurationSeconds() + " seconds");
        System.out.println("   Next: RED (Stop)");
        
        // After duration, change to next state
        context.setState(new RedState());
    }
    
    @Override
    public String getColor() {
        return "GREEN";
    }
    
    @Override
    public int getDurationSeconds() {
        return 45;
    }
}

// Extra State: Emergency Blinking
class EmergencyState implements TrafficLightState {
    private TrafficLightState previousState;
    
    public EmergencyState(TrafficLightState previousState) {
        this.previousState = previousState;
    }
    
    @Override
    public void handle(TrafficLight context) {
        System.out.println("⚠️ EMERGENCY MODE! Blinking YELLOW");
        System.out.println("   All directions proceed with caution");
    }
    
    @Override
    public String getColor() {
        return "BLINKING_YELLOW";
    }
    
    @Override
    public int getDurationSeconds() {
        return -1; // Infinite until manually changed
    }
    
    public TrafficLightState getPreviousState() {
        return previousState;
    }
}
```

#### Step 3: Context Class (Traffic Light)

```java
// 3. Context: TrafficLight that changes behavior based on state
class TrafficLight {
    private TrafficLightState currentState;
    
    public TrafficLight() {
        // Initial state
        this.currentState = new RedState();
        System.out.println("🚦 Traffic Light initialized: " + currentState.getColor());
    }
    
    public void setState(TrafficLightState state) {
        this.currentState = state;
    }
    
    public TrafficLightState getCurrentState() {
        return currentState;
    }
    
    public void change() {
        currentState.handle(this);
    }
    
    public void displayCurrentState() {
        System.out.println("\n🚦 Current Light: " + currentState.getColor());
    }
    
    public void emergencyMode() {
        System.out.println("\n🚨 Activating Emergency Mode!");
        setState(new EmergencyState(currentState));
    }
    
    public void normalMode() {
        if (currentState instanceof EmergencyState) {
            EmergencyState emergency = (EmergencyState) currentState;
            setState(emergency.getPreviousState());
            System.out.println("\n✅ Normal Mode Restored: " + currentState.getColor());
        }
    }
}
```

#### Step 4: Client Code (Maza aa gaya!)

```java
public class TrafficLightApp {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 🚦 Traffic Light System - State Pattern ===\n");
        
        TrafficLight trafficLight = new TrafficLight();
        
        // Normal cycle
        System.out.println("\n--- Normal Cycle ---");
        for (int i = 0; i < 6; i++) {
            trafficLight.change();
            Thread.sleep(500); // Simulating time
        }
        
        // Emergency mode
        System.out.println("\n--- Emergency Mode ---");
        trafficLight.emergencyMode();
        trafficLight.displayCurrentState();
        
        // In emergency, change has no effect
        System.out.println("\nAttempting to change in Emergency Mode:");
        trafficLight.change();
        trafficLight.displayCurrentState();
        
        // Back to normal
        System.out.println("\n--- Restoring Normal ---");
        trafficLight.normalMode();
        trafficLight.change();
        
        // Display state info
        TrafficLightState currentState = trafficLight.getCurrentState();
        System.out.println("\n📊 Current State Info:");
        System.out.println("Color: " + currentState.getColor());
        System.out.println("Duration: " + currentState.getDurationSeconds() + " seconds");
    }
}
```

---

### Part 3: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: Vending Machine (Product Selection, Payment, Dispensing)

**Scenario:** Vending machine ki alag-alag states - Idle, HasMoney, Dispensing, OutOfStock.

```java
// File: VendingMachineState.java

// State Interface
interface VendingMachineState {
    void insertMoney(VendingMachine machine, int amount);
    void selectProduct(VendingMachine machine, String product);
    void dispense(VendingMachine machine);
    void refundMoney(VendingMachine machine);
}

// Concrete State 1: Idle (Waiting for money)
class IdleState implements VendingMachineState {
    @Override
    public void insertMoney(VendingMachine machine, int amount) {
        machine.addBalance(amount);
        System.out.println("💰 Inserted ₹" + amount + ". Balance: ₹" + machine.getBalance());
        
        if (machine.getBalance() >= machine.getProductPrice(machine.getSelectedProduct())) {
            System.out.println("   ✅ Sufficient balance! Ready to dispense.");
            machine.setState(new HasMoneyState());
        }
    }
    
    @Override
    public void selectProduct(VendingMachine machine, String product) {
        if (machine.isProductAvailable(product)) {
            machine.setSelectedProduct(product);
            int price = machine.getProductPrice(product);
            System.out.println("🛒 Selected: " + product + " (₹" + price + ")");
            
            if (machine.getBalance() >= price) {
                machine.setState(new HasMoneyState());
                System.out.println("   ✅ Sufficient balance! Press dispense.");
            } else {
                System.out.println("   💰 Need ₹" + (price - machine.getBalance()) + " more");
            }
        } else {
            System.out.println("❌ " + product + " is out of stock!");
        }
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        System.out.println("⚠️ Please select a product first!");
    }
    
    @Override
    public void refundMoney(VendingMachine machine) {
        if (machine.getBalance() > 0) {
            System.out.println("💵 Refunding ₹" + machine.getBalance());
            machine.setBalance(0);
        } else {
            System.out.println("⚠️ No money to refund!");
        }
    }
}

// Concrete State 2: Has Money (Ready to dispense)
class HasMoneyState implements VendingMachineState {
    @Override
    public void insertMoney(VendingMachine machine, int amount) {
        machine.addBalance(amount);
        System.out.println("💰 Added more money. Balance: ₹" + machine.getBalance());
    }
    
    @Override
    public void selectProduct(VendingMachine machine, String product) {
        System.out.println("⚠️ Already selected " + machine.getSelectedProduct() + 
                          ". Dispense or cancel first.");
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        String product = machine.getSelectedProduct();
        int price = machine.getProductPrice(product);
        
        if (machine.getBalance() >= price) {
            System.out.println("🔄 Dispensing " + product + "...");
            machine.setBalance(machine.getBalance() - price);
            machine.decrementStock(product);
            machine.setState(new DispensingState());
            machine.getCurrentState().dispense(machine);
        } else {
            System.out.println("❌ Insufficient balance! Need ₹" + 
                              (price - machine.getBalance()) + " more");
        }
    }
    
    @Override
    public void refundMoney(VendingMachine machine) {
        System.out.println("💵 Cancelling. Refunding ₹" + machine.getBalance());
        machine.setBalance(0);
        machine.setSelectedProduct(null);
        machine.setState(new IdleState());
    }
}

// Concrete State 3: Dispensing (Product coming out)
class DispensingState implements VendingMachineState {
    @Override
    public void insertMoney(VendingMachine machine, int amount) {
        System.out.println("⏳ Please wait, dispensing in progress...");
    }
    
    @Override
    public void selectProduct(VendingMachine machine, String product) {
        System.out.println("⏳ Please wait, dispensing in progress...");
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        System.out.println("   📦 " + machine.getSelectedProduct() + " dispensed!");
        System.out.println("   ✅ Thank you! Please collect your item.");
        
        // Check if balance remains
        if (machine.getBalance() > 0) {
            System.out.println("   💵 Returning change: ₹" + machine.getBalance());
            machine.setBalance(0);
        }
        
        machine.setSelectedProduct(null);
        machine.setState(new IdleState());
    }
    
    @Override
    public void refundMoney(VendingMachine machine) {
        System.out.println("⚠️ Cannot refund while dispensing!");
    }
}

// Concrete State 4: Out of Stock
class OutOfStockState implements VendingMachineState {
    @Override
    public void insertMoney(VendingMachine machine, int amount) {
        System.out.println("⚠️ Machine is out of stock! Cannot accept money.");
        System.out.println("   Returning ₹" + amount);
    }
    
    @Override
    public void selectProduct(VendingMachine machine, String product) {
        System.out.println("❌ All products are out of stock!");
    }
    
    @Override
    public void dispense(VendingMachine machine) {
        System.out.println("❌ Nothing to dispense!");
    }
    
    @Override
    public void refundMoney(VendingMachine machine) {
        System.out.println("💵 Refunding ₹" + machine.getBalance());
        machine.setBalance(0);
    }
}

// Context
class VendingMachine {
    private VendingMachineState currentState;
    private Map<String, Integer> stock;
    private Map<String, Integer> prices;
    private int balance;
    private String selectedProduct;
    
    public VendingMachine() {
        this.currentState = new IdleState();
        this.balance = 0;
        this.stock = new HashMap<>();
        this.prices = new HashMap<>();
        
        // Initialize products
        stock.put("COLA", 5);
        stock.put("CHIPS", 3);
        stock.put("CANDY", 10);
        stock.put("WATER", 8);
        
        prices.put("COLA", 40);
        prices.put("CHIPS", 30);
        prices.put("CANDY", 10);
        prices.put("WATER", 20);
        
        System.out.println("🛒 Vending Machine Ready!");
        displayStock();
    }
    
    // State management
    public void setState(VendingMachineState state) { this.currentState = state; }
    public VendingMachineState getCurrentState() { return currentState; }
    
    // Balance management
    public int getBalance() { return balance; }
    public void setBalance(int balance) { this.balance = balance; }
    public void addBalance(int amount) { this.balance += amount; }
    
    // Product management
    public String getSelectedProduct() { return selectedProduct; }
    public void setSelectedProduct(String product) { this.selectedProduct = product; }
    public int getProductPrice(String product) { return prices.getOrDefault(product, 0); }
    public boolean isProductAvailable(String product) { return stock.getOrDefault(product, 0) > 0; }
    
    public void decrementStock(String product) {
        stock.merge(product, -1, Integer::sum);
        checkOutOfStock();
    }
    
    private void checkOutOfStock() {
        boolean allEmpty = stock.values().stream().allMatch(count -> count == 0);
        if (allEmpty) {
            setState(new OutOfStockState());
        }
    }
    
    // Delegation methods
    public void insertMoney(int amount) { currentState.insertMoney(this, amount); }
    public void selectProduct(String product) { currentState.selectProduct(this, product); }
    public void dispense() { currentState.dispense(this); }
    public void refundMoney() { currentState.refundMoney(this); }
    
    public void displayStock() {
        System.out.println("\n📦 Current Stock:");
        System.out.println("═══════════════════════");
        stock.forEach((product, count) -> 
            System.out.printf(" %-8s ₹%-3d (%d left)\n", product, prices.get(product), count));
        System.out.println("═══════════════════════");
    }
}

public class VendingMachineApp {
    public static void main(String[] args) {
        System.out.println("=== 🛒 Vending Machine - State Pattern ===\n");
        
        VendingMachine machine = new VendingMachine();
        
        // Scenario 1: Normal purchase
        System.out.println("\n--- Scenario 1: Normal Purchase ---");
        machine.insertMoney(50);
        machine.selectProduct("COLA");
        machine.dispense();
        
        // Scenario 2: Insufficient balance then add more
        System.out.println("\n--- Scenario 2: Add More Money ---");
        machine.insertMoney(10);
        machine.selectProduct("COLA");
        machine.insertMoney(30);
        machine.dispense();
        
        // Scenario 3: Cancel and refund
        System.out.println("\n--- Scenario 3: Cancel Order ---");
        machine.insertMoney(100);
        machine.selectProduct("CHIPS");
        machine.refundMoney();
        
        // Scenario 4: Product out of stock
        System.out.println("\n--- Scenario 4: Out of Stock Test ---");
        // Buy all CHIPS
        for (int i = 0; i < 5; i++) {
            machine.insertMoney(30);
            machine.selectProduct("CHIPS");
            machine.dispense();
        }
        
        System.out.println("\n--- Trying to buy CHIPS again ---");
        machine.insertMoney(30);
        machine.selectProduct("CHIPS");
        
        machine.displayStock();
    }
}
```

---

#### Example 2: Document Workflow (Draft → Review → Published)

**Scenario:** Document approval workflow - Draft, Moderation, Published, Rejected states.

```java
// File: DocumentWorkflowState.java
import java.time.LocalDateTime;

// State Interface
interface DocumentState {
    void submitForReview(Document doc, String reviewer);
    void approve(Document doc, String approver);
    void reject(Document doc, String rejector, String reason);
    void edit(Document doc, String newContent);
    void publish(Document doc);
    String getStatus();
}

// Concrete State 1: Draft
class DraftState implements DocumentState {
    @Override
    public void submitForReview(Document doc, String reviewer) {
        System.out.println("📤 Document submitted for review by: " + reviewer);
        doc.setReviewer(reviewer);
        doc.setState(new ReviewState());
        doc.addHistory("Submitted for review by " + reviewer);
    }
    
    @Override
    public void approve(Document doc, String approver) {
        System.out.println("❌ Cannot approve a draft! Submit for review first.");
    }
    
    @Override
    public void reject(Document doc, String rejector, String reason) {
        System.out.println("❌ Cannot reject a draft!");
    }
    
    @Override
    public void edit(Document doc, String newContent) {
        doc.setContent(newContent);
        System.out.println("✏️ Draft updated: " + newContent.substring(0, Math.min(30, newContent.length())) + "...");
        doc.addHistory("Content edited");
    }
    
    @Override
    public void publish(Document doc) {
        System.out.println("❌ Cannot publish directly! Submit for review first.");
    }
    
    @Override
    public String getStatus() { return "DRAFT"; }
}

// Concrete State 2: Under Review
class ReviewState implements DocumentState {
    @Override
    public void submitForReview(Document doc, String reviewer) {
        System.out.println("⚠️ Document is already under review by " + doc.getReviewer());
    }
    
    @Override
    public void approve(Document doc, String approver) {
        if (approver.equals(doc.getReviewer())) {
            System.out.println("✅ Document approved by " + approver);
            doc.setState(new ApprovedState());
            doc.addHistory("Approved by " + approver);
        } else {
            System.out.println("❌ Only assigned reviewer (" + doc.getReviewer() + ") can approve!");
        }
    }
    
    @Override
    public void reject(Document doc, String rejector, String reason) {
        if (rejector.equals(doc.getReviewer())) {
            System.out.println("❌ Document rejected by " + rejector + ". Reason: " + reason);
            doc.setRejectionReason(reason);
            doc.setState(new RejectedState());
            doc.addHistory("Rejected by " + rejector + ": " + reason);
        } else {
            System.out.println("❌ Only assigned reviewer can reject!");
        }
    }
    
    @Override
    public void edit(Document doc, String newContent) {
        System.out.println("❌ Cannot edit while under review!");
    }
    
    @Override
    public void publish(Document doc) {
        System.out.println("❌ Must be approved before publishing!");
    }
    
    @Override
    public String getStatus() { return "IN_REVIEW"; }
}

// Concrete State 3: Approved
class ApprovedState implements DocumentState {
    @Override
    public void submitForReview(Document doc, String reviewer) {
        System.out.println("⚠️ Document already approved!");
    }
    
    @Override
    public void approve(Document doc, String approver) {
        System.out.println("⚠️ Document already approved!");
    }
    
    @Override
    public void reject(Document doc, String rejector, String reason) {
        System.out.println("❌ Cannot reject an approved document!");
    }
    
    @Override
    public void edit(Document doc, String newContent) {
        doc.setContent(newContent);
        doc.setState(new DraftState());
        System.out.println("✏️ Content modified. Document moved back to DRAFT.");
        doc.addHistory("Edited after approval, moved to Draft");
    }
    
    @Override
    public void publish(Document doc) {
        System.out.println("🚀 Publishing document...");
        doc.setPublishedAt(LocalDateTime.now());
        doc.setState(new PublishedState());
        doc.addHistory("Published");
    }
    
    @Override
    public String getStatus() { return "APPROVED"; }
}

// Concrete State 4: Rejected
class RejectedState implements DocumentState {
    @Override
    public void submitForReview(Document doc, String reviewer) {
        doc.setReviewer(reviewer);
        doc.setState(new ReviewState());
        System.out.println("📤 Re-submitted for review by: " + reviewer);
        doc.addHistory("Re-submitted after rejection to " + reviewer);
    }
    
    @Override
    public void approve(Document doc, String approver) {
        System.out.println("❌ Cannot approve a rejected document! Re-submit first.");
    }
    
    @Override
    public void reject(Document doc, String rejector, String reason) {
        System.out.println("⚠️ Document is already rejected!");
    }
    
    @Override
    public void edit(Document doc, String newContent) {
        doc.setContent(newContent);
        doc.setState(new DraftState());
        System.out.println("✏️ Content edited. Document moved back to DRAFT.");
        doc.addHistory("Edited after rejection, moved to Draft");
    }
    
    @Override
    public void publish(Document doc) {
        System.out.println("❌ Cannot publish a rejected document!");
    }
    
    @Override
    public String getStatus() { return "REJECTED"; }
}

// Concrete State 5: Published
class PublishedState implements DocumentState {
    @Override
    public void submitForReview(Document doc, String reviewer) {
        System.out.println("⚠️ Document already published! Create a new version to modify.");
    }
    
    @Override
    public void approve(Document doc, String approver) {
        System.out.println("⚠️ Document already published!");
    }
    
    @Override
    public void reject(Document doc, String rejector, String reason) {
        System.out.println("⚠️ Cannot reject a published document!");
    }
    
    @Override
    public void edit(Document doc, String newContent) {
        // Create new version in draft
        System.out.println("📝 Creating new draft version for editing...");
        doc.setContent(newContent);
        doc.setState(new DraftState());
        doc.incrementVersion();
        doc.addHistory("New version " + doc.getVersion() + " created from published");
    }
    
    @Override
    public void publish(Document doc) {
        System.out.println("⚠️ Document already published on " + doc.getPublishedAt());
    }
    
    @Override
    public String getStatus() { return "PUBLISHED"; }
}

// Context
class Document {
    private String title;
    private String content;
    private DocumentState state;
    private String reviewer;
    private String rejectionReason;
    private LocalDateTime publishedAt;
    private int version;
    private List<String> history;
    
    public Document(String title, String content) {
        this.title = title;
        this.content = content;
        this.state = new DraftState();
        this.version = 1;
        this.history = new ArrayList<>();
        addHistory("Document created (v" + version + ")");
    }
    
    // State management
    public void setState(DocumentState state) { this.state = state; }
    public DocumentState getState() { return state; }
    
    // Getters and setters
    public String getTitle() { return title; }
    public String getContent() { return content; }
    public void setContent(String content) { this.content = content; }
    public String getReviewer() { return reviewer; }
    public void setReviewer(String reviewer) { this.reviewer = reviewer; }
    public void setRejectionReason(String reason) { this.rejectionReason = reason; }
    public String getRejectionReason() { return rejectionReason; }
    public LocalDateTime getPublishedAt() { return publishedAt; }
    public void setPublishedAt(LocalDateTime time) { this.publishedAt = time; }
    public int getVersion() { return version; }
    public void incrementVersion() { this.version++; }
    
    public void addHistory(String event) {
        history.add("[" + LocalDateTime.now() + "] " + event);
    }
    
    // Delegation methods
    public void submitForReview(String reviewer) { state.submitForReview(this, reviewer); }
    public void approve(String approver) { state.approve(this, approver); }
    public void reject(String rejector, String reason) { state.reject(this, rejector, reason); }
    public void edit(String newContent) { state.edit(this, newContent); }
    public void publish() { state.publish(this); }
    
    public void displayStatus() {
        System.out.println("\n📄 Document: " + title + " (v" + version + ")");
        System.out.println("═══════════════════════════════════════");
        System.out.println("Status: " + state.getStatus());
        if (reviewer != null) System.out.println("Reviewer: " + reviewer);
        if (rejectionReason != null) System.out.println("Rejection Reason: " + rejectionReason);
        if (publishedAt != null) System.out.println("Published: " + publishedAt);
        System.out.println("═══════════════════════════════════════");
    }
    
    public void displayHistory() {
        System.out.println("\n📜 Change History:");
        history.forEach(h -> System.out.println("  " + h));
    }
}

public class DocumentWorkflowApp {
    public static void main(String[] args) {
        System.out.println("=== 📄 Document Approval Workflow ===\n");
        
        Document doc = new Document("Annual Report 2024", "This is the annual report content...");
        doc.displayStatus();
        
        // Try illegal transitions
        System.out.println("\n--- Trying Illegal Actions ---");
        doc.publish();
        doc.approve("Manager");
        
        // Normal workflow
        System.out.println("\n--- Normal Workflow ---");
        doc.edit("Updated annual report with Q1 data...");
        doc.submitForReview("Manager Singh");
        doc.displayStatus();
        
        // Review and approve
        System.out.println("\n--- Review Phase ---");
        doc.approve("Manager Singh");
        doc.displayStatus();
        
        // Publish
        System.out.println("\n--- Publishing ---");
        doc.publish();
        doc.displayStatus();
        
        // Create new version
        System.out.println("\n--- Creating New Version ---");
        doc.edit("Annual report updated for 2025...");
        doc.displayStatus();
        
        // Scenario with rejection
        Document doc2 = new Document("Policy Update", "New remote work policy...");
        System.out.println("\n=== Second Document - Rejection Scenario ===\n");
        doc2.displayStatus();
        
        doc2.submitForReview("HR Director");
        doc2.reject("HR Director", "Needs more details on working hours");
        doc2.displayStatus();
        
        doc2.edit("Updated policy with detailed working hours and expectations...");
        doc2.submitForReview("HR Director");
        doc2.approve("HR Director");
        doc2.publish();
        
        // Display history
        doc.displayHistory();
        doc2.displayHistory();
    }
}
```

---

#### Example 3: Media Player (Playing, Paused, Stopped, Buffering)

**Scenario:** Video player ke alag-alag states - Playing, Paused, Stopped, Buffering.

```java
// File: MediaPlayerState.java

// State Interface
interface PlayerState {
    void play(MediaPlayer player);
    void pause(MediaPlayer player);
    void stop(MediaPlayer player);
    void seek(MediaPlayer player, int seconds);
    String getStateName();
}

// Concrete State 1: Stopped
class StoppedState implements PlayerState {
    @Override
    public void play(MediaPlayer player) {
        System.out.println("▶️ Starting playback from beginning...");
        player.setCurrentTime(0);
        
        // Simulate buffering before play
        player.setState(new BufferingState());
        player.getCurrentState().play(player);
    }
    
    @Override
    public void pause(MediaPlayer player) {
        System.out.println("⏸️ Player is already stopped!");
    }
    
    @Override
    public void stop(MediaPlayer player) {
        System.out.println("⏹️ Already stopped!");
    }
    
    @Override
    public void seek(MediaPlayer player, int seconds) {
        System.out.println("⚠️ Cannot seek while stopped. Start playing first!");
    }
    
    @Override
    public String getStateName() { return "STOPPED"; }
}

// Concrete State 2: Buffering
class BufferingState implements PlayerState {
    @Override
    public void play(MediaPlayer player) {
        System.out.println("🔄 Buffering...");
        // Simulate buffering time
        try {
            Thread.sleep(1000);
        } catch (InterruptedException e) {}
        
        System.out.println("✅ Buffering complete! Now playing.");
        player.setState(new PlayingState());
    }
    
    @Override
    public void pause(MediaPlayer player) {
        System.out.println("⏸️ Cancelling buffer...");
        player.setState(new PausedState());
    }
    
    @Override
    public void stop(MediaPlayer player) {
        System.out.println("⏹️ Stopping buffering...");
        player.setState(new StoppedState());
    }
    
    @Override
    public void seek(MediaPlayer player, int seconds) {
        System.out.println("⚠️ Cannot seek while buffering!");
    }
    
    @Override
    public String getStateName() { return "BUFFERING"; }
}

// Concrete State 3: Playing
class PlayingState implements PlayerState {
    @Override
    public void play(MediaPlayer player) {
        System.out.println("▶️ Already playing at " + player.getCurrentTime() + "s");
    }
    
    @Override
    public void pause(MediaPlayer player) {
        System.out.println("⏸️ Paused at " + player.getCurrentTime() + "s");
        player.setState(new PausedState());
    }
    
    @Override
    public void stop(MediaPlayer player) {
        System.out.println("⏹️ Playback stopped at " + player.getCurrentTime() + "s");
        player.setCurrentTime(0);
        player.setState(new StoppedState());
    }
    
    @Override
    public void seek(MediaPlayer player, int seconds) {
        System.out.println("⏩ Seeking to " + seconds + "s...");
        player.setCurrentTime(seconds);
        
        // Simulate buffering after seek
        player.setState(new BufferingState());
        player.getCurrentState().play(player);
    }
    
    @Override
    public String getStateName() { return "PLAYING"; }
}

// Concrete State 4: Paused
class PausedState implements PlayerState {
    @Override
    public void play(MediaPlayer player) {
        System.out.println("▶️ Resuming from " + player.getCurrentTime() + "s...");
        player.setState(new BufferingState());
        player.getCurrentState().play(player);
    }
    
    @Override
    public void pause(MediaPlayer player) {
        System.out.println("⏸️ Already paused!");
    }
    
    @Override
    public void stop(MediaPlayer player) {
        System.out.println("⏹️ Stopped from pause at " + player.getCurrentTime() + "s");
        player.setCurrentTime(0);
        player.setState(new StoppedState());
    }
    
    @Override
    public void seek(MediaPlayer player, int seconds) {
        System.out.println("⏩ Seeking to " + seconds + "s from paused state...");
        player.setCurrentTime(seconds);
    }
    
    @Override
    public String getStateName() { return "PAUSED"; }
}

// Context
class MediaPlayer {
    private PlayerState currentState;
    private String currentMedia;
    private int currentTime;
    private int duration;
    
    public MediaPlayer() {
        this.currentState = new StoppedState();
        this.currentTime = 0;
    }
    
    public void setState(PlayerState state) { this.currentState = state; }
    public PlayerState getCurrentState() { return currentState; }
    public int getCurrentTime() { return currentTime; }
    public void setCurrentTime(int time) { this.currentTime = time; }
    public String getCurrentMedia() { return currentMedia; }
    
    public void loadMedia(String mediaName, int duration) {
        this.currentMedia = mediaName;
        this.duration = duration;
        this.currentTime = 0;
        this.currentState = new StoppedState();
        System.out.println("📁 Loaded: " + mediaName + " (" + duration + " seconds)");
    }
    
    // Delegation methods
    public void play() { currentState.play(this); }
    public void pause() { currentState.pause(this); }
    public void stop() { currentState.stop(this); }
    public void seek(int seconds) { currentState.seek(this, seconds); }
    
    public void displayStatus() {
        System.out.println("\n🎬 Media Player Status");
        System.out.println("═══════════════════════════");
        System.out.println("Media: " + (currentMedia != null ? currentMedia : "None"));
        System.out.println("State: " + currentState.getStateName());
        System.out.println("Time: " + currentTime + "s / " + duration + "s");
        System.out.println("═══════════════════════════");
    }
}

public class MediaPlayerApp {
    public static void main(String[] args) {
        System.out.println("=== 🎬 Media Player - State Pattern ===\n");
        
        MediaPlayer player = new MediaPlayer();
        player.loadMedia("The Matrix.mp4", 3600);
        player.displayStatus();
        
        // Normal playback
        System.out.println("\n--- Normal Playback ---");
        player.play();
        player.displayStatus();
        
        // Pause and resume
        System.out.println("\n--- Pause and Resume ---");
        player.pause();
        player.displayStatus();
        
        player.play();
        player.displayStatus();
        
        // Seek
        System.out.println("\n--- Seeking ---");
        player.seek(1200);
        player.displayStatus();
        
        // Stop
        System.out.println("\n--- Stop ---");
        player.stop();
        player.displayStatus();
        
        // Illegal operations
        System.out.println("\n--- Illegal Operations ---");
        player.pause();
        player.seek(500);
        
        // Load new media
        System.out.println("\n--- New Media ---");
        player.loadMedia("Inception.mp4", 5400);
        player.play();
        player.displayStatus();
    }
}
```

---

#### Example 4: ATM Machine (Card Inserted, Authenticated, Transaction)

**Scenario:** ATM ki flow - NoCard → HasCard → Authenticated → Transaction Mode.

```java
// File: ATMState.java

// State Interface
interface ATMState {
    void insertCard(ATM atm, String cardNumber);
    void ejectCard(ATM atm);
    void enterPin(ATM atm, String pin);
    void withdraw(ATM atm, double amount);
    void deposit(ATM atm, double amount);
    void checkBalance(ATM atm);
    String getStateName();
}

// Concrete State 1: No Card
class NoCardState implements ATMState {
    @Override
    public void insertCard(ATM atm, String cardNumber) {
        if (atm.validateCard(cardNumber)) {
            atm.setCardNumber(cardNumber);
            System.out.println("💳 Card accepted: ****" + cardNumber.substring(cardNumber.length() - 4));
            atm.setState(new HasCardState());
        } else {
            System.out.println("❌ Invalid card! Ejecting...");
        }
    }
    
    @Override
    public void ejectCard(ATM atm) {
        System.out.println("⚠️ No card to eject!");
    }
    
    @Override
    public void enterPin(ATM atm, String pin) {
        System.out.println("⚠️ Insert card first!");
    }
    
    @Override
    public void withdraw(ATM atm, double amount) {
        System.out.println("⚠️ Insert card first!");
    }
    
    @Override
    public void deposit(ATM atm, double amount) {
        System.out.println("⚠️ Insert card first!");
    }
    
    @Override
    public void checkBalance(ATM atm) {
        System.out.println("⚠️ Insert card first!");
    }
    
    @Override
    public String getStateName() { return "NO_CARD"; }
}

// Concrete State 2: Has Card (Waiting for PIN)
class HasCardState implements ATMState {
    private int pinAttempts = 0;
    private static final int MAX_ATTEMPTS = 3;
    
    @Override
    public void insertCard(ATM atm, String cardNumber) {
        System.out.println("⚠️ Card already inserted!");
    }
    
    @Override
    public void ejectCard(ATM atm) {
        System.out.println("💳 Card ejected. Goodbye!");
        atm.setCardNumber(null);
        atm.setState(new NoCardState());
    }
    
    @Override
    public void enterPin(ATM atm, String pin) {
        if (atm.validatePin(pin)) {
            System.out.println("🔐 PIN accepted! Welcome " + atm.getAccountHolder());
            atm.setState(new AuthenticatedState());
        } else {
            pinAttempts++;
            if (pinAttempts >= MAX_ATTEMPTS) {
                System.out.println("❌ Too many invalid PIN attempts! Card blocked.");
                atm.ejectCard();
            } else {
                System.out.println("❌ Invalid PIN! Attempts remaining: " + (MAX_ATTEMPTS - pinAttempts));
            }
        }
    }
    
    @Override
    public void withdraw(ATM atm, double amount) {
        System.out.println("⚠️ Enter PIN first!");
    }
    
    @Override
    public void deposit(ATM atm, double amount) {
        System.out.println("⚠️ Enter PIN first!");
    }
    
    @Override
    public void checkBalance(ATM atm) {
        System.out.println("⚠️ Enter PIN first!");
    }
    
    @Override
    public String getStateName() { return "HAS_CARD"; }
}

// Concrete State 3: Authenticated
class AuthenticatedState implements ATMState {
    @Override
    public void insertCard(ATM atm, String cardNumber) {
        System.out.println("⚠️ Already authenticated! Eject current card first.");
    }
    
    @Override
    public void ejectCard(ATM atm) {
        System.out.println("💳 Card ejected. Thank you for banking with us!");
        atm.setCardNumber(null);
        atm.setState(new NoCardState());
    }
    
    @Override
    public void enterPin(ATM atm, String pin) {
        System.out.println("⚠️ Already authenticated!");
    }
    
    @Override
    public void withdraw(ATM atm, double amount) {
        double balance = atm.getBalance();
        if (amount > balance) {
            System.out.println("❌ Insufficient balance! Available: ₹" + balance);
        } else if (amount > atm.getDailyLimit()) {
            System.out.println("❌ Exceeds daily withdrawal limit of ₹" + atm.getDailyLimit());
        } else {
            atm.setBalance(balance - amount);
            atm.addTransaction("Withdrawal", -amount);
            System.out.println("💵 Dispensing ₹" + amount);
            System.out.println("   Remaining balance: ₹" + atm.getBalance());
        }
    }
    
    @Override
    public void deposit(ATM atm, double amount) {
        atm.setBalance(atm.getBalance() + amount);
        atm.addTransaction("Deposit", amount);
        System.out.println("💰 Deposited ₹" + amount);
        System.out.println("   New balance: ₹" + atm.getBalance());
    }
    
    @Override
    public void checkBalance(ATM atm) {
        System.out.println("💰 Current Balance: ₹" + atm.getBalance());
    }
    
    @Override
    public String getStateName() { return "AUTHENTICATED"; }
}

// Context
class ATM {
    private ATMState currentState;
    private String cardNumber;
    private String accountHolder;
    private double balance;
    private double dailyLimit;
    private List<String> transactions;
    
    // Dummy data store
    private Map<String, String> validCards = Map.of(
        "1234567890123456", "Rahul Sharma",
        "9876543210987654", "Priya Patel"
    );
    private Map<String, String> cardPins = Map.of(
        "1234567890123456", "1234",
        "9876543210987654", "4321"
    );
    private Map<String, Double> balances = Map.of(
        "1234567890123456", 25000.0,
        "9876543210987654", 50000.0
    );
    
    public ATM() {
        this.currentState = new NoCardState();
        this.dailyLimit = 20000;
        this.transactions = new ArrayList<>();
        System.out.println("🏧 ATM Ready! Please insert your card.");
    }
    
    // State management
    public void setState(ATMState state) { this.currentState = state; }
    public ATMState getCurrentState() { return currentState; }
    
    // Card management
    public String getCardNumber() { return cardNumber; }
    public void setCardNumber(String cardNumber) { 
        this.cardNumber = cardNumber;
        if (cardNumber != null) {
            this.accountHolder = validCards.get(cardNumber);
            this.balance = balances.get(cardNumber);
        }
    }
    public String getAccountHolder() { return accountHolder; }
    public boolean validateCard(String cardNumber) { return validCards.containsKey(cardNumber); }
    public boolean validatePin(String pin) { 
        return pin.equals(cardPins.get(cardNumber)); 
    }
    
    // Balance management
    public double getBalance() { return balance; }
    public void setBalance(double balance) { this.balance = balance; }
    public double getDailyLimit() { return dailyLimit; }
    
    public void addTransaction(String type, double amount) {
        transactions.add(type + ": ₹" + Math.abs(amount));
    }
    
    // Delegation methods
    public void insertCard(String cardNumber) { currentState.insertCard(this, cardNumber); }
    public void ejectCard() { currentState.ejectCard(this); }
    public void enterPin(String pin) { currentState.enterPin(this, pin); }
    public void withdraw(double amount) { currentState.withdraw(this, amount); }
    public void deposit(double amount) { currentState.deposit(this, amount); }
    public void checkBalance() { currentState.checkBalance(this); }
    
    public void displayStatus() {
        System.out.println("\n🏧 ATM Status: " + currentState.getStateName());
        if (accountHolder != null) {
            System.out.println("👤 Account: " + accountHolder);
            System.out.println("💳 Card: ****" + cardNumber.substring(cardNumber.length() - 4));
        }
        System.out.println("══════════════════════════════");
    }
}

public class ATMApp {
    public static void main(String[] args) {
        System.out.println("=== 🏧 ATM Machine - State Pattern ===\n");
        
        ATM atm = new ATM();
        atm.displayStatus();
        
        // Scenario 1: Normal withdrawal
        System.out.println("\n--- Normal Withdrawal ---");
        atm.insertCard("1234567890123456");
        atm.displayStatus();
        
        atm.enterPin("9999"); // Wrong PIN
        atm.enterPin("1111"); // Wrong PIN
        atm.enterPin("1234"); // Correct PIN
        atm.displayStatus();
        
        atm.checkBalance();
        atm.withdraw(5000);
        atm.withdraw(30000); // Exceeds balance
        atm.withdraw(25000); // Exceeds daily limit
        
        atm.ejectCard();
        atm.displayStatus();
        
        // Scenario 2: Deposit
        System.out.println("\n--- Deposit ---");
        atm.insertCard("9876543210987654");
        atm.enterPin("4321");
        atm.checkBalance();
        atm.deposit(10000);
        atm.checkBalance();
        atm.ejectCard();
        
        // Scenario 3: Illegal operations
        System.out.println("\n--- Testing Illegal Operations ---");
        atm.withdraw(1000); // No card
        atm.checkBalance(); // No card
    }
}
```

---

#### Example 5: Order Processing System (E-Commerce)

**Scenario:** Order lifecycle - New → Confirmed → Shipped → Delivered → Cancelled/Returned.

```java
// File: OrderState.java
import java.time.LocalDateTime;

// State Interface
interface OrderState {
    void confirm(Order order);
    void ship(Order order, String trackingNumber);
    void deliver(Order order);
    void cancel(Order order, String reason);
    void initiateReturn(Order order, String reason);
    String getStatusName();
}

// Concrete State 1: New Order
class NewOrderState implements OrderState {
    @Override
    public void confirm(Order order) {
        System.out.println("✅ Order confirmed! Preparing for shipment.");
        order.setState(new ConfirmedState());
        order.addHistory("Order confirmed");
    }
    
    @Override
    public void ship(Order order, String trackingNumber) {
        System.out.println("❌ Cannot ship an unconfirmed order!");
    }
    
    @Override
    public void deliver(Order order) {
        System.out.println("❌ Cannot deliver an unconfirmed order!");
    }
    
    @Override
    public void cancel(Order order, String reason) {
        System.out.println("❌ Order cancelled: " + reason);
        order.setState(new CancelledState(reason));
        order.addHistory("Order cancelled: " + reason);
    }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("❌ Cannot return an unconfirmed order!");
    }
    
    @Override
    public String getStatusName() { return "NEW"; }
}

// Concrete State 2: Confirmed
class ConfirmedOrderState implements OrderState {
    @Override
    public void confirm(Order order) {
        System.out.println("⚠️ Order already confirmed!");
    }
    
    @Override
    public void ship(Order order, String trackingNumber) {
        order.setTrackingNumber(trackingNumber);
        System.out.println("📦 Order shipped! Tracking: " + trackingNumber);
        order.setState(new ShippedState());
        order.addHistory("Order shipped: " + trackingNumber);
        order.sendNotification("Your order has been shipped!");
    }
    
    @Override
    public void deliver(Order order) {
        System.out.println("❌ Cannot deliver before shipping!");
    }
    
    @Override
    public void cancel(Order order, String reason) {
        System.out.println("⚠️ Order confirmed. Cancellation may incur charges.");
        order.setState(new CancelledState(reason));
        order.addHistory("Order cancelled after confirmation: " + reason);
        order.processRefund(0.9); // 90% refund
    }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("❌ Cannot return before delivery!");
    }
    
    @Override
    public String getStatusName() { return "CONFIRMED"; }
}

// Concrete State 3: Shipped
class ShippedOrderState implements OrderState {
    @Override
    public void confirm(Order order) {
        System.out.println("⚠️ Order already confirmed and shipped!");
    }
    
    @Override
    public void ship(Order order, String trackingNumber) {
        System.out.println("⚠️ Order already shipped! Tracking: " + order.getTrackingNumber());
    }
    
    @Override
    public void deliver(Order order) {
        order.setDeliveredAt(LocalDateTime.now());
        System.out.println("📬 Order delivered at " + order.getDeliveredAt());
        order.setState(new DeliveredState());
        order.addHistory("Order delivered");
        order.sendNotification("Your order has been delivered!");
    }
    
    @Override
    public void cancel(Order order, String reason) {
        System.out.println("⚠️ Order is in transit! Cannot cancel directly. RTO will be initiated.");
        order.setState(new CancelledState("RTO: " + reason));
        order.addHistory("Cancelled in transit: " + reason);
    }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("⚠️ Cannot return before delivery!");
    }
    
    @Override
    public String getStatusName() { return "SHIPPED"; }
}

// Concrete State 4: Delivered
class DeliveredOrderState implements OrderState {
    @Override
    public void confirm(Order order) {
        System.out.println("⚠️ Order already delivered!");
    }
    
    @Override
    public void ship(Order order, String trackingNumber) {
        System.out.println("⚠️ Order already delivered!");
    }
    
    @Override
    public void deliver(Order order) {
        System.out.println("⚠️ Order already delivered!");
    }
    
    @Override
    public void cancel(Order order, String reason) {
        System.out.println("⚠️ Cannot cancel a delivered order! Initiate return instead.");
    }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("↩️ Return initiated: " + reason);
        order.setState(new ReturnedState(reason));
        order.addHistory("Return initiated: " + reason);
        order.sendNotification("Return has been initiated for your order.");
    }
    
    @Override
    public String getStatusName() { return "DELIVERED"; }
}

// Concrete State 5: Cancelled
class CancelledOrderState implements OrderState {
    private String reason;
    
    public CancelledOrderState(String reason) {
        this.reason = reason;
    }
    
    @Override
    public void confirm(Order order) {
        System.out.println("❌ Cannot confirm a cancelled order!");
    }
    
    @Override
    public void ship(Order order, String trackingNumber) {
        System.out.println("❌ Cannot ship a cancelled order!");
    }
    
    @Override
    public void deliver(Order order) {
        System.out.println("❌ Cannot deliver a cancelled order!");
    }
    
    @Override
    public void cancel(Order order, String reason) {
        System.out.println("⚠️ Order already cancelled!");
    }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("⚠️ Cannot return a cancelled order!");
    }
    
    @Override
    public String getStatusName() { return "CANCELLED (" + reason + ")"; }
}

// Concrete State 6: Returned
class ReturnedOrderState implements OrderState {
    private String reason;
    
    public ReturnedOrderState(String reason) {
        this.reason = reason;
    }
    
    @Override
    public void confirm(Order order) { System.out.println("❌ Cannot confirm a returned order!"); }
    @Override
    public void ship(Order order, String trackingNumber) { System.out.println("❌ Cannot ship a returned order!"); }
    @Override
    public void deliver(Order order) { System.out.println("❌ Cannot deliver a returned order!"); }
    @Override
    public void cancel(Order order, String reason) { System.out.println("⚠️ Order already returned!"); }
    
    @Override
    public void initiateReturn(Order order, String reason) {
        System.out.println("⚠️ Return already initiated!");
    }
    
    @Override
    public String getStatusName() { return "RETURNED (" + reason + ")"; }
}

// Context
class Order {
    private String orderId;
    private String customerName;
    private double amount;
    private OrderState state;
    private String trackingNumber;
    private LocalDateTime deliveredAt;
    private List<String> history;
    private List<String> notifications;
    
    public Order(String orderId, String customerName, double amount) {
        this.orderId = orderId;
        this.customerName = customerName;
        this.amount = amount;
        this.state = new NewOrderState();
        this.history = new ArrayList<>();
        this.notifications = new ArrayList<>();
        addHistory("Order created: ₹" + amount);
    }
    
    public void setState(OrderState state) { this.state = state; }
    public OrderState getState() { return state; }
    public String getOrderId() { return orderId; }
    public double getAmount() { return amount; }
    public String getTrackingNumber() { return trackingNumber; }
    public void setTrackingNumber(String trackingNumber) { this.trackingNumber = trackingNumber; }
    public LocalDateTime getDeliveredAt() { return deliveredAt; }
    public void setDeliveredAt(LocalDateTime time) { this.deliveredAt = time; }
    
    public void addHistory(String event) {
        history.add("[" + LocalDateTime.now() + "] " + event);
    }
    
    public void sendNotification(String message) {
        notifications.add(message);
        System.out.println("   📱 Notification to " + customerName + ": " + message);
    }
    
    public void processRefund(double percentage) {
        double refundAmount = amount * percentage;
        System.out.println("   💰 Processing refund: ₹" + refundAmount + " (" + (percentage * 100) + "%)");
    }
    
    // Delegation methods
    public void confirm() { state.confirm(this); }
    public void ship(String trackingNumber) { state.ship(this, trackingNumber); }
    public void deliver() { state.deliver(this); }
    public void cancel(String reason) { state.cancel(this, reason); }
    public void initiateReturn(String reason) { state.initiateReturn(this, reason); }
    
    public void displayStatus() {
        System.out.println("\n📦 Order: " + orderId);
        System.out.println("═══════════════════════════════════════");
        System.out.println("Customer: " + customerName);
        System.out.println("Amount: ₹" + amount);
        System.out.println("Status: " + state.getStatusName());
        if (trackingNumber != null) System.out.println("Tracking: " + trackingNumber);
        if (deliveredAt != null) System.out.println("Delivered: " + deliveredAt);
        System.out.println("═══════════════════════════════════════");
    }
    
    public void displayHistory() {
        System.out.println("\n📜 Order History:");
        history.forEach(h -> System.out.println("  " + h));
    }
}

public class OrderProcessingApp {
    public static void main(String[] args) {
        System.out.println("=== 📦 E-Commerce Order Processing ===\n");
        
        // Scenario 1: Happy path
        System.out.println("--- Happy Path: Order to Delivery ---");
        Order order1 = new Order("ORD-101", "Rahul Sharma", 2500);
        order1.displayStatus();
        
        order1.confirm();
        order1.displayStatus();
        
        order1.ship("AWB123456789");
        order1.displayStatus();
        
        order1.deliver();
        order1.displayStatus();
        
        // Scenario 2: Cancellation
        System.out.println("\n--- Cancellation Scenarios ---");
        Order order2 = new Order("ORD-102", "Priya Patel", 5000);
        order2.displayStatus();
        order2.cancel("Changed my mind");
        order2.displayStatus();
        
        Order order3 = new Order("ORD-103", "Amit Singh", 8000);
        order3.confirm();
        order3.cancel("Found better price");
        order3.displayStatus();
        
        // Scenario 3: Return
        System.out.println("\n--- Return Scenario ---");
        Order order4 = new Order("ORD-104", "Neha Gupta", 3500);
        order4.confirm();
        order4.ship("AWB987654321");
        order4.deliver();
        order4.displayStatus();
        
        order4.initiateReturn("Defective product");
        order4.displayStatus();
        
        // Display histories
        System.out.println("\n" + "=".repeat(50));
        order1.displayHistory();
        order2.displayHistory();
        order4.displayHistory();
    }
}
```

---

### Part 6: State Pattern - Summary & Interview Points

| Aspect | Details |
|:---|:---|
| **Problem Solved** | Object behavior changes with internal state |
| **Use When** | Complex if-else based on state, state machine |
| **Key Components** | Context, State (Interface), ConcreteStates |
| **Real Examples** | Workflow engines, Media players, ATM, Vending machines |

**Interview Question:** *What is State Pattern?*

**Answer:** *"Sir, State pattern allows an object to alter its behavior when its internal state changes. The object appears to change its class. It encapsulates state-specific behavior into separate classes and delegates requests to the current state object. This eliminates if-else state checks and makes adding new states easy."*

**Interview Question:** *Difference between State and Strategy Pattern?*

| State Pattern | Strategy Pattern |
|:---|:---|
| State changes **automatically** internally | Strategy is **set externally** by client |
| States know about each other (transition logic) | Strategies are independent |
| Context behavior changes based on state | Client chooses which algorithm to use |
| Multiple transitions between states | Usually one strategy at a time |

**Interview Question:** *Difference between State and Observer Pattern?*

| State Pattern | Observer Pattern |
|:---|:---|
| Focus on **internal state** change | Focus on **notification** of state change |
| Encapsulates behavior per state | Decouples subject from observers |
| Object changes its own state | Subject notifies observers |

**Interview Question:** *Advantages of State Pattern?*

1. **Eliminates if-else:** No state-checking conditionals.
2. **Open/Closed Principle:** New states add easily without modifying existing code.
3. **Single Responsibility:** Each state class handles its own behavior.
4. **Testable:** Each state can be unit tested independently.

**Interview Question:** *When should you NOT use State Pattern?*

1. **Few states:** If states are 2-3 and rarely change, if-else might be simpler.
2. **No behavior change:** If all states have same behavior.
3. **Performance critical:** Extra object creation overhead for each state.

**Real Java Examples:**
```java
// Java's Thread has states (implicitly using state pattern)
Thread thread = new Thread();
// States: NEW → RUNNABLE → BLOCKED → WAITING → TIMED_WAITING → TERMINATED

// JSF/Spring Web Flow - Page navigation flows
// CDI Conversation scope manages state
```
