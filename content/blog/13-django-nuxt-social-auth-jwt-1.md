---
title: "How to Build Google Social Login in Django Rest Framework and Nuxt Auth and Refresh its JWT token Part 1"
date: 2020-12-01
slug: "django-nuxt-social-auth-jwt-1"
description: "Build end-to-end Social Login screen in Nuxt & Django"
keywords: ["Python", "Nuxt", "JWT", "DRF", "Django Rest Framework"]
draft: false
tags: ["Python", "Django", Nuxt", "JWT"]
math: false
toc: true
---

## Overview

Over the past few weeks, I’ve been working on a side project. One of the requirements is to enable the user to sign in to the application using Google social login. The web app is built using Nuxt for the frontend and Django REST Framework for the backend.
Initially, building the social sign in flow was not hard at first, but I struggled with refreshing the JWT token in the web app. Most of the resources on the internet do not discuss how to refresh the JWT token. In this tutorial series, I will show how to build a basic JAMstack app with Nuxt as the frontend and Django REST Framework for the backend and allow social login from a Google account.
What we’re going to build

We are going to build a simple Nuxt app that accepts user login with Google accounts. It will connect to a Django API Server as a backend.
How Nuxt Auth Google Login Works
Before we start, it’s good to know that the Nuxt Auth Google scheme is inherited from the OAuth2 login scheme. If you intend to use other OAuth2 providers (e.g., Apple, Facebook, etc.), the flow should be somewhat similar. This is how the login flow will work behind the scene:

Nuxt OAuth2 sequence diagram
When this.$auth.loginWith("google") function is invoked (typically by clicking a button), the browser will be redirected to the Google OAuth2 login screen with a URL like https://accounts.google.com/o/oauth2/auth?protocol=oauth2&response_type=token&access_type&client_id=to%20xxx&redirect_uri=http%3A%2F%2Flocalhost%3A3000%2Flogin&scope=openid%20profile%20email&state=XKVqiO-8r9Pwky8viP9dS&code_challenge_method=implicit. Notice the client_id & redirect URI parameters in the URL.
When the user choose which account he/she wants to connect to the login screen, Google will authenticate the user and return the authorization code to the Nuxt callback page.
NuxtJS will request a JWT access token from the Django API server by supplying the authorization code payload obtained from Google in step #2.
Django API Server will revalidate the code and access token from NuxtJS with the Google authentication server.
If the code and token are valid, Django API Server will create a user account in the DB and then returns the JWT access token & refresh token.
Nuxt then receives the new access token and refresh token from Django API Server and store the JWT tokens in both browser's local storage & browser cookies. However, it’s always recommended to store the JWT token in cookies only and never store it in browser local storage.
Nuxt Auth will then requests user information from Django API Server.
Django API Server will reply to the request with the user information. Nuxt Auth will store the user information.
How Nuxt Auth Refreshes JWT Token for OAuth2 Flow
Provided that the user’s access token has expired, and when you click a page in NuxtJS that sends a request to Django API Server, the Django API will reply with 401 Unauthorized access
Nuxt Auth will intercept the failed API requests to refresh the token and retry the API request earlier. Nuxt Auth will refresh the token at the endpoint you set in the URL endpoint in nuxt.config.js
Django API Server will reply with a new access token and refresh token
Nuxt Auth will replace the old access token and refresh token in cookies & browser local storage with the new one
Nuxt Auth will retry the API call made earlier with a new access token
Voila! You got a correct 200 OK result from the API server

## Let’s begin

### Prerequisites

These are the software I’m using to write this tutorial:

- Python v3.7.3
- Django v3.1.3
- Django REST Framework v3.12.2
- django-allauth v0.42.0
- dj-rest-auth v1.1.0
- PyJWT v1.7.0 (djangorestframework-simplejwt library does not support the - latest version of PyJWT yet)
- Node v10.15.3
- Nuxt v2.14.6
- Nuxt Auth module v5.12.3

You also must have access to the Google Cloud console in order to obtain Googel OAuth Client ID & Secret Key.

### Step 1: Build a Nuxt app

First, let’s begin by bootstraping a new Nuxt app

```bash
$ mkdir google-login
$ cd google-login
$ npm init nuxt-app frontend
create-nuxt-app v3.4.0
✨  Generating Nuxt.js project in frontend
? Project name: google-login
? Programming language: JavaScript
? Package manager: Npm
? UI framework: Buefy
? Nuxt.js modules: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Linting tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? Testing framework: None
? Rendering mode: Universal (SSR / SSG)
? Deployment target: Static (Static/JAMStack hosting)
? Development tools: (Press <space> to select, <a> to toggle all, <i> to invert selection)
? What is your GitHub username? sdil
? Version control system: Git
```

I’m using the Buefy UI framework as I’m familiar with it the most. You can use other UI frameworks if you wish to. Next, let’s install the Nuxt Auth v5 module:

```
$ cd frontend
$ npm install --save-exact @nuxtjs/auth-next
$ npm install @nuxtjs/auth-next @nuxtjs/axios
```

Add Nuxt Auth module in frontend/nuxt.config.js file and define google strategy:

```
# nuxt.config.js

  modules: [
     // https://go.nuxtjs.dev/buefy
     'nuxt-buefy',
+    '@nuxtjs/axios',
+    '@nuxtjs/auth-next'
   ],
+  auth: {
+    strategies: {
+      google: {
+        clientId: 'to be added'
+      },
+    }
+  },
```

Now, create a login page frontend/pages/login.vue with the following content:

login.vue
In line 15, we are logging in the user with thegoogle strategy we defined in nuxt.config.js file.
Next, create a protected page at frontend/pages/protected.vue

protected.vue
In line 12, we enabled the Nuxt Auth middleware, which means this page is only accessible when the user is signed in. Otherwise, the user will be redirected to the login page.
Let’s test our Nuxt app to make sure that Nuxt Auth is actually working. Run nuxt dev command to run the development server and navigate to localhost:3000/login.

It’s expected for you to see a Google login error at this point since we haven’t set up the Google OAuth yet.

### Step 2: Build Django API Server

Start a Django project & install the dependencies.

```bash
$ cd google-login
$ django-admin startproject backend
$ pip install django-rest-framework dj-rest-auth django-allauth django-cors-headers djangorestframework-simplejwt PyJWT==1.7.0
```

Update 16 Feb 2021: You’ll need PyJWT version 1.7.0 in your system because djangorestframework-simplejwt library does not support PyJWT latest version yet. Refer here: https://github.com/SimpleJWT/django-rest-framework-simplejwt/issues/326
Let’s create a new social-login app.

```
$ django-admin startapp social-login
```

Write backend/social-login/views.py file like below:

Install the social login app URLs in the root URL at backend/backend/urls.py:

Add the following lines inbackend/backend/settings.py like below:

Let’s test the login endpoint with curl CLI:

```bash
# Run DB migration first
$ python manage.py migrate
# Start Django webserver
$ python manage.py runserver 0:8000
# Run in another terminal
$ curl -i -X POST localhost:8000/social-login/google/ -H "Content-type: application/json" -d '{"code": "test", "access_token": "test"}'
HTTP/1.1 500 Internal Server Error
...
$ curl -i localhost:8000/auth/user/
HTTP/1.1 403 Forbidden
...
{"detail":"Authentication credentials were not provided."}
```

localhost:8000/social-login/google/ endpoint will be used to validate code & access tokens given by Google. This endpoint will also be used for a token refresh.
localhost:8000/auth/user/ endpoint will be used to retrieve user information.
This 500 error & 403 error are expected as we haven’t set up the Google OAuth login yet. This proves that our Django app is serving correctly.
Step 3: Setup Google OAuth Flow
Login to console.cloud.google.com and navigate to the API & Services > OAuth consent screen. We’ll first need to create an OAuth consent screen for our project.


Fill in the App name, user support email & developer contact information.

For scopes, add user email & profile and hit Save & Continue.
Next, go to Credentials & click Create New Credentials, and select “OAuth client ID”.

Fill in the details like the following.

Make sure you write the correct Authorized redirect URLs. In our case, it’s http://localhost:3000/login. If you’re deploying on the production server with a valid domain, change it accordingly.
Keep the Client ID & Secret shown after creation.

### Step 4: Add Google Client ID to Nuxt app

Set the client ID obtained in Step #3 in nuxt.config.js file like below

```js
# frontend/nuxt.config.js
 
  auth: {
     strategies: {
       google: {
-        clientId: 'to be added',
+        clientId: '<your cliendID here>',
+        codeChallengeMethod: '',
+        responseType: 'code',
+        endpoints: {
+          token: 'http://localhost:8000/social-login/google/',
+          userInfo: 'http://localhost:8000/auth/user/'
+        },
       },
     }
   },
```

By now, our Nuxt app should be able to authenticate users using their Google account. See below:

Yay, it works!

### Step 5: Add Google Client ID & Secret to Django app
First, you’ll need to create a new Django superuser
$ cd backend
$ python manage.py createsuperuser
Username (leave blank to use 'fadhil'): admin
Email address: admin@test.com
Password: 
Password (again): 
Superuser created successfully.
And then, go to localhost:8000/admin and navigate to the “Social applications” section. Click “Add new” and fill in the details.


Fill in the Client ID & Secret Key obtained in step #3. Lastly, in the Sites section, add the “example.com” site by clicking the arrow button in the middle.
Let’s test the web app from end to end.

Yay! It works as expected. Our Nuxt & Django API Server are well integrated.

### To be continued…
That’s all for part 1. We’re not done yet. For part 2, we will refactor the Django API server so that it can refresh the JWT token and ensure that the user is logged in throughout his/her session. Part 2 is available here: