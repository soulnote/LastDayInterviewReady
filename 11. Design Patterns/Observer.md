### Part 1: Problem Statement (Tight Coupling & Polling)

Sochiye aapke paas **Weather Station** hai jo temperature update karta hai. Multiple displays (Phone, TV, Web) ko ye data dikhana hai.

#### ❌ BAD PRACTICE: Polling ya Direct Call

```java
// Bina Observer ke - Polling ya Tight Coupling!

// Weather Station (Subject)
class WeatherStationBad {
    private float temperature;
    private PhoneDisplay phoneDisplay;
    private TVDisplay tvDisplay;
    private WebDisplay webDisplay;
    
    public void setTemperature(float temp) {
        this.temperature = temp;
        // Har display ko manually call karna pad raha hai!
        phoneDisplay.update(temperature);
        tvDisplay.update(temperature);
        webDisplay.update(temperature);
        // Naya display add karna hai? Yahan code change karo! 😱
    }
    
    public float getTemperature() {
        return temperature;
    }
}

// Displays (Observers) - Polling approach (Worse!)
class PhoneDisplay {
    private WeatherStationBad station;
    
    public PhoneDisplay(WeatherStationBad station) {
        this.station = station;
    }
    
    public void checkForUpdates() {
        // Har second poll karo - CPU waste!
        while (true) {
            float temp = station.getTemperature();
            System.out.println("Phone: " + temp + "°C");
            Thread.sleep(1000);
        }
    }
}
```

**Dikkatein:**
1. **Tight Coupling:** Subject ko saare observers ke baare mein pata hai.
2. **Open/Closed Violation:** Naya observer add karne ke liye Subject class modify karni padegi.
3. **Polling Waste:** Agar observer baar baar check kare to resources waste hote hain.

---

### Part 2: Observer Pattern Solution (Good Example)

Hum **Subject** interface aur **Observer** interface banayenge. Subject ke paas observers ki list hogi.

#### Step 1: Observer Interface

```java
import java.util.ArrayList;
import java.util.List;

// 1. Observer Interface: Sabhi displays isko implement karenge
interface WeatherObserver {
    void update(float temperature, float humidity, float pressure);
    String getName();
}
```

#### Step 2: Subject Interface

```java
// 2. Subject Interface: Weather station isko implement karega
interface WeatherSubject {
    void registerObserver(WeatherObserver observer);
    void removeObserver(WeatherObserver observer);
    void notifyObservers();
}
```

#### Step 3: Concrete Subject (Weather Station)

```java
// 3. Concrete Subject: Weather Station
class WeatherStation implements WeatherSubject {
    private List<WeatherObserver> observers;
    private float temperature;
    private float humidity;
    private float pressure;
    
    public WeatherStation() {
        this.observers = new ArrayList<>();
    }
    
    @Override
    public void registerObserver(WeatherObserver observer) {
        observers.add(observer);
        System.out.println("📝 " + observer.getName() + " subscribed to weather updates");
    }
    
    @Override
    public void removeObserver(WeatherObserver observer) {
        observers.remove(observer);
        System.out.println("❌ " + observer.getName() + " unsubscribed");
    }
    
    @Override
    public void notifyObservers() {
        for (WeatherObserver observer : observers) {
            observer.update(temperature, humidity, pressure);
        }
    }
    
    // When weather data changes, notify all observers
    public void setMeasurements(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        System.out.println("\n🌡️ Weather Station: New measurements received!");
        System.out.println("   Temp: " + temperature + "°C, Humidity: " + humidity + "%, Pressure: " + pressure + " hPa");
        measurementsChanged();
    }
    
    private void measurementsChanged() {
        notifyObservers();
    }
    
    // Getters for pull model (optional)
    public float getTemperature() { return temperature; }
    public float getHumidity() { return humidity; }
    public float getPressure() { return pressure; }
}
```

#### Step 4: Concrete Observers (Displays)

