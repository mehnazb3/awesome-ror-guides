# SOLID PRINCIPLES

SOLID principles in Ruby on Rails promotes clean, modular, and maintainable code. By adhering to these guidelines, developers can ensure their applications remain flexible and scalable as they evolve. Incorporating SOLID principles contributes to the overall health and longevity of a Ruby on Rails project, making it easier to adapt to changing requirements and enhance functionality. These five principles are:

## 1. Single responsibility principle

The Single responsibility principle states that a class to have only one reason to change, emphasizing that a class should encapsulate a single responsibility. In RoR, this can be applied by ensuring that each model, controller, or service class is focused on handling a specific task or functionality. This helps in creating modular and cohesive components.

## 2. Open closed principle

The Open closed principle encourages extending functionality without modifying existing code. In RoR, this can be achieved through the use of modules and inheritance. By designing classes that are open for extension but closed for modification, developers can add new features or behaviors without altering existing, working code.

## 3. Liskov Substitution principle

The liskov Substitution principle states that objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program. In RoR, this involves ensuring that subclasses can seamlessly substitute their parent classes. This principle aids in creating interchangeable components and promotes code reliability.

## 4. Interferce segregation principle

The ISP suggests that a class should not be forced to implement interfaces it does not use. In RoR, this can be applied by designing specific interfaces tailored to the requirements of individual classes. By adhering to this principle, developers prevent the introduction of unnecessary dependencies. Instead of having one generic interface, split it into multiple interfaces.

## 5. Dependency inversion principle

The dependency inversion principle emphasizes high-level modules not depending on low-level modules but both depending on abstractions. In RoR, this involves using dependency injection, interfaces, and inversion of control containers to achieve a decoupled and flexible code structure. This principle facilitates easier testing and maintenance.