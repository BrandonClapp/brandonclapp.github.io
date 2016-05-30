---
layout: post
title:  "Entity Framework Custom Database Initializer From web.config"
date:   2013-03-06 12:00:00 -0500
permalink: /entity-framework-custom-database-initializer-from-web-config/
comments: true
---

Create a class that inherits from DropCreateDatabaseIfModelChanges&lt;MyContext&gt; where MyContext is the class that is extends DbContext. The DropCreateDatabaseIfModelChanges class has a virtual method named Seed that can be overridden to enter sample data into the database. As the name infers, this database initializer class will be invoked when one of the model (POCO) classes in MyContext is changed. If a model is changed, the database is dropped then recreated with seed data. If it never existed to begin with, it just gets created with seed data.

### Create a Database Initializer Class

```csharp
class ContextInitializer : DropCreateDatabaseIfModelChanges
{
  protected override void Seed(Dbc context)
  {
    List messages = new List
    {
      new Message { Content = "Content for first message" },
      new Message { Content = "Content for second message" }
    };

    // Add data into context
    foreach (Message m in messages)
    {
      context.Messages.Add(m);
    }

    // Save the changes made to the context
    context.SaveChanges();
    InitializeDatabase(context);
  }
}
```


### Tell web.config an Initializer Exists

This class can now be specified in the web.config file so that the program knows where to look to find the initializer that we just created. If the database already exists and the tables in the database already match those of the POCO/model classes, nothing happens. If the model has changed or the database doesn’t exist, this class will be invoked, resulting in the database being seeded with sample data. This is probably something that should be turned off in production but could speed up the development process since it keeps the database sync with the .NET objects used by the developer, minimizing database inconsistencies.

**web.config:**

```xml
<!--Context.Dbc is the namespace qualified class of my context. Context is the DLL that it is found in.-->
<!--Context.ContextInitalizer is the namespace qualified class of my custom initializer. Context is the DLL that it is found in.-->
<entityFramework>
    <defaultConnectionFactory type="System.Data.Entity.Infrastructure.SqlConnectionFactory, EntityFramework" />
    <!-- This specifies that EF has an initializer -->
    <contexts>
      <context type="Context.Dbc, Context">
        <databaseInitializer type="Context.ContextInitializer, Context"></databaseInitializer>
      </context>
    </contexts>
    <!--End initializer-->
</entityFramework>
```
