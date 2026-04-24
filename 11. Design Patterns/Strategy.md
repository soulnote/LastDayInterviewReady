Bilkul! Ab baari hai **Strategy Design Pattern** ki. Ye pattern un cases mein kaam aata hai jahan **ek hi kaam ko karne ke multiple tarike (algorithms) ho** aur runtime pe decide karna ho ki kaunsa tarika use karna hai.

**Strategy Pattern ka Funda (Ek Line Mein):**
*"Algorithms ko alag-alag classes mein encapsulate karo, aur runtime pe swap karo - bilkul waise jaise phone mein typing keyboard (Gboard/SwiftKey/Samsung) change karte ho."*

---

### Part 1: Problem Statement (If-Else Hell)

Sochiye aapke paas **Payment System** hai. Payment Credit Card se bhi ho sakta hai, UPI se bhi, Paytm se bhi. Agar aap if-else use karoge to code mess ho jayega.

#### ❌ BAD PRACTICE: If-Else Ladder (Violates Open/Closed Principle)

```java
// Bina Strategy ke - If-Else Hell!
class PaymentProcessor {
    public void pay(String method, double amount) {
        if (method.equals("CREDIT_CARD")) {
            System.out.println("💳 Processing Credit Card payment of ₹" + amount);
            // Credit card specific logic (CVV, Expiry validation etc.)
        } 
        else if (method.equals("UPI")) {
            System.out.println("📱 Processing UPI payment of ₹" + amount);
            // UPI specific logic (VPA validation, PSP lookup)
        }
        else if (method.equals("PAYTM_WALLET")) {
            System.out.println("🪙 Processing Paytm Wallet payment of ₹" + amount);
            // Wallet specific logic (Balance check, Cashback)
        }
        else if (method.equals("NET_BANKING")) {
            System.out.println("🏦 Processing Net Banking payment of ₹" + amount);
            // Net banking specific logic (Bank selection, IFSC)
        }
        else {
            throw new IllegalArgumentException("Unknown payment method");
        }
        // Naya method add karna hai? Aur if-else lagao! 😱
    }
}
```

**Dikkatein:**
1. **Violates Open/Closed Principle:** Naya payment method add karne ke liye existing class modify karni padegi.
2. **Single Responsibility Violation:** Ek class mein saare algorithms stuffed hain.
3. **Testing Tough:** Har algorithm ko alag test karna mushkil.
4. **Runtime Swap Impossible:** Agar user checkout ke time payment method change kare to?

---

### Part 2: Strategy Pattern Solution (Good Example)

Hum **PaymentStrategy** interface banayenge. Har payment method apni alag class hogi.

#### Step 1: Strategy Interface

```java
// 1. Strategy Interface: Sabhi payment methods isko implement karenge
interface PaymentStrategy {
    void pay(double amount);
    String getMethodName();
    boolean validate();
}
```

#### Step 2: Concrete Strategies (Different Algorithms)

```java
// 2. Concrete Strategy 1: Credit Card Payment
class CreditCardStrategy implements PaymentStrategy {
    private String cardNumber;
    private String expiry;
    private String cvv;
    private String name;
    
    public CreditCardStrategy(String cardNumber, String expiry, String cvv, String name) {
        this.cardNumber = cardNumber;
        this.expiry = expiry;
        this.cvv = cvv;
        this.name = name;
    }
    
    @Override
    public boolean validate() {
        // Actual validation logic (Luhn algorithm etc.)
        return cardNumber.length() == 16 && cvv.length() == 3;
    }
    
    @Override
    public void pay(double amount) {
        if (!validate()) {
            System.out.println("❌ Credit Card validation failed!");
            return;
        }
        System.out.println("💳 [CREDIT CARD] Processing payment of ₹" + amount);
        System.out.println("   Card: **** **** **** " + cardNumber.substring(12));
        System.out.println("   Name: " + name);
        System.out.println("   ✅ Payment Successful!\n");
    }
    
    @Override
    public String getMethodName() {
        return "Credit Card";
    }
}

// Concrete Strategy 2: UPI Payment
class UPIStrategy implements PaymentStrategy {
    private String vpa;  // Virtual Payment Address (e.g., user@okhdfcbank)
    private String upiPin;
    
    public UPIStrategy(String vpa, String upiPin) {
        this.vpa = vpa;
        this.upiPin = upiPin;
    }
    
    @Override
    public boolean validate() {
        return vpa != null && vpa.contains("@") && upiPin != null;
    }
    
    @Override
    public void pay(double amount) {
        if (!validate()) {
            System.out.println("❌ UPI validation failed!");
            return;
        }
        System.out.println("📱 [UPI] Processing payment of ₹" + amount);
        System.out.println("   VPA: " + vpa);
        System.out.println("   ✅ Payment Successful!\n");
    }
    
    @Override
    public String getMethodName() {
        return "UPI";
    }
}

// Concrete Strategy 3: Wallet Payment
class WalletStrategy implements PaymentStrategy {
    private String walletId;
    private String mobileNumber;
    private double walletBalance;
    
    public WalletStrategy(String walletId, String mobileNumber, double balance) {
        this.walletId = walletId;
        this.mobileNumber = mobileNumber;
        this.walletBalance = balance;
    }
    
    @Override
    public boolean validate() {
        return walletBalance >= 0;  // Minimum balance check
    }
    
    @Override
    public void pay(double amount) {
        if (walletBalance < amount) {
            System.out.println("❌ Insufficient wallet balance! Available: ₹" + walletBalance);
            return;
        }
        walletBalance -= amount;
        System.out.println("🪙 [WALLET] Processing payment of ₹" + amount);
        System.out.println("   Wallet ID: " + walletId);
        System.out.println("   Mobile: " + mobileNumber);
        System.out.println("   Remaining Balance: ₹" + walletBalance);
        System.out.println("   ✅ Payment Successful!\n");
    }
    
    @Override
    public String getMethodName() {
        return "Wallet";
    }
}

// Concrete Strategy 4: Net Banking
class NetBankingStrategy implements PaymentStrategy {
    private String bankCode;
    private String username;
    private String password;
    
    public NetBankingStrategy(String bankCode, String username, String password) {
        this.bankCode = bankCode;
        this.username = username;
        this.password = password;
    }
    
    @Override
    public boolean validate() {
        return bankCode != null && username != null;
    }
    
    @Override
    public void pay(double amount) {
        if (!validate()) {
            System.out.println("❌ Net Banking validation failed!");
            return;
        }
        System.out.println("🏦 [NET BANKING] Processing payment of ₹" + amount);
        System.out.println("   Bank: " + getBankName(bankCode));
        System.out.println("   User: " + username);
        System.out.println("   ✅ Payment Successful!\n");
    }
    
    private String getBankName(String code) {
        return switch (code) {
            case "SBI" -> "State Bank of India";
            case "HDFC" -> "HDFC Bank";
            case "ICICI" -> "ICICI Bank";
            default -> code;
        };
    }
    
    @Override
    public String getMethodName() {
        return "Net Banking";
    }
}

// Concrete Strategy 5: Cash on Delivery (No payment now)
class CODStrategy implements PaymentStrategy {
    private String address;
    
    public CODStrategy(String address) {
        this.address = address;
    }
    
    @Override
    public boolean validate() {
        return address != null && !address.isEmpty();
    }
    
    @Override
    public void pay(double amount) {
        System.out.println("📦 [CASH ON DELIVERY] Order confirmed!");
        System.out.println("   Amount to be paid: ₹" + amount);
        System.out.println("   Delivery Address: " + address);
        System.out.println("   ✅ Order Placed! Pay when you receive.\n");
    }
    
    @Override
    public String getMethodName() {
        return "Cash on Delivery";
    }
}
```

