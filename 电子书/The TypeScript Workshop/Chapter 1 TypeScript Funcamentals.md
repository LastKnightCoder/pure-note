---
cssclass: lora
---

# TypeScript Fundamentals

```info
Overview
In this chapter, we'll briefly illustrate the problems that exist in JavaScript development environments, and we'll see exactly how TypeScript helps us write better and more maintainable code. This chapter will first help you set up the TypeScript compiler and then teach you the fundamentals. Additionally, we'll begin our journey into types, as they are the core feature of TypeScript – it's right in the name. Finally, you will be able to test your newly gained TypeScript skills by creating your own library.
```

## Introduction

The world of online applications has grown tremendously in the past few decades. With it, web-based applications have grown not only in size but also in complexity. JavaScript, a language that was originally thought of and used as a go-between between the core application logic and the user interface, is being seen in a different light. It is the de facto language with which web apps are being developed. However, it just was not designed for the building of large applications with lots of moving parts. Along came TypeScript.

TypeScript is a superset of JavaScript that provides lots of enterprise-level features that JavaScript lacks, such as modules, types, interfaces, generics, managed asynchrony, and so on. They make our code easier to write, debug, and manage. In this chapter, you will first learn how the TypeScript compiler works, how transpilation occurs, and how you can set up the compiler options to suit your needs. Then, you will dive straight into TypeScript types, functions, and objects. You will also learn how you can make your own types in TypeScript. Finally, you can test your skills by attempting to create your own library to work with strings. This chapter serves as a launchpad with which you can jump-start your TypeScript journey.

### The Evolution of TypeScript

TypeScript was designed by Microsoft as a special-purpose language with a single goal – to enable people to write better JavaScript. But why was that an issue at all? To understand the problem, we have to go back to the roots of the scripting languages for the web.

In the beginning, JavaScript was designed to enable only a basic level of interactivity on the web.

```note
JavaScript was initially developed in 1995 by Brendan Eich for use in Netscape Navigator.
```

It was specifically not designed to be the main language that runs within a web page, but to be a kind of glue between the browser and the plugins, such as Java applets that run on the site. The heavy lifting was supposed to be done by the plugin code, with JavaScript providing a simple layer of interoperability. JavaScript did not even have any methods that would enable it to access the server. Another design goal for JavaScript was that it had to be easy to use for non-professional developers. That meant that the language had to be extremely forgiving of errors, and quite lax with its syntax.

For a few years, that was the task that JavaScript (or, more properly, ECMAScript, as it was standardized) was actually doing. But more and more web pages came into existence, and more and more of them needed dynamic content. Suddenly, people needed to use a lot of JavaScript. Web pages started getting more and more complex, and they were now being referred to as web applications. JavaScript got the ability (via AJAX) to access servers and even other sites, and a whole ecosystem of libraries appeared that helped us write better web applications.

However, the language itself was still lacking lots of features that are present in most languages – primarily features that are targeted toward professional developers.

```note
Some of the most talked-about features included a lack of module/ namespace support, type-checked expressions, better scoping mechanisms, and better support for asynchronous functionality.
```

Since it was designed for small-scale usage, it was very troublesome to build, and especially to maintain, large applications built with JavaScript. On the other hand, once it was standardized, JavaScript became the only way to actually run code inside the browser. So, one solution that was popular in the 2000s was to make an emulation layer – a kind of a tool that enabled developers to use their favorite language to develop an application that will take the original source code as input and output equivalent JavaScript code. Such tools became known as transpilers – a portmanteau of the words "translator" and "compiler." While traditional compilers take source code as input and output machine code that can execute directly on the target machine, transpilers basically translated the source code from one language to another, specifically to JavaScript. The resulting code is then executed on the browser.

```note
The code actually gets compiled inside the browser, but that's another story.
```

There were two significant groups of transpilers present – ones that transpiled from an existing language (C#, Java, Ruby, and so on) and ones that transpiled from a language specifically designed to make web development easier (CoffeeScript, Dart, Elm, and so on).

```note
You can see a comprehensive list at https://packt.link/YRoA0.
```

The major problem with most transpilers was that they were not native to the web and JavaScript. The JavaScript that was generated was confusing and non-idiomatic – it looked like it was written by a machine and not a human. That would have been fine, except that generated mess was the code that was actually executing. So, using a transpiler meant that we had to forgo the debugging experience, as we could not understand what was actually being run. Additionally, the file size of the generated code was usually large, and more often than not, it included a huge base library that needed to load before we would be able to run our transpiled code.

Basically, by 2012 there were two options in sight – write a large web application using plain JavaScript, with all the drawbacks that it had, or write large web applications using a transpiler, writing better and more maintainable code, but being removed from the platform where our code actually runs.

Then, TypeScript was introduced.

### Design Goals of TypeScript

The core idea behind it was one that, in hindsight, seems quite obvious. Instead of replacing JavaScript with another language, why not just add the things that are missing? And why not add them in such a way that they can be very reasonably removed at the transpiling step, so that the generated code will not only look and be idiomatic but also be quite small and performant? What if we can add things such as static typing, but in an optional way, so that it can be used as much or as little as we want? What if all of that existed while we're developing and we can have nice tooling and use a nice environment, yet we're still able to debug and understand the generated code?

The design goals of TypeScript, as initially stated, were as follows: 
- Extend JavaScript to facilitate writing large applications. 
- Create a strict superset of JavaScript (that is, any valid JavaScript is valid TypeScript).
- Enhance the development tooling support.
- Generate JavaScript that runs on any JavaScript execution environment.
- Easy transfer between TypeScript and JavaScript code.
- Generate clean, idiomatic JavaScript.
- Align with future JavaScript standards.

Sounds like a pie-in-the-sky promise, and the initial response was a bit lukewarm. But, as time progressed, and as people actually tried it and started using it in real applications, the benefits became obvious.

Two areas where TypeScript became a power player were JavaScript libraries and server-side JavaScript, where the added strictness of type checking and formal modules enabled higher-quality code. Currently, all of the most popular web development frameworks are either natively written in TypeScript (such as Angular, Vue, and Deno) or have tight integrations with TypeScript (such as React and Node).

```ts
function add (x, y) {
  return x + y;
}
```

No, that's not a joke – that's real-life TypeScript. We just did not use any TypeScriptspecific features. We can save this file as add.ts and can compile it to JavaScript using the following command:

```shell
tsc add.ts
```

This will generate our output file, add.js. If we open it and look inside, we can see that the generated JavaScript is as follows:

```ts
function add(x, y) {
  return x + y;
}
```

Yes, aside from some spacing, the code is identical, and we have our first successful transpilation.