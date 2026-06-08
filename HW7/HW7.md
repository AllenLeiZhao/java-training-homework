## 1. write out the optimized Singleton Version and explain each line of code
```java
public class Singleton {
    // private: hide from external access
    // static: belongs to class, not instance
    // volatile: prevents instruction reordering and ensures visibility across threads
    private static volatile Singleton instance;

    // private constructor to avoid external access
    private Singleton() {}

    // static getter to make it be called without an instance
    public static Singleton getInstance() {
        // the outer null check, to optimize performance
        if(instance == null) {
            // synchronized to keep thread safe
            synchronized (Singleton.class) {
                // null check again avoid race condition
                if(instance == null) {
                    // really null -> create
                    instance = new Singleton();
                }
            }
        }
        // not null -> return
        return instance;
    }
}
```

## 2. What is Reflection?
Reflection is a Java mechanism that allows programs to inspect and manipulate
classes, methods, and fields at runtime dynamically, even if they are private.
It is widely used by frameworks like Spring and Hibernate to create objects
and inject dependencies dynamically. The drawback is that it is slower than
direct method calls and can break encapsulation by accessing private fields.

## 3. HTTP Status Codes
- 200 OK: request succeeded, returns requested data
- 201 Created: resource successfully created after POST
- 202 Accepted: request accepted but not yet processed
- 204 No Content: request succeeded but nothing to return, usually after DELETE
- 301 Moved Permanently: resource has permanently moved to a new URL
- 307 Temporary Redirect: resource is temporarily at a different URL
- 400 Bad Request: client sent invalid request, wrong format or missing fields
- 401 Unauthorized: not logged in, authentication required
- 403 Forbidden: logged in but no permission to access this resource
- 404 Not Found: resource does not exist
- 500 Internal Server Error: something went wrong on the server side

## 4. What is HTTP?
HTTP stands for HyperText Transfer Protocol. It is the communication protocol
used between client and server on the web. The client sends an HTTP request
containing a method, URL, headers, and an optional body. The server processes
it and returns an HTTP response containing a status code, headers, and a
response body usually in JSON format.

## 5. HTTP Methods
- GET: read or fetch data from server, no request body, should not change server state
- POST: create a new resource, has request body
- PUT: fully replace an existing resource, has request body
- DELETE: remove a resource, no request body
- PATCH: partially update an existing resource, only sends changed fields

## 6. POST vs PATCH
POST creates a new resource while PATCH partially updates an existing one.
When using PATCH, you only send the fields you want to change instead of
the entire resource. For example, to update only a student's age, PATCH
sends just the age field, whereas POST would create a brand new student record.

## 7. POST vs PUT
POST creates a new resource and is not idempotent, meaning calling it twice
creates two separate records. PUT fully replaces an existing resource and is
idempotent, meaning calling it multiple times with the same data always
results in the same state. For example, POST to /students always creates a
new student, while PUT to /students/1 always updates the same student.

## 8. What is Idempotent?
Idempotent means multiple identical requests produce the same result as a
single request. GET is idempotent because reading data never changes server
state. PUT is idempotent because updating with the same data always results
in the same state. DELETE is idempotent because deleting the same resource
multiple times still results in it being deleted. POST is not idempotent
because each call creates a new resource. PATCH is generally not idempotent
because the result may differ depending on the current state of the resource.
