---
layout: post
title:  ".NET Delegates (Typed Function Pointers)"
date:   May 19, 2014
permalink: /net-delegates-typed-function-pointers/
tags: C# Delegates
comments: true
---

A delegate is the .NET version of a type safe function pointer. All threads require an entry point to start execution. By definition when a primary thread is created it always runs Main() as it's entry point. Any additional threads you create will need an explicitly defined entry point - a pointer to the function where they should begin execution. So threads always require a delegate.

Delegates are often used in threading for other purposes too, mainly callbacks. If you want a thread to report some information back such as completion status, one possibility is to create a callback function that the thread can use. Again the thread needs a pointer to be able to execute the callback so delegates are used for this as well. Unlike an entry point these are optional, but the concept is the same.

The relationship between threads and delegates is secondary threads cannot just call methods like the primary app thread, so a function pointer is needed instead and delegates act as function pointers.

You do not see the delegate and you did not create one because the framework is doing it for you in the Thread constructor. You can pass in the method you want to use to start the thread, and the framework code creates a delegate that points to this method for you. If you wanted to use a callback you would have to create a delegate yourself.

Here is code without lambda expressions. SomeClass has some processing that takes a long time and is done on background threads. To help with this the SomeThreadTask has been created, and it contains the process code and everything the thread needs to run it. A second delegate is used for a callback when the thread is done.

Real world code would probably be more complicated, and the class shouldn't have to know how to create threads etc so it may make more sense to have manager objects, however, this example conveys the overall concept.

```csharp
// Create a delegate for our callback function.

using System;
using System.Threading;
public delegate void SomeThreadTaskCompleted(string taskId, bool isError);


public class SomeClass
{

    public static void Main(string[] args)
    {

    }

    private void DoBackgroundWork()
    {
        // Create a ThreadTask object.

        SomeThreadTask threadTask = new SomeThreadTask();

        // Create a task id.  Quick and dirty here to keep it simple.  
        // Read about threading and task identifiers to learn
        // various ways people commonly do this for production code.

        threadTask.TaskId = "MyTask" + DateTime.Now.Ticks.ToString();

        // Set the thread up with a callback function pointer.

        threadTask.CompletedCallback =
            new SomeThreadTaskCompleted(SomeThreadTaskCompletedCallback);


        // Create a thread.  We only need to specify the entry point function.
        // Framework creates the actual delegate for thread with this entry point.

        Thread thread = new Thread(threadTask.ExecuteThreadTask);

        // Do something with our thread and threadTask object instances just created
        // so we could cancel the thread etc.  Can be as simple as stick 'em in a bag
        // or may need a complex manager, just depends.

        // GO!
        thread.Start();

        // Go do something else.  When task finishes we will get a callback.

    }


    // Method that receives callbacks from threads upon completion.

    public void SomeThreadTaskCompletedCallback(string taskId, bool isError)
    {
        // Do post background work here.
        // Cleanup the thread and task object references, etc.
    }
}


// ThreadTask defines the work a thread needs to do and also provides any data
// required along with callback pointers etc.
// Populate a new ThreadTask instance with any data the thread needs
// then start the thread to execute the task.

internal class SomeThreadTask
{

    private string _taskId;
    private SomeThreadTaskCompleted _completedCallback;

    /// <summary>
    /// Get. Set simple identifier that allows main thread to identify this task.
    /// </summary>
    internal string TaskId
    {
        get { return _taskId; }
        set { _taskId = value; }
    }


    // Get, Set instance of a delegate used to notify the main thread when done.

    internal SomeThreadTaskCompleted CompletedCallback
    {
        get { return _completedCallback; }
        set { _completedCallback = value; }
    }


    // Thread entry point function.

    internal void ExecuteThreadTask()
    {
        // Often a good idea to tell the main thread if there was an error
        bool isError = false;

        // Thread begins execution here.

        // You would start some kind of long task here
        // such as image processing, file parsing, complex query, etc.

        // Thread execution eventually returns to this function when complete.

        // Execute callback to tell main thread this task is done.
        _completedCallback.Invoke(_taskId, isError);


    }

}
```
