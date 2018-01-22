---
title: "Enumeration - Best Practices"
date: 2018-01-10
tags: ["enumeration", "programming", "project"]
draft: false
---

## The Dream

Dear fellow developers, let me propose a big idea.

_**In the age of multi-repository and multi-language projects, it should
be easy to create, understand, and manage enumerations and constants in
a platform-independent manner.**_

This is the end goal of my project, [Enumerate.js][enumerate].

Differences of opinion have prevented this from happening in our world,
but I am confident that this is possible.

First, let me explain the issue with an example.

## The Problem

Bob, Jane, Carl, Linda and Jim work at Beautiful Software Incorporated. They are
working together on a multi-repository project and a simple database
of `User` objects. Here is their story.

One day, Bob decides to add an enumerated field called `user_type` to
their `User` database. Bob decides that the number `42` will represent a user
with a type of `Client`.

Jane uses Bob's documentation and commits this code into repository #2.

```
if (user.user_type == 42) {...}
```

Carl is not happy about this. Carl does not know what `42` means.
Carl decides to edit Jane's code.

```
if (user.user_type == 42) {...}  // WTF is '42'?!?!
```

Linda knows how to solve this. She edit's the code like this.

```
USER_TYPES = {
  EMPLOYEE: 42,
  ...
}

if (user.user_type == USER_TYPES.EMPLOYEE) {...}
```

Now Jim is angry. He does not want to import `USER_TYPES` everywhere, and
is worried that `USER_TYPES` will become out of sync with repository #1.

*Surely there must be a better way than this...*

## The Principles

*All developers have ran into disagreements like these*. Now, I would
like to propose some principles for enumerations to solve these issues.

### 1. Constants should be human readable

While integer-based enumerations have good performance implications, they
are difficult to read. By contrast, string-based enumerations are
much easier to read.

In the modern age of fast computers, the need for human readability almost
always outweighs the cost of performance.

```
if (user.user_type == 'employee') {...}  ✓ Good - I can understand this code easily
if (user.user_type == 2) {...}  x Bad - Not readable - what is '2'?
```

### 2. Constants should be globally unique for the entire project

To avoid ambiguity, all projects and enumerations should have uniquely
identifiable constants.

```
x Bad - Two enumerations have the same constants 'foo' and 'bar'
Enum1 = {'foo', 'bar', 'qux'}
Enum2 = {'foo', 'bar'}

✓ Good - The prefixes 'e1_' and 'e2_' make constants unique
Enum1 = {'e1_foo', 'e1_bar', 'e1_qux'}
Enum2 = {'e1_foo', 'e1_bar'}
```

### 3. Conditional statements with enumerations are not future proof

```
✓ Good - future proof and readable
DATA_MAP = {
   'employee': 'a',
   'user': 'b',
   'other': 'c',
}
print DATA_MAP[user.user_type]

x Bad
if (user.user_type == 'employee') print 'a'
if (user.user_type == 'user') print 'b'
if (user.user_type == 'other') print 'c'
```

### 4. Enumeration maps should be platform independent and serializable

It should be easy to transfer an enumeration from one repository to another.
Application-logic specific code should be ran sometime after the first
definition.

```
USER_MAP = {  x Bad - 'Date' and `MyClass1` are programming language specific
   'employee': Date(1, 2, 3),
   'employee': MyClass1,
}
```

### 5. It should be easy to synchronize constants over multiple projects

If a developer adds a new constant to an enumeration, it should be
easy and simple to transfer those changes to all related projects.


[enumerate]: https://github.com/enumeratejs