```java
// 4. Concrete Observer 1: Phone Display
class PhoneDisplay implements WeatherObserver {
    private String name = "📱 Phone Display";
    private float temperature;
    private float humidity;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    private void display() {
        System.out.println("\n" + name);
        System.out.println("┌─────────────────────┐");
        System.out.printf("│ 🌡️  Temp: %.1f°C    │\n", temperature);
        System.out.printf("│ 💧 Humidity: %.1f%%  │\n", humidity);
        System.out.println("└─────────────────────┘");
    }
    
    @Override
    public String getName() { return name; }
}

// Concrete Observer 2: TV Display (Shows more details)
class TVDisplay implements WeatherObserver {
    private String name = "📺 TV Display";
    private float temperature;
    private float humidity;
    private float pressure;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        this.pressure = pressure;
        display();
    }
    
    private void display() {
        System.out.println("\n" + name);
        System.out.println("╔══════════════════════════╗");
        System.out.printf("║   WEATHER FORECAST       ║\n");
        System.out.printf("║   Temperature: %.1f°C    ║\n", temperature);
        System.out.printf("║   Humidity:    %.1f%%     ║\n", humidity);
        System.out.printf("║   Pressure:    %.1f hPa  ║\n", pressure);
        System.out.println("╚══════════════════════════╝");
    }
    
    @Override
    public String getName() { return name; }
}

// Concrete Observer 3: Web Dashboard (Shows alerts)
class WebDashboard implements WeatherObserver {
    private String name = "🌐 Web Dashboard";
    private float temperature;
    private float humidity;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    private void display() {
        System.out.println("\n" + name);
        System.out.println("┌────────────────────────────────┐");
        System.out.printf("│ Temperature: %.1f°C            │\n", temperature);
        System.out.printf("│ Humidity: %.1f%%                │\n", humidity);
        
        // Add alerts based on conditions
        if (temperature > 35) {
            System.out.println("│ ⚠️  ALERT: Heat Wave Warning!  │");
        } else if (temperature < 5) {
            System.out.println("│ ⚠️  ALERT: Cold Wave Warning!  │");
        }
        if (humidity > 80) {
            System.out.println("│ ⚠️  ALERT: High Humidity!      │");
        }
        System.out.println("└────────────────────────────────┘");
    }
    
    @Override
    public String getName() { return name; }
}

// Concrete Observer 4: Statistics Display (Shows min/max/avg)
class StatisticsDisplay implements WeatherObserver {
    private String name = "📊 Statistics Display";
    private List<Float> tempHistory = new ArrayList<>();
    private float maxTemp = Float.MIN_VALUE;
    private float minTemp = Float.MAX_VALUE;
    private float sumTemp = 0;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        tempHistory.add(temperature);
        sumTemp += temperature;
        
        if (temperature > maxTemp) maxTemp = temperature;
        if (temperature < minTemp) minTemp = temperature;
        
        display();
    }
    
    private void display() {
        float avgTemp = sumTemp / tempHistory.size();
        System.out.println("\n" + name);
        System.out.println("┌─────────────────────────────┐");
        System.out.printf("│ 📈 Max Temp:    %.1f°C      │\n", maxTemp);
        System.out.printf("│ 📉 Min Temp:    %.1f°C      │\n", minTemp);
        System.out.printf("│ 📊 Avg Temp:    %.1f°C      │\n", avgTemp);
        System.out.printf("│ 📋 Readings:    %d           │\n", tempHistory.size());
        System.out.println("└─────────────────────────────┘");
    }
    
    @Override
    public String getName() { return name; }
}

// Concrete Observer 5: Mobile App (Shows "Feels Like")
class MobileAppDisplay implements WeatherObserver {
    private String name = "📲 Mobile App";
    private float temperature;
    private float humidity;
    
    @Override
    public void update(float temperature, float humidity, float pressure) {
        this.temperature = temperature;
        this.humidity = humidity;
        display();
    }
    
    private void display() {
        // Calculate "feels like" temperature (simple formula)
        float feelsLike = temperature;
        if (temperature > 27 && humidity > 40) {
            feelsLike = (float)(temperature + 0.33 * humidity - 0.7 * (humidity/100) - 4);
        }
        
        System.out.println("\n" + name);
        System.out.println("╭─────────────────────────────╮");
        System.out.printf("│ 🌡️  Actual:     %.1f°C      │\n", temperature);
        System.out.printf("│ 🤔 Feels Like:  %.1f°C      │\n", feelsLike);
        System.out.printf("│ 💧 Humidity:    %.1f%%       │\n", humidity);
        System.out.println("╰─────────────────────────────╯");
    }
    
    @Override
    public String getName() { return name; }
}
```

#### Step 5: Client Code (Maza aa gaya!)

```java
public class WeatherApp {
    public static void main(String[] args) {
        System.out.println("=== 🌤️ Weather Monitoring System - Observer Pattern ===\n");
        
        // Create Subject (Weather Station)
        WeatherStation station = new WeatherStation();
        
        // Create Observers (Displays)
        PhoneDisplay phone = new PhoneDisplay();
        TVDisplay tv = new TVDisplay();
        WebDashboard web = new WebDashboard();
        StatisticsDisplay stats = new StatisticsDisplay();
        MobileAppDisplay mobile = new MobileAppDisplay();
        
        // Register Observers
        System.out.println("--- Subscribing Displays ---");
        station.registerObserver(phone);
        station.registerObserver(tv);
        station.registerObserver(web);
        station.registerObserver(stats);
        station.registerObserver(mobile);
        
        // Simulate weather changes
        System.out.println("\n--- Weather Update 1 ---");
        station.setMeasurements(28.5f, 65.0f, 1013.2f);
        
        System.out.println("\n--- Weather Update 2 (Hot!) ---");
        station.setMeasurements(38.2f, 45.0f, 1010.5f);
        
        System.out.println("\n--- Weather Update 3 (Cold!) ---");
        station.setMeasurements(3.5f, 85.0f, 1020.1f);
        
        // Remove an observer
        System.out.println("\n--- Unsubscribing TV Display ---");
        station.removeObserver(tv);
        
        System.out.println("\n--- Weather Update 4 (TV won't receive) ---");
        station.setMeasurements(22.0f, 55.0f, 1015.0f);
        
        // Add new observer dynamically
        System.out.println("\n--- Adding Smart Watch Display ---");
        WeatherObserver smartWatch = new WeatherObserver() {
            @Override
            public void update(float temperature, float humidity, float pressure) {
                System.out.println("\n⌚ Smart Watch");
                System.out.println("┌──────────────┐");
                System.out.printf("│ %.1f°C       │\n", temperature);
                System.out.printf("│ %.1f%% 💧     │\n", humidity);
                System.out.println("└──────────────┘");
            }
            
            @Override
            public String getName() {
                return "⌚ Smart Watch";
            }
        };
        station.registerObserver(smartWatch);
        
        System.out.println("\n--- Weather Update 5 (All active displays) ---");
        station.setMeasurements(19.5f, 70.0f, 1018.0f);
    }
}
```

---

### Part 3: 5 Complete Java Code Examples (Real-World Scenarios)

#### Example 1: YouTube Subscription System (Publisher-Subscriber)

