
### Part 1: Problem Statement (Multiple Instances)

Sochiye aapke paas **Database Connection Pool** hai. Agar aap baar baar naya pool banaoge to bahut saare connections open ho jayenge aur database crash ho sakta hai.

#### ❌ BAD PRACTICE: Multiple Instances

```java
// Bina Singleton ke - Multiple objects ban rahe hain
class DatabaseConnection {
    private String connectionString;
    
    public DatabaseConnection() {
        System.out.println("🔗 Creating NEW database connection...");
        this.connectionString = "jdbc:mysql://localhost:3306/mydb";
        // Expensive operation: Actual TCP connection establish ho rahi hai
    }
    
    public void executeQuery(String query) {
        System.out.println("Executing: " + query);
    }
}

public class BadApp {
    public static void main(String[] args) {
        // Har baar naya connection ban raha hai - BAHUT EXPENSIVE!
        DatabaseConnection conn1 = new DatabaseConnection();
        DatabaseConnection conn2 = new DatabaseConnection();
        DatabaseConnection conn3 = new DatabaseConnection();
        
        // 3 alag connections - Database pe load, Memory waste
        System.out.println(conn1 == conn2); // false (Different objects)
    }
}
```

**Dikkatein:**
1. **Resource Waste:** Baar baar expensive object create ho raha hai.
2. **Inconsistent State:** Multiple objects mein alag-alag state ho sakti hai.
3. **No Global Control:** Koi guarantee nahi ki kitne objects banenge.

---

### Part 2: Singleton Pattern Solutions (Multiple Implementations)

Singleton implement karne ke **5 different tarike** hain. Har ek ki apni strength aur weakness hai.

#### Method 1: Eager Initialization (Simple but Wastes Memory)

```java
// 1. EAGER INITIALIZATION - Class load hote hi object ban jata hai
class EagerSingleton {
    // Static variable - Class load hote hi initialize
    private static final EagerSingleton instance = new EagerSingleton();
    
    // Private constructor - Bahar se koi new nahi bana sakta
    private EagerSingleton() {
        System.out.println("🔥 EagerSingleton: Instance created!");
    }
    
    // Global access point
    public static EagerSingleton getInstance() {
        return instance;
    }
    
    public void doSomething() {
        System.out.println("  📍 EagerSingleton doing work...");
    }
}
```

**Pros:** Thread-safe, Simple  
**Cons:** Object bana rahega chahe use ho ya nahi (Memory waste)

---

#### Method 2: Lazy Initialization (Single-Threaded Only)

```java
// 2. LAZY INITIALIZATION - Object tabhi banta hai jab zaroorat ho
class LazySingleton {
    private static LazySingleton instance;
    
    private LazySingleton() {
        System.out.println("💤 LazySingleton: Instance created on first request!");
    }
    
    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}
```

**Pros:** Memory efficient (Object tabhi banta hai jab use ho)  
**Cons:** **NOT Thread-Safe!** Multiple threads ek saath instance bana sakte hain.

---

#### Method 3: Thread-Safe Lazy (Synchronized Method)

```java
// 3. THREAD-SAFE LAZY - Synchronized method
class ThreadSafeSingleton {
    private static ThreadSafeSingleton instance;
    
    private ThreadSafeSingleton() {
        System.out.println("🔒 ThreadSafeSingleton: Instance created!");
    }
    
    // synchronized keyword ensures only one thread enters at a time
    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }
        return instance;
    }
}
```

**Pros:** Thread-safe  
**Cons:** **Performance hit!** Har baar `getInstance()` call pe synchronization overhead.

---

#### Method 4: Double-Checked Locking (Best Performance) ⭐ MOST POPULAR

```java
// 4. DOUBLE-CHECKED LOCKING - Industry Standard
class DoubleCheckedSingleton {
    // volatile keyword ensures visibility across threads
    private static volatile DoubleCheckedSingleton instance;
    
    private DoubleCheckedSingleton() {
        System.out.println("⚡ DoubleCheckedSingleton: Instance created!");
    }
    
    public static DoubleCheckedSingleton getInstance() {
        if (instance == null) {  // First check (No locking)
            synchronized (DoubleCheckedSingleton.class) {
                if (instance == null) {  // Second check (With locking)
                    instance = new DoubleCheckedSingleton();
                }
            }
        }
        return instance;
    }
    
    public void showMessage() {
        System.out.println("  📍 DoubleCheckedSingleton working...");
    }
}
```

**Pros:** Thread-safe, High Performance (Locking only once)  
**Cons:** Slightly complex, `volatile` keyword Java 5+ required

---

#### Method 5: Bill Pugh Singleton (Best Practice) 🏆 WINNER

```java
// 5. BILL PUGH SINGLETON (Initialization-on-demand holder) - RECOMMENDED
class BillPughSingleton {
    
    private BillPughSingleton() {
        System.out.println("🏆 BillPughSingleton: Instance created!");
    }
    
    // Inner static class - Loaded ONLY when getInstance() is called
    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }
    
    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
    
    public void doWork() {
        System.out.println("  📍 BillPughSingleton working...");
    }
}
```

