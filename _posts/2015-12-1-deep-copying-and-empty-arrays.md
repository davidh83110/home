---
title: "Deep copying Objects and Arrays"
date: 2015-12-1
description: Dealing with infinite nested structures
---

Oftentimes, especially when we deal with pure functions, we might need to deep copy objects and array. Although we may rely on JSON.stringify and JSON.parse methods for that, implementing a recursive routine that works for both objects and arrays, isn't something that hard.

***

### deepCopy

The idea is simple: we iterate through each key/value pair of an object (in the case of arrays, keys will be represented by numeric strings), and we copy each pair over a new object and return it.

We have watch to out for two things:

#### 1) Nested objects / arrays

Here is where the recursive part of our function kicks in: in the case of a nested object we simply recursively invoke deepCopy on that value.

#### 2) Arrays with holes

Arrays could contain holes, meaning their length won't be affected, but an index/value could be missing, i.e., var array = ['a', ,'b']. In this case the we don't have any array[1], but the length of such array will still return 3. For that reason, we want to be sure that we iterate only through the referenced keys. So how could we tell if array[1] is not just set to undefined but actually deferenced? 

Object.keys to the rescue! As we can treat Arrays as Objects when accessing their key/values we could rely for both data structures to  Object.keys(array) to retrieve an array containing all their keys. Later we can simply use those keys to iterate through arrays or objects. Doing so will also avoid us relying on obj.hasOwnProperty(key) as we would have done if we used a for in loop construct instead.


Let's see some code:

{% highlight javascript %}
function deepCopy(obj) {
  var copy = Array.isArray(obj) ? [] : {};

  var keys = Object.keys(obj);
  for (var i = 0; i < keys.length; i++) {
    var key = keys[i];
    if (obj[key] === undefined || obj[key] === null || typeof obj[key] !== 'object') {
      copy[key] = obj[key]; 
    } else {
      copy[key] = deepCopy(obj[key]);
    }
  }
  return copy;
}

// var obj = [{a:1, b: {b:2, c:3}, d: null, e: [1,2,3, [3, ,5]]}];
// JSON.stringify(deepCopy(obj)) === JSON.stringify(obj);
// -> true

{% endhighlight %}


