# OOP in C++ --- Interview Notes

## 1. Why OOP?

Object-oriented programming organizes software around objects containing
state and behavior. It helps with modularity, reuse, abstraction and
maintainability.

``` mermaid
flowchart TD
    A[Class] --> B[Object]
    B --> C[State]
    B --> D[Behavior]
    A --> E[Encapsulation]
    A --> F[Abstraction]
    A --> G[Inheritance]
    A --> H[Polymorphism]
```

## 2. Class vs Object

A **class** is a blueprint/user-defined type. An **object** is an
instance of that class.

``` cpp
class BankAccount {
private:
    double balance;
public:
    void deposit(double amount) { balance += amount; }
};

BankAccount a;
```

## 3. Four pillars

### Encapsulation

Bundle data and operations together and restrict direct access.

``` cpp
class Account {
private:
    double balance;
public:
    void deposit(double x) { if (x > 0) balance += x; }
};
```

Why useful:

-   protects invariants
-   reduces coupling
-   gives controlled access

### Abstraction

Expose what an object does, hide how it does it.

Example: a `pay()` method may hide validation, gateway calls, retries
and persistence.

### Inheritance

Represents an "is-a" relationship.

``` cpp
class Vehicle {};
class Car : public Vehicle {};
```

Types:

-   single
-   multilevel
-   multiple
-   hierarchical
-   hybrid

### Polymorphism

Same interface, different behavior.

**Compile time**

-   function overloading
-   operator overloading

**Runtime**

-   virtual functions
-   overriding

------------------------------------------------------------------------

# 4. Access modifiers

-   `public`: accessible through public interface
-   `private`: accessible inside class/friends
-   `protected`: class + derived classes

Default:

-   `class` members are private
-   `struct` members are public

------------------------------------------------------------------------

# 5. Constructor and Destructor

Constructor initializes object.

``` cpp
class A {
public:
    A() {}
    ~A() {}
};
```

Destructor releases resources when object lifetime ends.

Constructors can be:

-   default
-   parameterized
-   copy
-   move

------------------------------------------------------------------------

# 6. Copy constructor

``` cpp
A(const A& other);
```

Called to create an object from another object.

Important when a class owns resources.

------------------------------------------------------------------------

# 7. Shallow vs Deep Copy

Shallow copy copies pointer values.

``` text
A ──┐
    └──> same heap object
B ──┘
```

Deep copy creates independent resources.

``` text
A ──> heap object 1
B ──> heap object 2
```

If a class owns dynamic memory, the Rule of Three/Five becomes
important.

------------------------------------------------------------------------

# 8. Rule of Three / Five / Zero

### Rule of Three

If a class manually manages a resource and needs one of:

-   destructor
-   copy constructor
-   copy assignment operator

it often needs all three.

### Rule of Five

Modern C++ additionally considers:

-   move constructor
-   move assignment operator

### Rule of Zero

Prefer RAII types such as `std::vector`, `std::string`,
`std::unique_ptr` so the class does not manually manage ownership.

------------------------------------------------------------------------

# 9. Virtual functions

``` cpp
class Base {
public:
    virtual void show() {
        cout << "Base";
    }
    virtual ~Base() = default;
};

class Derived : public Base {
public:
    void show() override {
        cout << "Derived";
    }
};
```

``` cpp
Base* p = new Derived();
p->show();
```

prints `Derived`.

The virtual function enables runtime dispatch.

## Why virtual destructor?

If deleting a derived object through a base pointer:

``` cpp
delete p;
```

the base destructor should be virtual so the correct destructor chain
executes.

------------------------------------------------------------------------

# 10. Pure virtual function and abstract class

``` cpp
class Shape {
public:
    virtual double area() = 0;
};
```

A class containing a pure virtual function is abstract and cannot
normally be instantiated.

This is a common way to define an interface-like contract in C++.

------------------------------------------------------------------------

# 11. Overloading vs Overriding

                 Overloading             Overriding
  -------------- ----------------------- -------------------------------
  Relationship   same scope/interface    inheritance
  Parameters     differ                  compatible signature
  Binding        compile time            runtime with virtual dispatch
  Purpose        multiple ways to call   specialized behavior

------------------------------------------------------------------------

# 12. Composition vs Inheritance

**Inheritance:** Car IS-A Vehicle.

**Composition:** Car HAS-A Engine.

Prefer composition when the relationship is ownership/containment and
you do not need substitutability.

------------------------------------------------------------------------

# 13. Static members

A static data member belongs to the class rather than each object.