**Scenario:** Channel pe naya video aane par subscribers ko notification jaye.

```java
// File: YouTubeObserver.java
import java.util.*;

// Observer Interface
interface Subscriber {
    void update(String channelName, String videoTitle);
    String getName();
}

// Subject Interface
interface YouTubeChannel {
    void subscribe(Subscriber subscriber);
    void unsubscribe(Subscriber subscriber);
    void notifySubscribers();
    void uploadVideo(String title);
}

// Concrete Subject
class TechChannel implements YouTubeChannel {
    private String channelName;
    private List<Subscriber> subscribers = new ArrayList<>();
    private String latestVideo;
    
    public TechChannel(String name) {
        this.channelName = name;
    }
    
    @Override
    public void subscribe(Subscriber subscriber) {
        subscribers.add(subscriber);
        System.out.println("🔔 " + subscriber.getName() + " subscribed to " + channelName);
    }
    
    @Override
    public void unsubscribe(Subscriber subscriber) {
        subscribers.remove(subscriber);
        System.out.println("🔕 " + subscriber.getName() + " unsubscribed from " + channelName);
    }
    
    @Override
    public void notifySubscribers() {
        System.out.println("\n📢 " + channelName + " uploaded: " + latestVideo);
        for (Subscriber s : subscribers) {
            s.update(channelName, latestVideo);
        }
    }
    
    @Override
    public void uploadVideo(String title) {
        this.latestVideo = title;
        notifySubscribers();
    }
    
    public int getSubscriberCount() {
        return subscribers.size();
    }
}

// Concrete Observers
class EmailSubscriber implements Subscriber {
    private String email;
    
    public EmailSubscriber(String email) {
        this.email = email;
    }
    
    @Override
    public void update(String channelName, String videoTitle) {
        System.out.println("   📧 Sending email to " + email + 
                          ": New video '" + videoTitle + "' from " + channelName);
    }
    
    @Override
    public String getName() {
        return email;
    }
}

class MobileSubscriber implements Subscriber {
    private String deviceId;
    
    public MobileSubscriber(String deviceId) {
        this.deviceId = deviceId;
    }
    
    @Override
    public void update(String channelName, String videoTitle) {
        System.out.println("   📱 Push notification to device " + deviceId + 
                          ": " + channelName + " uploaded '" + videoTitle + "'");
    }
    
    @Override
    public String getName() {
        return "Device-" + deviceId;
    }
}

class PremiumSubscriber implements Subscriber {
    private String username;
    
    public PremiumSubscriber(String username) {
        this.username = username;
    }
    
    @Override
    public void update(String channelName, String videoTitle) {
        System.out.println("   ⭐ Premium user " + username + 
                          " gets early access to: " + videoTitle);
    }
    
    @Override
    public String getName() {
        return username + " (Premium)";
    }
}

public class YouTubeApp {
    public static void main(String[] args) {
        System.out.println("=== 🎥 YouTube Subscription System ===\n");
        
        // Create channels
        TechChannel codeWithHarry = new TechChannel("CodeWithHarry");
        TechChannel traversyMedia = new TechChannel("Traversy Media");
        
        // Create subscribers
        Subscriber rahul = new EmailSubscriber("rahul@gmail.com");
        Subscriber priya = new MobileSubscriber("DEVICE_ABC123");
        Subscriber amit = new PremiumSubscriber("amit_techie");
        Subscriber neha = new EmailSubscriber("neha@yahoo.com");
        
        // Subscribe to channels
        System.out.println("--- Subscribing ---");
        codeWithHarry.subscribe(rahul);
        codeWithHarry.subscribe(priya);
        codeWithHarry.subscribe(amit);
        
        traversyMedia.subscribe(amit);
        traversyMedia.subscribe(neha);
        
        // Upload videos
        System.out.println("\n--- Uploading Videos ---");
        codeWithHarry.uploadVideo("Java Tutorial for Beginners");
        traversyMedia.uploadVideo("React JS Crash Course 2024");
        
        // Unsubscribe and upload again
        System.out.println("\n--- Unsubscribing ---");
        codeWithHarry.unsubscribe(priya);
        
        System.out.println("\n--- New Upload ---");
        codeWithHarry.uploadVideo("Spring Boot Complete Course");
        
        System.out.println("\n📊 Channel Stats:");
        System.out.println("CodeWithHarry subscribers: " + codeWithHarry.getSubscriberCount());
        System.out.println("Traversy Media subscribers: " + traversyMedia.getSubscriberCount());
    }
}
```

---

#### Example 2: Stock Market Ticker (Real-time Price Updates)

**Scenario:** Stock price change hone par saare traders ko update jaye.