**Pros:** Thread-safe, High Performance, Lazy Loading, No Synchronization overhead  
**Cons:** None (This is the recommended way!)

---

#### Method 6: Enum Singleton (Reflection-Proof) 🛡️

```java
// 6. ENUM SINGLETON - Reflection aur Serialization se bachata hai
enum EnumSingleton {
    INSTANCE;
    
    private String config;
    
    EnumSingleton() {
        System.out.println("🛡️ EnumSingleton: Instance created!");
        this.config = "Default Config";
    }
    
    public void setConfig(String config) {
        this.config = config;
    }
    
    public String getConfig() {
        return config;
    }
    
    public void doSomething() {
        System.out.println("  📍 EnumSingleton working with config: " + config);
    }
}
```

**Pros:** Reflection-proof, Serialization-proof, Thread-safe  
**Cons:** Thoda unusual syntax, Lazy loading nahi hoti (Enum load hote hi instance ban jata hai)

---

### Part 3: Complete Demo (All Methods Comparison)

```java
public class SingletonDemo {
    public static void main(String[] args) {
        System.out.println("=== 🎯 Singleton Pattern - All Implementations ===\n");
        
        // 1. Eager Singleton
        System.out.println("--- 1. Eager Initialization ---");
        EagerSingleton e1 = EagerSingleton.getInstance();
        EagerSingleton e2 = EagerSingleton.getInstance();
        System.out.println("Same instance? " + (e1 == e2));
        e1.doSomething();
        
        // 2. Lazy Singleton (Not thread-safe)
        System.out.println("\n--- 2. Lazy Initialization ---");
        LazySingleton l1 = LazySingleton.getInstance();
        LazySingleton l2 = LazySingleton.getInstance();
        System.out.println("Same instance? " + (l1 == l2));
        
        // 3. Thread-Safe Singleton
        System.out.println("\n--- 3. Thread-Safe Singleton ---");
        ThreadSafeSingleton t1 = ThreadSafeSingleton.getInstance();
        ThreadSafeSingleton t2 = ThreadSafeSingleton.getInstance();
        System.out.println("Same instance? " + (t1 == t2));
        
        // 4. Double-Checked Locking
        System.out.println("\n--- 4. Double-Checked Locking ---");
        DoubleCheckedSingleton d1 = DoubleCheckedSingleton.getInstance();
        DoubleCheckedSingleton d2 = DoubleCheckedSingleton.getInstance();
        System.out.println("Same instance? " + (d1 == d2));
        d1.showMessage();
        
        // 5. Bill Pugh (Best Practice)
        System.out.println("\n--- 5. Bill Pugh Singleton (RECOMMENDED) ---");
        BillPughSingleton b1 = BillPughSingleton.getInstance();
        BillPughSingleton b2 = BillPughSingleton.getInstance();
        System.out.println("Same instance? " + (b1 == b2));
        b1.doWork();
        
        // 6. Enum Singleton
        System.out.println("\n--- 6. Enum Singleton ---");
        EnumSingleton en1 = EnumSingleton.INSTANCE;
        EnumSingleton en2 = EnumSingleton.INSTANCE;
        System.out.println("Same instance? " + (en1 == en2));
        en1.setConfig("Production Mode");
        en1.doSomething();
        en2.doSomething(); // Same config
        
        // Multithreading Demo
        System.out.println("\n--- 🧵 Multithreading Test ---");
        testMultithreading();
    }
    
    private static void testMultithreading() {
        // Create multiple threads trying to get singleton
        Runnable task = () -> {
            DoubleCheckedSingleton instance = DoubleCheckedSingleton.getInstance();
            System.out.println(Thread.currentThread().getName() + 
                             " got instance: " + instance.hashCode());
        };
        
        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");
        Thread t3 = new Thread(task, "Thread-3");
        Thread t4 = new Thread(task, "Thread-4");
        Thread t5 = new Thread(task, "Thread-5");
        
        t1.start(); t2.start(); t3.start(); t4.start(); t5.start();
        
        try {
            t1.join(); t2.join(); t3.join(); t4.join(); t5.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        System.out.println("All threads got the SAME hashcode! (Singleton works)");
    }
}
```

---

### Part 4: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: Database Connection Pool Manager

**Scenario:** Pure application mein sirf ek hi connection pool hona chahiye.

