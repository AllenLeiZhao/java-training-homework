#1.Java modifier scope: public, private, protected, default scope
- public: accessible to all classes in all packages
- private: most restrictive, only accessible within the same class
- protected: accessible within the same class, same package, and subclasses
- default (no modifier): accessible within the same package only

#2.What is static scope
- can be used to decorate a variable, method, code block, or inner class
- static variables and methods belong to the class, not to any instance, and can be called without creating an object
- static code block is executed once when the class is first loaded by JVM, ideal for initialization logic
- static inner class can be used and accessed without an outer class instance

#3.how does classloader work
ClassLoader is responsible for loading .class files from hard disk into JVM Method Area.
There are 3 types of ClassLoader:
- Bootstrap ClassLoader: loads core Java libraries (java.lang, java.util, etc.)
- Extension ClassLoader: loads extension libraries
- Application ClassLoader: loads application classes written by developers

ClassLoader follows the Parent Delegation Model:
when loading a class, it first delegates to the parent ClassLoader.
Only if the parent cannot load it, the child ClassLoader will load it.
This prevents core classes from being tampered with.

#4.Describe the difference between unchecked and checked exceptions in Java.
- Checked exceptions: detected at compile time, must be handled with try-catch or throws keyword. Examples: IOException, SQLException
- Unchecked exceptions: occur at runtime, not required to be handled. Examples: NullPointerException, ArrayIndexOutOfBoundsException

#5.What is the difference between finally, final, and finalize in Java?
- final:
    - final variable: cannot be reassigned after initialization
    - final method: cannot be overridden by subclasses
    - final class: cannot be extended (inherited)
- finally: used in try-catch-finally block, always executed regardless of whether an exception occurs, commonly used for cleanup (e.g. closing connections)
- finalize: called by GC before an object is garbage collected, deprecated since Java 9

#6.Define try-with resource. How can you say that it differs from an ordinary try?
- try-with-resources automatically closes resources (e.g. files, connections) after the try block finishes, without needing a finally block.
- The resource must implement the `AutoCloseable` interface.
- Ordinary try requires manually closing resources in a finally block, which is verbose and error-prone.
    ```java
    // Ordinary try
    Connection conn = null;
    try {
        conn = getConnection();
    } finally {
        if (conn != null) conn.close(); // manual cleanup
    }

    // try-with-resources
    try (Connection conn = getConnection()) {
        // use conn
    } // conn.close() called automatically
    ```

#7.Define Runtime Exception. Describe it with the help of an example.
- Runtime Exception is a subclass of Exception, and is the parent class of all unchecked exceptions.
- They occur during program execution and are not required to be handled at compile time.
- Common examples: NullPointerException, ArrayIndexOutOfBoundsException, ClassCastException, ArithmeticException.
    ```java
    // NullPointerException example
    String str = null;
    str.length(); // throws NullPointerException at runtime

    // ArrayIndexOutOfBoundsException example
    int[] arr = new int[3];
    System.out.println(arr[10]); // throws ArrayIndexOutOfBoundsException

    // ArithmeticException example
    int result = 8 / 0; // throws ArithmeticException: / by zero
    ```

#8.What is the difference between NoClassDefFoundError and ClassNotFoundException in Java
- ClassNotFoundException: a checked exception, thrown when you try to load a class 
  dynamically at runtime using Class.forName() or ClassLoader.loadClass(), 
  but the class cannot be found in the classpath.

- NoClassDefFoundError: an error (not exception), thrown when the class was present 
  at compile time but cannot be found at runtime. 
  Usually caused by missing JAR files or classpath issues during deployment.

    ```java
    // ClassNotFoundException example
    try {
        Class.forName("com.example.MyClass"); // class not in classpath
    } catch (ClassNotFoundException e) {
        e.printStackTrace();
    }

    // NoClassDefFoundError example
    // MyClass existed when compiling, but the JAR was removed before running
    MyClass obj = new MyClass(); // throws NoClassDefFoundError at runtime
    ```

#9.Why should we clean up activities such as I/O resources in the finally block?
- I/O resources such as file streams, database connections, and network sockets 
  are not managed by GC. They must be closed manually.
- If an exception occurs in the try block, the code after it will be skipped,
  leaving the resource open and causing resource leaks.
- finally block is always executed regardless of whether an exception occurs,
  ensuring resources are always closed properly.
- Unclosed resources can cause: memory leaks, file locks, connection pool exhaustion.

    ```java
    try {
        FileInputStream fis = new FileInputStream("file.txt");
        // if exception occurs here, fis will never be closed!
    } catch (IOException e) {
        e.printStackTrace();
    } finally {
        fis.close(); // always executed, ensures cleanup
    }

    // Better: use try-with-resources
    try (FileInputStream fis = new FileInputStream("file.txt")) {
        // fis.close() called automatically
    }
    ```

#10.Describe OutofMemoryError in exception handling.
- OutOfMemoryError is a subclass of Error (not Exception), thrown when JVM 
  cannot allocate more memory and GC cannot free enough space.
- It is an unrecoverable error, meaning the application usually cannot continue running.

