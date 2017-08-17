---
layout: post
title:  "Connection strings with Entity Framework 5 (Code First)"
date:   2013-03-06 22:19:00 -0500
permalink: /connection-strings-with-entity-framework-5-code-first/
comments: true
---

First, we add our connection string that Entity Framework will use to our web.config. Be sure to replace the connection name, server, and database from the example with your own values.

**web.config**

#### Add the connection string to web.config

```xml
<!--web.config of the project that is utilizing Entity Framework-->

<connectionStrings>
<add name="YourConnectionName"
		providerName="System.Data.SqlClient"
		connectionString="Server=YourServerNameOrIP; Database=YourDatabaseName;
		Integrated Security=SSPI" />
</connectionStrings>
```

This connection string will target the server’s (YourServerNameOrIP) default instance of SQL server (MSSQLSERVER) and create the database “YourDatabaseName” using integrated security (meaning that it will use the credentials the IIS application pool identity that your site is being hosted on). Alternatively, a user id and password could be specified here if a user with appropriate privileges has been set up in SQL server. At minimum, the user will need to have the dbcreator server role on SQL server in order for the database to be created.

#### Specify your connection string to DbContext

```csharp
// In the class inheriting from DbContext

namespace Context
{
    public class Dbc : DbContext
    {
        public Dbc() : base("YourConnectionName") { }
        public DbSet Messages { get; set; }

    }
}
```

Provide the connection string to the constructor. This is what tells entity framework, specifically, which connection string to use. In this case, it would use the connection string named “YourConnectionName”.

**Troubleshooting Note:**

If issues arise getting Entity Framework working with the MSSQLSERVER instance, open up SQL Server Configuration Manager on the machine hosting the database, go to SQL Server Services, and make sure that the SQLEXPRESS instance is stopped (if it is installed). An incorrect connection string may cause entity framework to use its “default” behavior, which may be (in my case it was) to use the SQLEXPRESS instance. If this happens, it will appear that the connection string is correct and working, but may not be working on the intended instance.
