---
title: "2020: What's next for me"
date: 2019-12-08
slug: "2020-whats-next"
description: "This is an example post for hugo-theme-codex."
keywords: ["Devops", "Resolution"]
draft: false
tags: ["Devops"]
math: false
toc: true
---

I am super excited for 2020. There were lots of announcements from AWS re:Invent and Kubernetes Conference recently that excites me for 2020. There were lots of things for me to catch up.

## DevOps

### AWS SysOps Certification

This is my top priority for now. I have been studying AWS SysOps Associate certification from [A Cloud Guru](https://acloud.guru) in the past few months. Personally it is a bit hard for me because we do not use AWS cloud services at the company, so I have to spare my own time to study. I have a few more lessons before finishing the whole course. I hope that I can finish them and take the exam end of this year or early next year.

So far I really enjoyed the course. It really opens my eyes on how to use and how to administer them. I am now at 81% of the course progress. I will be doing some revisions before actually register for the exam.

![A Cloud Guru course progress](/blog/3-acloudguru.png)

### Kubernetes

I have been researching and evaluating Kubernetes for a few weeks at my company. I have stumbled upon many interesting concepts in Kubernetes. I have spent time researching about multi-tenancy, multi-cluster & storage on baremetal server. I have evaluated several Kubernetes distributions and I find [k3s](k3s.io) (with [k3sup](http://k3sup.dev)) is the best and easiest to get started.

Here are things I would love to try on Kubernetes:

- VMware Velero for backups
- Multi cluster on GKE (with traffic splitting by region)
- Multi cluster deployment using Booking's Shipper
- Inter-cluster communication using Cilium
- Hardened containerization gVisor, Kata Container and/or Firecracker (use WeaveWork FireKube)
- EKS on Fargate
- NATS pub/sub
- Prometheus & Loki

*Wouldn't be too much to consider for CKA or CKAD certification by the end of the year.*

### Vitess DB & Cockroach DB

I am surprised that complex database deployments on Kubernetes are surprisingly easy using Kubernetes Operator/Helm. In the coming year, I would love to use [Vitess DB](https://vitess.io) DB and [Cockroach DB](cockroachlabs.com) for my personal project. It might be overkill for small projects, but I would love the get the hands on experience.

I would love to have a real feel of administering DBs on Kubernetes. 

### Container Services

With announcements from AWS, Azure and GCP, the container services are getting hot. I would love to try ECS Fargate, GCP Cloud Run, Azure Container Service. I also would love to try [firecraker-containerd](https://firecracker-microvm.github.io/).

### Other Devops stuffs

I would love to try the new distributed DB in town [rqlite](https://github.com/rqlite/rqlite), get myself familiar with Ansible and Terraform. As of now, I am only comfortable with Puppet for server orchestration.

## Web Development

### Go

Go is the [3rd most wanted programming language](https://insights.stackoverflow.com/survey/2019#most-loved-dreaded-and-wanted) according to StackOverflow. I see a lot of sysadmins are using this day-to-day. There are lots of cool projects out there written in Go eg Kubernetes, Docker, Dgraph, BoltDB, InfluxDB, etcd, etc . Personally I do not have much experience in Golang because we do not use this widely in our company so again, I have to spare my own time to learn this cool tech.

[![Introduction to Golang](https://img.youtube.com/vi/C8LgvuEBraI/0.jpg)](https://www.youtube.com/watch?v=C8LgvuEBraI)

After watching this video, I immediately fell in love with the beauty of Golang. Again, coming from Python background, Go introduces many new concepts for me. I have been learning Golang for a few months now. However I haven't write anything really useful using Go as of now. I am planning to write microservices in Go using GORM, Echo/Gin webserver, etc. and also CLI tools.

### Python 3

I am spending most of my time in the company writing Python code, so I am doing this on a daily basis. However there is one new Python API that excites me. [FastAPI](https://fastapi.tiangolo.com/) is a new that has an impressive performance and is on par with Go and NodeJS. It's like a newer, faster version of Flask. I'll be rewriting my Trip Planner project using FastAPI to benefit its speed.

### DynamoDB NoSQL

[![AWS DynamoDB Deep Dive session](https://img.youtube.com/vi/6yqfmXiZTlM/0.jpg)](https://www.youtube.com/watch?v=yfJZc3sJZ8E)

Personally, I am a bit skeptical on DynamoDB at first. Last time we have trouble on partitioning and distributing data evenly across the shards. But during a [deep dive on DynamoDB](https://www.youtube.com/watch?v=6yqfmXiZTlM) at AWS re:Invent 2019, I saw lots of compliments from folks on Twitter about DynamoDB that makes me wanna go a bit deeper about this. You can read the gist from [here](https://www.jeremydaly.com/takeaways-from-dynamodb-deep-dive-advanced-design-patterns-dat403/). DynamoDB introduces many new things to me including Single Table design and access patterns that is different from how we handle regular RDBMS.

### Frontend

Not to emphasize much on this, but I'll be trying Webflow for personal projects. I am not a front end guy so this is a bit hard for me. At least I can do fancy web animation with No-code :P I have tried ReactJS before and I can feel that it is not really 'my thing'. I'm still stuck with Bootstrap and jQuery. At least it works for me LOL

## Personal

### Umrah

I am planning to perform Umrah in Mecca with my wife some times in next year. Lets pray for this to happen.

### Vacations

My wife and I love to go for vacation. We haven't decide yet where to go in 2020 but on top of my head, maybe somewhere in Asia. We've been to South Korea this year.

### Keto-diet & Eat Clean

I am now at 74kg. At 168cm, my ideal weight is **56-71 kg**. I am planning to lose about 5 to 8kg from now and eat clean with my wife.

On top of all, I wish to write more blog posts in 2020 :D

## Adios

I have a lot of things to do in 2020. I know that a good engineer should not be distracted by new technologies but instead a good engineer should focus on inter-personal qualities and fundamental programming skills. However, I want to have a good and right DevOps skillset for now. Personally, I have a decent inter-personal skill where **I have mentored 2 new members** in my team and I regularly conduct brown bag sharing sessions with my teammates.
