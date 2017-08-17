---
layout: post
title:  "How To: ASP.NET MVC HtmlHelper Extension Methods"
date:   May 23, 2014
permalink: /how-to-asp-net-mvc-htmlhelper-extension-methods/
tags: C# ASP.NET MVC
comments: true
---

Creating extension methods for our HtmlHelpers is a great way to minimize a lot of logic in our view. HtmlHelper extensions minimize unnecessary clutter and keep our view focused on how the page is displayed to our user rather than the logic behind it.

**Create a Helper Extension Method**

```csharp
using System.Net;
using System.Web.Mvc;

namespace SomeProject.HtmlExtensions
{
    public static class Helper
    {
        public static bool RemoteFileExists(this HtmlHelper helper, string remoteUrl)
        {
            try
            {
                //Creating the HttpWebRequest
                HttpWebRequest request = WebRequest.Create(remoteUrl) as HttpWebRequest;

                //Setting the Request method HEAD, you can also use GET too.
                request.Method = "HEAD";

                //Getting the Web Response.
                HttpWebResponse response = request.GetResponse() as HttpWebResponse;

                //Returns TRUE if the Status code == 200
                return (response.StatusCode == HttpStatusCode.OK);
            }
            catch
            {
                //Any exception will return false.
                return false;
            }
        }
    }
}
```

##### Use the Extended Helper Method in the View

```csharp
// You will need to add a using directive at the top
// so that our view knows that the extension method exists

@using SomeProject.HtmlExtensions

@if(Html.RemoteFileExists(remoteUrl: "http://someurl.com/images/someimage.jpg"))
{
    // The remote file existed. Do something with it.
}
```

**Notice that the extension method takes a single string and can only be used by the Html property of the page (which is the WebViewPage object)**

![Extension Method](/assets/images/posts/content/implement-extension.png)
<!-- <img src="http://brandonclapp.com/wp-content/uploads/2014/05/implement-extension.png" alt="implement-extension" width="584" height="97" class="alignnone size-full wp-image-56" /> -->