#### Step 3: Context Class (Uses Strategy)

```java
// 3. Context: Shopping Cart that uses a payment strategy
class ShoppingCart {
    private List<Item> items;
    private PaymentStrategy paymentStrategy;
    
    public ShoppingCart() {
        this.items = new ArrayList<>();
    }
    
    public void addItem(Item item) {
        items.add(item);
        System.out.println("🛒 Added: " + item);
    }
    
    public void removeItem(Item item) {
        items.remove(item);
    }
    
    public double calculateTotal() {
        return items.stream().mapToDouble(Item::getPrice).sum();
    }
    
    // Strategy setter - Runtime pe change kar sakte hain!
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
        System.out.println("💳 Payment method set to: " + strategy.getMethodName());
    }
    
    public void checkout() {
        if (paymentStrategy == null) {
            System.out.println("❌ Please select a payment method first!");
            return;
        }
        
        double total = calculateTotal();
        if (total == 0) {
            System.out.println("❌ Cart is empty!");
            return;
        }
        
        System.out.println("\n🧾 Checkout Summary:");
        System.out.println("   Items: " + items.size());
        System.out.println("   Total: ₹" + total);
        
        paymentStrategy.pay(total);
    }
    
    // Item class (inner for simplicity)
    static class Item {
        private String name;
        private double price;
        
        public Item(String name, double price) {
            this.name = name;
            this.price = price;
        }
        
        public double getPrice() { return price; }
        
        @Override
        public String toString() {
            return name + " (₹" + price + ")";
        }
    }
}
```

#### Step 4: Client Code (Maza aa gaya!)

```java
import java.util.*;

public class ECommerceApp {
    public static void main(String[] args) {
        System.out.println("=== 🛍️ E-Commerce Checkout - Strategy Pattern ===\n");
        
        // Create shopping cart
        ShoppingCart cart = new ShoppingCart();
        cart.addItem(new ShoppingCart.Item("Wireless Mouse", 599));
        cart.addItem(new ShoppingCart.Item("Mechanical Keyboard", 2999));
        cart.addItem(new ShoppingCart.Item("USB-C Cable", 299));
        
        // Scenario 1: Pay with Credit Card
        System.out.println("--- Scenario 1: Credit Card Payment ---");
        PaymentStrategy creditCard = new CreditCardStrategy(
            "4532123456789012", "12/28", "123", "Rahul Kumar"
        );
        cart.setPaymentStrategy(creditCard);
        cart.checkout();
        
        // Scenario 2: User changes mind - wants to pay via UPI (Runtime Swap!)
        System.out.println("--- Scenario 2: Switched to UPI ---");
        ShoppingCart cart2 = new ShoppingCart();
        cart2.addItem(new ShoppingCart.Item("Samsung SSD 1TB", 8999));
        
        PaymentStrategy upi = new UPIStrategy("rahul@okhdfcbank", "123456");
        cart2.setPaymentStrategy(upi);
        cart2.checkout();
        
        // Scenario 3: Pay with Wallet (with balance check)
        System.out.println("--- Scenario 3: Wallet Payment (Sufficient Balance) ---");
        ShoppingCart cart3 = new ShoppingCart();
        cart3.addItem(new ShoppingCart.Item("Coffee Mug", 349));
        
        PaymentStrategy wallet = new WalletStrategy("WALLET123", "9876543210", 500);
        cart3.setPaymentStrategy(wallet);
        cart3.checkout();
        
        // Scenario 4: Wallet with insufficient balance
        System.out.println("--- Scenario 4: Wallet Payment (Insufficient Balance) ---");
        ShoppingCart cart4 = new ShoppingCart();
        cart4.addItem(new ShoppingCart.Item("Headphones", 1999));
        
        PaymentStrategy lowWallet = new WalletStrategy("WALLET456", "9876543211", 1000);
        cart4.setPaymentStrategy(lowWallet);
        cart4.checkout();
        
        // Scenario 5: Cash on Delivery
        System.out.println("--- Scenario 5: Cash on Delivery ---");
        ShoppingCart cart5 = new ShoppingCart();
        cart5.addItem(new ShoppingCart.Item("T-Shirt", 799));
        
        PaymentStrategy cod = new CODStrategy("123 Main Street, Mumbai - 400001");
        cart5.setPaymentStrategy(cod);
        cart5.checkout();
    }
}
```

