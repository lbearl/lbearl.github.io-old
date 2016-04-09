---
layout: post
title: Enterprise Software Retrospective
---

## One Month In
The company I work for recently started a new initiative: a brand new piece of software completely divorced from the software we traditionally worked on. I along with three of my colleages were selected to lead the entire architecture and build out of core features. All four of us are somewhat experienced developers, but none of us has ever worked on an architecture team at the very beginning of a software project which is very much enterprise-class software.

Just to not confuse anyone, when I say enterprise-class or "big" software I'm specifically referring to software that is expected to grow to encompass multiple modules and have a codebase in excess of 500 KLOC. For smaller applications the concerns I bring up here probably aren't relevant. In this article I am specifically referring to software which is (hopefully) going to have a lifetime measured in many years if not decades with hopefully only minimal refactoring of the core structure. 

## What We Did
As happens so often in the software world, we were given a whole bunch of pretty "loose" requirements, and it was up to the four us with minimal direction to prioritize and implement everything that we needed. The project basically ended up being implemented in the following order:

1. Project Layout (we used the [Onion Architecture](http://jeffreypalermo.com/blog/the-onion-architecture-part-1/), which I have become a very big fan of)
2. Initial Core Application Services
3. Some development niceties that are ASP.NET specific (like things to ensure that we always have an Anti-Forgery Token, etc.)
4. The Authentication and Authorization systems (using ASP.NET Identity)
5. The logging framework
6. Site Navigation and initial site pages for core things

## What We Probably Should Have Done
While everything works with the order we did the work in, it is also causing a number of issues because as we finish certain tasks we then have to go back and update other code. The most painful item on this list is the logging framework. I am now thoroughly convinced that one of the first things that should be setup in any new large project is a logging framework. It doesn't help much for development at the very beginning, but as soon as your first release happens and you push the application live to a web server or anywhere that isn't a developer's desktop that logging framework becomes your best friend when something breaks. We happen to be a 100% Microsoft shop, so we could always do something fancy with remote debugging, but that has issues of its own (including ensuring that you can reproduce the issue).

## Lessons Learned
Large enterprise-scale software is challenging to design, there will always be a number of moving parts. As developers and architects it makes our lives easier if we take a step back from the initial problem and try and see if there are dependencies which don't necessarily block developement, but which will require a fair amount of rework in order to incorporate after the fact. Logging is probably an example that will be true for just about every software project out there, but another great example is a localization framework (I developed one that doesn't use resx files because I don't like that approach). At minimum before any views are created, the localization should be fleshed out, otherwise there will be a massive amount of rework (depending on what the view contains it may be almost a full rewrite). 

I hope that anyone who reads this can not repeat the mistakes made, although these mistakes are hardly the most costly out there.