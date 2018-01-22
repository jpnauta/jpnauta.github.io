---
title: "Javascript - Asynchronously Synchronous"
date: 2014-02-24
tags: ["javascript", "nodejs"]
draft: false
---

One of the most powerful features of Javascript is its *callback*
system. While callbacks act similar to threads, they are distinctly
different and more powerful.

Callbacks are similar to threads because they act in an
*asynchronous* manner. This means that they may occur at any time
and in any order (within reason). This is important because it
prevents *blocking*. For example, other tasks can be performed
while a task is reading information from a hard disk.
This is absolutely imperative for any high performance software
system.

However, callbacks are *not truly asynchronous* because they only
allow one task to be performed at a time. Unlike threads, no two
callbacks can be running at the same time.

_So what?_ This means that Javascript callbacks can share variables
with little to no headache. Consider the following situation in NodeJS:

```
var filePaths = ['test.txt', 'test2.txt'];
var fileCount = 0; //Indicates how many files in filePaths exist

for (var i in filePaths) {
    fs.exists(filePaths[i], function(exists) {
        if (exists) {
            fileCount++; //If file exists, increase counter
        }
    });
}
```

If this were implemented using threads, there’s a possibility
that the file count would become corrupted due to
[concurrency issues](http://www.vogella.com/tutorials/JavaConcurrency/article.html).
However, because of callbacks, the system can be efficient, yet
avoid (most) threading problems.

All in all, callbacks are hybrids of threads, balancing performance
and simplicity. They are _asynchronously synchronous_ because they
could be called at any time, yet they cannot run at the same time
as other functions.