---

### Part 3: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: Navigation App (Different Routing Algorithms)

**Scenario:** Google Maps jaisa app - Car route, Walking route, Bicycle route.

```java
// File: NavigationStrategy.java

// Strategy Interface
interface RouteStrategy {
    void calculateRoute(String source, String destination);
    String getStrategyName();
    int getEstimatedTime();
}

// Concrete Strategy 1: Car Route (Fastest)
class CarRouteStrategy implements RouteStrategy {
    @Override
    public void calculateRoute(String source, String destination) {
        System.out.println("🚗 [CAR ROUTE] Calculating fastest route...");
        System.out.println("   Using highways and main roads");
        System.out.println("   Avoiding traffic jams");
        System.out.println("   Route: " + source + " → Highway → " + destination);
    }
    
    @Override
    public String getStrategyName() {
        return "Car (Fastest)";
    }
    
    @Override
    public int getEstimatedTime() {
        return 25; // minutes
    }
}

// Concrete Strategy 2: Walking Route (Shortest)
class WalkingRouteStrategy implements RouteStrategy {
    @Override
    public void calculateRoute(String source, String destination) {
        System.out.println("🚶 [WALKING ROUTE] Calculating shortest walking path...");
        System.out.println("   Using footpaths and pedestrian crossings");
        System.out.println("   Avoiding stairs when possible");
        System.out.println("   Route: " + source + " → Park → " + destination);
    }
    
    @Override
    public String getStrategyName() {
        return "Walking (Shortest)";
    }
    
    @Override
    public int getEstimatedTime() {
        return 45;
    }
}

// Concrete Strategy 3: Bicycle Route (Bike-friendly)
class BicycleRouteStrategy implements RouteStrategy {
    @Override
    public void calculateRoute(String source, String destination) {
        System.out.println("🚲 [BICYCLE ROUTE] Finding bike-friendly path...");
        System.out.println("   Using cycle tracks and less-traffic roads");
        System.out.println("   Avoiding steep inclines");
        System.out.println("   Route: " + source + " → Cycle Track → " + destination);
    }
    
    @Override
    public String getStrategyName() {
        return "Bicycle (Bike-friendly)";
    }
    
    @Override
    public int getEstimatedTime() {
        return 35;
    }
}

// Concrete Strategy 4: Public Transport
class PublicTransportStrategy implements RouteStrategy {
    @Override
    public void calculateRoute(String source, String destination) {
        System.out.println("🚌 [PUBLIC TRANSPORT] Finding best combination...");
        System.out.println("   Bus 123 → Metro Blue Line → Walk 5 min");
        System.out.println("   Cost: ₹45");
        System.out.println("   Route: " + source + " → Bus Stop → Metro → " + destination);
    }
    
    @Override
    public String getStrategyName() {
        return "Public Transport (Economical)";
    }
    
    @Override
    public int getEstimatedTime() {
        return 50;
    }
}

// Context
class NavigationApp {
    private RouteStrategy strategy;
    
    public void setRouteStrategy(RouteStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void navigate(String source, String destination) {
        if (strategy == null) {
            System.out.println("Please select a travel mode!");
            return;
        }
        
        System.out.println("\n🗺️ Navigation Mode: " + strategy.getStrategyName());
        strategy.calculateRoute(source, destination);
        System.out.println("⏱️ Estimated Time: " + strategy.getEstimatedTime() + " minutes\n");
    }
}

public class MapsApp {
    public static void main(String[] args) {
        NavigationApp app = new NavigationApp();
        String source = "Home";
        String dest = "Office";
        
        System.out.println("=== 🗺️ Google Maps - Strategy Pattern ===\n");
        
        // User selects Car
        app.setRouteStrategy(new CarRouteStrategy());
        app.navigate(source, dest);
        
        // User changes to Walking
        app.setRouteStrategy(new WalkingRouteStrategy());
        app.navigate(source, dest);
        
        // User checks Public Transport
        app.setRouteStrategy(new PublicTransportStrategy());
        app.navigate(source, dest);
        
        // User tries Bicycle
        app.setRouteStrategy(new BicycleRouteStrategy());
        app.navigate(source, dest);
    }
}
```

---

#### Example 2: Compression Utility (ZIP, RAR, GZIP)

**Scenario:** File compression tool jahan user different algorithms choose kar sake.