```java
// File: ConnectionPool.java
import java.util.*;

class ConnectionPool {
    // Bill Pugh Singleton Implementation
    private ConnectionPool() {
        System.out.println("🏊 Initializing Database Connection Pool...");
        initializePool();
    }
    
    private static class Holder {
        private static final ConnectionPool INSTANCE = new ConnectionPool();
    }
    
    public static ConnectionPool getInstance() {
        return Holder.INSTANCE;
    }
    
    // Pool related fields
    private List<Connection> availableConnections = new ArrayList<>();
    private List<Connection> usedConnections = new ArrayList<>();
    private static final int POOL_SIZE = 10;
    
    private void initializePool() {
        for (int i = 0; i < POOL_SIZE; i++) {
            availableConnections.add(new Connection("CONN-" + (i + 1)));
        }
        System.out.println("✅ Pool initialized with " + POOL_SIZE + " connections");
    }
    
    public synchronized Connection getConnection() {
        if (availableConnections.isEmpty()) {
            System.out.println("⚠️ No available connections! Waiting...");
            return null;
        }
        Connection conn = availableConnections.remove(0);
        usedConnections.add(conn);
        System.out.println("🔗 Borrowed: " + conn.getId() + 
                          " (Available: " + availableConnections.size() + ")");
        return conn;
    }
    
    public synchronized void releaseConnection(Connection conn) {
        usedConnections.remove(conn);
        availableConnections.add(conn);
        System.out.println("🔓 Released: " + conn.getId() + 
                          " (Available: " + availableConnections.size() + ")");
    }
    
    public int getAvailableCount() {
        return availableConnections.size();
    }
    
    public int getUsedCount() {
        return usedConnections.size();
    }
    
    // Inner Connection class
    static class Connection {
        private String id;
        private boolean closed = false;
        
        public Connection(String id) {
            this.id = id;
        }
        
        public String getId() { return id; }
        
        public void executeQuery(String query) {
            if (closed) {
                System.out.println("❌ Connection " + id + " is closed!");
                return;
            }
            System.out.println("   📊 [" + id + "] Executing: " + query);
        }
        
        public void close() {
            closed = true;
        }
    }
}

// Repository using the singleton pool
class UserRepository {
    public void getUserById(int id) {
        ConnectionPool pool = ConnectionPool.getInstance();
        ConnectionPool.Connection conn = pool.getConnection();
        
        if (conn != null) {
            conn.executeQuery("SELECT * FROM users WHERE id = " + id);
            pool.releaseConnection(conn);
        }
    }
    
    public void getAllUsers() {
        ConnectionPool pool = ConnectionPool.getInstance();
        ConnectionPool.Connection conn = pool.getConnection();
        
        if (conn != null) {
            conn.executeQuery("SELECT * FROM users");
            pool.releaseConnection(conn);
        }
    }
}

public class DatabaseApp {
    public static void main(String[] args) {
        System.out.println("=== 💾 Database Connection Pool (Singleton) ===\n");
        
        // Get singleton instance
        ConnectionPool pool1 = ConnectionPool.getInstance();
        ConnectionPool pool2 = ConnectionPool.getInstance();
        
        System.out.println("\nSame pool instance? " + (pool1 == pool2));
        System.out.println("Available connections: " + pool1.getAvailableCount());
        
        System.out.println("\n--- Using Repository ---");
        UserRepository repo = new UserRepository();
        
        repo.getUserById(101);
        repo.getUserById(102);
        repo.getAllUsers();
        
        System.out.println("\n📊 Pool Stats:");
        System.out.println("Available: " + pool1.getAvailableCount());
        System.out.println("Used: " + pool1.getUsedCount());
        
        // Multiple services use same pool
        System.out.println("\n--- Another Service Using Same Pool ---");
        ConnectionPool.Connection conn1 = pool1.getConnection();
        ConnectionPool.Connection conn2 = pool2.getConnection(); // Same pool
        
        System.out.println("\n📊 Final Pool Stats:");
        System.out.println("Available: " + pool1.getAvailableCount());
        System.out.println("Used: " + pool1.getUsedCount());
        
        pool1.releaseConnection(conn1);
        pool2.releaseConnection(conn2);
    }
}
```

---

#### Example 2: Application Configuration Manager

**Scenario:** App settings ek jagah se manage karni hai, pure app mein consistent.

