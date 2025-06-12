---
date: '2025-06-12T09:44:32-06:00'
author: Patrick Miller
draft: false
image: "/images/blog/2025/browserpass-logotype-horizontal.png"
summary: 
title: 'Browserpass: The Full CRUD'
url: browserpass-extension-full-crud
categories:
- Programming
tags:
- javascript
- browserpass
---
## Contributing To Open Source: Sharing Is Caring

So much of the Internet is based on open source tools and technology that are free to use, which we can often take for granted. Combine that with my personally frugal and fiscial approach to expenses along with my distrust for numerous propriety companies being hacked due to their security flaws or social engineering and you have, me ... someone who would rather learn how to utilize open source encryption tools than pay for password managment. For years I've been using CLI tools like passwordstore<sup>[[1]](#references)</sup>, gopass<sup>[[2]](#references)</sup>, and the like; simply because well, password management has been pretty much required for years, I enjoy doing it, and it also gives me a chance to stick it to proprietery software companies and tell them "No" they can't have my money. <i class="fa-regular fa-dollar-sign"></i> <i class="fa-regular fa-face-smile-wink"></i>

I recently started contributing to an open source browser plugin called Browserpass<sup>[[3]](#references)</sup> because I didn't want to just be an internet consumer anymore. I wanted to start building, contributing, and giving back like so many before me; so that others might also enjoy the benefits of the Internet as I have.

## Old-School JavaScript Patterns Meet Security Principles

Picture this: you're working on software that handles people's most precious digital media, their passwords and secrets; and now you're going to work on editing and deleting them <i class="fa-regular fa-face-surprise"></i>. No pressure, right? Well, that's exactly the challenge I tackled when contributing to the Browserpass Extension<sup>[[3]](#references)</sup> to implement the ability to Add, Edit, and Remove individual entries in the Pull Request ["Add Ability to Manage New & Existing Passwords" #290](#references)<sup>[[4]](#references)</sup>.

The mission? Implement the holy trinity of data manipulation that every developer knows by heart: **Create, Update, and Delete** operations. Simiply because *reading* passwords just wasn't enough.

## The Security-First Mindset: Why I Went Retro with Prototypes

Here's where things get interesting (and where some modern JavaScript developers might clutch their pearls). Instead of reaching for the latest shiny framework or pulling in yet another dependency, I made a deliberate choice to keep things lean and mean using only JavaScript's built-in **Prototype**.

Why? Because when you're dealing with password management, every additional library is another potential attack vector. So I couldn't afford to introduce unnecessary complexity or imports that might compromise security.

Think of it as the digital equivalent of a Swiss Army knife—simple, reliable, and battle-tested. Sometimes the old ways are the best ways, especially when people's digital lives are on the line.

This resulted in three different model files: `popup/models/{Login.js, Settings.js, Tree.js}`

- `Login.js` dealt with individual encryption entries retreieved from the native-message host and all the functionality of listing, parsing, generating, identifying which store (collection of files) it belonged to, saving, deleting, and so forth
- `Settings.js` handled behavior with requesting the saved settings from background.js and common calls to check setting values
- `Tree.js` recursively built a Tree/Map of directories from all the file paths of encrypted secrets and used later for displaying a list of existing directories at a given path in order to auto insert path segments when creating a new entry

## Don't Repeat Yourself (But Do Repeat This Principle)

The DRY principle isn't just a catchy acronym—it's a lifestyle choice and a required approach to code/software that doesn't suck. In PR #290<sup>[[4]](#references)</sup>, I religiously applied the DRY principle to avoid the cardinal sin of copy-pasta programming.

Instead of scattered, duplicate code handling different CRUD operations, I created reusable components that could be composed and extended. Each business logic model was carefully implemented to handle its specific domain while sharing common patterns and behaviors through the prototype chain.

This approach not only reduced the codebase size but also made debugging a breeze. When you find a bug in one place, you know exactly where all its cousins are hiding.

Just be sure when you find you're needing to reuse functionality to move it to a sharable location for reuse and don't create multiple functions which essentially do the exact same thing, instead of copy-pasting the function. Now it is important to strike a balance that your code reuse doesn't become a huge obsessive abstraction.

## Separation of Concerns: Because Chaos Is Not a Valid Architecture Pattern

This implemented a clean separation of concerns by creating specific **Behavior Models** for different aspects of the password management. Each model knows its job and sticks to it; no Swiss Army knife anti-patterns here.

The Create and Update operations handled new and existing password management and were largely similar, the main difference being whiether or not we have `editing=true`. So the two different functionalies could manage new and existing entries without stepping on each other's toes using the exact same component with some simple boolean logic. Delete operations... well, they made things disappear responsibly (no mysterious password vanishing acts), by confirming the action with the end user before removal; though it always reminds me of the Windows confirmation pop up but I try to avoid the excessiveness of those ... Are you sure you're sure?

This modular approach meant that each component could be tested, debugged, and modified independently. It's like having a well-organized toolbox where you can find exactly what you need without dumping everything on the floor.

In the end, I was able to reuse one component for the add/edit functionality and have a single function to delete.

## The Beauty of Vanilla JavaScript in a Framework-Heavy World

In an era where developers often reach for `npm i` to add something which already does the task, vanilla JavaScript still has its place, especially in security, critical applications. By leveraging the built-in prototype, I achieved:

- **Minimal attack surface**: Fewer dependencies mean fewer potential vulnerabilities
- **Better performance**: No framework overhead means snappier user interactions
- **Predictable behavior**: When you control the entire stack, debugging becomes detective work instead of archeology
- **Future-proof code**: Prototypes aren't going anywhere, those and apparently jQuery, unlike that framework you loved last year

## The Real-World Impact

The end result? Browserpass end users can now create new password entries directly from their browser, update existing ones without switching to the command line, and delete entries they no longer need. All while maintaining the rock-solid security standards that make browserpass a trusted tool in the unix password store community.

## Lessons from the Trenches

This contribution reinforced some fundamental truths about software development:

1. **Security trumps convenience**: Sometimes the boring choice is the right choice
2. **Simple beats complex**: Prototype patterns are simple, debuggable, and effective
3. **Separation of concerns isn't optional**: Well-organized code is maintainable code
4. **DRY principles scale**: Less repetition means fewer bugs and easier maintenance

Working on browserpass taught me that contributing to open source isn't just about adding features—it's about understanding the project's values and constraints, then finding elegant solutions that honor both.

## The Takeaway

In a world obsessed with the next big framework, sometimes the most revolutionary thing you can do is write solid, secure, vanilla code. When people trust you with their digital keys to the kingdom, you better make sure those keys are forged from the finest steel—not the latest experimental alloy.

And hey, if you can make password management a little bit easier while keeping it secure, you've done your good deed for the Internet. One contribution at a time.

---

*Want to check out the technical details? The full implementation is available in the browserpass-extension repository (see references below), where security meets elegant code architecture. Because the best security features are the ones users actually want to use.*

---

### References

1. [passwordstore (pass) - the standard unix password manager](https://www.passwordstore.org)
1. [gopass - the team password manager](https://www.gopass.pw)
1. [Browserpass Extension](https://github.com/browserpass/browserpass-extension)
1. [Add Ability to Manage New & Existing Passwords #290](https://github.com/browserpass/browserpass-extension/pull/290)
