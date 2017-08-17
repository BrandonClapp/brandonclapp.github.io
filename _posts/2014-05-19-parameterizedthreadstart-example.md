---
layout: post
title:  "C#: Passing parameters to a thread with ParameterizedThreadStart"
date:   May 19, 2014
permalink: /parameterizedthreadstart-example/
tags: C# Threading
comments: true
---

Much like the name applies, **ParameterizedThreadStart** provides a way to pass parameters to a thread upon start. In the following example, the variable *name* is being passed to the *SayHello* method when `myThread.Start(name)` is called.

```csharp
using System;
using System.Threading;

namespace Sample
{
    class Program
    {
        static void Main(string[] args)
        {
            string name = "Bob";

            // Pass a new ParameterizedThreadStart object to the Thread class constructor.
            // The ParameterizedThreadStart construcotr takes a delegate
            // pointing to an event handler which returns void and takes an object.

            Thread myThread = new Thread(new ParameterizedThreadStart(SayHello));

            // The parameter that is passed to the event handler is passed through
            // the Start(object) method.

            myThread.Start(name);
        }

        // Worker method must take object as parameter.
        // The actual type can be cast in the method body.
        public static void SayHello(object name)
        {
            Console.WriteLine("Hello, {0}.", (string)name);
        }
    }
}

```

The following red and blue lines help to illustrate what is happening in the previous block of code.

![ParameterizedThreadStart](/assets/images/posts/content/ParameterizedThreadStart.png)