```java
// File: StockMarketObserver.java
import java.util.*;

// Observer Interface
interface StockObserver {
    void update(String symbol, double price, double change);
    String getObserverName();
}

// Subject Interface
interface StockMarket {
    void registerObserver(StockObserver observer);
    void removeObserver(StockObserver observer);
    void notifyObservers(String symbol);
    void updateStockPrice(String symbol, double newPrice);
}

// Concrete Subject
class NSEStockMarket implements StockMarket {
    private Map<String, Double> stockPrices = new HashMap<>();
    private Map<String, Double> previousPrices = new HashMap<>();
    private List<StockObserver> observers = new ArrayList<>();
    
    public NSEStockMarket() {
        // Initialize with some stocks
        stockPrices.put("RELIANCE", 2500.0);
        stockPrices.put("TCS", 3800.0);
        stockPrices.put("HDFC", 1600.0);
        stockPrices.put("INFY", 1500.0);
        
        previousPrices.putAll(stockPrices);
    }
    
    @Override
    public void registerObserver(StockObserver observer) {
        observers.add(observer);
        System.out.println("📈 " + observer.getObserverName() + " started tracking stocks");
    }
    
    @Override
    public void removeObserver(StockObserver observer) {
        observers.remove(observer);
        System.out.println("📉 " + observer.getObserverName() + " stopped tracking");
    }
    
    @Override
    public void notifyObservers(String symbol) {
        double currentPrice = stockPrices.get(symbol);
        double previousPrice = previousPrices.get(symbol);
        double change = currentPrice - previousPrice;
        double changePercent = (change / previousPrice) * 100;
        
        for (StockObserver observer : observers) {
            observer.update(symbol, currentPrice, changePercent);
        }
    }
    
    @Override
    public void updateStockPrice(String symbol, double newPrice) {
        if (stockPrices.containsKey(symbol)) {
            previousPrices.put(symbol, stockPrices.get(symbol));
            stockPrices.put(symbol, newPrice);
            
            System.out.println("\n💹 STOCK UPDATE: " + symbol);
            double change = newPrice - previousPrices.get(symbol);
            String direction = change >= 0 ? "▲" : "▼";
            System.out.printf("   %s ₹%.2f %s (%.2f%%)\n", 
                             direction, newPrice, 
                             change >= 0 ? "UP" : "DOWN", 
                             (change / previousPrices.get(symbol)) * 100);
            
            notifyObservers(symbol);
        }
    }
}

// Concrete Observers
class TraderPortfolio implements StockObserver {
    private String traderName;
    private Map<String, Integer> holdings = new HashMap<>();
    private double initialInvestment;
    
    public TraderPortfolio(String name) {
        this.traderName = name;
        // Simulate holdings
        holdings.put("RELIANCE", 10);
        holdings.put("TCS", 5);
        this.initialInvestment = 25000 + 19000; // 10*2500 + 5*3800
    }
    
    @Override
    public void update(String symbol, double price, double changePercent) {
        if (holdings.containsKey(symbol)) {
            int quantity = holdings.get(symbol);
            double currentValue = quantity * price;
            double profit = currentValue - (quantity * getInitialPrice(symbol));
            
            System.out.printf("   👤 %s: %s (%d shares) - Value: ₹%.2f (P/L: ₹%.2f)\n",
                             traderName, symbol, quantity, currentValue, profit);
        }
    }
    
    private double getInitialPrice(String symbol) {
        return symbol.equals("RELIANCE") ? 2500.0 : 3800.0;
    }
    
    @Override
    public String getObserverName() {
        return "Trader: " + traderName;
    }
}

class AlertSystem implements StockObserver {
    private Map<String, Double> alertThresholds = new HashMap<>();
    
    public AlertSystem() {
        alertThresholds.put("RELIANCE", 5.0); // Alert on 5% change
        alertThresholds.put("TCS", 3.0);
    }
    
    @Override
    public void update(String symbol, double price, double changePercent) {
        if (alertThresholds.containsKey(symbol)) {
            double threshold = alertThresholds.get(symbol);
            if (Math.abs(changePercent) >= threshold) {
                System.out.printf("   🚨 ALERT: %s moved by %.2f%% (Threshold: %.2f%%)\n",
                                 symbol, Math.abs(changePercent), threshold);
            }
        }
    }
    
    @Override
    public String getObserverName() {
        return "Alert System";
    }
}

class MobileNotificationService implements StockObserver {
    private String userId;
    
    public MobileNotificationService(String userId) {
        this.userId = userId;
    }
    
    @Override
    public void update(String symbol, double price, double changePercent) {
        String emoji = changePercent >= 0 ? "📈" : "📉";
        System.out.printf("   📱 Push to %s: %s %s is now ₹%.2f (%+.2f%%)\n",
                         userId, emoji, symbol, price, changePercent);
    }
    
    @Override
    public String getObserverName() {
        return "Mobile User: " + userId;
    }
}

public class TradingApp {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 📊 NSE Stock Market Ticker ===\n");
        
        NSEStockMarket nse = new NSEStockMarket();
        
        // Register observers
        TraderPortfolio trader1 = new TraderPortfolio("Rakesh Jhunjhunwala");
        TraderPortfolio trader2 = new TraderPortfolio("Radhakishan Damani");
        AlertSystem alerts = new AlertSystem();
        MobileNotificationService mobileUser = new MobileNotificationService("user_98765");
        
        nse.registerObserver(trader1);
        nse.registerObserver(trader2);
        nse.registerObserver(alerts);
        nse.registerObserver(mobileUser);
        
        // Simulate market movements
        nse.updateStockPrice("RELIANCE", 2625.50); // Up 5%
        
        nse.updateStockPrice("TCS", 3686.00); // Down 3%
        
        nse.updateStockPrice("HDFC", 1648.00); // Up 3%
        
        // Remove one observer
        System.out.println("\n--- " + trader2.getObserverName() + " logs out ---");
        nse.removeObserver(trader2);
        
        nse.updateStockPrice("RELIANCE", 2700.00); // Up again
    }
}
```

---

#### Example 3: E-Commerce Price Drop Alert

**Scenario:** User ne product ko wishlist mein add kiya, price drop hone par notification jaye.