```java
// File: CompressionStrategy.java
import java.util.HashMap;
import java.util.Map;

// Strategy Interface
interface CompressionStrategy {
    byte[] compress(byte[] data);
    byte[] decompress(byte[] data);
    String getAlgorithmName();
    double getCompressionRatio();
}

// Concrete Strategy 1: ZIP Compression
class ZipCompression implements CompressionStrategy {
    @Override
    public byte[] compress(byte[] data) {
        System.out.println("🗜️ [ZIP] Compressing " + data.length + " bytes...");
        // Simulate compression (actual implementation would use java.util.zip)
        int compressedSize = (int)(data.length * 0.6); // 40% reduction
        return new byte[compressedSize];
    }
    
    @Override
    public byte[] decompress(byte[] data) {
        System.out.println("📂 [ZIP] Decompressing...");
        return new byte[data.length * 2];
    }
    
    @Override
    public String getAlgorithmName() {
        return "ZIP (Deflate)";
    }
    
    @Override
    public double getCompressionRatio() {
        return 0.6; // 60% of original size
    }
}

// Concrete Strategy 2: GZIP Compression
class GzipCompression implements CompressionStrategy {
    @Override
    public byte[] compress(byte[] data) {
        System.out.println("🗜️ [GZIP] Compressing " + data.length + " bytes...");
        int compressedSize = (int)(data.length * 0.55);
        return new byte[compressedSize];
    }
    
    @Override
    public byte[] decompress(byte[] data) {
        System.out.println("📂 [GZIP] Decompressing...");
        return new byte[data.length * 2];
    }
    
    @Override
    public String getAlgorithmName() {
        return "GZIP (LZ77)";
    }
    
    @Override
    public double getCompressionRatio() {
        return 0.55;
    }
}

// Concrete Strategy 3: No Compression (Store as-is)
class NoCompression implements CompressionStrategy {
    @Override
    public byte[] compress(byte[] data) {
        System.out.println("📄 [NONE] Storing without compression...");
        return data;
    }
    
    @Override
    public byte[] decompress(byte[] data) {
        return data;
    }
    
    @Override
    public String getAlgorithmName() {
        return "None (Store as-is)";
    }
    
    @Override
    public double getCompressionRatio() {
        return 1.0;
    }
}

// Context
class FileCompressor {
    private CompressionStrategy strategy;
    private Map<String, byte[]> storage = new HashMap<>();
    
    public void setStrategy(CompressionStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void saveFile(String fileName, byte[] data) {
        System.out.println("\n💾 Saving file: " + fileName);
        System.out.println("   Algorithm: " + strategy.getAlgorithmName());
        
        byte[] compressed = strategy.compress(data);
        storage.put(fileName, compressed);
        
        double ratio = strategy.getCompressionRatio();
        System.out.println("   Original: " + data.length + " bytes");
        System.out.println("   Compressed: " + compressed.length + " bytes");
        System.out.println("   Space saved: " + String.format("%.1f%%", (1 - ratio) * 100));
        System.out.println("   ✅ File saved!\n");
    }
    
    public byte[] loadFile(String fileName) {
        byte[] compressed = storage.get(fileName);
        if (compressed == null) {
            System.out.println("❌ File not found: " + fileName);
            return null;
        }
        
        System.out.println("\n📂 Loading file: " + fileName);
        return strategy.decompress(compressed);
    }
}

public class BackupUtility {
    public static void main(String[] args) {
        System.out.println("=== 💿 File Compression Utility ===\n");
        
        FileCompressor compressor = new FileCompressor();
        
        // Sample data
        String textData = "This is a sample text that will be compressed. " +
                         "Repeated text repeated text repeated text.";
        byte[] data = textData.getBytes();
        
        // Strategy 1: ZIP
        compressor.setStrategy(new ZipCompression());
        compressor.saveFile("document.txt", data);
        
        // Strategy 2: GZIP (Better compression)
        compressor.setStrategy(new GzipCompression());
        compressor.saveFile("document.txt.gz", data);
        
        // Strategy 3: No Compression (For already compressed files)
        compressor.setStrategy(new NoCompression());
        compressor.saveFile("image.jpg", new byte[1000]);
    }
}
```

---

#### Example 3: Sorting Strategy (Different Algorithms for Different Data)

**Scenario:** Data set ke hisaab se sorting algorithm choose karna.

