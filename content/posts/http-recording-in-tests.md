---
title: "HTTP Recording in Tests"
date: 2023-08-16
tags: ["http", "recording", "python", "vcr", "vcrpy", "cassettes"]
draft: false
---

## Overview

How do you test code that calls an external API? The answer may seem simple at first -
APIs are the backbone of the internet, so surely we should have figured this out by now.

If you were like me, you probably ignored this problem when you wrote your first API test.
Why not simply perform live API calls in unit tests? Then a test intermittently fails due to
an API outage. Next devs complain tests can't be ran without internet access. Finally, your
API token expires after 90 days. This clearly isn't working.

The next logical solution is API mocking. If we can't make real API calls, then we'll simply
fake them! Sadly though, this solution is hardly perfect. We quickly find out that our mocks don't
accurately resemble the real world, which means your tests don't match what you're seeing in
production. It also takes a significant amount of time to create mock data, which deters
developers from writing any tests. How do we easily create tests that simulate real HTTP
requests but don't actually run real HTTP requests?

## HTTP Recording in Tests

Instead of manually mocking HTTP requests, there is a movement to record real HTTP request/response
pairs and use them instead of mocking. This strategy has the same benefits of mocking, but the
responses resemble the real world and do not require manual developer effort.

You can see this movement occurring in multiple programming languages. Depending on your programming
language, I recommend you take a look at:
- [pollyjs](https://github.com/Netflix/pollyjs)
- [easyvcr-java](https://github.com/EasyPost/easyvcr-java)
- [vcr](https://github.com/vcr/vcr)
- [vcrpy](https://vcrpy.readthedocs.io/en/latest/)

These libraries are typically used while writing unit tests in a manner similar to this:
- Developers write tests that run real HTTP requests
- Once tests have been finalized, the real HTTP requests are recorded as a file 
  alongside the tests and committed to the git repository
- When tests are ran by the CI build or other developers, the recorded HTTP requests
  are used when running the test, and therefore the tests never invoke any external APIs

# Concepts

Regardless of what framework you are using, here are some core concepts of
these frameworks that will help you get started.

`Cassettes/Recordings`: when HTTP requests are recorded, their request/response pairs need to
be stored in some location so they can be used during a replay. Depending on your framework,
this is called a cassette, recording, or something similar.

`Record Mode`: there is typically a global setting which tells the system whether HTTP requests
should be recorded or replayed. In `record` mode, real HTTP requests are performed, and any
requests perfomed are recorded into a file alongside their corresponding responses. In `playback`
mode, the HTTP request/response pairs are recalled from `record` mode and ran instead of real
HTTP requests.

`Adapters`: HTTP recording is typically done by patching functions that perform HTTP requests.
Different projects uses different HTTP libraries, so we need an adapter which knows
how to wrap our HTTP library of choice and perform the tasks needed to record and replay cassettes.

`Request Matchers`: How does the system know which response to return when there are multiple requests? There
are two typical strategies for this:
- `HTTP-based Request Matchers`: This matching strategy pairs compares the given request properties to the
  properties recorded in the cassette. This strategy is the generally preferred, but can be frustrating since
  the developer must fine tune the configuration used to match requests and responses.
- Alternatively, `order-based Request Matchers` pair the first request to the first response, second request
  to the second response, and so on.

## Code Examples

Interested in learning more? I highly suggest you take a look at this [vcrpy boilerplate](https://github.com/jpnauta/vcrpy-boilerplate),
which demonstrates how this technique can be done in Python.
