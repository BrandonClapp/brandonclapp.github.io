---
layout: post
title:  "Arranging an array of flat paths into a JSON tree like structure"
date:   March 4, 2015
permalink: /arranging-an-array-of-flat-paths-into-a-json-tree-like-structure/
tags: JavaScript treeview array
comments: true
---

I recently worked on a project where I was required to take an array of flat paths separated by slashes and arrange them into a JSON object in order to display them in a tree from the front end. To be clear, the paths that I was provided looked something like this. The following example utilizes [lodash](https://lodash.com/){:target="_blank"}, but could also be accomplished with [`Array.prototype.forEach()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach){:target="_blank"}. I, unfortunately, had to support IE8.

**"Flat Paths" example:**

```
/FolderA/FolderB/FolderC/Item1
/FolderA/FolderB/Item1
/FolderB/FolderD/FolderE/Item1
/FolderB/FolderD/FolderE/Item2
/FolderA/FolderF/Item1
/ItemInRoot
```

Given this list of paths, I came up with the following solution. First we will organize our list of paths into an array, like so.

```javascript
var paths = [
    '/FolderA/FolderB/FolderC/Item1',
    '/FolderA/FolderB/Item1',
    '/FolderB/FolderD/FolderE/Item1',
    '/FolderB/FolderD/FolderE/Item2',
    '/FolderA/FolderF/Item1',
    '/ItemInRoot'
];
```

Once we have our paths in an array, we then need to set up the function that will process this array.

```javascript
function arrangeIntoTree(paths, cb) {
    var tree = [];

    // This example uses the underscore.js library.
    _.each(paths, function(path) {

        var pathParts = path.split('/');
        pathParts.shift(); // Remove first blank element from the parts array.

        var currentLevel = tree; // initialize currentLevel to root

        _.each(pathParts, function(part) {

            // check to see if the path already exists.
            var existingPath = _.findWhere(currentLevel, {
                name: part
            });

            if (existingPath) {
                // The path to this item was already in the tree, so don't add it again.
                // Set the current level to this path's children
                currentLevel = existingPath.children;
            } else {
                var newPart = {
                    name: part,
                    children: [],
                }

                currentLevel.push(newPart);
                currentLevel = newPart.children;
            }
        });
    });

    cb(tree);
}
```

Now that we've implemented our function for arranging items into a tree, we can now consume it.

```javascript
arrangeIntoTree(paths, function(tree) {
    console.log('tree: ', tree);
});
```

Upon invoking our method, we should see something similar to the following in our console.

![JSON Tree Structure](/assets/images/posts/content/json-tree-structure.png)
