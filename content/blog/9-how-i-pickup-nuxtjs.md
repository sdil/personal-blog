---
title: "I am relearning JS"
date: 2020-04-21
slug: "relearn-js"
description: "My journey to relearn JavaScript after so many years and how you can start too"
keywords: ["VueJS", "NuxtJS", "Frontend", "Programming"]
draft: false
tags: ["VueJS", "Frontend", "Programming"]
math: false
toc: true
---

![YAFIG](/blog/9/yafig.png)

The picture above shows you what I’ve built after learning Vue.js and Nuxt.js in one week.

I am building an Instagram clone project called “Yet Another Free (OSS) Instagram Clone” (YAFIG). The front-end site is built entirely in Vue.js and Nuxt.js, in a week.

## Who Am I

I am a full-time software engineer working on CDN products. I spent my entire professional career (~three years) working with back-end services, primarily in Python, Java, and Lua.

Sometimes I also work with DevOps toolchains (Jenkins, Docker, Kubernetes, Puppet, etc.). I have decent experience writing a server-side web app using Flask and Django.

However, one of my weaknesses is JavaScript. I am completely clueless when it comes to front-end programming and modern JavaScript programming in general. JavaScript was my enemy (and my fear) for a long time.

---

## My Personal JS History

Personally, I attempted to learn JavaScript a very long time ago. In the year 2015, I used to learn Ruby on Rails and Ruby Sinatra (what a nostalgic framework). It was trendy at that time. There were also MooTools and jQuery.

I was 15 years old at that time; most of the time, I was just following YouTube tutorials from RailsCast’s YouTube channel back then. jQuery and MooTools were OK for me at that time because they are pretty straightforward to write in (until you face the asynchronous issues in JS).

After that came Ember.js, Backbone.js, Meteor, Knockout.js, and many other frameworks. And then, Node.js and npm came, together with Angular, and dependency issues in the JS ecosystem. Things were moving too fast.

I tried to pick up those frameworks at that time to stay updated with the latest tech in the industry. Unfortunately, I couldn’t keep moving at their pace. I abandoned the JS world to focus primarily on back-end programming, where things are more matured (and boring).

When React.js was released to the public, I always wanted to learn the framework. I’ve read its documentation and tutorials, but I still could not understand how it worked under the hood.

The structure is always complicated to me and not what I expected. The same thing happened for the Ionic framework. I was then skeptical about the JS world. Every new JS framework released will always disappoint me. I really wish there was a new JS framework as simple as jQuery again.

### The Big Why

You have to find your own “Big Why” you want to do something. Previously, I wasn’t really into Vue.js. Everyone was talking about React.js but only a few of them mention Vue.js.

And then, everything changed after I watched this video:

VueJS Documentary by Honeypot

In this video, Evan You, the creator and the maintainer of Vue.js is portrayed as a hero in China for his creation. The narrator mentioned that Vue.js was released at the right time.

In 2014, the Angular.js community was a bit shaken when Google released version 2 of the framework where the complete rewrite of the frameworks causes a lot of backward-incompatibility issues.

React.js, on the other hand, was great, but they are still too complicated for many people (like me).

Vue.js was built to solve that exact problem. That is when I was intrigued! Vue.js has a much lower entry barrier and a lower learning curve compared to React.

After doing so much research, evaluating the Vue ecosystem and tradeoffs, I started learning Vue.js.

### Urges to Write an App

I have a lot of app ideas in mind. Too bad, I could not make them a reality myself because I wasn’t able to write proper JavaScript. It’s a blocker for me. I knew I had to pick up something to build a complete app. jQuery is not modular enough for me to develop a modern app.

I have set my own goal: To learn a modern JS framework and develop my own app from end to end.

I do not intend to develop a sophisticated UI for my app (at least for now) or even further my career as a front-end engineer (again, at least for now). An adequate UI for a CRUD-based system is good enough for me.

I am inspired by people like Tigran Hakobyan, Takuya Matsuyama, and levels.io who are able to build and market their apps by themselves.

---

## What I’ve Achieved So Far

After learning Vue during weekends and out-of-office hours, I have successfully built an Instagram clone in Vue.js. This is not much, but I am really proud of what I’ve achieved.

So far, I have the following features in my app:

- Authentication against a dummy JSON server, FakeJson.
- Server-side rendering using Nuxt.js.
- Static web hosting on Netlify.
- CSS framework using Buefy.
- Autocompletion search using lodash.debounce and axios (the snippet is from the Buefy documentation).
- User tracking using Google Analytics.

The full code is available in my GitHub repository.
P.s.: I will write more about this project in the future.

I still haven’t grasped the concepts of webpack, Babel, and other low-level JavaScript structure works. Hopefully, I will someday.

## My Personal Opinion

Nuxt.js is great at simplifying Vue.js. What Nuxt.js does may be suitable for 80% of use cases. If you are building a CRUD-based system, Nuxt.js should be your first framework to consider.

Even though the Vue.js ecosystem has a lesser adoption in the market compared to React.js, I still believe that Vue is here to stay. React may have a big corp (Facebook) backing the ecosystem, but Vue and Nuxt have GitLab, Doist, and many more.

If you wish to work as a front-end developer, I personally suggest you go with React as they may offer you more job opportunities worldwide. However, if you just want your things to get done as a solo developer, you should give Vue.js a try.

## How I Learned Vue.js

I can’t stress this enough; there are excellent resources for you to start learning Vue and Nuxt.

- Pro tip 1: Watch at 1.5x speed so that you don’t quickly get bored watching them.
- Pro tip 2: Follow and write every single line the instructor wrote on the screen, even though you do not understand what it means yet. Run/execute it first, then Google it afterward.

**Disclaimer**: I am not linked nor affiliated with Traversy Media. I just find these videos super helpful and hopefully you do too.

You should learn how Vue works in general. In this video, Brad explains the fundamental concepts of Vue.js. It may look tedious at first until you watch the next video here:

In this video, Brad explains how Vuex helps you manage the states in the app. Please pay attention to this video because you’re going to use this every time you write a Vue.js app later.

In this video, Brad explains how Nuxt.js works by building a simple yet fun project. At this stage, you should understand how Nuxt helps you simplify the process of managing state and routers (and many other things) within the Vue.js app.

By the end of the third tutorial, you should be able to develop your own CRUD-based app.

It should take you more or less one day to watch and try every single video. If you still do not understand how to write a simple to-do app and dad jokes app at this point, maybe you should look for more tutorials on YouTube.

## What’s Next

At this point, you should have a good fundamental understanding of how Vue.js and Nuxt.js work, generally. You don’t need to know too many details to start building an app.

You can now explore how Vue and Nuxt handle authentication, how to integrate them with CSS frameworks (e.g., Bulma, Tailwind, and Bootstrap), and use utility libraries (e.g., Lodash).

And then, you can start building exciting projects!

## Conclusion

To be honest, I am pleased that I discovered Vue.js and Nuxt.js. Thank you, Evan You for your creation. It’s a JS framework that excites me once again after so long.

There are many new shiny frameworks nowadays like Svelte, Next.js, Gatsby, etc. Personally, I have no reason to migrate to those new frameworks yet. I am pleased and satisfied with Nuxt now, and you might be too.
