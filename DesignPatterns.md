# DesignPatterns

---------------

## Creational Design Patterns


### Abstract Factory Pattern

*  The Abstract Factory Pattern is a creational design pattern that provides an interface for creating `families` of related or dependent objects without specifying their concrete classes,
  in simpler terms the Abstract Factory Pattern is a way of organizing how you create groups of things that are related to each other.
*  We are solving Open-Close Principle and Liskof Sub.
*  Which Means we are abstracting the factory creation using a Factory. Is object Scope pattern, we are using Association as the predominatne Idea,
*  The Abstract Factory Pattern is a creational design pattern that provides an interface for creating `families` of related or dependent objects without specifying their concrete classes.
*  Example: Computer-A needs Memory-A, CPU-A
  ```java
    //Version 1
    ComputerA computer1 = new ComputerA();
    computer1.createComputer(); // hard couping to ModelA

    // Computer Class
    public class ComputerA{
      addMemoryA(){}
      addCPUA(){}
      createComputer(){
        this.cpu = new CPUA();
        this.memory = mew MemoryA();
      }
    }

  // Iteration 2

    ComputerA computer1 = new ComputerA();
    ComputerB computer2 = new ComputerB();

    computer1.createComputer(); // hard couping to ModelA
    computer2.createComputer(); // hard couping to ModelB

    // Computer Class
    public class ComputerA{
      addMemoryA(){}
      addCPUA(){}
      createComputer(){
        this.cpu = new CPUA();
        this.memory = mew MemoryA();
      }
    }
    public class ComputerB{
      addMemoryB(){}
      addCPUB(){}
      createComputer(){
        this.cpu = new CPUB();
        this.memory = mew MemoryB();
      }
    }

// Voiliated the DRY principle
// Iteration 3

// Create Abstrat Classed for Memory and CPU and move the common methods to the Abstract Class.
    public abstract class Memory{
       toString(){
        // code to print memory details.
      }
    }

    public class MemoryA extends Memory{
    }
    public class MemoryB extends Memory{
    }

// Create a the abstract class for Computer

    public abstract class Computer{
      Memory memory;
      CPU cpu;
      createComputer();
    }

    Computer computer1 = new ComputerA();
    Computer computer2 = new ComputerB();

    computer1.createComputer();
    computer2.createComputer();

// Issues here, DRY is voilated as the createComputer methos is duplicated in both the classes. Also, if a new Model is implemented we have to dup the code again.
// Iteration 4

    Computer computer = new ComputerA();

    computer.createComputer();
    computer2.createComputer();

// Iteration 5

ComputerFactory computerFactory;
ComputerFactoryA computerFactory;
ComputerFactoryB computerFactory;

public interface ComputerFactory{
  createComputer();
  addCPU();
  addMemory();
}

ComputerFactoryA implements ComputerFactory{
  createFactory(Computer c){
    c.addCPU(getCPU());
    c.addMemory();
  }
  getCPU(){
    CPU cpu = new CPUA();
    return cpu;
  }
}
```

----------------


### Factory Method Pattern

*    In case of Abstract Factory the createComputer() methos which is the cleint is dependent of the AbstractFactory Class, to generate the objects, which is an association relationship. Where as incase of Factory Method, the clkient will depend pn its subclass, which is Inheritance.
*    Also know as Virtural COnstructor.
*    Inca se of AF the client is outside where as in case of FM the client is within the Computer class itself.
*    For exmaple
    *    There is an Employee class, with method getReadyForTavel(), which books Car, Hotel and FlightBooking. 
    *    The above Eployee class is abstract and the getReadyForTavel() each subClass, like base Emploiee, ExpEployee etc. So the base eployee will have a economy level booksing for FlightBooking (EcoFlightBooking which is subclass of FlightBooking) etc.
    *     
          


---------------

### Builder Pattern Pattern

The **Builder Pattern** is a creational design pattern used to construct complex objects step by step. Unlike other creational patterns, the Builder pattern focuses on the construction process, allowing the creation of a product with varying configurations or combinations of attributes without a telescoping constructor (a constructor with too many parameters).

### Key Idea
The Builder pattern separates the **construction of an object** from its **representation**, allowing you to build complex objects in a controlled, step-by-step manner. It’s particularly useful when the object to be constructed has many optional parameters, or when the construction process is complex and needs to be broken down into steps.

### Builder Pattern Example

Let’s consider a simple example of building a **House** that can have various configurations (e.g., number of rooms, type of roof, garage, etc.).

#### Step 1: Define the `House` class (the product)

