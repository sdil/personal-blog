---
title: "My Learnings"
description: "My learnings"
keywords: []
draft: false
toc: false
---

This page records everything I’ve studied, learned or practiced. This page is inspired by [Ben Barbersmith's](https://www.barbersmith.com/notes/self-education/).

The points with (\*) are my favorites.

## 2021

I quit my job at Onapp KL after ~4 years working

- Learned B-tree and B\* tree data structure, database indexing and optimization, database transaction and isolation levels
- Learned Ruby on Rails (again after 10 years) and its design patterns (Presenter, Service, Concern) for my new day job
- \*Built a POC API server for [JKJAV website](/blog/how-i-would-built-malaysia-az-site/) using Redis and Go Fiber
- \*Set up Meilisearch to enable searching on our Firestore database. Took a few hours to set up the Meilisearch instance and sync the data from Firestore to Meilisearch.
- \*Shipped my first Golang code to production, integration of an API server with a Malaysia payment gateway (iPay88). The API server is consumed by a mobile app.
- Taught myself beginner-level Elixir Phoenix framework. Published a simple benchmark [here](https://github.com/sdil/benchmark-frameworks). Tried to build several web apps and API servers using Phoenix but still not feeling productive with it yet.
- Used Django REST Framework, Nuxt and TailwindCSS to build [Tax App](/projects/#tax-app---feb-2021)
- Studied UI/UX by reading and watching [Refactoring UI by Adam Wathan & Steve Schoger](https://refactoringui.com/)
- Taught myself OWASP security vulnerabilities (CSRF, XSS, SQL Injection) and mitigations (CSRF Token, SameSite cookies, CSP)
- Other techs I taught myself: \*Redis, Supabase, Chakra UI, Tailwind

## 2020

- \*Taught myself intermediate-level Django & Django REST Framework. Loving this framework.
- \*Taught myself beginner-level Nuxt & Vue
  - Built many web JAMstack projects together with Django REST Framework
- Taught myself PostgreSQL and CockroachDB. Learned a bit of DB knowledges like compaction, Write Ahead Logs (WAL), RocksDB storage engine, indexing, materialized views, etc.
- Taught myself AWS products eg. DynamoDB, S3, Lambda, APIGW, CDK, RDS, etc. Built [Voting Serverless App](/projects/#voting-serverless---aug-2020) using these techs. Learned DynamoDB single-table pattern.
- Taught myself GraphQL client. Built a simple [chat app](/projects/#chat-app---oct-2020) that utilizes GraphQL query, mutations and subscriptions. It's built using Hasura and Nuxt.
- Taught myself GCP products eg. GCS, BigQuery, Data Studio, GCR, Cloud Build, etc.
- Studied programming theories like Domain Driven Development, Dependency Injection, Event Sourcing, CQRS, Saga pattern, Design Patterns, [Microservice](https://microservices.io/) architecture, Port & Adapter pattern, Clean architecture pattern, etc.
- Other techs I taught myself: Firebase Auth, Stripe integration (Billing & Payments), Hasura, Hashicorp Nomad

## 2019

- \*Taught myself intermediate-level Kubernetes
  - Wrote a Proof of Concept Kubernetes CSI-compatible storage controller based on [Rancher Local Path Provisioner](/blog/rancher-local-path-provisioner/).
  - Plan, set up & manage multiple on-prem edge clusters
- Taught myself basic AWS & GCP cloud. Taking A Cloud Guru courses
- Taught myself basic Go. Built non-production web apps using Echo & Gin web framework
- Other techs I taught myself: Ansible

## 2018

I started working as a full-time SWE at Onapp KL

- \*Taught myself basics of message brokering & how asynchronous, microservice system works. It radically changed my views on how scalable systems are built.
- Learned about Python concurrency, multithreading and multiprocessing. Encountered a problem with Global Interpreter Lock (GIL) and solved it by not sharing a variable between the threads
- Taught myself DevOps tools like Puppet, Grafana, Netdata, etc.
- Other techs I taught myself: InfluxDB, Nginx Lua, MongoDB, etc.

## 2017

This is during my university internship year

- \*Taught myself Python. Wrote my first unit tests. Fell in love with Python ever since.
- Taught myself Docker. My personal development process has changed ever since.
- Other techs I taught myself: Java Spring & Hibernate, Behavioral Testing with Ruby Cucumber, CI/CD with Jenkins, Elastic stack

## 2016

- Studied basic Java, PHP & MySQL in my university years.
- Developed a document management system with a team of 5 students using IBM Cloud, Cloudant & Cloud Foundry for our university dissertation project.

## 2011

- Taught myself Ruby on Rails, Sinatra & jQuery. Built a simple blog and aiming to build a Youtube clone. Too ambitious for 16 years old kid.
- Deployed my first Heroku deployment
- Taught myself MySQL