```java
// File: PriceAlertObserver.java
import java.util.*;

// Observer Interface
interface PriceAlertObserver {
    void update(Product product, double oldPrice, double newPrice);
    String getObserverName();
}

// Subject (Product)
class Product {
    private String name;
    private double price;
    private String category;
    private List<PriceAlertObserver> observers = new ArrayList<>();
    
    public Product(String name, double price, String category) {
        this.name = name;
        this.price = price;
        this.category = category;
    }
    
    public void addObserver(PriceAlertObserver observer) {
        observers.add(observer);
        System.out.println("🔔 " + observer.getObserverName() + " set price alert for " + name);
    }
    
    public void removeObserver(PriceAlertObserver observer) {
        observers.remove(observer);
    }
    
    private void notifyObservers(double oldPrice) {
        if (this.price < oldPrice) { // Only notify on price drop
            for (PriceAlertObserver observer : observers) {
                observer.update(this, oldPrice, this.price);
            }
        }
    }
    
    public void setPrice(double newPrice) {
        if (newPrice != this.price) {
            double oldPrice = this.price;
            this.price = newPrice;
            
            if (newPrice < oldPrice) {
                System.out.println("\n💰 PRICE DROP! " + name);
                System.out.printf("   Was: ₹%.2f → Now: ₹%.2f (Save ₹%.2f!)\n", 
                                 oldPrice, newPrice, oldPrice - newPrice);
            }
            notifyObservers(oldPrice);
        }
    }
    
    public String getName() { return name; }
    public double getPrice() { return price; }
    public String getCategory() { return category; }
}

// Concrete Observers
class CustomerAlert implements PriceAlertObserver {
    private String customerName;
    private String email;
    private double targetPrice;
    
    public CustomerAlert(String name, String email, double targetPrice) {
        this.customerName = name;
        this.email = email;
        this.targetPrice = targetPrice;
    }
    
    @Override
    public void update(Product product, double oldPrice, double newPrice) {
        if (newPrice <= targetPrice) {
            System.out.printf("   📧 Email to %s (%s): %s dropped below your target ₹%.2f! " +
                             "Current price: ₹%.2f\n", 
                             customerName, email, product.getName(), targetPrice, newPrice);
        } else {
            System.out.printf("   📧 Email to %s: %s price dropped to ₹%.2f (Still above your target ₹%.2f)\n",
                             customerName, product.getName(), newPrice, targetPrice);
        }
    }
    
    @Override
    public String getObserverName() {
        return customerName;
    }
}

class PushNotificationObserver implements PriceAlertObserver {
    private String deviceToken;
    
    public PushNotificationObserver(String deviceToken) {
        this.deviceToken = deviceToken;
    }
    
    @Override
    public void update(Product product, double oldPrice, double newPrice) {
        double savings = oldPrice - newPrice;
        System.out.printf("   📱 Push to %s...: 🏷️ %s is now ₹%.2f! Save ₹%.2f!\n",
                         deviceToken.substring(0, 8) + "...", 
                         product.getName(), newPrice, savings);
    }
    
    @Override
    public String getObserverName() {
        return "Device-" + deviceToken.substring(0, 8);
    }
}

class CompetitorPriceTracker implements PriceAlertObserver {
    private String competitorName;
    
    public CompetitorPriceTracker(String name) {
        this.competitorName = name;
    }
    
    @Override
    public void update(Product product, double oldPrice, double newPrice) {
        System.out.printf("   🏢 %s Analytics: %s price changed. Market trend: %s\n",
                         competitorName, product.getName(), 
                         newPrice < oldPrice ? "Deflationary" : "Inflationary");
    }
    
    @Override
    public String getObserverName() {
        return competitorName + " (Competitor)";
    }
}

public class ECommercePriceAlert {
    public static void main(String[] args) {
        System.out.println("=== 🛒 E-Commerce Price Alert System ===\n");
        
        // Create products
        Product iPhone = new Product("iPhone 15 Pro", 134900.0, "Electronics");
        Product laptop = new Product("MacBook Air M3", 114900.0, "Electronics");
        Product shoes = new Product("Nike Air Max", 12995.0, "Fashion");
        
        // Create observers
        CustomerAlert rahul = new CustomerAlert("Rahul", "rahul@email.com", 120000.0);
        CustomerAlert priya = new CustomerAlert("Priya", "priya@email.com", 100000.0);
        PushNotificationObserver mobile = new PushNotificationObserver("DEVICE_TOKEN_ABC123XYZ");
        CompetitorPriceTracker amazon = new CompetitorPriceTracker("Amazon");
        
        // Register alerts
        System.out.println("--- Setting Price Alerts ---");
        iPhone.addObserver(rahul);
        iPhone.addObserver(mobile);
        iPhone.addObserver(amazon);
        
        laptop.addObserver(priya);
        laptop.addObserver(amazon);
        
        shoes.addObserver(mobile);
        
        // Simulate price drops
        System.out.println("\n--- Black Friday Sale! ---");
        iPhone.setPrice(119900.0);  // Big drop
        laptop.setPrice(109900.0);  // Small drop (above Priya's target)
        shoes.setPrice(9999.0);     // Drop
        
        // Another price cut
        System.out.println("\n--- Clearance Sale! ---");
        laptop.setPrice(98900.0);   // Now below Priya's target
    }
}
```

---

#### Example 4: Task Management System (Event Bus)

**Scenario:** Task create/update/delete hone par multiple services ko notify karna.

