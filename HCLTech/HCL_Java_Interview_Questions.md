# HCL Tech – Java Developer Interview Questions (2–5 YOE)

---

## Q1. Which CI/CD pipeline are you using?

**Speak :**
"In my current project, we use Jenkins for CI/CD. Every push to the feature branch triggers a Jenkins pipeline — it runs the Maven build, executes unit tests with JUnit, does a SonarQube code quality scan, and if everything passes, it builds a Docker image and deploys it to our Kubernetes cluster in the dev/staging environment. Production deployment is usually a manual approval step in the same pipeline."

**Key points:**
- Name the actual tool you use: Jenkins / GitLab CI / GitHub Actions / Azure DevOps
- Mention stages: build → test → code quality → package → deploy
- Mention approval gates for production (shows maturity, not just "it auto-deploys")

*(No code snippet needed — this is experience-based, keep it conversational)*

---

## Q2. What is a Functional Interface? What is its use? What makes it different in Java 8?

**Code:**
```java
@FunctionalInterface
interface Calculator {
    int operate(int a, int b);
}

// Usage with lambda
Calculator add = (a, b) -> a + b;
System.out.println(add.operate(5, 3)); // 8
```

**Speak:**
"A functional interface is an interface with exactly one abstract method. It can have any number of default or static methods, but only one abstract method — that's the rule. The `@FunctionalInterface` annotation isn't mandatory, but it's good practice because the compiler will throw an error if you accidentally add a second abstract method.

What makes it special in Java 8 is that it enables lambda expressions. Before Java 8, if I wanted to pass behavior as a parameter, I had to create an anonymous inner class. Now, because of functional interfaces, I can just write a lambda — it's the same thing under the hood, just much less boilerplate."

**Key points:**
- One abstract method = SAM (Single Abstract Method) interface
- `@FunctionalInterface` is optional but enforces the contract at compile time
- Enables lambda expressions and method references
- Examples: `Runnable`, `Comparator`, `Callable` were functional interfaces even before Java 8 — Java 8 just gave them lambda support

---

## Q3. What are the different Functional Interfaces available in Java 8?

**Code:**
```java
Function<Integer, Integer> square = x -> x * x;
Predicate<Integer> isEven = x -> x % 2 == 0;
Consumer<String> printer = s -> System.out.println(s);
Supplier<String> greeting = () -> "Hello";
BiFunction<Integer, Integer, Integer> sum = (a, b) -> a + b;
UnaryOperator<Integer> increment = x -> x + 1;
```

**Speak:**
"Java 8 gives us the `java.util.function` package with several ready-made functional interfaces so we don't have to write our own every time. The main ones are `Function`, `Predicate`, `Consumer`, `Supplier`, `BiFunction`, and `UnaryOperator`. Each one has a specific shape — takes something, returns something, or neither — and I pick the one that matches what I need instead of writing a custom interface."

**Key points:**
- `Function<T,R>` — takes T, returns R
- `Predicate<T>` — takes T, returns boolean
- `Consumer<T>` — takes T, returns nothing
- `Supplier<T>` — takes nothing, returns T
- `BiFunction<T,U,R>` — two inputs, one output
- All live in `java.util.function`

---

## Q4. What do Predicate, Consumer, Supplier functional interfaces mean?

**Code:**
```java
// Predicate - tests a condition, returns boolean
Predicate<String> isEmpty = str -> str.isEmpty();
System.out.println(isEmpty.test("")); // true

// Consumer - consumes a value, returns nothing
Consumer<String> print = str -> System.out.println("Value: " + str);
print.accept("Dip Developer"); // Value: Dip Developer

// Supplier - supplies/produces a value, no input
Supplier<Double> randomValue = () -> Math.random();
System.out.println(randomValue.get());
```

**Speak:**
"Think of it in terms of direction of data. `Predicate` takes an input and gives back true or false — it's used for filtering, like in `stream().filter()`. `Consumer` takes an input and does something with it, but returns nothing — that's your `forEach()`. `Supplier` takes no input at all, and just produces a value when you call `get()` — useful for lazy initialization or generating values on demand, like default values or factory methods."

**Key points:**
- Predicate → `test()` method → boolean → used in `filter()`
- Consumer → `accept()` method → void → used in `forEach()`
- Supplier → `get()` method → returns T → used for lazy loading / factories
- Easy memory trick: Predicate = question, Consumer = action, Supplier = source

