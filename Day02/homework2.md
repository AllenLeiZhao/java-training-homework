#1.String vs StringBuilder vs StringBuffer
- String is immutable, StringBuilder and StringBuffer is mutable
- StringBuffer is thread safe, StringBuilder is not
- Although thread safe, StringBuffer can't guarrantee the order 

#2.Comparator vs Comparable, when to use which one
- Both are interfaces used for sorting objects.
- Comparable is implemented within the class itself by overriding `compareTo()`. It defines the default natural sorting order of the class.
- Comparator is used externally by overriding `compare()`. It defines custom sorting logic without modifying the original class.
- Comparator can be passed as a lambda expression or anonymous class when calling sort methods.
- Use Comparable when there is one natural default order. Use Comparator when you need multiple or custom sorting strategies.

#3.Overriding vs overloading
- Overloding is in-class, same method different signatures, and resolved at compile time.
- Overriding is inbetween class, same method, same signature, different behaviours, and resolved at runtime.

#4.JRE vs JDK vs JVM
    JDK
    ├── JRE
    │   ├── JVM
    │   └── Core Libraries
    └── Dev Tools (javac, jar, etc.)

- JVM (Java Virtual Machine): executes bytecode (.class files), translates to machine code for the current platform. Enables "write once, run anywhere".
- JRE (Java Runtime Environment): minimum environment required to run a Java program. Contains JVM + core libraries.
- JDK (Java Development Kit): required for development. Contains JRE + compiler (javac) + dev tools.
- Production environments only need JRE. Developers need JDK.

#5.Java 8 basic data types
- Primitive type: byte(8bit), short(16bit), int(32bit), long(64bit), float(32bit), double(64bit), char(16bit), boolean(1bit)

#6.Primitive type, reference type
- Primitive type: byte(8bit), short(16bit), int(32bit), long(64bit), float(32bit), double(64bit), char(16bit), boolean(1bit)
- reference type: all the objects, including wrapper classes, .e.g:String, Integer, Double

#7.How does JVM work
JVM works in the following steps:
1. **Class Loader**: loads .class bytecode files from hard disk into memory
2. **Runtime Data Area**: allocates memory across 5 areas (Method Area, Heap, Stack, PC Register, Native Method Stack)
3. **Execution Engine**: executes the bytecode in two ways:
   - Interpreter: executes line by line, slower
   - JIT Compiler (Just-In-Time): compiles frequently used hot code into machine code and caches it, faster
4. **Garbage Collector**: automatically recycles objects no longer in use in the Heap

This enables "write once, run anywhere" because JVM translates the same .class bytecode into machine code for any platform.

#8.JVM memory data model
JVM has 5 runtime memory areas:

| Area | Thread | Stores |
|------|--------|--------|
| Method Area | Shared | Class templates, static variables, method bytecode, constant pool |
| Heap | Shared | All object instances and their fields. GC works here |
| VM Stack | Per thread | Method execution data: local variables, references, parameters |
| PC Register | Per thread | Current executing instruction address |
| Native Method Stack | Per thread | Data for native (C/C++) method execution |

Key distinctions:
- Method definition (how a method looks) → Method Area
- Method execution data (local variables while running) → Stack
- Primitive local variables → Stack
- Primitive fields inside an object → Heap (together with the object)
- Static variables → Method Area
- All new objects → Heap

#9.How does GC work
GC (Garbage Collection) automatically recycles objects that are no longer in use in the Heap.

GC uses reachability analysis starting from GC Roots (local variables in Stack, static variables, constants).
- Objects reachable from GC Roots → alive, keep
- Objects not reachable → garbage, recycle

GC works in 3 steps:
1. **Mark**: mark all reachable objects as alive
2. **Sweep**: remove unreachable objects and free memory
3. **Compact**: reorganize remaining objects to avoid memory fragmentation

Example:
- `Employee e = new Employee();` → object has reference → alive
- `e = null;` → reference broken → garbage → will be collected

#10.young/old/perm generation
The Heap is divided into generations to optimize GC performance:

    Heap
    ├── Young Generation
    │   ├── Eden Space      ← new objects created here
    │   ├── Survivor S0
    │   └── Survivor S1
    ├── Old Generation      ← long-lived objects
    └── Perm Generation     ← replaced by Metaspace in Java 8+

Object lifecycle:
1. New objects are created in Eden Space
2. Eden full → Minor GC → surviving objects move to S0 or S1
3. Objects copy between S0 and S1 on each Minor GC, age increases by 1
4. Age exceeds 15 → promoted to Old Generation
5. Old Generation full → Major GC / Full GC

Perm Generation vs Metaspace:
- Perm Generation: fixed size, stores class metadata, prone to OutOfMemoryError
- Metaspace (Java 8+): uses native memory, dynamically sized, more flexible

#11.difference types of GC
| GC Type | Characteristics | Use Case |
|---------|----------------|----------|
| Serial GC | Single-threaded, Stop-the-World | Small applications |
| Parallel GC | Multi-threaded, Stop-the-World, throughput priority | Batch processing |
| CMS GC | Concurrent, low pause time, deprecated since Java 9, removed in Java 14 | Legacy low-latency |
| G1 GC | Region-based, balances throughput and latency, default since Java 9 | Large heap applications |
| ZGC | Ultra-low latency (<10ms pause), Java 15+ | Very large heap |

Stop-the-World: all user threads are paused while GC is running, which impacts performance.
