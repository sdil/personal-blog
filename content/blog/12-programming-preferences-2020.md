---
title: "My personal thoughts, opinions & preferences on programming in 2020"
date: 2020-10-14
slug: "programming-preferences-2020"
description: "Personal technology preferences after building many projects in the past"
keywords: ["Preferences", "Programming"]
draft: false
tags: ["Preferences", "Programming"]
math: false
---

![Computer](https://cdn-images-1.medium.com/max/800/0*kRfN0M31_fZo-zYH)
Photo by John Schnobrich on Unsplash

Over the past few months, I've been building many side projects in my free time for learning apart from working full-time as a software engineer for the past 3 years. When building the side projects, I intentionally use many different technology stacks that I'm not familiar with in order to learn and make more informed choices in the future.
Over that period, I've developed my preferences towards the tech stacks of choice and approaches on how I'm gonna build my next project.

- Prefers API-first development that automatically generates API documentation for you. It's always better to spend extra hours documenting your API rather than spending extra hours every time figuring out how to use the API endpoint you wrote 3 months ago. It also frees up your mental memory. You may use FastAPI, Django REST Framework, or Connexion for this.

![Example API Doc](https://cdn-images-1.medium.com/max/800/1*-O5V_OaGorDtdLDY-LZjvw.png)
Example API Doc

- Prefers GraphQL over RESTful API when writing frontend code. GraphQL server will automatically generate a schema inspector that will help the frontend developers to easily use the API.  However, writing a GraphQL server is still a big pain these days, so use Hasura (or AWS AppSync although less preferred) for that. As for frontend development, the Javascript client libraries are fairly great, well maintained, and easy to use.
- When building a REST API, always follow API design conventions and best practices like [this](https://docs.microsoft.com/en-us/azure/architecture/best-practices/api-design) one from Microsoft.

- Prefers building monolithic API/Web development (using Django) over microservice development for ease of development & deployment. Microservice creates vast new problems to handle (networking, security, deployment, management, observability, etc.) that you need to tackle.
- Prefers Jamstack over regular web app development. The deployment to the edge is super easy and cheap. That means, the website (frontend) is served very fast from all around the world and is always available. It also works perfectly together with the API-first development approach mentioned earlier which gives you a unified API.
- Prefers matured & boring technology over bleeding-edge tech for critical projects. Keep things simple and it will make you go faster! Let the other people discover bugs first (on new tech) and you enjoy the battle-tested tech. I always refer to the InfoQ Trends Report for reference. I would be more interested in the Early Majority & Late Majority quadrants rather than the Innovators & Early Adopters quadrants.

![InfoQ Chart](https://cdn-images-1.medium.com/max/800/0*f889wxWe28e5-Ui1.jpg)
https://www.infoq.com/articles/architecture-trends-2020/

- Prefers Async-first web development with Transactional Outbox pattern over synchronous web development. Return the result to the user immediately after validated and atomically inserted the data into the database. All other operations should be done as asynchronous jobs by workers. Prefers to use Change-Data-Capture (CDC) to achieve this. This is a [good example of this approach](https://medium.com/r/?url=https%3A%2F%2Faws.amazon.com%2Fblogs%2Fcompute%2Fbuilding-storage-first-applications-with-http-apis-service-integrations%2F).
- Prefers Kubernetes for sophisticated deployment and prefers other container orchestrators (eg. HashiCorp Nomad, AWS ECS, Heroku, or GCP Cloud Run) for personal project deployments. Use Kubernetes only when your project requires complex network rules (using CNI), automatic scaling (using HPA), running stateful service that needs automatic storage provisioning (using CSI), deploying on 10s, 100s, or 1000s of nodes, observability, automatic SSL cert provisioning, complex ingress rules, custom CRD controller, canary deployment, health check, etc. because that's what makes Kubernetes great. Otherwise, use a simpler orchestrator that doesn't deal with them. Kubernetes is not easy to tame.
- Prefers serverless Container development and deployment (eg Cloud Run, ECS Fargate) over Function development and deployment (eg. AWS Lambda). The advantages of container-based development are there's no vendor lock-in and toolings and Developer Experience (DX) is much better for the container ecosystem compared to function-based development. I still not comfortable building applications in the FaaS way (on AWS Lambda) and still struggling with how to deploy the app in a staging environment, how to debug correctly. However, Yan Chui might change my mind in his upcoming lesson. In either case, you wouldn't have to worry about VPC settings, NAT Gateway, Firewall rules, Security Groups, OS patching, etc. which is great!
- Prefers monolithic SQL databases over NoSQL databases for most projects. SQL databases give you flexibility for you to design your model. NoSQL (especially DynamoDB) on the other hand requires you to know the access pattern before starts your development which costs you so much time when refactoring the app in the future. If you have a really really specific use case (eg. high throughput database), then only use NoSQL DB. This is not the case if you're using multiple databases.
Prefers managed serverless service offerings/SaaS over build my own/self-hosted, as long as it's not my core business product. For example, use Stripe to handle payment rather than building my own payment gateway to handle the payment, use cloud transcoding to transcode rather than deploying VMs, and run `ffmpeg` to transcode my videos, using GCP BigQuery to conduct analysis rather than spinning and set up a new dedicated MySQL server to run analysis, etc.. This is highly influenced by [this article](https://medium.com/@dabit3/full-stack-development-in-the-era-of-serverless-computing-c1e49bba8580).
- Prefers opinionated frameworks/languages over unopinionated ones. Usually, these frameworks/languages, especially the ones with a high number of Github stars are built & contributed by experts in their fields. I prefer Django over Flask/FastAPI, NuxtJS over VueJS, Buefy over Bulma CSS. Too much flexibility can lead you to poor choices and you'll have to implement the best practices on your own (which costs a lot of time and effort).
- Prefers Firebase Auth over other authentication/identity providers because of its generous free tier (unlimited user accounts). Firebase Auth is good enough for me whenever I don't need to build my own auth service (Auth service is more complicated than you think. You'll need to handle token expiration, password reset, email confirmation, refresh token, social logins, etc.).
- Use the right tool for the right job eg. use Elasticsearch for full-text search and not a SQL database for that, use Columnar store database (eg. Clickhouse, Redshift, and GCP BigQuery) for OLAP and use Row store database (eg. MySQL, AWS Aurora and PostgreSQL) for OLTP and not vice versa, use RDBMS indexes & materialized views for quick queries, use API Gateway when managing multiple API services and endpoints. When dealing with multiple datastores, you must decide which one to be the source of truth.
- Learn how people are using tech outside your company & outside your job scope. Watch a lot of conference talks, workshops, and read blogs. Learn from gurus like Kelsey Hightower, Chris Richardson, Jaana Dogan, folks from FANG, etc. on how they build stuff. It will open up your mind!
- When reading library/service documentation, read it thoughtfully, and do not skip the best practices section. That's where you know you're using the library/service the right way as intended.
- Try not to reinvent the wheel. Use matured & stable libraries/frameworks whenever possible. Most of the libraries & frameworks (established ones like Django) were written to make sure that you follow the best practices in the industry. For example, Django by default mitigate many security vulnerabilities for you so you can focus on your application.
- Not preferring a Multi-cloud strategy, it adds too much hassle. Unless you're a big corporation with 100s of SREs, don't dream of it. Most of the time, it's overkill. 1 major cloud provider should be sufficient to handle your workload.
- Unit tests really help you when you're upgrading your libraries/languages in the future. The system should behave the same before and after you upgrade. Other benefits of it are you can avoid mistakes from happening after refactoring when you anticipate no surprises. It also really valuable when you're working in a big team and the other team members are making changes to the program logic that affect other components.
The code is a liability. Less code is better.
Do not overdo when building an MVP. The speed is the key.
Build incrementally and see the results immediately.
When using cloud services, always try to leverage spot-instances/preemptible instances whenever possible to significantly reduce your cloud bill.

These thoughts and preferences are only for the perspective of technological choices. It doesn't cover the business considerations because that is not what I was doing. I wish to get involved more in product & business decisions in the future.
Hope that this benefits you in making better choices as well.

Feel free to reach me on Twitter or on my personal blog.