```java
// File: ConfigManager.java
import java.util.*;
import java.io.*;

class ConfigurationManager {
    private Properties properties = new Properties();
    private String configFilePath = "app.config";
    
    // Eager Initialization (Config always needed)
    private static final ConfigurationManager INSTANCE = new ConfigurationManager();
    
    private ConfigurationManager() {
        System.out.println("⚙️ Loading application configuration...");
        loadDefaultConfig();
        loadFromFile();
    }
    
    public static ConfigurationManager getInstance() {
        return INSTANCE;
    }
    
    private void loadDefaultConfig() {
        properties.setProperty("app.name", "MyApplication");
        properties.setProperty("app.version", "1.0.0");
        properties.setProperty("app.environment", "development");
        properties.setProperty("database.host", "localhost");
        properties.setProperty("database.port", "3306");
        properties.setProperty("database.name", "mydb");
        properties.setProperty("cache.enabled", "true");
        properties.setProperty("cache.ttl", "3600");
        properties.setProperty("log.level", "INFO");
        properties.setProperty("max.connections", "100");
        properties.setProperty("timeout", "30000");
    }
    
    private void loadFromFile() {
        // Simulate loading from file
        System.out.println("   📁 Loading overrides from " + configFilePath);
        // In real app: properties.load(new FileInputStream(configFilePath));
        properties.setProperty("app.environment", "production"); // Override
    }
    
    public String getProperty(String key) {
        return properties.getProperty(key);
    }
    
    public String getProperty(String key, String defaultValue) {
        return properties.getProperty(key, defaultValue);
    }
    
    public int getIntProperty(String key, int defaultValue) {
        String value = properties.getProperty(key);
        return value != null ? Integer.parseInt(value) : defaultValue;
    }
    
    public boolean getBooleanProperty(String key, boolean defaultValue) {
        String value = properties.getProperty(key);
        return value != null ? Boolean.parseBoolean(value) : defaultValue;
    }
    
    public void setProperty(String key, String value) {
        properties.setProperty(key, value);
    }
    
    public void reload() {
        System.out.println("🔄 Reloading configuration...");
        properties.clear();
        loadDefaultConfig();
        loadFromFile();
    }
    
    public void printAllConfigs() {
        System.out.println("\n📋 Current Configuration:");
        System.out.println("═══════════════════════════════════════");
        properties.entrySet().stream()
            .sorted(Map.Entry.comparingByKey())
            .forEach(entry -> System.out.printf("  %-20s = %s\n", 
                                               entry.getKey(), entry.getValue()));
        System.out.println("═══════════════════════════════════════");
    }
}

// Services using the singleton config
class DatabaseService {
    private ConfigurationManager config = ConfigurationManager.getInstance();
    
    public void connect() {
        String host = config.getProperty("database.host");
        String port = config.getProperty("database.port");
        String dbName = config.getProperty("database.name");
        int timeout = config.getIntProperty("timeout", 30000);
        
        System.out.println("\n🗄️ Connecting to database:");
        System.out.println("   Host: " + host);
        System.out.println("   Port: " + port);
        System.out.println("   Database: " + dbName);
        System.out.println("   Timeout: " + timeout + "ms");
    }
}

class CacheService {
    private ConfigurationManager config = ConfigurationManager.getInstance();
    
    public void initialize() {
        boolean enabled = config.getBooleanProperty("cache.enabled", true);
        int ttl = config.getIntProperty("cache.ttl", 3600);
        
        System.out.println("\n💾 Cache Service:");
        System.out.println("   Enabled: " + enabled);
        System.out.println("   TTL: " + ttl + " seconds");
    }
}

class LoggingService {
    private ConfigurationManager config = ConfigurationManager.getInstance();
    
    public void configure() {
        String level = config.getProperty("log.level", "INFO");
        System.out.println("\n📝 Logging configured at level: " + level);
    }
}

public class ConfigApp {
    public static void main(String[] args) {
        System.out.println("=== ⚙️ Configuration Manager (Singleton) ===\n");
        
        // Get singleton config
        ConfigurationManager config1 = ConfigurationManager.getInstance();
        ConfigurationManager config2 = ConfigurationManager.getInstance();
        
        System.out.println("\nSame config instance? " + (config1 == config2));
        
        // Use config in different services
        DatabaseService dbService = new DatabaseService();
        dbService.connect();
        
        CacheService cacheService = new CacheService();
        cacheService.initialize();
        
        LoggingService logService = new LoggingService();
        logService.configure();
        
        // Update config globally
        System.out.println("\n--- Updating Configuration ---");
        config1.setProperty("log.level", "DEBUG");
        config1.setProperty("cache.enabled", "false");
        
        // All services see the change
        System.out.println("\n--- After Update ---");
        logService.configure();
        cacheService.initialize();
        
        // Print all configs
        config1.printAllConfigs();
    }
}
```

---

#### Example 3: Logger Utility (Most Common Use Case)

**Scenario:** Pure application mein ek hi logger instance hona chahiye.