```java
// File: SortingStrategy.java
import java.util.Arrays;
import java.util.Random;

// Strategy Interface
interface SortingStrategy {
    void sort(int[] array);
    String getAlgorithmName();
    long getTimeComplexity();
}

// Concrete Strategy 1: Quick Sort (Best for large random data)
class QuickSortStrategy implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("⚡ [QUICK SORT] Sorting " + array.length + " elements...");
        quickSort(array, 0, array.length - 1);
    }
    
    private void quickSort(int[] arr, int low, int high) {
        if (low < high) {
            int pi = partition(arr, low, high);
            quickSort(arr, low, pi - 1);
            quickSort(arr, pi + 1, high);
        }
    }
    
    private int partition(int[] arr, int low, int high) {
        int pivot = arr[high];
        int i = low - 1;
        for (int j = low; j < high; j++) {
            if (arr[j] <= pivot) {
                i++;
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
            }
        }
        int temp = arr[i + 1];
        arr[i + 1] = arr[high];
        arr[high] = temp;
        return i + 1;
    }
    
    @Override
    public String getAlgorithmName() {
        return "Quick Sort";
    }
    
    @Override
    public long getTimeComplexity() {
        return 0; // O(n log n) average
    }
}

// Concrete Strategy 2: Bubble Sort (Best for nearly sorted data)
class BubbleSortStrategy implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("🫧 [BUBBLE SORT] Sorting " + array.length + " elements...");
        int n = array.length;
        for (int i = 0; i < n - 1; i++) {
            boolean swapped = false;
            for (int j = 0; j < n - i - 1; j++) {
                if (array[j] > array[j + 1]) {
                    int temp = array[j];
                    array[j] = array[j + 1];
                    array[j + 1] = temp;
                    swapped = true;
                }
            }
            if (!swapped) break; // Already sorted
        }
    }
    
    @Override
    public String getAlgorithmName() {
        return "Bubble Sort";
    }
    
    @Override
    public long getTimeComplexity() {
        return 0;
    }
}

// Concrete Strategy 3: Insertion Sort (Best for small arrays)
class InsertionSortStrategy implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("📌 [INSERTION SORT] Sorting " + array.length + " elements...");
        for (int i = 1; i < array.length; i++) {
            int key = array[i];
            int j = i - 1;
            while (j >= 0 && array[j] > key) {
                array[j + 1] = array[j];
                j--;
            }
            array[j + 1] = key;
        }
    }
    
    @Override
    public String getAlgorithmName() {
        return "Insertion Sort";
    }
    
    @Override
    public long getTimeComplexity() {
        return 0;
    }
}

// Concrete Strategy 4: Java's Built-in Dual-Pivot QuickSort
class JavaBuiltinSortStrategy implements SortingStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("☕ [JAVA ARRAYS.SORT] Using Dual-Pivot Quicksort...");
        Arrays.sort(array);
    }
    
    @Override
    public String getAlgorithmName() {
        return "Java Arrays.sort()";
    }
    
    @Override
    public long getTimeComplexity() {
        return 0;
    }
}

// Context with Auto-Selection Logic
class SmartSorter {
    private SortingStrategy strategy;
    
    // Auto-select strategy based on data characteristics
    public void autoSelectStrategy(int[] array) {
        if (array.length < 50) {
            strategy = new InsertionSortStrategy();
            System.out.println("🤖 Auto-selected: Insertion Sort (Small array)");
        } else if (isNearlySorted(array)) {
            strategy = new BubbleSortStrategy();
            System.out.println("🤖 Auto-selected: Bubble Sort (Nearly sorted)");
        } else {
            strategy = new JavaBuiltinSortStrategy();
            System.out.println("🤖 Auto-selected: Java Built-in Sort (Large random)");
        }
    }
    
    private boolean isNearlySorted(int[] array) {
        int inversions = 0;
        for (int i = 0; i < array.length - 1; i++) {
            if (array[i] > array[i + 1]) inversions++;
        }
        return inversions < array.length * 0.1; // Less than 10% out of order
    }
    
    public void setStrategy(SortingStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void sort(int[] array) {
        System.out.println("\n📊 Using: " + strategy.getAlgorithmName());
        long start = System.nanoTime();
        strategy.sort(array);
        long end = System.nanoTime();
        System.out.println("   ⏱️ Time: " + (end - start) / 1000 + " μs");
    }
}

public class DataSortingApp {
    public static void main(String[] args) {
        System.out.println("=== 📊 Smart Sorting - Strategy Pattern ===\n");
        
        SmartSorter sorter = new SmartSorter();
        Random rand = new Random();
        
        // Scenario 1: Small array
        int[] smallArray = {5, 2, 8, 1, 9, 3, 7, 4, 6};
        System.out.println("Original: " + Arrays.toString(smallArray));
        sorter.autoSelectStrategy(smallArray);
        sorter.sort(smallArray);
        System.out.println("Sorted: " + Arrays.toString(smallArray));
        
        // Scenario 2: Large random array
        int[] largeArray = new int[1000];
        for (int i = 0; i < largeArray.length; i++) {
            largeArray[i] = rand.nextInt(10000);
        }
        System.out.println("\n--- Large Array (1000 elements) ---");
        sorter.autoSelectStrategy(largeArray);
        sorter.sort(largeArray);
        
        // Scenario 3: Nearly sorted array
        int[] nearlySorted = {1, 2, 3, 5, 4, 6, 7, 8, 9};
        System.out.println("\n--- Nearly Sorted Array ---");
        System.out.println("Original: " + Arrays.toString(nearlySorted));
        sorter.autoSelectStrategy(nearlySorted);
        sorter.sort(nearlySorted);
        System.out.println("Sorted: " + Arrays.toString(nearlySorted));
        
        // Scenario 4: Manual selection
        System.out.println("\n--- Manual Selection ---");
        sorter.setStrategy(new QuickSortStrategy());
        int[] manualArray = {64, 34, 25, 12, 22, 11, 90};
        System.out.println("Original: " + Arrays.toString(manualArray));
        sorter.sort(manualArray);
        System.out.println("Sorted: " + Arrays.toString(manualArray));
    }
}
```

---

#### Example 4: Discount Strategy (E-Commerce Promotions)

**Scenario:** Different discount types - Percentage, Fixed Amount, Buy One Get One.

