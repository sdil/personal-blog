---
title: "Why I Love Django After Building a Web App for 3 Months"
date: 2020-04-21
slug: "love-django"
description: "Why I Love Django After Building a Web App for 3 Months"
keywords: ["Django", "Programming", "Python"]
draft: false
tags: ["Django", "Python"]
math: false
toc: true
---

For the past three months, I've been writing a web application using Django for our company's new project. It hasn't launched yet, so I couldn't disclose too much information about it. However, I've learned a ton while working on this project while working on it along with the team.

Previously, I have been working with Flask, Cherrypy, and Bottle to build a small and simple web app for a particular need. I was shocked after a few weeks of writing the web app in Django. Before writing my first line of Django code, I read this excellent ebook written by Andrew Pinkham

[![Advanced Web Development in Python with Django](https://learning.oreilly.com/covers/9780136552949/300w/)](https://learning.oreilly.com/videos/advanced-web-development/9780136552949)

If you are new to Python or Django web programming, I recommend you to start with a book or tutorial that explains its fundamental first to get the idea.

## Reasons to Love Django Framework

### Reason 1: Big Corps are Betting on Them
Before we discuss the cool things about Django, let's see who is betting on the framework. Numerous organizations use Django in their stack. Most notably mentions are Instagram, Mozilla, Pinterest, and NASA. Instagram has been using Django since day one because it allows them to evolve quickly. I understand that the framework is giving some pains after years of their exponential growth, but Django is the one that will enable them to scale that big, that fast.

Watch the video below to know how Django is being used at Instagram.

[![Django @ Instagram](https://img.youtube.com/vi/lx5WQjXLlq8/0.jpg)](https://www.youtube.com/watch?v=lx5WQjXLlq8)

### Reason 2: Rapid Prototyping/Development
Writing a Web app using Django is as easy as ABC. Django makes it really easy takes an extremely little effort to build a web app from scratch. Compared to minimal frameworks like Flask, you would need to write database migration scripts, page routing, user authentication, serializer, etc.. In Django, everything is taken care of for you.

Honestly, I would probably take 1–2 weeks if I were to write a complete and fully tested user management module alone from scratch in Flask with user registration, user login, social logins, password reset, email activation, password encryption, and role-based access control (RBAC). That would sum up thousands of lines of code. In Django, it would take less than 1 day using its scaffold django startapptool.

Writing a model, views, and templates are also super easy. Let's say you are creating a new poll module for your web app, all you need to start a new app, define its model, write the views and template. And lastly, register the app in the config file. Voila!

Other benefits of using Django related to Day 1 operations includes:

- **Easy deployment model**

  You can easily deploy the Django app at Heroku, Python Anywhere, or AWS Elastic Beanstalk with just a git command. You can also deploy virtually anywhere as a container.

- **Fits 80% of Web app use case**

  If you are building a web app that requires a User Management system, ORM, Background Job, Django has you covered. All the good things come out of the box.

- **Unittest is super clean**

  I'm surprised that Django comes with lots of handy unit test tools. Building a resilient application is now fun again!

- **Security is taken care of for you**

  Django comes with built-in mitigation for common attacks eg. XSS, SQLi, CSRF, password cracking, and clickjacking.

If you are a freelancer that hunts and works on various projects at a rapid pace, this should be under your toolbelt.

### Reason 3: Day 2 Operations is a Peace of Mind

Building a web app is just 20% of the job. Maintaining the web app for the next 5 years is another 80% of the job. That includes extending its features, server & database migrations, ensuring the service uptime, data cleaning, server scaling, etc. All those processes are easy in Django, as long as you design your system right.

Django has an extremely helpful community. This is really important for your project's longevity. Django was first released in the year 2005 and 15 years later, the community keeps growing day by day and showing no sign of slowing down. More people and big corps are relying on this project means it will stay here for many more years ahead. A vibrant developer community means:

- **Plenty of StackOverflow questions**

  There are over 220,000 questions with tag Django has been asked in StackOverflow. Almost every single question/problem you encountered related to Django probably has been asked and discussed there. 

- **Extending your app is easy**

  Django comes with tons of plugins ready to be used. This is a long list of packages available to use https://djangopackages.org/

- **Official guides from companies**

  Due to its popularity, many companies like Google Cloud, AWS, Cockroach DB, DigitalOcean, etc. are writing guides based on the Django ecosystem.

- **A vast talent pool for hiring**

  Django is a trendy framework. If your company is looking to hire Django developers, you probably could tap them easily in the job market. They're not scarce.

---

## When to use Django

![Web Queue Worker architecture](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/images/web-queue-worker-logical.svg)

Web-Queue-Worker architecture style. Courtesy of MicrosoftAs a rule of thumb, if you're developing a monolithic Web-Queue-Worker architecture project, you should at least give Django a try. Microsoft has explained in depth about the Web-Queue-Worker architecture on this [documentation page](https://docs.microsoft.com/en-us/azure/architecture/guide/architecture-styles/web-queue-worker).

The monolithic Web-Queue-Worker architecture is a very common web architecture. This architecture is suitable for many use cases. Personally, I will be using Django if I were to start a project that:

- **Expects a full-featured web app** that comes with a complete user management system, comprehensive API documentation, complete unit test coverage, etc.
- **I'm working on a solo or on a small team project** and don't want to worry about scalability, inter-service communications, and complex deployment strategy (e.g., Kubernetes orchestration)
- **I need it fast (MVP in days or weeks, not months)** and willing to sacrifice flexibility, optimization, and customization because of its low entry barrier. That's one tradeoff.
- **I know that I will NEVER be using the latest techs** in the projects (eg. AWS QLDB, AWS Neptune DB, AWS DynamoDB, Firebase Realtime Database, ScyllaDB) and stick to traditional (mature and stable) techs throughout its lifetime.
- **I know that I will NEVER divert from Web-Queue-Worker architecture**. This means you should never implement Event Sourcing, CQRS, Hexagonal/Port & Adapter, and/or microservices architecture on top of the Django app. It is not built for that. If you do so, you're probably using it wrong.
- **Avoiding vendor locking**

  Unlike serverless programming (I'm talking about AWS Lambda, Azure Functions, and Google Functions), unless you are doing it on Kubernetes, most of the serverless programming means you're locked in that particular vendor and moving away from them is not easy.

- Probably building another Instagram, Youtube, Medium, Airbnb and Yelp clones (well, not that complete sophisticated system, but you get the idea)

### Monolithic vs. Microservices Paradigm
Everybody is into microservice nowadays, but there's nothing to be shame about the monolithic architecture. Basecamp has been doing monolithic, and they're proud of it.

Microservice comes at a cost of complexity. Architecting and managing hundreds or thousands of microservices is not an easy task. There are so many things you'll need to consider. Not to mention, debugging and tracing is a tricky process. Microservice is simply not for everyone.

---

## When NOT to use Django
This is my personal opinion. There's always a tradeoff for any web frameworks.

- **If you wanted to work with the latest technology**

  Most of the new technologies may take some time to be adopted by the community. If you wanted to work with new technologies like FaunaDB, DynamoDB, etc. you might need to write your adapters/plugins for that in Django.

- **Your team has expanded beyond two pizza-sized team**

  This is my very personal opinion. If the team has grown to over 100s of developers working on the same codebase, maybe you should consider breaking them into small microservices with a very clear separation boundary. However, I might be wrong and Instagram has been doing this right for years.

- **When you want full control of your code**

  Django is an opinionated framework. You must structure your application according to how Django is designed and stick with it. You can't even switch the ORM library. If you value flexibility over ease of use, you should consider using a minimal framework like Flask.

- **You want to build something really small**

  Maybe you want to build a website that just displays the current time at any timezone or a webhook that responds to Slack messages, then Django is overkill for you. You should consider using a minimal web framework.

- **You wanted to LEARN everything from end-to-end**

  You won't experience building everything from end-to-end in Django because most of the operations are abstracted for you.

---

## Conclusion

I've been using Django for only two months. All I've seen right now is the bright sides of Django. Maybe after a year, I will discover more Django drawbacks and pitfalls and also solutions to it, I am more than happy to share my experience here again. 

I know that Django has many more things to offer that I haven't explored yet: Django Channels, In-memory caching, Celery task, Django REST, Asynchronous support, etc. I also need to learn more about Django's best practices in order to optimize my application. Learning is a never-ending process.