```java
// File: Logger.java
import java.io.*;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

class Logger {
    // Bill Pugh Singleton
    private Logger() {
        System.out.println("📝 Logger initialized");
        try {
            writer = new PrintWriter(new FileWriter("application.log", true));
        } catch (IOException e) {
            System.err.println("Failed to initialize log file");
        }
    }
    
    private static class Holder {
        private static final Logger INSTANCE = new Logger();
    }
    
    public static Logger getInstance() {
        return Holder.INSTANCE;
    }
    
    // Logging fields
    private PrintWriter writer;
    private DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss.SSS");
    private String currentLogLevel = "INFO";
    
    // Log levels priority
    private Map<String, Integer> levelPriority = Map.of(
        "DEBUG", 1,
        "INFO", 2,
        "WARN", 3,
        "ERROR", 4
    );
    
    public void setLogLevel(String level) {
        this.currentLogLevel = level.toUpperCase();
        info("Log level changed to: " + currentLogLevel);
    }
    
    private boolean shouldLog(String level) {
        return levelPriority.getOrDefault(level.toUpperCase(), 0) >= 
               levelPriority.getOrDefault(currentLogLevel, 2);
    }
    
    private void log(String level, String message) {
        if (!shouldLog(level)) return;
        
        String timestamp = LocalDateTime.now().format(dtf);
        String logEntry = String.format("[%s] [%-5s] %s", timestamp, level, message);
        
        // Console output
        String color = switch (level) {
            case "ERROR" -> "\u001B[31m";
            case "WARN" -> "\u001B[33m";
            case "INFO" -> "\u001B[32m";
            case "DEBUG" -> "\u001B[36m";
            default -> "\u001B[0m";
        };
        System.out.println(color + logEntry + "\u001B[0m");
        
        // File output
        if (writer != null) {
            writer.println(logEntry);
            writer.flush();
        }
    }
    
    public void debug(String message) { log("DEBUG", message); }
    public void info(String message) { log("INFO", message); }
    public void warn(String message) { log("WARN", message); }
    public void error(String message) { log("ERROR", message); }
    public void error(String message, Throwable throwable) {
        log("ERROR", message);
        if (throwable != null) {
            log("ERROR", "  Exception: " + throwable.getMessage());
            for (StackTraceElement elem : throwable.getStackTrace()) {
                log("ERROR", "    at " + elem.toString());
            }
        }
    }
    
    public void close() {
        if (writer != null) {
            info("Logger shutting down");
            writer.close();
        }
    }
}

// Classes using the singleton logger
class PaymentService {
    private Logger logger = Logger.getInstance();
    
    public void processPayment(double amount) {
        logger.info("Processing payment of ₹" + amount);
        try {
            // Simulate payment processing
            if (amount > 10000) {
                throw new RuntimeException("Amount exceeds limit");
            }
            logger.info("Payment successful!");
        } catch (Exception e) {
            logger.error("Payment failed!", e);
        }
    }
}

class AuthenticationService {
    private Logger logger = Logger.getInstance();
    
    public void login(String username) {
        logger.debug("Login attempt for user: " + username);
        logger.info("User " + username + " logged in successfully");
    }
    
    public void logout(String username) {
        logger.info("User " + username + " logged out");
    }
}

class DataService {
    private Logger logger = Logger.getInstance();
    
    public void fetchData() {
        logger.debug("Fetching data from database...");
        logger.info("Data fetched: 150 records");
    }
}

public class LoggingApp {
    public static void main(String[] args) {
        System.out.println("=== 📝 Application Logger (Singleton) ===\n");
        
        Logger logger = Logger.getInstance();
        
        // Set initial log level
        logger.setLogLevel("DEBUG");
        
        // Use logger across services
        PaymentService paymentService = new PaymentService();
        AuthenticationService authService = new AuthenticationService();
        DataService dataService = new DataService();
        
        System.out.println("\n--- User Login Flow ---");
        authService.login("rahul@email.com");
        dataService.fetchData();
        
        System.out.println("\n--- Payment Processing ---");
        paymentService.processPayment(500);
        paymentService.processPayment(15000); // Will fail
        
        System.out.println("\n--- Changing Log Level to INFO (DEBUG won't appear) ---");
        logger.setLogLevel("INFO");
        
        authService.login("priya@email.com");
        dataService.fetchData();
        
        System.out.println("\n--- Logout ---");
        authService.logout("rahul@email.com");
        
        // Verify same instance
        Logger anotherLogger = Logger.getInstance();
        System.out.println("\nSame logger instance? " + (logger == anotherLogger));
        
        logger.info("Application finished");
        logger.close();
        
        System.out.println("\n✅ Check 'application.log' file for complete logs!");
    }
}
```

---

#### Example 4: Cache Manager (In-Memory Cache)

**Scenario:** Application-wide cache jahan data store karna hai.