``` cpp
class Counter {
public:
    static int count;
};
```

All objects share it.

Static member functions do not have a `this` pointer.

------------------------------------------------------------------------

# 14. `this` pointer

Inside a non-static member function, `this` points to the current
object.

``` cpp
class User {
    int id;
public:
    User(int id) : id(id) {}
};
```

`this->id` refers to the object's member.

------------------------------------------------------------------------

# 15. Friend function

A friend can access private/protected members even though it is not a
normal member function.

Use sparingly because it weakens encapsulation.

------------------------------------------------------------------------

# 16. Virtual table intuition

For polymorphic classes, implementations commonly use a **vtable/vptr**
mechanism to support runtime dispatch.

Do not claim the C++ standard mandates a specific vtable layout; it
specifies behavior, not the implementation mechanism.

------------------------------------------------------------------------

# 17. RAII

Resource Acquisition Is Initialization.

Tie resource lifetime to object lifetime.

Examples:

-   `std::vector`
-   `std::string`
-   `std::unique_ptr`
-   `std::lock_guard`

This is fundamental modern C++.

------------------------------------------------------------------------

# 18. Smart pointers

### unique_ptr

Single ownership.

``` cpp
auto p = std::make_unique<int>(10);
```

### shared_ptr

Shared ownership with reference counting.

### weak_ptr

Non-owning reference to an object managed by `shared_ptr`, useful for
avoiding ownership cycles.

### Interview question

**Why prefer smart pointers?**

They make ownership explicit and automatically release resources
according to RAII, reducing leaks.

------------------------------------------------------------------------

# 19. SOLID

### S --- Single Responsibility

One class should have one reason to change.

### O --- Open/Closed

Open for extension, closed for modification.

### L --- Liskov Substitution

Derived types should be usable where the base type is expected without
breaking correctness.

### I --- Interface Segregation

Prefer small focused interfaces.

### D --- Dependency Inversion

Depend on abstractions rather than concrete implementations.

------------------------------------------------------------------------

# 20. Important interview Q&A

### Q: Encapsulation vs abstraction?

**Answer:** Encapsulation controls access to state and bundles state
with behavior. Abstraction hides implementation complexity and exposes a
useful interface.

### Q: Why runtime polymorphism needs virtual functions?

**Answer:** A base pointer/reference can refer to a derived object.
`virtual` enables dynamic dispatch so the most-derived override is
selected at runtime.

### Q: What is object slicing?

**Answer:** Copying a derived object into a base object by value
discards the derived-specific part.

### Q: Why should base destructors be virtual?

**Answer:** When deleting a derived object through a base pointer, a
virtual destructor ensures the derived destructor is invoked correctly.

### Q: Inheritance or composition?

**Answer:** Use inheritance for a true substitutable "is-a"
relationship. Use composition for "has-a" relationships and when you
want lower coupling.

### Q: What is RAII?

**Answer:** A resource is acquired during object initialization and
released automatically when the object's lifetime ends.

### Q: `struct` vs `class` in C++?

**Answer:** Both can have methods, constructors, inheritance and access
control. The default member access is public for `struct` and private
for `class`; default inheritance follows the same distinction.


# 21. More C++ interview topics

## Multiple inheritance and diamond problem

```text
        A
       / \
      B   C
       \ /
        D
```

If B and C both inherit A, D can receive two A subobjects.

Virtual inheritance can solve the duplicated-base-subobject issue.

## Exception handling

```cpp
try {
    // risky operation
}
catch (const std::exception& e) {
    // handle
}
```

Prefer RAII for resource cleanup. Destructors should not normally throw.

## Templates

Templates enable generic programming.

```cpp
template<typename T>
T maximum(T a, T b) {
    return a > b ? a : b;
}
```

STL containers/algorithms heavily use templates.

## STL containers to know

- `vector`
- `deque`
- `list`
- `stack`
- `queue`
- `priority_queue`
- `set`
- `map`
- `unordered_set`
- `unordered_map`

Know average/worst complexity and ordering guarantees.

## Const correctness

```cpp
const int x = 10;
const string& s
```

A `const` member function promises not to modify observable object state through that object.

## lvalue/rvalue and move semantics

Modern C++ can move resources instead of copying them.

```cpp
std::move(obj)
```

does not itself move anything; it casts an expression so move overloads can be selected.

`unique_ptr` is movable but not copyable.

## Interview Q: Why is `unique_ptr` non-copyable?

Because copying would create two owners of the same unique resource. It is movable to transfer ownership.
