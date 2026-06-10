## Record Link: https://docs.google.com/document/d/1GOOwb6PKDhIkx3mneJmYnNk3EtEmumLk2u1iCgf60zw/edit?tab=t.0

## 1. How to write Restful API in Spring Boot?
To build a REST API in Spring Boot, I follow the three-tier architecture and map each HTTP method to a CRUD operation.
I define the entity with JPA annotations, create a repository by extending JpaRepository for the basic CRUD, add a service for the business logic, and a rest controller for the endpoints. For example, I use POST to create, GET to read all or read one by id, PUT to update, and DELETE to remove. The two rules I keep in mind: the URL is a noun, never a verb, and every response returns a payload plus the right status code.

## 2. How did you debug?
My debugging follows a clear flow: I start from the logs, work back to the request, reproduce the issue, and only then go into the code with breakpoints.
First I check the log for that error, looking at the HTTP status code and the error or exception message. From there I look at the request that triggered it. Then I try to reproduce the issue, first in the production-like environment. Once I can reproduce it, I move into the dev environment, where I take the request payload, fire it through Postman, and run the application locally in IntelliJ. Finally I attach the debugger, set breakpoints, and step through line by line to watch the variable values and find the root cause.

## 3. What is fair lock?
A fair lock guarantees threads acquire the lock in the order they requested it, in other words first in first out, which prevents thread starvation.
So whichever thread waits the longest gets the lock next, following FIFO order. In Java you create one with a ReentrantLock constructed with the boolean true. The trade-off is performance: it's slower than the default non-fair lock because it has to maintain a waiting queue, so I only use it when ordering matters and starvation isn't acceptable.

## 4. New feature in Java 11?
Java 11 is an LTS release, and the most notable features are the standardized HTTP Client and two new garbage collectors, ZGC and Epsilon GC.
The new HTTP Client lives in the java net http package and supports HTTP two and both synchronous and asynchronous requests, replacing the older third-party clients. ZGC is a new low-latency garbage collector, introduced as an experimental feature in Java 11, designed to keep pause times very short even on large heaps. Epsilon GC is a no-op collector that handles memory allocation but never reclaims it, which is mainly used for performance testing and benchmarking. On top of that, Java 11 also added the var keyword for local variable type inference, and new String methods like isBlank, strip, lines, and repeat.

## 5. If we need to design task management application, create task, query task, CRUD, how would you approach it?
It depends on the requirements, so I clarify a few things first, then design it top-down with a three-tier structure.
I'd first ask whether it's a web service or local, and single-user or multi-user. Then I model a task entity with id, title, description, status, and due date, and split it into controller, service, and repository, with the five CRUD endpoints. To make it production-ready I'd add validation, global exception handling, and pagination. The key point is: clarify first, state the approach, then code.

## 6. Design the locking schema so that when a thread call method1(), it needs to until some other thread call method2()
This is a thread-signaling problem, and the standard solution is wait and notify on a shared lock object inside synchronized blocks.
Both methods synchronize on the same lock. In method one the thread calls wait, which releases the lock and pauses it. In method two the thread does its work and calls notify, which wakes the waiting thread so it can continue. One detail: I always put wait inside a while loop to guard against spurious wakeups. A modern alternative is a ReentrantLock with a Condition, or a CountDownLatch for a one-time event.

## 7. Diff recursion and iteration
Recursion solves a problem by calling itself on a smaller sub-problem, while iteration repeats using a loop. The main difference is memory.
Recursion needs a base case to stop, and every call adds a frame to the call stack, so deep inputs can cause a stack overflow. It's cleaner for tree or graph traversal. Iteration uses a loop, runs in constant stack space, and is generally faster, so it's safer for deep or simple linear inputs.

## 8. LinkedHashMap V.S. HashMap
Both store key-value pairs with O(1) average access, but LinkedHashMap keeps a predictable order and HashMap does not.
HashMap gives no ordering guarantee. LinkedHashMap extends HashMap and adds a doubly-linked list across the entries, so iteration follows insertion order by default. It can also run in access-order mode, which makes it perfect for an LRU cache.

## 9. How to group people to key(age), value(list of People)?
I'd use the Stream API, specifically the groupingBy collector, which groups elements by a classifier function into a map.
I call stream on the list, then collect using groupingBy with a method reference for the person's age. The result is a map where the key is the age and the value is automatically a list of people with that age. If I want something different as the value, like a count, I add a downstream collector such as counting.

## 10. How can you use optional?
Optional is a Java 8 container for a value that might be null, so you can avoid null pointer exceptions.
I create one with Optional ofNullable, and then consume it with clear methods instead of manual null checks: orElse returns a default, orElseThrow throws if empty, ifPresent runs code only when a value exists, and map transforms it. It's mainly meant for return types, and I avoid calling get directly without checking.