```java
// File: CacheManager.java
import java.util.*;
import java.util.concurrent.*;

class CacheManager {
    // Enum Singleton (Best for cache as it's serialization-proof)
    public enum Instance {
        CACHE;
        
        private Map<String, CacheEntry> cache;
        private ScheduledExecutorService cleaner;
        
        Instance() {
            System.out.println("💾 Initializing Cache Manager...");
            this.cache = new ConcurrentHashMap<>();
            this.cleaner = Executors.newSingleThreadScheduledExecutor();
            
            // Clean expired entries every minute
            cleaner.scheduleAtFixedRate(this::cleanExpiredEntries, 1, 1, TimeUnit.MINUTES);
        }
        
        private void cleanExpiredEntries() {
            int removed = 0;
            Iterator<Map.Entry<String, CacheEntry>> it = cache.entrySet().iterator();
            while (it.hasNext()) {
                Map.Entry<String, CacheEntry> entry = it.next();
                if (entry.getValue().isExpired()) {
                    it.remove();
                    removed++;
                }
            }
            if (removed > 0) {
                System.out.println("🧹 Cleaned " + removed + " expired cache entries");
            }
        }
        
        public void put(String key, Object value) {
            put(key, value, 3600); // Default 1 hour TTL
        }
        
        public void put(String key, Object value, long ttlSeconds) {
            cache.put(key, new CacheEntry(value, ttlSeconds));
            System.out.println("💾 Cached: " + key + " (TTL: " + ttlSeconds + "s)");
        }
        
        public Object get(String key) {
            CacheEntry entry = cache.get(key);
            if (entry == null) {
                System.out.println("❌ Cache MISS: " + key);
                return null;
            }
            if (entry.isExpired()) {
                cache.remove(key);
                System.out.println("⏰ Cache EXPIRED: " + key);
                return null;
            }
            System.out.println("✅ Cache HIT: " + key);
            return entry.value;
        }
        
        public void remove(String key) {
            cache.remove(key);
            System.out.println("🗑️ Removed from cache: " + key);
        }
        
        public void clear() {
            cache.clear();
            System.out.println("🧹 Cache cleared");
        }
        
        public int size() {
            return cache.size();
        }
        
        public void printStats() {
            System.out.println("\n📊 Cache Statistics:");
            System.out.println("════════════════════════");
            System.out.println("Total entries: " + cache.size());
            System.out.println("Keys: " + cache.keySet());
            System.out.println("════════════════════════");
        }
        
        private static class CacheEntry {
            Object value;
            long expiryTime;
            
            CacheEntry(Object value, long ttlSeconds) {
                this.value = value;
                this.expiryTime = System.currentTimeMillis() + (ttlSeconds * 1000);
            }
            
            boolean isExpired() {
                return System.currentTimeMillis() > expiryTime;
            }
        }
    }
    
    // Convenience methods
    public static void put(String key, Object value) {
        Instance.CACHE.put(key, value);
    }
    
    public static void put(String key, Object value, long ttlSeconds) {
        Instance.CACHE.put(key, value, ttlSeconds);
    }
    
    public static Object get(String key) {
        return Instance.CACHE.get(key);
    }
    
    public static void remove(String key) {
        Instance.CACHE.remove(key);
    }
    
    public static void clear() {
        Instance.CACHE.clear();
    }
    
    public static int size() {
        return Instance.CACHE.size();
    }
}

// Services using the cache
class UserService {
    public User getUserById(int id) {
        String cacheKey = "user:" + id;
        
        // Try cache first
        User cachedUser = (User) CacheManager.get(cacheKey);
        if (cachedUser != null) {
            return cachedUser;
        }
        
        // Simulate DB call
        System.out.println("   📡 Fetching user " + id + " from database...");
        User user = new User(id, "User" + id, "user" + id + "@email.com");
        
        // Store in cache
        CacheManager.put(cacheKey, user, 300); // 5 minutes TTL
        
        return user;
    }
}

class ProductService {
    public List<Product> getAllProducts() {
        String cacheKey = "products:all";
        
        List<Product> cached = (List<Product>) CacheManager.get(cacheKey);
        if (cached != null) {
            return cached;
        }
        
        System.out.println("   📡 Fetching products from database...");
        List<Product> products = Arrays.asList(
            new Product(1, "Laptop", 55000),
            new Product(2, "Mouse", 999),
            new Product(3, "Keyboard", 2499)
        );
        
        CacheManager.put(cacheKey, products, 600); // 10 minutes TTL
        
        return products;
    }
}

class User {
    int id;
    String name;
    String email;
    
    public User(int id, String name, String email) {
        this.id = id;
        this.name = name;
        this.email = email;
    }
    
    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "', email='" + email + "'}";
    }
}

class Product {
    int id;
    String name;
    double price;
    
    public Product(int id, String name, double price) {
        this.id = id;
        this.name = name;
        this.price = price;
    }
    
    @Override
    public String toString() {
        return "Product{id=" + id + ", name='" + name + "', price=" + price + "}";
    }
}

public class CachingApp {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 💾 Cache Manager (Singleton) ===\n");
        
        UserService userService = new UserService();
        ProductService productService = new ProductService();
        
        // First call - Cache MISS
        System.out.println("--- First Request (Cache Miss) ---");
        User user1 = userService.getUserById(101);
        System.out.println("Result: " + user1);
        
        // Second call - Cache HIT
        System.out.println("\n--- Second Request (Cache Hit) ---");
        User user2 = userService.getUserById(101);
        System.out.println("Result: " + user2);
        
        // Different user - Cache MISS
        System.out.println("\n--- Different User (Cache Miss) ---");
        User user3 = userService.getUserById(102);
        System.out.println("Result: " + user3);
        
        // Products caching
        System.out.println("\n--- Products Request ---");
        List<Product> products1 = productService.getAllProducts();
        System.out.println("Products: " + products1);
        
        System.out.println("\n--- Products Request (Cached) ---");
        List<Product> products2 = productService.getAllProducts();
        System.out.println("Products: " + products2);
        
        // Print cache stats
        CacheManager.Instance.CACHE.printStats();
        
        // Test TTL expiry
        System.out.println("\n--- Testing TTL Expiry (Waiting 2 seconds) ---");
        Thread.sleep(2000);
        System.out.println("Cache size before expiry: " + CacheManager.size());
        
        // Wait more for cleaner to run
        System.out.println("Waiting for cache cleaner...");
        Thread.sleep(61000);
        
        System.out.println("\n--- After Expiry ---");
        CacheManager.Instance.CACHE.printStats();
        
        // Clear cache
        System.out.println("\n--- Manual Clear ---");
        CacheManager.clear();
        CacheManager.Instance.CACHE.printStats();
    }
}
```

---

#### Example 5: Printer Spooler (Single Printer Access)

**Scenario:** Office mein ek hi printer hai, multiple computers se print requests queue mein jani chahiye.

