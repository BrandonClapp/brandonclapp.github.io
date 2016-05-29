---
layout: post
title:  "C#: Performing GET and deserializing a restful web service endpoint returning json"
date:   April 18, 2014
permalink: /c-invoking-and-deserializing-a-restful-web-service-endpoint-returning-json/
tags: C# HTTP
comments: true
---

HTTP requests can be made a number of different ways in .NET. The **HttpRequest** and **WebClient** (older) both provide functionality for creating HTTP requests. Below illustrates how to use each of these classes.

```csharp
using Newtonsoft.Json;
using System.Collections.Generic;
using System.Net;
using System.Net.Http;
using System.Web.Mvc;

namespace WebApiTestApp.Controllers
{
    public class HomeController : Controller
    {
        // Using HttpClient class
        public ActionResult Index()
        {
            string temp = "";
            HttpClient client = new HttpClient();

            string result = client.GetStringAsync("http://us.battle.net/api/wow/data/character/races?locale=en_US").Result;

            Result re = JsonConvert.DeserializeObject<Result>(result);
            foreach (var race in re.races)
            {
                temp += race.Name + " ";
            }

            // Alternatively, don't deserialize to a type. Use a dynamic object instead.

            dynamic dyn = JsonConvert.DeserializeObject(result);

            foreach (var race in dyn.races)
            {
                temp += race.name + " ";
            }

            return View();
        }

        // Using WebClient
        public ActionResult Index2()
        {
            string temp = "";

            WebClient client = new WebClient();
            string result = client.DownloadString("http://us.battle.net/api/wow/data/character/races?locale=en_US");

            Result re = JsonConvert.DeserializeObject<Result>(result);
            foreach (var race in re.races)
            {
                temp += race.Name + " ";
            }

            return View();
        }
    }

    public class Result
    {
        public List<Race> races { get; set; }
    }

    public class Race
    {
        [JsonProperty("id")]
        public int Id { get; set; }

        [JsonProperty("mask")]
        public string Mask { get; set; }

        [JsonProperty("side")]
        public string Side { get; set; }

        [JsonProperty("name")]
        public string Name { get; set; }
    }
}
```
