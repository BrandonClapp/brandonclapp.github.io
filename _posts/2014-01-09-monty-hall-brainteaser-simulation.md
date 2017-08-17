---
layout: post
title:  "Monty Hall Brainteaser Simulation"
date:   Jan 9, 2014
permalink: /monty-hall-brainteaser-simulation/
comments: true
---

The Monty Hall problem is a brain teaser, in the form of a probability puzzle (Gruber, Krauss and others), loosely based on the American television game show Let's Make a Deal and named after its original host, Monty Hall. The problem was originally posed in a letter by Steve Selvin to the American Statistician in 1975 (Selvin 1975a), (Selvin 1975b). It became famous as a question from a reader's letter quoted in Marilyn vos Savant's "Ask Marilyn" column in Parade magazine in 1990 (vos Savant 1990a):

<blockquote>Suppose you're on a game show, and you're given the choice of three doors: Behind one door is a car; behind the others, goats. You pick a door, say No. 1, and the host, who knows what's behind the doors, opens another door, say No. 3, which has a goat. He then says to you, "Do you want to pick door No. 2?" Is it to your advantage to switch your choice?</blockquote>

<a target="_blank" href="http://en.wikipedia.org/wiki/Monty_Hall_problem">Full Wikipedia Article</a>

Here is a quick **C#** script to simulate this puzzle.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading;

namespace ConsoleApplication1
{
    class Program
    {
        public static double Wins { get; set; }
        public static double TotalTries { get; set; }

        static void Main(string[] args)
        {

            for (; ; )
            {

                List<Door> doors = GenerateRandomizedDoors().ToList();

                // Doesn't matter if the selection is constant or random. Same result.
                //int selection = 2;
                int selection = GenerateRandomSelection();

                Door correctDoor = doors.Single(d => d.Result == true);

                Console.WriteLine("Choose a door.");
                foreach (Door door in doors) Console.WriteLine("Door " + door.Number);

                // int selection = int.Parse(Console.ReadLine());
                Console.WriteLine("Chose: " + selection);

                Door selectedDoor = doors.FirstOrDefault(d => d.Number == selection);

                // Remove the first or last false from the list (also randomized)
                if (RemoveIncorrectFromFront()) doors.Remove(doors.First(d => d.Result == false && d.Number != selectedDoor.Number));
                else doors.Remove(doors.Last(d => d.Result == false && d.Number != selectedDoor.Number));

                Console.WriteLine("\nChoose again.");
                foreach (Door door in doors) Console.WriteLine("Door " + door.Number);
                Console.WriteLine("Chose: " + selection);

                // second selection
                // selection = int.Parse(Console.ReadLine());

                selectedDoor = doors.First(d => d.Number == selectedDoor.Number); // currently just using the same one as originally selected.

                TotalTries++;
                if (selectedDoor.Number == correctDoor.Number)
                {
                    Console.WriteLine("\nCorrect!");
                    Wins++;
                }
                else Console.WriteLine("\nWrong.\nCorrect door was: " + correctDoor.Number);

                Console.WriteLine("\nWin pct: " + Wins / TotalTries);
                Console.WriteLine(Wins + " wins / " + TotalTries + " tries");

                Console.WriteLine("\n\n");
                Thread.Sleep(100);
            }
        }

        static IEnumerable<Door> GenerateRandomizedDoors()
        {
            var rand = RandomProvider.GetThreadRandom();

            List<bool> options = new List<bool> { true, false, false };
            List<Door> doors = new List<Door>();

            // Randomly add the options to doors list so that the order is random

            int indexToAdd = rand.Next(0, 3);

            doors.Add(new Door { Number = 1, Result = options[indexToAdd] });
            options.RemoveAt(indexToAdd);

            indexToAdd = rand.Next(0, 2);
            doors.Add(new Door { Number = 2, Result = options[indexToAdd] });
            options.RemoveAt(indexToAdd);

            doors.Add(new Door { Number = 3, Result = options[0] });
            options.RemoveAt(0);

            return doors;
        }
        static int GenerateRandomSelection()
        {
            var randomPick = RandomProvider.GetThreadRandom();
            int selection = randomPick.Next(1, 4);
            return selection;
        }
        static bool RemoveIncorrectFromFront()
        {
            Random rand = RandomProvider.GetThreadRandom();
            int determinator = rand.Next(0, 100);
            if (determinator < 50) return true;
            else return false;
        }
    }

    public class Door
    {
        public int Number { get; set; }
        public bool Result { get; set; }
    }

    public static class RandomProvider
    {
        private static int seed = Environment.TickCount;

        private static ThreadLocal<Random> randomWrapper = new ThreadLocal<Random>(() =>
            new Random(Interlocked.Increment(ref seed))
        );

        public static Random GetThreadRandom()
        {
            return randomWrapper.Value;
        }
    }
}
```