```java
// File: PrinterSpooler.java
import java.util.*;
import java.util.concurrent.*;

class PrinterSpooler {
    // Double-Checked Locking Singleton
    private static volatile PrinterSpooler instance;
    private Queue<PrintJob> printQueue;
    private boolean isPrinting;
    private ExecutorService printerThread;
    
    private PrinterSpooler() {
        System.out.println("🖨️ Initializing Printer Spooler...");
        this.printQueue = new LinkedList<>();
        this.isPrinting = false;
        this.printerThread = Executors.newSingleThreadExecutor();
        startPrinting();
    }
    
    public static PrinterSpooler getInstance() {
        if (instance == null) {
            synchronized (PrinterSpooler.class) {
                if (instance == null) {
                    instance = new PrinterSpooler();
                }
            }
        }
        return instance;
    }
    
    private void startPrinting() {
        printerThread.submit(() -> {
            while (true) {
                try {
                    processNextJob();
                    Thread.sleep(1000); // Check queue every second
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                    break;
                }
            }
        });
    }
    
    private synchronized void processNextJob() {
        if (!isPrinting && !printQueue.isEmpty()) {
            PrintJob job = printQueue.poll();
            isPrinting = true;
            
            printerThread.submit(() -> {
                try {
                    printJob(job);
                } finally {
                    isPrinting = false;
                }
            });
        }
    }
    
    private void printJob(PrintJob job) {
        System.out.println("\n🖨️ ===== PRINTING ===== ");
        System.out.println("Job ID: " + job.id);
        System.out.println("Document: " + job.documentName);
        System.out.println("Pages: " + job.pages);
        System.out.println("Submitted by: " + job.submittedBy);
        System.out.println("═══════════════════════");
        
        // Simulate printing time
        for (int i = 1; i <= job.pages; i++) {
            System.out.println("   📄 Printing page " + i + "/" + job.pages);
            try {
                Thread.sleep(1000); // 1 second per page
            } catch (InterruptedException e) {
                System.err.println("Print job interrupted!");
                return;
            }
        }
        
        System.out.println("✅ Print job " + job.id + " completed!");
        System.out.println("🖨️ ====================\n");
    }
    
    public synchronized void submitPrintJob(String documentName, int pages, String user) {
        String jobId = "JOB-" + UUID.randomUUID().toString().substring(0, 8);
        PrintJob job = new PrintJob(jobId, documentName, pages, user);
        printQueue.offer(job);
        
        System.out.println("📋 Print job submitted: " + jobId);
        System.out.println("   Document: " + documentName);
        System.out.println("   User: " + user);
        System.out.println("   Queue position: " + printQueue.size());
        
        // Wake up printer if idle
        if (!isPrinting) {
            processNextJob();
        }
    }
    
    public synchronized List<PrintJob> getQueueStatus() {
        return new ArrayList<>(printQueue);
    }
    
    public synchronized int getQueueSize() {
        return printQueue.size();
    }
    
    public boolean isPrinting() {
        return isPrinting;
    }
    
    public void shutdown() {
        printerThread.shutdown();
    }
    
    // Inner class
    static class PrintJob {
        String id;
        String documentName;
        int pages;
        String submittedBy;
        long timestamp;
        
        public PrintJob(String id, String doc, int pages, String user) {
            this.id = id;
            this.documentName = doc;
            this.pages = pages;
            this.submittedBy = user;
            this.timestamp = System.currentTimeMillis();
        }
        
        @Override
        public String toString() {
            return String.format("[%s] %s (%d pages) - %s", 
                               id, documentName, pages, submittedBy);
        }
    }
}

// Computers sending print requests
class Computer {
    private String name;
    private PrinterSpooler printer;
    
    public Computer(String name) {
        this.name = name;
        this.printer = PrinterSpooler.getInstance();
    }
    
    public void print(String document, int pages) {
        System.out.println("\n💻 " + name + " sending print request...");
        printer.submitPrintJob(document, pages, name);
    }
}

public class OfficePrinterApp {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 🖨️ Office Printer Spooler (Singleton) ===\n");
        
        // Get singleton printer instance
        PrinterSpooler printer1 = PrinterSpooler.getInstance();
        PrinterSpooler printer2 = PrinterSpooler.getInstance();
        
        System.out.println("Same printer instance? " + (printer1 == printer2));
        
        // Create computers
        Computer comp1 = new Computer("Rahul's Laptop");
        Computer comp2 = new Computer("Priya's Desktop");
        Computer comp3 = new Computer("Amit's MacBook");
        Computer comp4 = new Computer("Meeting Room PC");
        
        // Send print jobs
        comp1.print("Quarterly_Report.pdf", 3);
        Thread.sleep(500);
        
        comp2.print("Presentation.pptx", 5);
        Thread.sleep(500);
        
        comp3.print("Invoice_2024_001.pdf", 2);
        Thread.sleep(500);
        
        comp4.print("Meeting_Minutes.docx", 4);
        
        // Check queue status
        Thread.sleep(2000);
        System.out.println("\n📊 Current Print Queue:");
        List<PrinterSpooler.PrintJob> queue = printer1.getQueueStatus();
        queue.forEach(job -> System.out.println("   " + job));
        
        // Wait for all jobs to complete
        System.out.println("\n⏳ Waiting for print jobs to complete...");
        while (printer1.getQueueSize() > 0 || printer1.isPrinting()) {
            Thread.sleep(2000);
        }
        
        System.out.println("\n✅ All print jobs completed!");
        printer1.shutdown();
    }
}
```