```java
// File: TaskEventObserver.java
import java.util.*;
import java.time.LocalDateTime;

// Event Types
enum TaskEventType {
    CREATED, UPDATED, DELETED, ASSIGNED, COMPLETED
}

// Event Object
class TaskEvent {
    private TaskEventType type;
    private Task task;
    private LocalDateTime timestamp;
    private String performedBy;
    
    public TaskEvent(TaskEventType type, Task task, String performedBy) {
        this.type = type;
        this.task = task;
        this.timestamp = LocalDateTime.now();
        this.performedBy = performedBy;
    }
    
    public TaskEventType getType() { return type; }
    public Task getTask() { return task; }
    public LocalDateTime getTimestamp() { return timestamp; }
    public String getPerformedBy() { return performedBy; }
}

// Task class
class Task {
    private String id;
    private String title;
    private String description;
    private String assignee;
    private boolean completed;
    
    public Task(String id, String title) {
        this.id = id;
        this.title = title;
        this.completed = false;
    }
    
    public String getId() { return id; }
    public String getTitle() { return title; }
    public void setTitle(String title) { this.title = title; }
    public String getDescription() { return description; }
    public void setDescription(String description) { this.description = description; }
    public String getAssignee() { return assignee; }
    public void setAssignee(String assignee) { this.assignee = assignee; }
    public boolean isCompleted() { return completed; }
    public void setCompleted(boolean completed) { this.completed = completed; }
}

// Observer Interface
interface TaskObserver {
    void onTaskEvent(TaskEvent event);
    String getObserverName();
}

// Subject (Task Manager)
class TaskManager {
    private List<TaskObserver> observers = new ArrayList<>();
    private Map<String, Task> tasks = new HashMap<>();
    
    public void addObserver(TaskObserver observer) {
        observers.add(observer);
        System.out.println("📋 " + observer.getObserverName() + " registered for task events");
    }
    
    public void removeObserver(TaskObserver observer) {
        observers.remove(observer);
    }
    
    private void notifyObservers(TaskEvent event) {
        for (TaskObserver observer : observers) {
            observer.onTaskEvent(event);
        }
    }
    
    public Task createTask(String title, String creator) {
        String id = UUID.randomUUID().toString().substring(0, 8);
        Task task = new Task(id, title);
        tasks.put(id, task);
        
        TaskEvent event = new TaskEvent(TaskEventType.CREATED, task, creator);
        System.out.println("\n✨ " + creator + " created task: " + title + " (ID: " + id + ")");
        notifyObservers(event);
        return task;
    }
    
    public void assignTask(String taskId, String assignee, String assignedBy) {
        Task task = tasks.get(taskId);
        if (task != null) {
            task.setAssignee(assignee);
            TaskEvent event = new TaskEvent(TaskEventType.ASSIGNED, task, assignedBy);
            System.out.println("\n👤 Task assigned to " + assignee);
            notifyObservers(event);
        }
    }
    
    public void completeTask(String taskId, String completedBy) {
        Task task = tasks.get(taskId);
        if (task != null) {
            task.setCompleted(true);
            TaskEvent event = new TaskEvent(TaskEventType.COMPLETED, task, completedBy);
            System.out.println("\n✅ Task completed: " + task.getTitle());
            notifyObservers(event);
        }
    }
}

// Concrete Observers
class EmailNotificationObserver implements TaskObserver {
    @Override
    public void onTaskEvent(TaskEvent event) {
        Task task = event.getTask();
        String message = switch (event.getType()) {
            case CREATED -> "New task created: " + task.getTitle();
            case ASSIGNED -> "You've been assigned: " + task.getTitle();
            case COMPLETED -> "Task completed: " + task.getTitle();
            default -> "Task update: " + task.getTitle();
        };
        
        System.out.println("   📧 [EMAIL] " + message + " (Event by: " + event.getPerformedBy() + ")");
    }
    
    @Override
    public String getObserverName() {
        return "Email Service";
    }
}

class SlackNotificationObserver implements TaskObserver {
    @Override
    public void onTaskEvent(TaskEvent event) {
        Task task = event.getTask();
        String emoji = switch (event.getType()) {
            case CREATED -> "🆕";
            case ASSIGNED -> "👤";
            case COMPLETED -> "🎉";
            default -> "📝";
        };
        
        System.out.println("   💬 [SLACK] " + emoji + " " + event.getType() + 
                          ": " + task.getTitle() + " by " + event.getPerformedBy());
    }
    
    @Override
    public String getObserverName() {
        return "Slack Integration";
    }
}

class ActivityLoggerObserver implements TaskObserver {
    private List<String> activityLog = new ArrayList<>();
    
    @Override
    public void onTaskEvent(TaskEvent event) {
        String log = String.format("[%s] %s - Task '%s' %s by %s",
                                  event.getTimestamp(), 
                                  event.getTask().getId(),
                                  event.getTask().getTitle(),
                                  event.getType(),
                                  event.getPerformedBy());
        activityLog.add(log);
        System.out.println("   📝 [LOG] " + log);
    }
    
    @Override
    public String getObserverName() {
        return "Activity Logger";
    }
    
    public void printActivityLog() {
        System.out.println("\n📊 Activity Log:");
        activityLog.forEach(System.out::println);
    }
}

class AnalyticsObserver implements TaskObserver {
    private Map<TaskEventType, Integer> eventCounts = new HashMap<>();
    
    @Override
    public void onTaskEvent(TaskEvent event) {
        eventCounts.merge(event.getType(), 1, Integer::sum);
        System.out.println("   📈 [ANALYTICS] Event count: " + eventCounts);
    }
    
    @Override
    public String getObserverName() {
        return "Analytics Engine";
    }
}

public class TaskManagementApp {
    public static void main(String[] args) {
        System.out.println("=== 📋 Task Management System - Event Bus ===\n");
        
        TaskManager taskManager = new TaskManager();
        
        // Register observers
        EmailNotificationObserver email = new EmailNotificationObserver();
        SlackNotificationObserver slack = new SlackNotificationObserver();
        ActivityLoggerObserver logger = new ActivityLoggerObserver();
        AnalyticsObserver analytics = new AnalyticsObserver();
        
        taskManager.addObserver(email);
        taskManager.addObserver(slack);
        taskManager.addObserver(logger);
        taskManager.addObserver(analytics);
        
        // Create and manage tasks
        Task task1 = taskManager.createTask("Implement Login Feature", "Rahul");
        taskManager.assignTask(task1.getId(), "Priya", "Rahul");
        taskManager.completeTask(task1.getId(), "Priya");
        
        Task task2 = taskManager.createTask("Fix Payment Gateway Bug", "Amit");
        taskManager.assignTask(task2.getId(), "Neha", "Amit");
        
        // Print activity log
        logger.printActivityLog();
    }
}
```