Common causes:
- Creating too many objects in Heap (e.g. infinite loop creating objects)
- Memory leak: objects are referenced but never used, GC cannot collect them
- Using unbounded thread pools (e.g. CachedThreadPool) creating too many threads
- Loading too many large files into memory

    ```java
    // Example: infinite loop causing OOM
    List<byte[]> list = new ArrayList<>();
    while (true) {
        list.add(new byte[1024 * 1024]); // keep adding 1MB chunks
        // eventually throws OutOfMemoryError: Java heap space
    }
    ```

How to solve:
- Increase heap size: -Xmx2g (JVM argument)
- Fix memory leaks: remove unused object references
- Use pagination instead of loading all data at once
- Use proper thread pool configuration (avoid CachedThreadPool)
- Analyze with tools: JProfiler, heap dump

#11.What is Generics in Java? What are the advantages of using Generics?
## Q11. What is Generics in Java? What are the advantages of using Generics?
Generics allow classes, interfaces, and methods to accept type parameters,
enabling the same code to work with different types without duplication.

    ```java
    // Without Generics: need separate class for each type
    class StringBox {
        private String value;
        public String get() { return value; }
    }
    class IntegerBox {
        private Integer value;
        public Integer get() { return value; }
    }

    // With Generics: one class works for all types
    class Box<T> {
        private T value;
        public T get() { return value; }
    }

    Box<String> stringBox = new Box<>();
    Box<Integer> intBox = new Box<>();
    Box<Employee> employeeBox = new Box<>();
    ```

Advantages:
1. Code reusability: one class/method handles any type, no duplication
2. Type safety: compile-time type checking, prevents ClassCastException at runtime
3. No manual casting: no need to cast when retrieving objects

#12.How does Generics works in Java ? What is type erasure ?
Generics work at compile time only. The compiler uses type information to perform
type checking and insert automatic casts, then removes (erases) all generic type
information before generating bytecode. This process is called type erasure.

    ```java
    // Source code
    List<String> list = new ArrayList<>();
    list.add("hello");
    String s = list.get(0);

    // After type erasure (bytecode level)
    List list = new ArrayList();
    list.add("hello");
    String s = (String) list.get(0); // compiler inserts cast automatically
    ```

Why type erasure?
- To maintain backward compatibility with pre-Java 5 code that had no generics

Consequence:
- At runtime, List<String> and List<Integer> are the same type: List
- Cannot use generics with primitive types (must use wrapper classes)
- Cannot do: new T() or T.class at runtime

#13.What is the difference between List<? extends T>  and  List <? super T>?
This follows the PECS principle: Producer Extends, Consumer Super.

List<? extends T>:
- Accepts T and any subclass of T
- Read only: you can read elements as type T
- Cannot write: compiler doesn't know the exact subtype, adding anything is unsafe

    ```java
    List<? extends Animal> list = new ArrayList<Dog>();
    Animal a = list.get(0); // ✅ can read as Animal
    list.add(new Dog());    // ❌ compile error, cannot write
    ```

List<? super T>:
- Accepts T and any superclass of T
- Write only: you can add T or its subclasses
- Cannot safely read: can only read as Object

    ```java
    List<? super Dog> list = new ArrayList<Animal>();
    list.add(new Dog());    // ✅ can write
    Dog d = list.get(0);    // ❌ compile error, can only read as Object
    ```

Use case:
- extends → when you only need to read from the list (Producer)
- super → when you only need to write to the list (Consumer)

#14.what is Optional class (write a demo code to use ofNullable, orElse, orElseThrow method)
Optional is a container class introduced in Java 8 to handle NullPointerException 
more elegantly. Instead of returning null, a method can return an Optional object.

Common methods:
- Optional.ofNullable(value): creates an Optional, allows null value
- orElse(default): returns value if present, otherwise returns default
- orElseThrow(): returns value if present, otherwise throws exception

    ```java
    String[] arr = new String[3]; // all elements are null

    // ofNullable: wraps a potentially null value
    Optional<String> opt = Optional.ofNullable(arr[0]);

    // orElse: return default value if null
    String result = opt.orElse("default value");
    System.out.println(result); // "default value"

    // orElseThrow: throw exception if null
    String result2 = opt.orElseThrow(() -> new RuntimeException("value is null"));
    // throws RuntimeException because arr[0] is null

    // Practical example
    Optional<String> name = Optional.ofNullable(employee.getName());
    String displayName = name.orElse("Unknown");
    ```

#15.what is OOP
OOP (Object-Oriented Programming) is a programming paradigm that organizes code 
around objects and classes. Java is based on 4 core OOP principles:

1. Encapsulation
- Hide internal data using private fields, expose via public getters/setters
- Protects data from unauthorized access and modification

2. Inheritance
- A child class extends a parent class, inheriting its fields and methods
- Java supports single class inheritance, multiple interface inheritance

3. Polymorphism
- Overloading: same method name, different parameters, resolved at compile time
- Overriding: child class redefines parent method, resolved at runtime

4. Abstraction
- Hide implementation details, expose only necessary interfaces
- Achieved via abstract classes and interfaces