```java
// File: DiscountStrategy.java

// Strategy Interface
interface DiscountStrategy {
    double applyDiscount(double originalPrice);
    String getDiscountDescription();
    double getDiscountAmount(double price);
}

// Concrete Strategy 1: Percentage Discount
class PercentageDiscount implements DiscountStrategy {
    private double percentage;
    
    public PercentageDiscount(double percentage) {
        this.percentage = percentage;
    }
    
    @Override
    public double applyDiscount(double originalPrice) {
        return originalPrice * (1 - percentage / 100);
    }
    
    @Override
    public String getDiscountDescription() {
        return percentage + "% OFF";
    }
    
    @Override
    public double getDiscountAmount(double price) {
        return price * percentage / 100;
    }
}

// Concrete Strategy 2: Fixed Amount Discount
class FixedAmountDiscount implements DiscountStrategy {
    private double discountAmount;
    
    public FixedAmountDiscount(double amount) {
        this.discountAmount = amount;
    }
    
    @Override
    public double applyDiscount(double originalPrice) {
        return Math.max(0, originalPrice - discountAmount);
    }
    
    @Override
    public String getDiscountDescription() {
        return "₹" + discountAmount + " OFF";
    }
    
    @Override
    public double getDiscountAmount(double price) {
        return Math.min(discountAmount, price);
    }
}

// Concrete Strategy 3: No Discount
class NoDiscount implements DiscountStrategy {
    @Override
    public double applyDiscount(double originalPrice) {
        return originalPrice;
    }
    
    @Override
    public String getDiscountDescription() {
        return "No Discount";
    }
    
    @Override
    public double getDiscountAmount(double price) {
        return 0;
    }
}

// Concrete Strategy 4: Buy One Get One Free (BOGO)
class BOGODiscount implements DiscountStrategy {
    private int buyQuantity;
    private int freeQuantity;
    
    public BOGODiscount(int buy, int free) {
        this.buyQuantity = buy;
        this.freeQuantity = free;
    }
    
    @Override
    public double applyDiscount(double originalPrice) {
        // For single item, effectively 50% off for BOGO
        double effectiveMultiplier = (double) buyQuantity / (buyQuantity + freeQuantity);
        return originalPrice * effectiveMultiplier;
    }
    
    @Override
    public String getDiscountDescription() {
        return "Buy " + buyQuantity + " Get " + freeQuantity + " FREE";
    }
    
    @Override
    public double getDiscountAmount(double price) {
        return price - applyDiscount(price);
    }
}

// Concrete Strategy 5: Seasonal Discount (Based on month)
class SeasonalDiscount implements DiscountStrategy {
    private int month; // 1-12
    
    public SeasonalDiscount(int month) {
        this.month = month;
    }
    
    @Override
    public double applyDiscount(double originalPrice) {
        double discountPercent = switch (month) {
            case 12 -> 30; // Christmas
            case 11 -> 25; // Black Friday
            case 1  -> 20;  // New Year
            case 7, 8 -> 15; // Monsoon
            default -> 10;
        };
        return originalPrice * (1 - discountPercent / 100);
    }
    
    @Override
    public String getDiscountDescription() {
        String monthName = switch (month) {
            case 12 -> "Christmas";
            case 11 -> "Black Friday";
            case 1 -> "New Year";
            case 7, 8 -> "Monsoon";
            default -> "Seasonal";
        };
        return monthName + " Sale";
    }
    
    @Override
    public double getDiscountAmount(double price) {
        return price - applyDiscount(price);
    }
}

// Product class
class Product {
    private String name;
    private double price;
    
    public Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    public String getName() { return name; }
    public double getPrice() { return price; }
}

// Context
class ShoppingCartWithDiscount {
    private List<Product> items = new ArrayList<>();
    private DiscountStrategy discountStrategy = new NoDiscount();
    
    public void addItem(Product product) {
        items.add(product);
    }
    
    public void setDiscountStrategy(DiscountStrategy strategy) {
        this.discountStrategy = strategy;
    }
    
    public double calculateSubtotal() {
        return items.stream().mapToDouble(Product::getPrice).sum();
    }
    
    public void printReceipt() {
        double subtotal = calculateSubtotal();
        double discount = discountStrategy.getDiscountAmount(subtotal);
        double total = discountStrategy.applyDiscount(subtotal);
        
        System.out.println("\n🧾 ===== RECEIPT =====");
        System.out.println("Items:");
        items.forEach(item -> System.out.printf("  %-20s ₹%8.2f\n", item.getName(), item.getPrice()));
        System.out.println("----------------------");
        System.out.printf("Subtotal:            ₹%8.2f\n", subtotal);
        System.out.printf("Discount (%s):   -₹%8.2f\n", 
                         discountStrategy.getDiscountDescription(), discount);
        System.out.println("----------------------");
        System.out.printf("TOTAL:               ₹%8.2f\n", total);
        System.out.println("======================\n");
    }
}

public class DiscountEngine {
    public static void main(String[] args) {
        System.out.println("=== 🏷️ Discount Engine - Strategy Pattern ===\n");
        
        ShoppingCartWithDiscount cart = new ShoppingCartWithDiscount();
        cart.addItem(new Product("Laptop", 55000));
        cart.addItem(new Product("Mouse", 999));
        cart.addItem(new Product("Keyboard", 2499));
        
        // Strategy 1: Percentage Discount
        System.out.println("--- 20% Discount Coupon Applied ---");
        cart.setDiscountStrategy(new PercentageDiscount(20));
        cart.printReceipt();
        
        // Strategy 2: Fixed Amount Discount
        ShoppingCartWithDiscount cart2 = new ShoppingCartWithDiscount();
        cart2.addItem(new Product("Headphones", 1999));
        System.out.println("--- ₹500 Off Coupon Applied ---");
        cart2.setDiscountStrategy(new FixedAmountDiscount(500));
        cart2.printReceipt();
        
        // Strategy 3: BOGO (Buy One Get One)
        ShoppingCartWithDiscount cart3 = new ShoppingCartWithDiscount();
        cart3.addItem(new Product("T-Shirt", 799));
        cart3.addItem(new Product("T-Shirt", 799)); // Free one
        System.out.println("--- Buy One Get One Free ---");
        cart3.setDiscountStrategy(new BOGODiscount(1, 1));
        cart3.printReceipt();
        
        // Strategy 4: Seasonal Discount (December)
        System.out.println("--- Christmas Sale (December) ---");
        cart.setDiscountStrategy(new SeasonalDiscount(12));
        cart.printReceipt();
        
        // Strategy 5: No Discount
        System.out.println("--- Regular Price (No Discount) ---");
        cart.setDiscountStrategy(new NoDiscount());
        cart.printReceipt();
    }
}
```