```java
public class House {
    private String foundation;
    private String structure;
    private String roof;
    private boolean hasGarage;
    private boolean hasSwimmingPool;
    private int rooms;

    // Private constructor to enforce the use of the Builder
    private House(HouseBuilder builder) {
        this.foundation = builder.foundation;
        this.structure = builder.structure;
        this.roof = builder.roof;
        this.hasGarage = builder.hasGarage;
        this.hasSwimmingPool = builder.hasSwimmingPool;
        this.rooms = builder.rooms;
    }

    @Override
    public String toString() {
        return "House [foundation=" + foundation + ", structure=" + structure + ", roof=" + roof 
                + ", hasGarage=" + hasGarage + ", hasSwimmingPool=" + hasSwimmingPool + ", rooms=" + rooms + "]";
    }

    // Static nested Builder class
    public static class HouseBuilder {
        private String foundation;
        private String structure;
        private String roof;
        private boolean hasGarage;
        private boolean hasSwimmingPool;
        private int rooms;

        public HouseBuilder(String foundation, String structure) {
            this.foundation = foundation;
            this.structure = structure;
        }

        public HouseBuilder setRoof(String roof) {
            this.roof = roof;
            return this;
        }

        public HouseBuilder setGarage(boolean hasGarage) {
            this.hasGarage = hasGarage;
            return this;
        }

        public HouseBuilder setSwimmingPool(boolean hasSwimmingPool) {
            this.hasSwimmingPool = hasSwimmingPool;
            return this;
        }

        public HouseBuilder setRooms(int rooms) {
            this.rooms = rooms;
            return this;
        }

        public House build() {
            return new House(this);
        }
    }
}
```

In this example, we have:
- A **`House` class** with various attributes (`foundation`, `structure`, `roof`, etc.).
- A **`HouseBuilder` nested static class** that provides methods to set these attributes and construct the final `House` object.
- The `build()` method returns the fully constructed `House`.

#### Step 2: Using the Builder

```java
public class BuilderPatternExample {
    public static void main(String[] args) {
        House house = new House.HouseBuilder("Concrete", "Brick")
                            .setRoof("Shingle")
                            .setGarage(true)
                            .setRooms(4)
                            .setSwimmingPool(true)
                            .build();

        System.out.println(house);
    }
}
```

#### Output:
```
House [foundation=Concrete, structure=Brick, roof=Shingle, hasGarage=true, hasSwimmingPool=true, rooms=4]
```

### Why Use the Builder Pattern?

Without the builder pattern, we might face several issues, especially if we use constructors with multiple parameters (often called **telescoping constructors**). These issues violate key design principles.

### Issues Without the Builder Pattern

1. **Telescoping Constructor Problem**:
   Without the builder, if you want to provide optional parameters, you would need to write multiple constructors. As the number of optional parameters grows, the constructors become unmanageable and confusing.

   ```java
   public class House {
       public House(String foundation, String structure) {}
       public House(String foundation, String structure, String roof) {}
       public House(String foundation, String structure, String roof, boolean hasGarage) {}
       public House(String foundation, String structure, String roof, boolean hasGarage, boolean hasSwimmingPool) {}
       // And so on...
   }
   ```

   This approach leads to code that's hard to read and maintain, and you might end up writing many overloaded constructors just to accommodate all possible parameter combinations.

2. **Violates Single Responsibility Principle (SRP)**:
   The **Single Responsibility Principle** states that a class should have one reason to change. When using constructors to handle all possible combinations of attributes, the constructor becomes responsible for multiple things, i.e., handling both mandatory and optional attributes. This increases complexity and violates the SRP because the class is doing more than just defining the house—it’s also managing the object creation.

3. **Violates Open-Closed Principle (OCP)**:
   The **Open-Closed Principle** (from SOLID) says that classes should be open for extension but closed for modification. When you use constructors for object creation, every time you add a new optional parameter, you have to modify the existing constructor or add a new one. This means modifying the class each time there's a change in the parameters, which violates OCP.

