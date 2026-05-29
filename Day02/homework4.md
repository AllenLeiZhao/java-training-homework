## 1.what is functional interface
Functional interface is an interface with exactly one abstract method, which could be expressed by lambda expression.The @FunctionalInterface annotation is optional but recommended 
to let the compiler verify it has only one abstract method.

## 2.what is default method
A default method is a method with an implementation defined in an interface,
introduced in Java 8. Classes that implement the interface can use it directly
without overriding it.

Why was it introduced?
- Before Java 8, adding a new method to an interface would break all existing 
  implementing classes (they must implement every method).
- Default methods allow adding new methods to interfaces without breaking 
  existing implementations.

## 3.what is the difference between Predicate, Supplier, Consumer, Function? 
All four are built-in functional interfaces in Java 8, but with different input/output:

| Interface | Input | Output | Method |
|-----------|-------|--------|--------|
| Predicate<T> | T | boolean | test() |
| Function<T,R> | T | R | apply() |
| Consumer<T> | T | void | accept() |
| Supplier<T> | nothing | T | get() |

## 4.write a piece of code to use the  Predicate, Supplier, Consumer, Function interface
```java
// Predicate:
Predicate<String> longString = s -> s.length() > 10;
longString.test("aaa"); // return false;
// Function:
Function<String, Integer> getLength = s -> s.length();
getLength.apply(null); // NPE
getLength.apply(""); // return 0
getLength.apply("aaa"); // return 3
// Consumer:
Consumer<String> acceptLength = s -> System.out.println("Length is" + s.length() + ".");
acceptLength.accept("aaa");
// Supplier:
Supplier<Integer> tellLength = () -> 10;
tellLength.get(); // return 10
```
## 5.what is method reference
Method reference is a shorthand for a lambda expression that simply calls 
an existing method, using the :: operator.

4 types of method references:

    ```java
    // 1. Static method reference
    Function<String, Integer> parse = Integer::parseInt;
    // equivalent to: s -> Integer.parseInt(s)

    // 2. Instance method reference (on a specific instance)
    String str = "hello";
    Supplier<String> toUpper = str::toUpperCase;
    // equivalent to: () -> str.toUpperCase()

    // 3. Instance method reference (on arbitrary instance)
    Function<String, String> toUpper2 = String::toUpperCase;
    // equivalent to: s -> s.toUpperCase()

    // 4. Constructor reference
    Supplier<Student> newStudent = Student::new;
    // equivalent to: () -> new Student()
    ```

When to use:
- Use method reference when lambda just calls an existing method with no extra logic
- Use lambda when you need extra operations

    ```java
    // use method reference
    students.forEach(System.out::println);

    // use lambda (extra logic)
    students.forEach(s -> System.out.println("Student: " + s.getName()));
    ```

## 6.what is CompleteableFuture
CompletableFuture is a class introduced in Java 8 for asynchronous programming.
It allows tasks to run in a separate thread without blocking the main thread,
and supports chaining multiple tasks together.

Difference from Callable:
- Callable: future.get() blocks the main thread until result is ready
- CompletableFuture: non-blocking, supports task chaining

    ```java
    // Callable (blocking)
    FutureTask<String> task = new FutureTask<>(() -> "result");
    new Thread(task).start();
    String result = task.get(); // blocks main thread here!

    // CompletableFuture (non-blocking)
    CompletableFuture
        .supplyAsync(() -> getDataFromDB())      // async task 1
        .thenApplyAsync(data -> callAPI(data))   // async task 2, runs after task 1
        .thenApply(result -> formatResult(result)); // task 3
    ```

Common methods:
- runAsync(): async task with no return value
- supplyAsync(): async task with return value
- thenApply(): chain next task synchronously
- thenApplyAsync(): chain next task asynchronously

Use case:
- Calling multiple external APIs in parallel
- Processing large data concurrently
- Any task that should not block the main thread

## 7.Default keyword  vs Java default scope
Default keyword:
- Used in interfaces to define a method with a default implementation
- Introduced in Java 8
- Implementing classes can use it directly or override it
Default scope (access modifier):
- Not a keyword, it is the absence of any access modifier
- Accessible within the same package only
- Also called "package-private"


## 8.Coding: create a list of students, Student Class has name, age, score three fields. 
```java
List<Student> list = new ArrayList<>();

//use stream api to find all the students’ name starting with ‘A’
public List<Student> nameStartsWithA(List<Student> students) {
    return students.stream()
            .filter(s -> s.getName().charAt(0) == 'A')
            .collect(Collectors.toList());
}
//use stream api to get the sum of all the students score
public double sumOfScore(List<Student> students) {
    return students.stream()
        .mapToDouble(Student::getScore)
        .sum();
}
// use stream api to find all the students whose sore >= 60
public List<Student> highScore(List<Student> students) {
    return students.stream()
            .filter(s -> s.getScore() >= 60)
            .collect(Collectors.toList());
}
// use stream api to retrieve all students name
public List<String> getNames(List<Student> students) {
    return students.stream()
            .map(Student::getName)
            .collect(Collectors.toList());
}
// use stream api to count the frequency of each age
public Map<Integer, Long> freqOfAge(List<Student> students) {
    return students.stream()
            .collect(Collectors.groupingBy(
                Student::getAge,
                Collectors.counting()
            ));
}
//use steam api to count the number of boys girls (groupby, collector.toMap())
public Map<String, Long> freqOfGender(List<Student> students) {
    return students.stream()
            .collect(Collectors.toMap(
                Student::getGender,
                s -> 1L,
                Long::sum
            ));
}
```

## 9.intermediate operation vs terminal operation
Intermediate operations:
- Return a Stream, allowing method chaining
- Lazy: not executed until a terminal operation is called
- Examples: filter(), map(), sorted(), distinct(), limit(), skip()

Terminal operations:
- Trigger the execution of the entire pipeline
- Return a non-Stream result (List, int, void, Optional, etc.)
- Examples: collect(), count(), sum(), forEach(), reduce(), findFirst()

## 10.Coding: given a char array, use stream api to count the frequency of each char
```java
public Map<Character, Long> charFreq(char[] array) {
    return new String(array).chars()
            .mapToObj(c -> (char)c)
            .collect(Collectors.groupingBy(
                c -> c,
                Collectors.counting()
            ));
}
```

## 11.Steam API: map() vs flatmap();
map():
- Transforms each element into another element
- One-to-one: input size = output size
flatMap():
- Transforms each element into a Stream, then flattens all streams into one
- One-to-many: input size <= output size