---

#### Example 5: Chat Room with Multiple Participants

**Scenario:** Group chat jahan ek user message bheje to saare online users ko receive ho.

```java
// File: ChatRoomObserver.java
import java.util.*;
import java.time.LocalTime;
import java.time.format.DateTimeFormatter;

// Message class
class ChatMessage {
    private String sender;
    private String content;
    private LocalTime timestamp;
    private String roomName;
    
    public ChatMessage(String sender, String content, String roomName) {
        this.sender = sender;
        this.content = content;
        this.roomName = roomName;
        this.timestamp = LocalTime.now();
    }
    
    public String getSender() { return sender; }
    public String getContent() { return content; }
    public LocalTime getTimestamp() { return timestamp; }
    public String getRoomName() { return roomName; }
}

// Observer Interface
interface ChatObserver {
    void onMessage(ChatMessage message);
    void onUserJoined(String username);
    void onUserLeft(String username);
    String getUsername();
}

// Subject (Chat Room)
class ChatRoom {
    private String roomName;
    private List<ChatObserver> participants = new ArrayList<>();
    private List<ChatMessage> messageHistory = new ArrayList<>();
    
    public ChatRoom(String name) {
        this.roomName = name;
    }
    
    public void join(ChatObserver user) {
        participants.add(user);
        System.out.println("\n🚪 " + user.getUsername() + " joined " + roomName);
        notifyUserJoined(user.getUsername());
        
        // Send recent message history to new user
        System.out.println("   [Sending last 3 messages to " + user.getUsername() + "]");
        int start = Math.max(0, messageHistory.size() - 3);
        for (int i = start; i < messageHistory.size(); i++) {
            user.onMessage(messageHistory.get(i));
        }
    }
    
    public void leave(ChatObserver user) {
        participants.remove(user);
        System.out.println("\n👋 " + user.getUsername() + " left " + roomName);
        notifyUserLeft(user.getUsername());
    }
    
    private void notifyUserJoined(String username) {
        for (ChatObserver participant : participants) {
            if (!participant.getUsername().equals(username)) {
                participant.onUserJoined(username);
            }
        }
    }
    
    private void notifyUserLeft(String username) {
        for (ChatObserver participant : participants) {
            participant.onUserLeft(username);
        }
    }
    
    public void sendMessage(ChatObserver sender, String content) {
        ChatMessage message = new ChatMessage(sender.getUsername(), content, roomName);
        messageHistory.add(message);
        
        System.out.println("\n💬 [" + roomName + "] " + sender.getUsername() + ": " + content);
        
        for (ChatObserver participant : participants) {
            // Don't send message back to sender
            if (!participant.getUsername().equals(sender.getUsername())) {
                participant.onMessage(message);
            }
        }
    }
    
    public int getParticipantCount() {
        return participants.size();
    }
}

// Concrete Observers (Chat Users)
class ChatUser implements ChatObserver {
    private String username;
    private String status = "🟢 Online";
    private DateTimeFormatter timeFormat = DateTimeFormatter.ofPattern("HH:mm");
    
    public ChatUser(String username) {
        this.username = username;
    }
    
    @Override
    public void onMessage(ChatMessage message) {
        String time = message.getTimestamp().format(timeFormat);
        System.out.println("   📨 [" + username + "'s screen] " + 
                          message.getSender() + " (" + time + "): " + message.getContent());
    }
    
    @Override
    public void onUserJoined(String username) {
        System.out.println("   👤 [" + this.username + "'s screen] " + username + " joined the chat");
    }
    
    @Override
    public void onUserLeft(String username) {
        System.out.println("   👤 [" + this.username + "'s screen] " + username + " left the chat");
    }
    
    @Override
    public String getUsername() {
        return username;
    }
    
    public void setStatus(String status) {
        this.status = status;
    }
}

// Specialized Observer: Chat Bot
class ChatBot implements ChatObserver {
    private String botName;
    private Map<String, String> responses = new HashMap<>();
    
    public ChatBot(String name) {
        this.botName = name;
        // Predefined responses
        responses.put("hello", "Hello! How can I help you?");
        responses.put("help", "Available commands: hello, time, weather, joke");
        responses.put("time", "Current time is " + LocalTime.now().format(DateTimeFormatter.ofPattern("HH:mm:ss")));
        responses.put("weather", "It's always sunny in the cloud! ☀️");
        responses.put("joke", "Why do Java developers wear glasses? Because they don't C#! 😄");
    }
    
    @Override
    public void onMessage(ChatMessage message) {
        String content = message.getContent().toLowerCase();
        
        for (Map.Entry<String, String> entry : responses.entrySet()) {
            if (content.contains(entry.getKey())) {
                System.out.println("   🤖 [" + botName + "] Responding to: " + message.getContent());
                // Bot would send message back to room
                break;
            }
        }
    }
    
    @Override
    public void onUserJoined(String username) {
        System.out.println("   🤖 [" + botName + "] Welcome " + username + "!");
    }
    
    @Override
    public void onUserLeft(String username) {
        // Bot doesn't react to leaves
    }
    
    @Override
    public String getUsername() {
        return botName;
    }
}

// Specialized Observer: Message Logger
class MessageLogger implements ChatObserver {
    private List<String> log = new ArrayList<>();
    
    @Override
    public void onMessage(ChatMessage message) {
        String logEntry = String.format("[%s] %s: %s", 
                                       message.getTimestamp(), 
                                       message.getSender(), 
                                       message.getContent());
        log.add(logEntry);
    }
    
    @Override
    public void onUserJoined(String username) {
        log.add("[SYSTEM] " + username + " joined");
    }
    
    @Override
    public void onUserLeft(String username) {
        log.add("[SYSTEM] " + username + " left");
    }
    
    @Override
    public String getUsername() {
        return "Logger";
    }
    
    public void exportLog() {
        System.out.println("\n📝 Chat Log:");
        log.forEach(System.out::println);
    }
}

public class ChatApplication {
    public static void main(String[] args) {
        System.out.println("=== 💬 Group Chat Application ===\n");
        
        // Create chat room
        ChatRoom general = new ChatRoom("General");
        
        // Create users
        ChatUser rahul = new ChatUser("Rahul");
        ChatUser priya = new ChatUser("Priya");
        ChatUser amit = new ChatUser("Amit");
        ChatBot bot = new ChatBot("HelperBot");
        MessageLogger logger = new MessageLogger();
        
        // Users join
        general.join(rahul);
        general.join(priya);
        general.join(bot);
        general.join(logger);
        
        // Send messages
        general.sendMessage(rahul, "Hello everyone!");
        general.sendMessage(priya, "Hi Rahul! How are you?");
        general.sendMessage(rahul, "I'm good! @HelperBot tell me a joke");
        general.sendMessage(bot, "Here's a joke: Why do Java developers wear glasses? Because they don't C#!");
        
        // New user joins late
        general.join(amit);
        general.sendMessage(amit, "Hey guys, sorry I'm late!");
        
        // User leaves
        general.leave(priya);
        
        // More messages
        general.sendMessage(rahul, "Anyone still here?");
        
        // Print log
        logger.exportLog();
        
        System.out.println("\n📊 Room stats: " + general.getParticipantCount() + " participants");
    }
}
```