4. **Reduces Readability and Maintainability**:
   Constructors with too many parameters (especially if they're all the same type) are hard to read and understand. It’s difficult to know which argument corresponds to which property when reading the code. For example, the following constructor call is ambiguous:

   ```java
   House house = new House("Concrete", "Brick", "Shingle", true, false, 4);
   ```
   What does `true` or `false` represent? Is it the garage or swimming pool?

5. **Violates Interface Segregation Principle (ISP)**:
   By using constructors with too many parameters, you're essentially forcing clients (the users of your `House` class) to provide all arguments, even when some of them may be optional. This leads to a situation where clients are burdened with providing unnecessary data, violating the **Interface Segregation Principle**, which states that clients should not be forced to depend on methods or parameters they do not use.

### Advantages of Using the Builder Pattern

1. **Handles Complex Construction Logic**:
   The Builder pattern breaks down the construction of complex objects into smaller steps, each of which is manageable and customizable. It’s useful for objects that have multiple configurations or require specific sequences of initialization.

2. **Respects Open-Closed Principle**:
   The base class `House` doesn't need to be modified when new optional parameters are added. Instead, we extend the builder, keeping the `House` class closed for modification and open for extension.

3. **Adheres to Single Responsibility Principle**:
   The responsibility for creating an object is moved to the `Builder` class, which allows the `House` class to focus solely on representing the product, adhering to the SRP.

4. **Increases Readability and Flexibility**:
   With the builder, the object creation process is clearly outlined and much more readable. It’s easy to understand what each parameter represents:

   ```java
   House house = new House.HouseBuilder("Concrete", "Brick")
                         .setRoof("Shingle")
                         .setGarage(true)
                         .setRooms(4)
                         .build();
   ```
   This improves maintainability and prevents the misuse of constructor arguments.

5. **Immutable Objects**:
   The builder pattern can help create immutable objects since all fields of the product (`House`) can be `final`, and the object is constructed in a controlled manner through the builder, ensuring immutability.

### Conclusion
The **Builder Pattern** helps avoid constructor overloads and telescoping constructors, promotes code clarity, and adheres to key design principles like the **Single Responsibility Principle** and the **Open-Closed Principle**. It’s especially useful when you have complex objects with multiple optional parameters.


-----------------

### Singleton Pattern


The **Singleton Pattern** is a creational design pattern that ensures a class has **only one instance** and provides a global point of access to it. It is used when you want to control the instantiation of a class to ensure that only one object of that class is ever created throughout the application's lifecycle.

### Key Characteristics of the Singleton Pattern:
1. **Single Instance**: The class itself controls the creation of its sole instance.
2. **Global Access**: Provides a way to access the single instance globally, typically through a static method.

### Singleton Example

Let’s consider a scenario where we need a **Configuration Manager** that reads and provides configuration settings to the entire application. Since the configuration should be loaded only once, it makes sense to use a Singleton pattern.

#### Step 1: Singleton Class Implementation

```java
public class ConfigurationManager {
    // Step 1: Create a private static variable to hold the single instance
    private static ConfigurationManager instance;

    // Step 2: Private constructor to prevent instantiation from outside
    private ConfigurationManager() {
        // Load configuration from file or database
        System.out.println("Loading configuration settings...");
    }

    // Step 3: Public static method to provide the single instance (Lazy Initialization)
    public static ConfigurationManager getInstance() {
        if (instance == null) {
            instance = new ConfigurationManager();
        }
        return instance;
    }

    public void printConfiguration() {
        System.out.println("Configuration: {API_URL: 'https://api.example.com', TIMEOUT: 5000}");
    }
}
```

#### Step 2: Accessing the Singleton

```java
public class SingletonExample {
    public static void main(String[] args) {
        // Fetch the single instance of ConfigurationManager
        ConfigurationManager config1 = ConfigurationManager.getInstance();
        ConfigurationManager config2 = ConfigurationManager.getInstance();

        // Use the instance to access configuration settings
        config1.printConfiguration();

        // Verify both variables point to the same instance
        System.out.println(config1 == config2);  // Output: true
    }
}
```

### Explanation:
- The `ConfigurationManager` class has a **private constructor** to prevent instantiation from outside the class.
- The `getInstance()` method ensures that only **one instance** of the class is created (lazy initialization, meaning the instance is created when first requested).
- Both `config1` and `config2` point to the same instance, proving that the class is a singleton.

### Issues If Singleton Pattern Is Not Used:
Without the Singleton pattern, we would face issues related to object instantiation, leading to violations of key design principles.

#### 1. **Multiple Instances**:
   - If multiple instances of the `ConfigurationManager` class are created, each could load different or conflicting configurations, leading to inconsistencies in the application.
   - In cases like logging, database connections, or configurations, having multiple instances could lead to resource wastage, inconsistent behavior, and race conditions.

#### 2. **Violates Single Responsibility Principle (SRP)**:
   - If a class is responsible for both its core functionality (like managing configurations) and also managing multiple instantiations, it violates the **Single Responsibility Principle**. The responsibility of controlling the number of instances should not be mixed with the class's primary responsibility.
   - By using Singleton, the class focuses only on managing its core functionality (in this case, configuration management), while the Singleton mechanism ensures instance control.

#### 3. **Global State Mismanagement**:
   - Without Singleton, different parts of the application could create different instances of a class, leading to mismanagement of global state. For example, if one part of the application updates a configuration setting but other parts hold references to different instances, they may not reflect the latest changes.

#### 4. **Resource Wastage**:
   - For classes like database connections or thread pools, creating multiple instances wastes resources and can degrade performance. The Singleton pattern ensures that only **one instance** handles such resources efficiently.

#### 5. **Tight Coupling**:
   - If classes that need a single instance of `ConfigurationManager` or other services were manually instantiated in each class, this could create tight coupling between different classes. The Singleton pattern helps decouple instantiation logic from the client code by providing a global point of access.

#### 6. **Open-Closed Principle (OCP) Violations**:
   - Without Singleton, client code might rely on direct instantiation of the class (using `new` keyword), which violates the **Open-Closed Principle**. To ensure the class remains flexible, Singleton controls how the class is instantiated without needing modification to client code when implementation changes are made.

### Principles Violated Without the Singleton Pattern:
1. **Single Responsibility Principle (SRP)**:
   - Without Singleton, the class might have to manage both its core functionality and the instantiation control. By applying Singleton, the class focuses only on its core responsibility (e.g., managing configuration).

2. **Open-Closed Principle (OCP)**:
   - If client code needs to change how an object is instantiated every time new requirements emerge, it violates the OCP. The Singleton Pattern encapsulates the instantiation logic, preventing modifications to client code when changes occur.

3. **Global State Control**:
   - Without Singleton, global shared resources like configurations or database connections may be mismanaged, leading to inconsistent global state.

### Thread-Safe Singleton (Optional Extension)
In multi-threaded environments, care should be taken to ensure that the Singleton pattern is **thread-safe**. Here’s an example of a thread-safe Singleton using **synchronized**:

```java
public class ConfigurationManager {
    private static ConfigurationManager instance;

    // Private constructor
    private ConfigurationManager() {
        System.out.println("Loading configuration settings...");
    }

    // Synchronized method for thread-safety
    public static synchronized ConfigurationManager getInstance() {
        if (instance == null) {
            instance = new ConfigurationManager();
        }
        return instance;
    }
}
```

In this version, the `getInstance()` method is synchronized to prevent multiple threads from creating more than one instance simultaneously. However, synchronization can introduce performance overhead. A better alternative for thread-safety is **double-checked locking**.

### Conclusion:
The **Singleton Pattern** ensures that a class has only one instance, making it ideal for managing shared resources like configurations, database connections, or logging systems. Without it, principles like **Single Responsibility**, **Open-Closed Principle**, and **global state management** could be violated.


----


## Structural design pattern

*    These pattern are concerned about how the classes interact with each other.
*    `Class patterns use Inheritance to `compose` Interface or implementation.
*    `Object` patterns describe ways to `compose` objects to reliaze new functionality.

--------------------
### Facade Pattern

The **Facade Pattern** is a structural design pattern that provides a simplified, unified interface to a complex system of classes, libraries, or frameworks. It hides the complexities of the system and provides a single entry point through which the client can interact with the subsystem, making it easier to use.

### Key Idea:
The Facade Pattern acts as a high-level interface that simplifies interactions with complex subsystems, so the client doesn't have to deal with the intricacies of multiple classes or libraries.

### Simple Example of Facade Pattern

Let’s consider a **home entertainment system** with multiple subsystems like a DVD player, projector, sound system, and lighting. Instead of interacting with each device separately, a **Facade** provides a unified interface to start or stop the entire system.

#### Step 1: Subsystems (DVDPlayer, Projector, SoundSystem, Lights)

These are the individual components of the entertainment system, each with its own complex interface.

```java
// Subsystem class: DVD player
public class DVDPlayer {
    public void on() {
        System.out.println("DVD player turned on.");
    }

    public void play(String movie) {
        System.out.println("Playing movie: " + movie);
    }

    public void off() {
        System.out.println("DVD player turned off.");
    }
}

// Subsystem class: Projector
public class Projector {
    public void on() {
        System.out.println("Projector turned on.");
    }

    public void setWideScreenMode() {
        System.out.println("Projector in widescreen mode.");
    }

    public void off() {
        System.out.println("Projector turned off.");
    }
}

// Subsystem class: Sound system
public class SoundSystem {
    public void on() {
        System.out.println("Sound system turned on.");
    }

    public void setVolume(int level) {
        System.out.println("Sound system volume set to: " + level);
    }

    public void off() {
        System.out.println("Sound system turned off.");
    }
}

// Subsystem class: Lights
public class Lights {
    public void dim(int level) {
        System.out.println("Lights dimmed to: " + level + "%");
    }
}
```

#### Step 2: Facade (`HomeTheaterFacade`)

The Facade simplifies interactions with all the subsystems by providing a unified interface. This class hides the complexity and exposes only simple methods like `watchMovie()` and `endMovie()`.

```java
// Facade class that simplifies interaction with subsystems
public class HomeTheaterFacade {
    private DVDPlayer dvdPlayer;
    private Projector projector;
    private SoundSystem soundSystem;
    private Lights lights;

    public HomeTheaterFacade(DVDPlayer dvdPlayer, Projector projector, SoundSystem soundSystem, Lights lights) {
        this.dvdPlayer = dvdPlayer;
        this.projector = projector;
        this.soundSystem = soundSystem;
        this.lights = lights;
    }

    public void watchMovie(String movie) {
        System.out.println("Get ready to watch a movie...");
        lights.dim(30);
        projector.on();
        projector.setWideScreenMode();
        soundSystem.on();
        soundSystem.setVolume(10);
        dvdPlayer.on();
        dvdPlayer.play(movie);
    }

    public void endMovie() {
        System.out.println("Shutting down the movie theater...");
        dvdPlayer.off();
        soundSystem.off();
        projector.off();
        lights.dim(100);
    }
}
```

#### Step 3: Client Code

The client interacts with the Facade, not the individual subsystems. The Facade simplifies the client’s job by exposing simple methods.

```java
public class FacadePatternDemo {
    public static void main(String[] args) {
        // Subsystems
        DVDPlayer dvdPlayer = new DVDPlayer();
        Projector projector = new Projector();
        SoundSystem soundSystem = new SoundSystem();
        Lights lights = new Lights();

        // Facade
        HomeTheaterFacade homeTheater = new HomeTheaterFacade(dvdPlayer, projector, soundSystem, lights);

        // Use the facade to control the entire system
        homeTheater.watchMovie("Inception");
        homeTheater.endMovie();
    }
}
```

### Output:
```plaintext
Get ready to watch a movie...
Lights dimmed to: 30%
Projector turned on.
Projector in widescreen mode.
Sound system turned on.
Sound system volume set to: 10
DVD player turned on.
Playing movie: Inception
Shutting down the movie theater...
DVD player turned off.
Sound system turned off.
Projector turned off.
Lights dimmed to: 100%
```

### Explanation:

1. **Subsystems**: `DVDPlayer`, `Projector`, `SoundSystem`, and `Lights` are independent subsystems, each with its own interface and methods.
2. **Facade**: The `HomeTheaterFacade` provides a simple interface to control the complex interactions between these subsystems.
3. **Client**: The client interacts only with the `HomeTheaterFacade`, calling `watchMovie()` to set up everything and `endMovie()` to shut everything down.

### Benefits of the Facade Pattern:

1. **Simplifies Usage**:
   The Facade provides a unified interface to interact with a complex subsystem, making it easier for clients to use.

2. **Loose Coupling**:
   The client interacts with the Facade and is decoupled from the specific implementation details of the subsystems. This makes the system easier to maintain and modify.

3. **Hides Complexity**:
   The complexity of dealing with multiple subsystems is hidden behind the Facade. The client doesn't need to understand how the subsystems work or interact.

### Issues Without the Facade Pattern:

1. **Complex Interactions**:
   Without the Facade, the client would have to interact with all the subsystems directly (`DVDPlayer`, `Projector`, etc.). This increases the complexity of the client code and makes it more error-prone.
   
2. **Tight Coupling**:
   Without a Facade, the client is tightly coupled to multiple subsystems. Any change in the subsystem's interface (e.g., if the projector changes its method for setting wide-screen mode) would require changes in all clients interacting with that subsystem, violating **Loose Coupling**.

3. **Violates Single Responsibility Principle (SRP)**:
   The client would be responsible for orchestrating all the interactions between subsystems. This adds too much responsibility to the client, violating the **Single Responsibility Principle**. By introducing a Facade, the client delegates this responsibility to the Facade.

4. **Violates Open-Closed Principle (OCP)**:
   If the client has to interact with multiple subsystems directly, and a new subsystem is introduced (for example, adding a `PopcornMaker` subsystem), the client code would need to be modified. This violates the **Open-Closed Principle** because the client is not closed to modification. With a Facade, the client remains unchanged, and you can extend the Facade without affecting the client.

### Principles Violated Without the Facade Pattern:

1. **Single Responsibility Principle (SRP)**:
   - Without the Facade, the client is responsible for managing the interactions between multiple subsystems. This mixes responsibilities—file handling and interaction logic. With the Facade, the client has only one responsibility: to call high-level methods.

2. **Open-Closed Principle (OCP)**:
   - Without the Facade, modifying or adding new subsystems (like a new sound system) would require changes in the client code. This violates OCP because the client should be closed to modification. By using a Facade, the client code doesn't change when new functionality is added, as it's all handled by the Facade.

3. **Dependency Inversion Principle (DIP)**:
   - Without the Facade, the client depends directly on concrete implementations of multiple subsystems, leading to tight coupling. The Facade acts as an intermediary that abstracts these dependencies, so the client only depends on the Facade (which is an abstraction), and not the concrete subsystem classes.

### Conclusion:
The **Facade Pattern** provides a high-level interface to a complex system, making it easier to use and maintain. It simplifies client interaction by hiding the intricacies of the underlying subsystems. Without the Facade, your code could become tightly coupled to complex subsystems, violate key principles like **Single Responsibility**, **Open-Closed**, and **Dependency Inversion**, and become harder to maintain and extend.

-----------------

### Bridge Pattern

Decouple an abstraction from its implementationso that the two can vary indepencently.
For example , An application wants to be able to use one of the sertveal DBs available. However, each DB has different API. how to write one set of code such that the code is not affected by which DB is used or when a new DB is considered.


-----------------


### Adapter Pattern

Let's implement the **Adapter Pattern** using a file processor system that processes **JSON** and **XML** files. The system is initially designed to handle **JSON** files, but you want to extend it to handle **XML** files without modifying the existing client code. The Adapter will allow the client (file processor) to work with XML files as if they were JSON files.

Convert the interface of a class into another interface clients expect. Adapter lets classes work together that coluldnt otherwise because of incompatible interfaces.

### Scenario:
- **JSONProcessor**: The system currently supports only JSON file processing.
- **XMLProcessor**: You want to add support for XML files.
- **Adapter**: The adapter will convert XML file processing so that the system can treat it as JSON processing.

### Step 1: Define the Target Interface (`FileProcessor`)

The client expects this interface for processing files, which processes JSON files.

```java
// Target interface representing the common interface for file processors
public interface FileProcessor {
    void process(String data);
}
```

### Step 2: Implement the JSON Processor (`JSONProcessor`)

This is the existing class that processes JSON data.

```java
// Concrete implementation for processing JSON files
public class JSONProcessor implements FileProcessor {
    @Override
    public void process(String data) {
        System.out.println("Processing JSON file: " + data);
    }
}
```

### Step 3: Create the Adaptee (`XMLProcessor`)

This is the class for processing XML files, but its method is incompatible with the `FileProcessor` interface.

```java
// Incompatible class for processing XML files
public class XMLProcessor {
    public void processXML(String xmlData) {
        System.out.println("Processing XML file: " + xmlData);
    }
}
```

### Step 4: Create the Adapter (`XMLAdapter`)

The adapter class implements the `FileProcessor` interface and internally uses the `XMLProcessor` to process XML files. It adapts the `process()` method to call the `processXML()` method.

```java
// Adapter class that makes XMLProcessor compatible with FileProcessor
public class XMLAdapter implements FileProcessor {
    private XMLProcessor xmlProcessor;

    public XMLAdapter(XMLProcessor xmlProcessor) {
        this.xmlProcessor = xmlProcessor;
    }

    @Override
    public void process(String data) {
        // Adapting XML data to JSON-style processing by converting the method call
        xmlProcessor.processXML(data);
    }
}
```

### Step 5: Client Code

The client uses the `FileProcessor` interface to process both JSON and XML files. The Adapter allows the XML files to be processed without modifying the client code.

```java
public class FileProcessingDemo {
    public static void main(String[] args) {
        // Existing JSON processor
        FileProcessor jsonProcessor = new JSONProcessor();
        jsonProcessor.process("{ 'name': 'file.json', 'type': 'json' }");

        // XML processor adapted to the FileProcessor interface
        XMLProcessor xmlProcessor = new XMLProcessor();
        FileProcessor xmlAdapter = new XMLAdapter(xmlProcessor);
        xmlAdapter.process("<file><name>file.xml</name><type>xml</type></file>");
    }
}
```

### Explanation:
1. **FileProcessor**: The `FileProcessor` interface defines a `process()` method that accepts file data in a unified way.
2. **JSONProcessor**: This class already processes JSON files as expected.
3. **XMLProcessor**: This class handles XML files but has a method (`processXML()`) that isn’t compatible with `FileProcessor`.
4. **XMLAdapter**: The adapter implements `FileProcessor` and internally uses `XMLProcessor` to process XML files. It "adapts" the XML file handling so that it fits into the existing system.

### Output:
```plaintext
Processing JSON file: { 'name': 'file.json', 'type': 'json' }
Processing XML file: <file><name>file.xml</name><type>xml</type></file>
```

### Issues Without the Adapter Pattern:

1. **Incompatible Interfaces**:
   Without the Adapter, the system would not be able to process XML files since the `XMLProcessor` has an incompatible method (`processXML()`). The client would need to handle both JSON and XML differently, leading to code duplication.

2. **Violates the **Single Responsibility Principle (SRP)**:
   If you don't use an Adapter, the client (e.g., `FileProcessingDemo`) would have to handle both file types separately, mixing its responsibility of file processing with handling different formats. The Adapter separates this responsibility, allowing the client to focus on file processing without worrying about file types.

3. **Violates the **Open-Closed Principle (OCP)**:
   If you directly modify the client to support XML processing, the code will violate the **Open-Closed Principle**, which states that a class should be open for extension but closed for modification. Without Adapter, every time a new file format is introduced (e.g., YAML, CSV), the client code would have to be modified, making it less maintainable.

4. **Tight Coupling**:
   Without an Adapter, the client would be tightly coupled to specific file formats and processors. This reduces flexibility and makes the system harder to maintain or extend. By introducing the Adapter pattern, the client can work with any file processor that implements the `FileProcessor` interface, promoting loose coupling.

5. **Duplication of Logic**:
   Without Adapter, handling XML and JSON files would require duplicating logic for checking file types and calling different methods in the client. The Adapter centralizes this adaptation logic, avoiding code duplication and keeping the client code clean.

### Principles Violated Without the Adapter Pattern:

1. **Single Responsibility Principle (SRP)**:
   Without Adapter, the client code would need to handle both file processing and the differences between formats, mixing responsibilities.

2. **Open-Closed Principle (OCP)**:
   Modifying the client to support new file formats would violate the OCP, as new formats would require client code changes. With Adapter, new formats (like XML) can be supported by creating new adapters without modifying the existing client code.

3. **Liskov Substitution Principle (LSP)**:
   Without Adapter, the client would need to rely on different implementations directly. With Adapter, any file processor (JSON or XML) can be used as long as it conforms to the `FileProcessor` interface, ensuring that the LSP is maintained.

4. **Dependency Inversion Principle (DIP)**:
   Without Adapter, the client would depend on concrete classes like `XMLProcessor` or `JSONProcessor`. With Adapter, the client depends on the `FileProcessor` abstraction, which follows the DIP by relying on abstractions rather than concrete implementations.

### Conclusion:
The **Adapter Pattern** allows otherwise incompatible classes (like JSON and XML processors) to work together by providing a common interface. Without it, the system would violate key design principles like **Single Responsibility**, **Open-Closed**, and **Dependency Inversion**, making the code harder to maintain, extend, and modify. The Adapter provides a clean solution to introduce new file types without modifying the existing system.


------------------------

### Decorator Pattern


The **Decorator Pattern** is a structural design pattern that allows behavior to be added to an individual object, dynamically, without affecting the behavior of other objects from the same class. This is useful when you want to extend functionality without modifying existing code.

If we don’t use the **Decorator Pattern** in scenarios like adding toppings to a pizza, we might face several issues related to **code complexity**, **rigidity**, and **violations of key design principles**. Here's a breakdown of the issues and how they violate design principles:

### Issues Without Using the Decorator Pattern

1. **Class Explosion / Inflexibility**:
   Without the decorator pattern, one way to handle toppings would be to create subclasses for each combination of pizza and toppings. For example, you'd create separate classes like `CheesePizza`, `PepperoniPizza`, `CheesePepperoniPizza`, etc. As more toppings are added, the number of possible combinations grows exponentially, leading to a large number of subclasses. This makes the code difficult to manage, inflexible, and bloated.

2. **Violates Open-Closed Principle (OCP)**:
   The **Open-Closed Principle** (from SOLID principles) states that classes should be open for extension but closed for modification. Without the decorator pattern, whenever we want to add a new topping (e.g., mushrooms), we would need to modify existing pizza classes or create new ones. This violates the principle because it forces us to change the existing code every time a new topping is introduced.

3. **Violates Single Responsibility Principle (SRP)**:
   The **Single Responsibility Principle** states that a class should have one reason to change. If you handle toppings by modifying the core `Pizza` class, that class will now have multiple responsibilities: calculating the price of the pizza *and* handling all possible topping combinations. This results in a class that is responsible for more than one thing, making it harder to maintain and prone to errors.

4. **Lack of Reusability**:
   Without decorators, the logic for each topping might be duplicated across multiple pizza types (e.g., adding cheese to `PepperoniPizza` and `VeggiePizza` would require duplicating the cheese logic in both classes). This violates the **DRY (Don't Repeat Yourself)** principle, and any changes in how cheese or other toppings work would need to be reflected across multiple classes.

### What the Decorator Pattern Solves
The **Decorator Pattern** allows us to add toppings (extra responsibilities) dynamically without altering existing classes. Each topping becomes a decorator that wraps around the base pizza object and modifies its behavior (in this case, the description and cost).

#### Advantages of the Decorator Pattern:
- **Extends Functionality Dynamically**: You can add new toppings (or any behavior) at runtime by simply decorating the existing pizza object.
- **Open for Extension, Closed for Modification**: The base `Pizza` class is never modified. New toppings can be added without changing any existing code.
- **Single Responsibility**: The base `Pizza` class only handles plain pizzas. Topping decorators have a single responsibility: they handle the addition of specific toppings.
- **Avoids Class Explosion**: Instead of creating numerous pizza subclasses, each decorator handles one topping, and you can combine them flexibly.

### Example of Decorator Pattern with Pizza and Toppings

#### Step 1: Create the `Pizza` Interface
The `Pizza` interface defines two methods: `getDescription` and `getCost`.

```java
// Component
public interface Pizza {
    String getDescription();
    double getCost();
}
```

#### Step 2: Create a Concrete Pizza (e.g., PlainPizza)
A basic pizza with no toppings.

```java
// Concrete Component
public class PlainPizza implements Pizza {
    @Override
    public String getDescription() {
        return "Plain Pizza";
    }

    @Override
    public double getCost() {
        return 5.00;  // Base cost for a plain pizza
    }
}
```

#### Step 3: Create the Topping Decorators
Each topping is a decorator that "wraps" a pizza and adds its cost to the base pizza.

```java
// Abstract Decorator
public abstract class ToppingDecorator implements Pizza {
    protected Pizza tempPizza;

    public ToppingDecorator(Pizza newPizza) {
        this.tempPizza = newPizza;
    }

    @Override
    public String getDescription() {
        return tempPizza.getDescription();
    }

    @Override
    public double getCost() {
        return tempPizza.getCost();
    }
}

// Concrete Decorators
public class Cheese extends ToppingDecorator {
    public Cheese(Pizza newPizza) {
        super(newPizza);
    }

    @Override
    public String getDescription() {
        return tempPizza.getDescription() + ", Cheese";
    }

    @Override
    public double getCost() {
        return tempPizza.getCost() + 1.50;  // Adding cost of cheese
    }
}

public class Pepperoni extends ToppingDecorator {
    public Pepperoni(Pizza newPizza) {
        super(newPizza);
    }

    @Override
    public String getDescription() {
        return tempPizza.getDescription() + ", Pepperoni";
    }

    @Override
    public double getCost() {
        return tempPizza.getCost() + 2.00;  // Adding cost of pepperoni
    }
}

public class Olives extends ToppingDecorator {
    public Olives(Pizza newPizza) {
        super(newPizza);
    }

    @Override
    public String getDescription() {
        return tempPizza.getDescription() + ", Olives";
    }

    @Override
    public double getCost() {
        return tempPizza.getCost() + 0.75;  // Adding cost of olives
    }
}
```

#### Step 4: Using the Decorators
Now, we can create pizzas with various combinations of toppings dynamically.

```java
public class PizzaMaker {
    public static void main(String[] args) {
        // Create a plain pizza
        Pizza myPizza = new PlainPizza();

        // Add toppings
        myPizza = new Cheese(myPizza);
        myPizza = new Pepperoni(myPizza);
        myPizza = new Olives(myPizza);

        // Get final description and cost
        System.out.println("Pizza description: " + myPizza.getDescription());
        System.out.println("Total cost: $" + myPizza.getCost());
    }
}
```

#### Output:
```
Pizza description: Plain Pizza, Cheese, Pepperoni, Olives
Total cost: $9.25
```

### Summary of Issues Solved:
- **Class Explosion**: We avoid creating multiple subclasses for each pizza and topping combination. Instead, toppings are added as decorators that wrap the pizza dynamically.
- **Open-Closed Principle**: The pizza system can be extended with new toppings (decorators) without modifying existing pizza classes.
- **Single Responsibility Principle**: Each class has a clear responsibility: `PlainPizza` handles the base pizza, and each decorator handles one specific topping.
- **Flexible and Reusable**: Toppings can be combined in any order, and the logic for each topping is encapsulated in its own class, allowing reuse across multiple types of pizzas.







## Compose Method Pattern

*    SLAP principle: `Single Level Of Abstraction` principle.
*    So the Compose methos should follow the SLAP principle.

*    


  
