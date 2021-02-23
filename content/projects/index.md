---
title: "Projects"
description: "projects from me"
keywords: []
draft: false
toc: true
---

These are the list of weekend projects I did for fun and learning.

## Commercial Projects

### Tax App - Feb 2021

![Tax app](tax-app.gif)

**Objective**: An app that helps commoners to manage tax better. Users can add/upload their income statement, their tax relief spendings and tax rebates to the system. We will calculate how much is their tax for that year and identify which tax relief categories are not fully utilized. The receipts/statements can be upload and stored for at least 7 years to comply to Malaysian's LHDN requirement.

**Technologies Used**: Django Rest Framework, Nuxt, PWA, PostgreSQL, Heroku, Scout APM, Logentries (log aggregation), Google Social Login

### Raudhah Ilmi Dropship Centre - Nov 2020

![raudhah ilmi](raudhahilmi.gif)

This is a special project built for my lovely wife's bookshop.

**Objective**: Dropship agents to be able to calculate shipping price, submit orders and track orders easily, even from a mobile phone.

**Technologies used**: Django Rest Framework, PostgreSQL, Linode VM, AWS S3, Google Oauth2 social login, Netlify, Nuxt Auth, Nuxt PWA.

I'll be working and launching more commercial projects/startups in 2021. Stay tuned ;)

## Learning / Open Source Projects

These are my open source projects published on Github.

### Phoenix NuxtJS Todolist - Abandoned

Visit the project [here](https://todo-app-phoenix-nuxtjs.vercel.app/todo). View the source code [here](https://github.com/sdil/todo-app-phoenix-nuxtjs). See me building this project in this [Twitter thread](https://twitter.com/sdil/status/1349369045284311044).

**Objective**: Learning to build Elixir Phoenix backend, complete with Authentication, Social Login, File Upload and Background Job. I am really impressed with Phoenix speed compared to Python Django. I've made a quick benchmark test (and learned how to speed them up) [here](https://github.com/sdil/benchmark-frameworks).

**Technologies Used**: Elixir, Phoenix, PostgreSQL, Docker, Pow

**Outcomes**: I am struggled to integrate the Phoenix API server with Nuxt SPA frontend especially on the social login part. I was using Pow and Pow Assent for user management. I would revisit Elixir Phoenix again when building a server-rendered projects later.

### Chat App - Oct 2020

![chat app](chat.gif)

Visit the project [here](https://chat.fadhil-blog.dev). View the source code [here](https://github.com/sdil/graphql-chat). See me building this project in this [Twitter thread](https://twitter.com/sdil/status/1306045334414479360).

**Objective**: Learning to build **GraphQL** project and **accept payment** by building a simple chat app, in public.

**Technologies Used**: Hasura, Websocket, PostgreSQL, Docker, Firebase Auth, Stripe, NuxtJS + Apollo

### Hasura Firebase Auth Webhook - Sep 2020

Visit the project & source code [here](https://github.com/sdil/hasura-firebase-auth-webhook). See me building this project in this [Twitter thread](https://twitter.com/sdil/status/1309432290930380801).

**Objective**: While building a [GraphQL Chat App](#chat-app---wip), I'm integrating my Hasura GraphQL engine with Firebase Auth. This webhook will secure the GraphQL endpoints with Firebase Auth.

**Technologies Used**: Go, Docker (with multi-stage build), Github Actions & Github Container Registry.

### Voting Serverless - Aug 2020

![voting serverless](voting-serverless.png)

Visit the project [here](https://vote.fadhil-blog.dev). View the source code [here](https://github.com/sdil/voting-serverless-cdk). See me building this project in this [Twitter thread](https://twitter.com/sdil/status/1284816892301959168).

**Objective**: Learning **Serverless technology** by building a simple scalable voting app, in public.

**Technologies Used**: AWS CDK, DynamoDB, DynamoDB Streams, SQS, Lambda, API Gateway (HTTP API), S3, Cloudfront, ACM, X-Ray, Cognito User Pools, NuxtJS

### Yet Another Free Instagram Clone (YAFIG) - Abandoned

![YAFIG](yafig.png)

Visit the project [here](https://yafig.netlify.app). View the source code [here](https://github.com/yafig/api-server-monolith).

**Objective**: Learning to build **production-ready REST API** using Django REST Framework (monolith) by building a simple Instagram clone, in public.

**Technologies Used**: Django, Django REST Framework, PostgreSQL, Docker, Celery, Sentry, AWS S3, Netlify, NuxtJS.

**Outcomes**: I have achieved most of the objectives in this project. I don't intend to make it perfect.

### Other Projects

I've made minor contributions on many open source projects, mostly related to Kubernetes. You can visit them in my [Github profile](https://github.com/sdil?tab=repositories).
