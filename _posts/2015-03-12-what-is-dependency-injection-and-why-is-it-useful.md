---
layout: post
title:  "What is dependency injection and why is it useful?"
date:   March 12, 2015
permalink: /what-is-dependency-injection-and-why-is-it-useful/
tags:
  - Dependency-Injection
  - Design-Pattern
categories:
  - Design Patterns
comments: true
header: what-is-dependency-injection.jpg
---

A dependency is an external entity that a class, or function, needs to do it's job. Just as I am writing this blog post, I also have dependencies on my computer. Remove the computer and I surely would not be able to finish the post.

Injecting dependencies that a class needs is a good way to decouple our classes. Sure, any class can new up an object and call methods or access properties on that object, but doing so also convolutes that class with logic that doesn't necessarily belong there. Injecting a classes dependencies is a good way to apply the **single responsibility principal**, which promotes good object oriented design. If you're not familiar with it, it simply states that every class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class.

Consider the following example. Below, each class serves it's own purpose and has it's own job. Dependencies are injected into classes that need them. This is basically saying "give me the tools that I need, and I can do my job".

```csharp
namespace Scratchpad
{
    class Program
    {
        static void Main(string[] args)
        {
            Microwave microwave = new Microwave();
            Refrigerator refrigerator = new Refrigerator();

            // Dad's purpose is to make lunch.
            Dad dad = new Dad(microwave, refrigerator);

            // Kid's purpose is to play.
            // But they must eat first before they play.
            Kid kid = new Kid(dad);

            kid.Play();
        }
    }

    // Responsible for altering molecular structure of ingredients
    // to produce lunch
    public class Microwave
    {
        public Lunch Zap(Ingredient[] ingredients, int seconds)
        {
            // Do a bunch of scientific stuff
            return new Lunch();
        }
    }

    // Responsible for storing ingredients
    public class Refrigerator
    {
        public Ingredient[] Ingredients { get; set; }
    }

    public class Lunch { }

    public class Ingredient { }

    // Resonsible for making lunch.
    public class Dad
    {
        private Microwave Microwave { get; set; }
        private Refrigerator Refrigerator { get; set; }

        public Dad(Microwave microwave, Refrigerator refrigerator)
        {
            Microwave = microwave;
            Refrigerator = refrigerator;
        }

        public Lunch FixLunch()
        {
            Ingredient[] ingredients = GetIngredients(Refrigerator);
            Lunch lunch = Microwave.Zap(ingredients, 60);

            return lunch;
        }

        private Ingredient[] GetIngredients(Refrigerator refrigerator)
        {
            // ... Open connection to refrigerator
            return refrigerator.Ingredients;
        }
    }

    // Responsible for playing.
    public class Kid
    {
        private Dad Dad;
        private bool _hungry = true;

        public Kid(Dad dad)
        {
            Dad = dad;
        }

        private void Eat()
        {
            Lunch lunch = Dad.FixLunch();
            // .. Eat the lunch... PickUpFork(), Chew() etc..

            _hungry = false;
        }

        public void Play()
        {
            if (_hungry)
            {
                Eat();
            }

            // ... Have fun playing.
        }
    }
}
```

Here, I'm trying to adhere to the single responsibility principal. In the previous example, the **Dad** class has the responsibility of making lunch. The **Kid** class only has the responsibility of playing, but first she must eat lunch. The Kid class doesn't know how to make lunch, nor is it the Kid's responsibility to make lunch (in this example) so the Dad is the dependency. So that the Kid doesn't need to know the complexities of making lunch, we keep this logic separate in the Dad class.

Dad knows how to make lunch, but he also has dependencies of his own. He must consult with the **Refrigerator** and **Microwave** classes in order to help in the process. Dad doesn't need to know the complexities of vibrating atoms to cook food or store cold sandwich meat, so these responsibilities are also outsourced to the Microwave and Refrigerator classes.

Each class is properly injected with the dependencies that it requires to perform it's job. When we create a new Dad object, we specify the Refrigerator and Microwave classes that will help him fulfill his responsibility of making lunch. When we create a new Kid object, we supply her with the Dad object that will help her eat so that she can fulfill her responsibility.

#### Increasing dependency injection flexibility with interfaces

By "demanding" the dependencies in the constructor, you make your classes more flexible - especially if they are interfaces and not concrete implementations. Why is this, you may ask? Suppose that the Dad class constructor in the example above required IMicrowave and IRefrigerator interfaces instead of the concrete implementations to be created. Now suppose that the family got a complete kitchen makeover and replaced their appliances. In the real world, this may be the equivalent to creating a new version of a library that changes how it's responsibilities are implemented. The Microwave and Refrigerator just got upgraded to new implementations. There are a couple of things to be said here.

<ol>
<li>If Dad had been constructing the Microwave and Refrigerator objects internally, a concrete implementation would have had to been created before he could make lunch. This means that when the new kitchen appliance models (concrete implementations) comes along, the creation of the appliances need to be changed in the Dad class. Imagine if the Microwave and Refrigerator were being used by the entire family and/or there were more than two appliances in the kitchen.</li>
<li>If Dad's constructor now takes interfaces instead of concrete implementations, nothing inside of the Dad class needs to change. The new model (concrete implementation) of IMicrowave should still know how to *Zap()* and the new concrete of IRefrigerator should still know how to hold an array of *Ingredient*'s. So long as the new classes still implement the same interface, there shouldn't be any breaking changes.</li>
</ol>


#### Why should I use dependency injection?

Dependency injection is a pattern at your disposal. Like all tools and approaches, use it when it's appropriate. Ideally, it's used to promote testability and easy maintenance.

**Pro's**

- Flexibility
- Testability
- Easier maintenance
- Decouples classes (which is a reason for the previous 3 points)

**Con's**

- Creating instances of your classes can become cumbersome, especially when your classes grow to too many responsibilities and have too many dependencies. If not maintained, it can become a nightmare.


Because of the con listed above, dependency injection frameworks were created to help with the creation of dependency objects. This is also known as **inversion of control**, which I'll cover in a follow up post.