---

### Part 5: Singleton Pattern - Breaking and Prevention

#### How to Break Singleton?

```java
// 1. Reflection Attack
class ReflectionAttack {
    public static void main(String[] args) throws Exception {
        Constructor<BillPughSingleton> constructor = 
            BillPughSingleton.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        
        BillPughSingleton instance1 = constructor.newInstance();
        BillPughSingleton instance2 = BillPughSingleton.getInstance();
        
        System.out.println("Same instance? " + (instance1 == instance2)); // FALSE! Broken!
    }
}

// 2. Serialization Attack
class SerializationAttack {
    public static void main(String[] args) throws Exception {
        BillPughSingleton instance1 = BillPughSingleton.getInstance();
        
        // Serialize
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("singleton.ser"));
        oos.writeObject(instance1);
        oos.close();
        
        // Deserialize
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("singleton.ser"));
        BillPughSingleton instance2 = (BillPughSingleton) ois.readObject();
        ois.close();
        
        System.out.println("Same instance? " + (instance1 == instance2)); // FALSE! Broken!
    }
}

// 3. Cloning Attack
class CloningAttack {
    public static void main(String[] args) throws CloneNotSupportedException {
        // If Singleton implements Cloneable
        // BillPughSingleton instance2 = (BillPughSingleton) instance1.clone();
    }
}
```

#### How to Prevent Breaking Singleton?

```java
// 🛡️ ULTIMATE SINGLETON - All attacks prevented!
class UltimateSingleton implements Serializable {
    private static final long serialVersionUID = 1L;
    
    // 1. Prevent Reflection
    private UltimateSingleton() {
        if (Holder.INSTANCE != null) {
            throw new RuntimeException("Use getInstance() method!");
        }
    }
    
    private static class Holder {
        private static final UltimateSingleton INSTANCE = new UltimateSingleton();
    }
    
    public static UltimateSingleton getInstance() {
        return Holder.INSTANCE;
    }
    
    // 2. Prevent Serialization
    protected Object readResolve() {
        return getInstance();
    }
    
    // 3. Prevent Cloning
    @Override
    protected Object clone() throws CloneNotSupportedException {
        throw new CloneNotSupportedException("Singleton cannot be cloned!");
    }
}

// 🏆 BEST SOLUTION: Use Enum (Already protected against all attacks!)
enum PerfectSingleton {
    INSTANCE;
    
    // Your methods here
    public void doSomething() {
        System.out.println("Perfect Singleton working!");
    }
}
```

---

### Part 6: Singleton Pattern - Summary & Interview Points

| Aspect | Details |
|:---|:---|
| **Purpose** | Ensure single instance, global access point |
| **Use When** | Exactly one instance needed (Logger, Config, Cache, Pool) |
| **Key Components** | Private constructor, Static instance, Public getter |
| **Real Examples** | `Runtime.getRuntime()`, `Desktop.getDesktop()`, Spring Beans |

**Interview Question:** *What is Singleton Pattern?*

**Answer:** *"Sir, Singleton pattern ensures a class has only one instance and provides a global point of access to it. It's used for centralized management of resources like database connections, configuration, logging, and caching. Java's `Runtime` class is a perfect example."*

**Interview Question:** *Different ways to implement Singleton?*

| Method | Thread-Safe | Lazy | Performance |
|:---|:---|:---|:---|
| Eager Initialization | ✅ | ❌ | Best |
| Lazy (Simple) | ❌ | ✅ | Best |
| Synchronized Method | ✅ | ✅ | Worst |
| Double-Checked Locking | ✅ | ✅ | Good |
| Bill Pugh (Holder) | ✅ | ✅ | Best |
| Enum | ✅ | ❌ | Best |

**Interview Question:** *When NOT to use Singleton?*

1. **Testing Difficulty:** Singletons make unit testing hard (global state).
2. **Tight Coupling:** Code becomes dependent on singleton.
3. **Concurrency Issues:** Shared mutable state requires careful synchronization.
4. **Single Responsibility Violation:** Often become "God Objects".

**Interview Question:** *Singleton vs Static Class?*

| Singleton | Static Class |
|:---|:---|
| Can implement interfaces | Cannot implement interfaces |
| Can extend classes | Cannot extend (final) |
| Can be lazy loaded | Loaded at class load |
| Object created on heap | No object, methods on stack |
| Can pass as parameter | Cannot pass |

**Interview Question:** *How to make Singleton thread-safe?*

*"Sir, multiple ways: 1) Synchronized getInstance() method, 2) Double-checked locking with volatile, 3) Bill Pugh inner static class (recommended), 4) Enum singleton (best for reflection/serialization protection)."*

**Real Java Singleton Examples:**
```java
// Java Runtime
Runtime runtime = Runtime.getRuntime();

// Java Desktop
Desktop desktop = Desktop.getDesktop();

// Java SecurityManager
SecurityManager sm = System.getSecurityManager();

// Spring Framework (by default)
@Component
@Scope("singleton") // Default scope
public class MyService { }
```