---

#### Example 5: Logging Strategy (Different Output Destinations)

**Scenario:** Logs Console pe bhi, File mein bhi, Remote Server pe bhi bhejne hain.

```java
// File: LoggingStrategy.java
import java.io.FileWriter;
import java.io.IOException;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

// Strategy Interface
interface LoggingStrategy {
    void log(String level, String message);
    void setLogLevel(String minLevel);
}

// Concrete Strategy 1: Console Logging
class ConsoleLoggingStrategy implements LoggingStrategy {
    private String minLevel = "INFO";
    private static final DateTimeFormatter dtf = 
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    
    @Override
    public void log(String level, String message) {
        if (shouldLog(level)) {
            String timestamp = LocalDateTime.now().format(dtf);
            String color = getColorForLevel(level);
            String reset = "\u001B[0m";
            
            System.out.println(color + "[" + timestamp + "] " + 
                             String.format("%-7s", level) + " - " + message + reset);
        }
    }
    
    private String getColorForLevel(String level) {
        return switch (level) {
            case "ERROR" -> "\u001B[31m"; // Red
            case "WARN" -> "\u001B[33m";  // Yellow
            case "INFO" -> "\u001B[32m";  // Green
            case "DEBUG" -> "\u001B[36m"; // Cyan
            default -> "\u001B[0m";       // Reset
        };
    }
    
    private boolean shouldLog(String level) {
        int currentPriority = getPriority(level);
        int minPriority = getPriority(minLevel);
        return currentPriority >= minPriority;
    }
    
    private int getPriority(String level) {
        return switch (level) {
            case "ERROR" -> 4;
            case "WARN" -> 3;
            case "INFO" -> 2;
            case "DEBUG" -> 1;
            default -> 0;
        };
    }
    
    @Override
    public void setLogLevel(String minLevel) {
        this.minLevel = minLevel;
    }
}

// Concrete Strategy 2: File Logging
class FileLoggingStrategy implements LoggingStrategy {
    private String filePath;
    private String minLevel = "INFO";
    private static final DateTimeFormatter dtf = 
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    
    public FileLoggingStrategy(String filePath) {
        this.filePath = filePath;
    }
    
    @Override
    public void log(String level, String message) {
        if (shouldLog(level)) {
            String timestamp = LocalDateTime.now().format(dtf);
            String logEntry = "[" + timestamp + "] " + 
                            String.format("%-7s", level) + " - " + message + "\n";
            
            try (FileWriter fw = new FileWriter(filePath, true)) {
                fw.write(logEntry);
            } catch (IOException e) {
                System.err.println("Failed to write to log file: " + e.getMessage());
            }
        }
    }
    
    private boolean shouldLog(String level) {
        int currentPriority = getPriority(level);
        int minPriority = getPriority(minLevel);
        return currentPriority >= minPriority;
    }
    
    private int getPriority(String level) {
        return switch (level) {
            case "ERROR" -> 4;
            case "WARN" -> 3;
            case "INFO" -> 2;
            case "DEBUG" -> 1;
            default -> 0;
        };
    }
    
    @Override
    public void setLogLevel(String minLevel) {
        this.minLevel = minLevel;
    }
    
    public String getFilePath() {
        return filePath;
    }
}

// Concrete Strategy 3: Remote Server Logging (Simulated)
class RemoteLoggingStrategy implements LoggingStrategy {
    private String serverUrl;
    private String apiKey;
    private String minLevel = "ERROR"; // Only errors to remote
    
    public RemoteLoggingStrategy(String serverUrl, String apiKey) {
        this.serverUrl = serverUrl;
        this.apiKey = apiKey;
    }
    
    @Override
    public void log(String level, String message) {
        if (shouldLog(level)) {
            System.out.println("🌐 [REMOTE] Sending log to " + serverUrl);
            System.out.println("   Level: " + level + ", Message: " + message);
            // Actual HTTP call would go here
        }
    }
    
    private boolean shouldLog(String level) {
        int currentPriority = getPriority(level);
        int minPriority = getPriority(minLevel);
        return currentPriority >= minPriority;
    }
    
    private int getPriority(String level) {
        return switch (level) {
            case "ERROR" -> 4;
            case "WARN" -> 3;
            case "INFO" -> 2;
            case "DEBUG" -> 1;
            default -> 0;
        };
    }
    
    @Override
    public void setLogLevel(String minLevel) {
        this.minLevel = minLevel;
    }
}

// Composite Strategy: Log to multiple destinations
class CompositeLoggingStrategy implements LoggingStrategy {
    private List<LoggingStrategy> strategies = new ArrayList<>();
    
    public void addStrategy(LoggingStrategy strategy) {
        strategies.add(strategy);
    }
    
    @Override
    public void log(String level, String message) {
        strategies.forEach(s -> s.log(level, message));
    }
    
    @Override
    public void setLogLevel(String minLevel) {
        strategies.forEach(s -> s.setLogLevel(minLevel));
    }
}

// Logger Context
class Logger {
    private LoggingStrategy strategy;
    private static Logger instance;
    
    private Logger() {
        // Default to console
        this.strategy = new ConsoleLoggingStrategy();
    }
    
    public static Logger getInstance() {
        if (instance == null) {
            instance = new Logger();
        }
        return instance;
    }
    
    public void setStrategy(LoggingStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void debug(String msg) { strategy.log("DEBUG", msg); }
    public void info(String msg) { strategy.log("INFO", msg); }
    public void warn(String msg) { strategy.log("WARN", msg); }
    public void error(String msg) { strategy.log("ERROR", msg); }
    
    public void setLogLevel(String level) {
        strategy.setLogLevel(level);
    }
}

public class LoggingSystem {
    public static void main(String[] args) {
        System.out.println("=== 📋 Logging System - Strategy Pattern ===\n");
        
        Logger logger = Logger.getInstance();
        
        // Strategy 1: Console only (Development)
        System.out.println("--- Development Mode (Console) ---");
        logger.setStrategy(new ConsoleLoggingStrategy());
        logger.setLogLevel("DEBUG");
        logger.debug("User authentication started");
        logger.info("User logged in successfully");
        logger.warn("Password expiring in 5 days");
        logger.error("Failed to connect to database");
        
        // Strategy 2: File only (Production minimal)
        System.out.println("\n--- Production Mode (File) ---");
        FileLoggingStrategy fileStrategy = new FileLoggingStrategy("app.log");
        logger.setStrategy(fileStrategy);
        logger.setLogLevel("INFO");
        logger.debug("This won't be logged"); // Below INFO level
        logger.info("Application started");
        logger.warn("High memory usage detected");
        logger.error("Service unavailable");
        System.out.println("Logs written to: " + fileStrategy.getFilePath());
        
        // Strategy 3: Composite (Both Console AND File AND Remote)
        System.out.println("\n--- Full Logging (Composite) ---");
        CompositeLoggingStrategy composite = new CompositeLoggingStrategy();
        composite.addStrategy(new ConsoleLoggingStrategy());
        composite.addStrategy(new FileLoggingStrategy("app-full.log"));
        composite.addStrategy(new RemoteLoggingStrategy("https://logs.example.com", "api-key-123"));
        
        logger.setStrategy(composite);
        logger.setLogLevel("INFO");
        logger.info("User request processed");
        logger.error("Payment gateway timeout");
        
        // Strategy 4: Remote only (For critical errors)
        System.out.println("\n--- Remote Monitoring ---");
        logger.setStrategy(new RemoteLoggingStrategy("https://alerts.example.com", "alert-key"));
        logger.error("CRITICAL: Database connection pool exhausted!");
    }
}
```