---

### Part 6: Observer Pattern - Summary & Interview Points

| Aspect | Details |
|:---|:---|
| **Problem Solved** | One-to-many dependency, automatic notification |
| **Use When** | State change of one object affects many others |
| **Key Components** | Subject (Observable), Observer, ConcreteSubject, ConcreteObserver |
| **Real Examples** | Event Listeners, Pub/Sub, Reactive Programming |

**Interview Question:** *What is Observer Pattern?*

**Answer:** *"Sir, Observer pattern defines a one-to-many dependency between objects so that when one object (Subject) changes state, all its dependents (Observers) are notified and updated automatically. It's the foundation of event-driven programming. Example: Java's `java.util.Observable` (deprecated but concept remains), EventListener in Swing, and Pub/Sub systems."*

**Interview Question:** *Push vs Pull Model in Observer Pattern?*

| Push Model | Pull Model |
|:---|:---|
| Subject sends all data with notification | Subject only notifies, Observer pulls data |
| Observer gets data even if not needed | Observer requests only what it needs |
| Less flexible, more network traffic | More flexible, less coupling |

**Interview Question:** *What are the disadvantages of Observer Pattern?*

1. **Memory Leaks:** Observers must be explicitly removed (Lapsed Listener Problem).
2. **Unexpected Updates:** Cascade of updates can occur.
3. **Order of Notification:** Not guaranteed, can cause issues.

**Interview Question:** *Difference between Observer and Pub/Sub Pattern?*

| Observer | Pub/Sub |
|:---|:---|
| Subject knows about observers | Publisher and subscriber don't know each other |
| Usually in same process | Usually across network via Message Broker |
| Tight coupling via interface | Loose coupling via topics/channels |

**Java's Built-in Support (Legacy):**
```java
// Old Java way (Deprecated but good for interviews)
import java.util.Observable;
import java.util.Observer;

class MyObservable extends Observable {
    public void change() {
        setChanged();
        notifyObservers("Data changed!");
    }
}
```

**Modern Java Way (Better):**
```java
// Using PropertyChangeSupport
import java.beans.PropertyChangeListener;
import java.beans.PropertyChangeSupport;

class ModernSubject {
    private PropertyChangeSupport support = new PropertyChangeSupport(this);
    
    public void addListener(PropertyChangeListener listener) {
        support.addPropertyChangeListener(listener);
    }
    
    public void setValue(String newValue) {
        String oldValue = this.value;
        this.value = newValue;
        support.firePropertyChange("value", oldValue, newValue);
    }
}
```

**Reactive Programming (RxJava, Project Reactor):**
```java
// Modern reactive approach
Flux<String> stream = Flux.just("Hello", "World");
stream.subscribe(
    data -> System.out.println("Received: " + data),
    error -> System.err.println("Error: " + error),
    () -> System.out.println("Completed!")
);
```
