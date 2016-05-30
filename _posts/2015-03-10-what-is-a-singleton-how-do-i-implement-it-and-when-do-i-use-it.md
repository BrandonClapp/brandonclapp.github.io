---
layout: post
title:  "What is a Singleton? How do I implement it? And when do I use it?"
date:   March 10, 2015
permalink: /what-is-a-singleton-how-do-i-implement-it-and-when-do-i-use-it/
tags:
  - Singleton
  - Design-Pattern
categories:
  - Design Patterns
comments: true
---

This explanation will use C# to explain the basic ideas of a singleton and the singleton pattern. Keep in mind that if you're using a programming language that doesn't support classes, this explanation may require a bit more analysis.

#### What is a singleton?

A singleton is simply a class which only allows a single instance. You can't new up instances of a singleton class. Static classes in C# are a good example of singleton behavior because, in most cases, there can only ever be one instance of a static class.

```csharp
namespace Scratchpad
{
    class Program
    {
        static void Main(string[] args)
        {
            // This is valid.
            Logger.Log("Yo");

            // This is a compile time error.
            Logger singleton = new Logger("Yo");
        }
    }

    public static class Logger
    {
        public static void Log(string msg)
        {
            // Logging!
        }
    }
}
```

Static classes do not have constructors, are sealed (you can't derive from them), and are abstract (you can't instantiate it directly). While static classes and singleton implementations are very similar in behavior, there are differences. The differences can be [summed up by Jon Skeet](http://stackoverflow.com/a/519536/1730061){:target="_blank"} as


> A singleton allows access to a single created instance - that instance
(or rather, a reference to that instance) can be passed as a parameter
to other methods, and treated as a normal object.
A static class allows only static methods.


If you only need static methods from your singleton, then just use a static class to expose these static methods - There is no reason to implement your own singleton. However, if you need to be able to use your singleton as if it were a regular instance of a class, then read on.

The single instance of the class will be distributed from logic internal to our singleton implementation. It must ensure that one, and only one, instance is ever created. Now that we know vaguely what a singleton is, lets look at how we might implement one.

#### How do I implement a singleton?

```csharp
namespace Scratchpad
{
    class Program
    {
        static void Main(string[] args)
        {
            // The first time, we get a new instance of Instance
            Logger.Instance.Log("Yo");

            // The second time,
            // Instance is a references to the first one created.
            Logger.Instance.Log("Hi there");
        }
    }

    public class Logger
    {
        private static Logger instance;

        private Logger()
        {
            // Private constructor,
            // which cannot be called to create new instances.
            // Stuff that should only happen once
        }

        public static Logger Instance
        {
            get
            {
                // If our private instance field is null
                if (instance == null)
                {
                    // new up an instance
                    // and store it in our private field.
                    instance = new Logger();
                }
                return instance;
            }
        }

        public void Log(string msg)
        {
            // Logging.
        }
    }
}
```

Here, our **Logger** class has a private constructor. This restricts outside sources from creating *new* instances of the class. The `Instance` property on the Logger class has a getter which holds the logic for how the entire class is referenced. First, we check to see if `instance`, a private field of type Logger, is null. If it is, then we're going to assume that it hasn't been accessed before, and therefore it is okay to new up an instance of the Logger class and store it into the private field.

The next time that the getter is called, the private field, `instance`, will no longer be null. In fact, it will refer to the Logger object that we created the first time that it was accessed. Rather than creating a new instance again, the original is returned.

While this conveys the basic idea of what a singleton is supposed to do, there is still one problem. Concurrency. **The previous code only works for a single thread.** Keep in mind, though, that even if you don't intend to use the code that you write in a multi-threaded fashion, that doesn't stop others from doing so.

#### Threading with Singleton's

In the previous code example, we created an implementation for a singleton, however, we didn't account for what would happen if multiple threads were trying to access our `Instance` property at the same time. There is a possibility that two threads will attempt to check if the conditional statement

```
instance == null
```

is true at the same time. Suppose that this line is being run by two (or more) threads at the same time, but neither thread has actually created a new instance and assigned it to the private `instance` field. The outcome of this scenario would result in multiple instances of our singleton, meaning that our singleton implementation currently is not thread safe.

[What is this thing you call thread safe?](http://blogs.msdn.com/b/ericlippert/archive/2009/10/19/what-is-this-thing-you-call-thread-safe.aspx){:target="_blank"}

```csharp
public class Logger
{
    private static readonly object mutex = new object();
    private static Logger instance;

    private Logger()
    {
    }

    public static Logger Instance
    {
        get
        {
            lock (mutex)
            {
                // Only one thread can get into this
                // block of code at a time now.
                if (instance == null)
                {
                    instance = new Logger();
                }
                return instance;
            }
        }
    }

    public void Log(string msg)
    {
        // Doing logging.
    }
}
```

To fix this issue, we first start by creating a static readonly field object named mutex. If you're not familiar with what a mutex is, it's discussed in [Thread Synchronization](https://msdn.microsoft.com/en-us/library/ms173179.aspx){:target="_blank"} on MSDN. The following is the best definition that I've been able to find.

**`Mutex`** - A mutual exclusion object that allows multiple threads to synchronize access to a shared resource. A mutex has two states; locked and unlocked. Once a mutex has been locked by a thread, other threads attempting to lock it will block (stop until the lock has been removed). When the locking thread unlocks (releases) the mutex, one of the blocked threads will acquire (lock) it and proceed.

Locking can get very complicated, and I'm by no means the person that you want explaining it. The point is, though, that using singleton's with multiple threads can present it's own set of problems, and in most cases, even make the singleton implementation more work than it's worth. In most cases, a static class with static methods is the easier approach.

#### When to use a singleton

Singletons and static classes with static data members are nothing more than global variables with fancy packaging. When you get right down to it, this is all a singleton is. There are few cases when global objects make sense. One scenario that is commonly used for singleton's is logging. It wouldn't make much sense to create multiple instances of the same logger, right? In many cases, you start with the idea that you'll only ever need one logger, but what happens when you suddenly need an email logger and a chat logger? Consider that you're using a singleton to reference your database. What happens when another database gets added to your project? Also, few objects that we create will be used everywhere within our application, so having this global behavior may not make sense in most cases. Because of it's global nature and not adhering to an object oriented paradigm, some also consider it to be an anti-pattern. Instead, an easier approach may be to use regular classes that can be instantiated and simply only instantiate them once.

#### Summary

- Singleton's are often thought of to be an anti-pattern.
- The general consensus is that singleton's are bad.
  - [Are singleton's really that bad?](http://stackoverflow.com/questions/1020312/are-singletons-really-that-bad){:target="_blank"}
  - [What is so bad about singletons?](http://stackoverflow.com/questions/137975/what-is-so-bad-about-singletons){:target="_blank"}
- Implementing a singleton requires additional threading considerations.
- In most cases, you don't need to implement your own singleton. Static classes with static methods work just as well.