---

### Part 6: Strategy Pattern - Summary & Interview Points

| Aspect | Details |
|:---|:---|
| **Problem Solved** | Multiple algorithms, runtime algorithm selection |
| **Use When** | Many related classes differ only in behavior |
| **Key Components** | Context, Strategy (Interface), ConcreteStrategies |
| **Real Examples** | `Collections.sort()` with `Comparator`, Java I/O Streams |

**Interview Question:** *What is Strategy Pattern and when to use it?*

**Answer:** *"Sir, Strategy pattern defines a family of algorithms, encapsulates each one, and makes them interchangeable. It lets the algorithm vary independently from clients that use it. Use it when you have multiple ways of doing something (payment methods, sorting algorithms, compression) and want to switch at runtime. Example: `Collections.sort(list, comparator)` where comparator is the strategy."*

**Interview Question:** *Difference between Strategy and State Pattern?*

| Strategy | State |
|:---|:---|
| Client chooses strategy | State changes automatically |
| One-time decision | Multiple transitions |
| Algorithms are independent | States know about each other |

**Interview Question:** *Difference between Strategy and Factory Pattern?*

| Strategy | Factory |
|:---|:---|
| Behavioral Pattern | Creational Pattern |
| Focus on **how** to do something | Focus on **what** to create |
| Algorithms encapsulated | Object creation encapsulated |

**Interview Question:** *Advantages of Strategy Pattern?*

1. **Open/Closed Principle:** New strategies add without modifying context.
2. **Eliminates Conditional Statements:** No if-else ladders.
3. **Testability:** Each strategy can be tested independently.
4. **Runtime Flexibility:** Change behavior at runtime.

**Real Java Example:**
```java
// Comparator is a Strategy!
List<String> names = Arrays.asList("Rahul", "Priya", "Amit");

// Strategy 1: Natural order
Collections.sort(names);

// Strategy 2: Custom (by length)
Collections.sort(names, (a, b) -> a.length() - b.length());

// Strategy 3: Reverse order
Collections.sort(names, Collections.reverseOrder());
```

Ab aapke paas **Strategy Design Pattern** ke **5 Complete Java Examples** hain. Har example real-world scenario cover karta hai. Confident feel kar rahe ho?