---

## Q5. What are Consumer, Producer, etc.?

**Speak:**
"So technically in the `java.util.function` package, there's no interface literally called 'Producer' — the interviewer likely means `Supplier`, since it produces a value. I'd clarify that gently: 'Java doesn't have a `Producer` interface by that name — I think you mean `Supplier`, which produces a value with no input.' It's fine to politely correct the terminology — it actually shows you know the API well instead of just guessing."

**Key points:**
- This is a common trick/loosely-worded question — there is no `Producer` functional interface in Java
- Confidently clarify: "Supplier is what produces a value"
- Don't just nod along — correcting politely shows depth of knowledge
- If they mean Kafka Producer/Consumer, that's a different (messaging) context — ask to confirm which they mean

---

## Q6. What is an Immutable class?

**Code:**
```java
public final class Employee {
    private final String name;
    private final int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() { return name; }
    public int getAge() { return age; }
    // No setters!
}
```

**Speak:**
"An immutable class is a class whose object state cannot be changed once it's created. `String` is the classic example. To make a class immutable, I follow a few rules: make the class `final` so it can't be subclassed, make all fields `private final`, don't provide any setters, and initialize everything through the constructor. If the class holds mutable objects like a `List` or `Date`, I also need to return defensive copies from the getters instead of the actual reference — otherwise someone outside the class can still mutate the internal state."

**Key points:**
- Class declared `final`
- Fields `private final`
- No setters, only getters
- All values set via constructor
- Defensive copying for mutable fields (Lists, Dates, etc.)
- Real-world example: `String`, `Integer`, `LocalDate`

---

## Q7. map() vs flatMap()

**Code:**
```java
List<String> words = List.of("Hello", "World");

// map - one-to-one transformation
List<Integer> lengths = words.stream()
        .map(String::length)
        .collect(Collectors.toList());
// [5, 5]

// flatMap - flattens nested structure into a single stream
List<List<Character>> nested = List.of(
        List.of('H', 'e'), List.of('W', 'o')
);
List<Character> flat = nested.stream()
        .flatMap(List::stream)
        .collect(Collectors.toList());
// [H, e, W, o]
```

**Speak:**
"`map()` transforms each element one-to-one — one input produces exactly one output. `flatMap()` is used when each element itself produces a stream, and I want to flatten all of those streams into a single stream. A common example is when I have a `List<List<String>>` and I want a single flat `List<String>` — `map()` would give me a stream of streams, but `flatMap()` merges them into one flat stream."

**Key points:**
- `map()`: 1-to-1 transformation, `Stream<T>` → `Stream<R>`
- `flatMap()`: 1-to-many, flattens nested structures, `Stream<Stream<T>>` → `Stream<T>`
- Real use case: flattening `List<List<X>>`, or splitting a sentence into words across multiple strings

---

## Q8. Which design pattern are you using? / What is Singleton pattern?

**Code:**
```java
public class DatabaseConnection {
    private static volatile DatabaseConnection instance;

    private DatabaseConnection() {}

    public static DatabaseConnection getInstance() {
        if (instance == null) {
            synchronized (DatabaseConnection.class) {
                if (instance == null) {
                    instance = new DatabaseConnection();
                }
            }
        }
        return instance;
    }
}
```

**Speak:**
"Singleton ensures a class has only one instance throughout the application, and provides a global point of access to it. I make the constructor private so nobody can instantiate it directly, and expose a static `getInstance()` method. In multi-threaded environments, I use double-checked locking with a `volatile` instance variable to make it thread-safe without paying the synchronization cost on every call.

As for design patterns I actually use — in Spring, every `@Service` and `@Component` bean is a Singleton by default, managed by the Spring container itself. I also use Builder pattern for constructing complex objects, and Factory pattern when I need to create objects based on some runtime condition."

**Key points:**
- Private constructor + static `getInstance()`
- Thread safety: double-checked locking with `volatile`, or simpler — enum singleton
- Spring beans are Singleton-scoped by default — tie this back to real Spring usage
- Mention Builder/Factory too if asked "which patterns do you use" broadly

---

## Q9. REST API vs SOAP

**Speak:**
"REST is an architectural style, SOAP is a strict protocol. REST typically uses JSON and is lightweight, stateless, and works over standard HTTP methods — GET, POST, PUT, DELETE. SOAP uses XML only, has a strict contract defined by WSDL, and supports built-in features like WS-Security for enterprise-grade security and ACID-compliant transactions. In practice, I use REST for most modern applications because it's faster and easier to work with, but SOAP still shows up in banking or legacy enterprise systems where strict contracts and built-in security standards are required."

**Key points:**
- REST = architectural style, lightweight, JSON, stateless, uses HTTP verbs
- SOAP = protocol, XML only, WSDL contract, built-in security/transactions
- REST → modern web/mobile apps; SOAP → banking, legacy enterprise, government systems
- REST is generally faster and easier to consume

*(No code snippet needed — conceptual/comparison question)*

---

## Q10. @Controller vs @RestController

**Code:**
```java
@Controller
public class ViewController {
    @GetMapping("/home")
    public String home() {
        return "home"; // resolves to home.jsp / home.html view
    }
}

@RestController
public class ApiController {
    @GetMapping("/api/users")
    public List<User> getUsers() {
        return userService.getAllUsers(); // returned directly as JSON
    }
}
```

**Speak:**
"`@Controller` is used for traditional MVC applications where the return value is a view name — like a JSP or Thymeleaf page — that gets resolved by a view resolver. `@RestController` is `@Controller` combined with `@ResponseBody`, so whatever I return gets serialized directly into the HTTP response body, usually as JSON. If I'm building a REST API, I always use `@RestController` so I don't have to annotate every single method with `@ResponseBody`."

**Key points:**
- `@RestController` = `@Controller` + `@ResponseBody`
- `@Controller` → returns view names (MVC)
- `@RestController` → returns data directly (JSON/XML) — used for REST APIs
- Saves you from adding `@ResponseBody` on every method

---

## Q11. Which annotation is used for the service class? Can we swap @Controller and @Service — will it work?

**Code:**
```java
@Service
public class UserService {
    public User findUser(Long id) {
        // business logic
        return userRepository.findById(id).orElseThrow();
    }
}
```

**Speak:**
"For the service layer, we use `@Service`. It's a specialization of `@Component`, and it tells Spring 'this is a business logic bean' — functionally it behaves exactly like `@Component`, but it makes the code more readable and self-documenting.

Now, can we swap `@Controller` and `@Service`? Technically — yes, the application might still start, because both are ultimately just `@Component` under the hood and Spring will register the bean either way. But it's a bad idea. `@Controller` has special meaning to Spring MVC — it tells the `DispatcherServlet` this bean can handle web requests and its methods should be scanned for `@RequestMapping`/`@GetMapping` annotations. If I put `@Service` on a class with `@GetMapping` methods, Spring MVC won't treat it as a request handler, so those endpoints simply won't work. So it 'compiles' and the app 'runs' — but the actual feature breaks."

**Key points:**
- `@Service` = specialization of `@Component`, semantic clarity for business layer
- All of `@Component`, `@Service`, `@Repository`, `@Controller` are functionally beans — but each has stereotype-specific behavior
- `@Controller` is specifically recognized by `DispatcherServlet` for request mapping
- Swapping them: app may still start, but `@Service`-annotated request handlers won't get wired as web endpoints — it silently breaks the API layer
- Good gotcha to emphasize: "starts fine ≠ works fine"

---

## Q12. PUT vs PATCH

**Code:**
```java
// PUT - replaces the entire resource
@PutMapping("/users/{id}")
public User updateUser(@PathVariable Long id, @RequestBody User user) {
    return userService.save(user); // full object required
}

// PATCH - partially updates the resource
@PatchMapping("/users/{id}")
public User patchUser(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
    return userService.partialUpdate(id, updates); // only changed fields
}
```

**Speak:**
"`PUT` is used to update a resource completely — I need to send the full object, and it replaces whatever exists at that URI. If I omit a field, it usually gets overwritten with null or a default value. `PATCH` is for partial updates — I only send the fields that actually changed, and the rest of the resource stays untouched.

Another key point: `PUT` is idempotent — calling it multiple times with the same payload always results in the same state. `PATCH` is not guaranteed to be idempotent, especially if it's doing something like incrementing a counter."

**Key points:**
- PUT = full replace, requires complete payload, idempotent
- PATCH = partial update, only changed fields, not always idempotent
- If a field is missing in PUT request, it often gets nulled out — good real-world caveat to mention
- Idempotency is the interviewer's favorite follow-up — be ready to explain it

---
