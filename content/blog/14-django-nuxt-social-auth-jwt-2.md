---
title: "How to Build Google Social Login in Django Rest Framework and Nuxt Auth and Refresh its JWT token Part 2"
date: 2020-12-01
slug: "django-nuxt-social-auth-jwt-2"
description: "Refreshing JWT Token and recommended settings for production environment"
keywords: ["Python", "Nuxt", "JWT", "DRF", "Django Rest Framework"]
draft: false
tags: ["Python", "Nuxt", "JWT"]
math: false
toc: true
---

You can find the first part of this tutorial here. In the first part of the tutorial, we have built a functioning frontend and backend app that allows users to authenticate via Google login.

## Refreshing a JWT Token

Weare not done yet! Even though our app can authenticate a new user correctly, our still Nuxt app cannot refresh its JWT token. When the token is expired, and the token refresh is failed, the user will be logged out unexpectedly. This would be a bad experience for your user.
Let’s simulate a token expiry by setting the token lifetime duration to 1 second in backend/backend/settings.py the file and navigate to the page. You’ll get a 401 Unauthorized error when making a request to the backend from your Nuxt app like below.

```python
// backend/backend/settings.py
SIMPLE_JWT = {
-    'ACCESS_TOKEN_LIFETIME': timedelta(minutes=5),
+    'ACCESS_TOKEN_LIFETIME': timedelta(seconds=5),
     'REFRESH_TOKEN_LIFETIME': timedelta(days=30),
     'ROTATE_REFRESH_TOKENS': True, # IMPORTANT
     'BLACKLIST_AFTER_ROTATION': True, # IMPORTANT
```

Now edit protected.vue frontend page so that it makes a request to our backend API on every page load.

```js
// frontend/pages/protected.vue
@@ -1,6 +1,6 @@
 <template>
   <section class="section">
-      {{ $auth.user.email }}
+      {{ user }}
   </section>
 </template>

 
 export default {
   middleware: 'auth',
+  async fetch() {
+    this.user = await this.$axios.get("http://localhost:8000/auth/user/")
+  },
+  data() {
+    return {
+      user: null
+    }
+  },
```

Here’s the demo simulating a JWT token expiry:

From the demo, we can see that:
The first time loading pagelocalhost:3000/protected is successful after the login. You can see that request to http://localhost:8000/social-login/google/ to authenticate the user is successful, and the request to http://localhost:8000/auth/user to fetch user info is also successful.
However, after 5 seconds, when refreshing the page localhost:3000/protected, the user is logged out involuntarily. Nuxt Auth tried to refresh the token by requesting to http://localhost:8000/social-login/google/. However, the Backend API returns a 400 Bad Request error because it doesn’t understand the refresh token process as it only expects the access token generation.
From here, we can conclude that our endpoint must both generate access and refresh an expired token.
Important Note: Please set the token lifetime duration to a sensible number to you. Usually, the duration is set to 5 minutes to 15 minutes for access token validity and 14 days to 30 days for refresh token validity. However, this depends on the nature of your system.

## Step 6: Refreshing the JWT Token

To fix this, we’ll modify how our /social-login/google endpoint works to ensure that it is able to generate new access tokens and also refreshes the expired tokens. Edit the view as per below:
The code above is probably not the optimum one, and I would be happy to accept suggestions from the readers. In line 19, it checks whether if there’s any “code” key in the HTTP POST payload sent by the user. It’s typically sent after the social login process is done from Google Auth. If it does have “code”, then generate an access token for the user. Otherwise, get the refresh token from the payload and refresh the token for the user. In line 28, we have to assign the value request.data[“refresh”] = request.data.get(“refresh_token”) because Nuxt Auth is sending a refresh token with the key “refresh_token” while Django SimpleJWT library is expecting the refresh token with the key "refresh" instead. It’s a bit confusing because there is no standard on how that parameter should be named. In lines 31 & 32, we’re again doing a similar thing because of interoperability between the two systems. Nuxt Auth is expecting a response with keys refresh_token and access_token while TokenRefreshView are responding with keys refresh and access. Now both of them are talking to each other.
Finally, don’t forget to change the url.py like below:
Now our Nuxt app can refresh the token successfully. See the demo below:

From this demo, we can see that:
The request to localhost:3000/protected is successful on the first load
The second request to the same page requires a token refresh and our Backend successfully refreshes the token.
The user is not logged out of the app after successfully refreshed the token.
Securing Your Nuxt App
There’s one more thing, let’s secure our Nuxt app. You can add more customizations to your implementation by adding these parameters in your nuxt.config.js https://auth.nuxtjs.org/schemes/oauth2
Here are some best practices to secure your Nuxt app:
Set Samesitesettings to Lax to disallow other sites to get the cookie data from your web app.
Only allow auth cookie from HTTPS. Bear in mind that this might not work in your local development environment unless you set up HTTPS in localhost. You can disable it in the local development environment and enable it in the production environment.
Disable the localStorage as it is blasphemous for storing auth token and it’s vulnerable to many attacks
Use an environment variable to store clientId. Programming 101.
You can follow the following settings:

```js
auth: {
    strategies: {
      google: {
        clientId: process.env.GOOGLE_SOCIAL_LOGIN_CLIENT_ID,
        codeChallengeMethod: "",
        responseType: 'code',
        endpoints: {
          token: `${process.env.API_SERVER_URL}social-login/google/`,
          userInfo: `${process.env.API_SERVER_URL}auth/user/`,
        },
      },
    },
    cookie: {
      options: {
        secure: process.env.NODE_ENV === "production", // Enable in Prod only!
        sameSite: 'lax',
      }
    },
    localStorage: false
  },
```

You can inspect the auth cookie in Chrome Debugger like below:

From this demo, we can see that:
The auth tokens are not stored in the browser’s localStorage
The auth tokens are stored in the browser cookie and are set to Lax Samesite settings. This is crucial to mitigate CSRF attacks on your system.
If you’re in production (and set the environment variable NODE_ENV = “production” , Nuxt will only set the auth token if the backend API is served from the HTTPS endpoint.
In production, this is how the auth cookies look like:

Important Note: Bear in mind that these settings mitigate CSRF attacks, but your web app is still vulnerable to XSS attacks. Please assess the system security in production.
Thanks for reading!
vickieli.medium.com

### Http-only Auth Cookie

It’s impossible to use the Http-only cookie in our current setup. This is because the Nuxt Auth module has to be able to access auth cookie to inject the Authorization header with the access token from the cookie. If you prefer to use Http-only cookie for auth token, please refer to this awesome article
Session Authentication with Django, Django REST Framework and Nuxt
This will be a continuation of the discussion about how data flows in Django + Nuxt applications, looking specifically…
briancaffey.github.io

With session authentication, you must serve both frontend & backend from the same domain eg. example.com/app for the frontend and example.com/api for the backend. In contrast to our current setup in this tutorial, you can serve the frontend from domain eg. app.example.com and backend from another domain api.example.com. This allows you to leverage static hosting services like Vercel, Netlify & Cloudflare Pages to serve the frontend for high availability and for free.

## Voila!

To recap

- Our backend API http://localhost:8000/social-login/google/ is now generates a new access token & refresh access token so that the user is not logged out of the session
- Our Nuxt app now stores the access token & refresh token obtained from the Backend API in the browser’s cookie and not in the browser’s localStorage.

If you’re stuck at any point in this tutorial, feel free to peek at this git commit or checkout this Github repository.
sdil/nuxt-auth-drf-social-login-demo
GitHub is home to over 50 million developers working together to host and review code, manage projects, and build…
github.com

Baserow.io, an using open-source Airtable alternative is using almost similar authentication we did in this tutorial.

### Shameless Plug

If you enjoy reading this tutorial, please support me here to write more articles in the future.
