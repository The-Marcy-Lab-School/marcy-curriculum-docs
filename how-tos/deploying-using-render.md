# How to Deploy on Render

**Table of Contents:**

* [Overview](deploying-using-render.md#overview)
  * [Render vs. Github Pages](deploying-using-render.md#render-vs-github-pages)
  * [Create An Account](deploying-using-render.md#create-an-account)
* [Deploy A Simple No-Database App](deploying-using-render.md#deploy-a-simple-no-database-app)
* [Deploy A Fullstack Server With A Database](deploying-using-render.md#deploy-a-fullstack-server-with-a-database)
  * [Future changes to your code](deploying-using-render.md#future-changes-to-your-code)

## Overview

### Render vs. Github Pages

* Github Pages provides **static site hosting**.
  * This means that the server that Github Pages runs on your behalf can only send static files to the client (HTML, CSS, and JS files).
  * Github Pages static sites are not capable of receiving or sending messages via HTTP.
* Render provides **web service and database hosting** (it can also host static sites).
  * This means that the server that Render runs on your behalf can send static assets, receive and send messages via HTTP, and interact with a database.
  * Render also can host your database giving you a one-stop-shop for running your fullstack application.

### Create An Account

Start by creating an account using your **GitHub** account. This will let you easily deploy straight from a GitHub repository.

![create an account using GitHub](img/create-account.png)

This will take you to your Dashboard where you can see existing deployments.

![alt text](img/dashboard.png)

## Deploy A Simple No-Database REST API

Follow these instructions to deploy an Express app that does NOT include a database and does NOT serve static assets. If your app serves static assets or includes a database, go to the next sections.

1. Make sure you are signed in using your GitHub account
2. https://dashboard.render.com/ and click on New +
3. Select **Web Service**
4. Build and deploy from a Git repository
5. Find and Connect your GitHub repository
6. Find your repository and select **Connect**
7. Fill out the information for your Server

   * Name - the name of your app (it will appear in the URL that render gives you. For example: app-name-here.onrender.com)
   * Region - select US East (Ohio)
   * Branch - main
   * Root Directory - `server/` or wherever your `index.js` file lives. Use `./` if it is in the root of your repo.
   * Runtime - Node
   * Build Command - leave blank
   * Start Command - `node index.js`
   * Instance Type - select **Free**
   * Select **Create Web Service**

This should take you to your web service's dashboard where you can see the latest build information and the URL. In a few minutes your server will be up and running!

Any time that you want to make an update to your deployed server, just commit and push the change to your repo!

![alt text](img/web-service-dashboard.png)

## Deploy A Static Server

Follow the same steps as above. However, when configuring the server, make the following changes:

- Build Command — `cd [name_of_frontend_vite_folder] && npm i && npm run build`
- Start Command — `cd [name_of_server_folder] && npm i && node index.js`

For example, if your project had a `vite-project` folder for the frontend, and a `server` folder for the backend, your configuration would look like this:

![On render, the build and start commands are set](img/render-deploying-static-build-start.png)

## Deploy A Fullstack Server With A Database

1. Make sure you have an account on https://render.com/ and that you sign in using Github
2. Create a Postgres Server
   * https://dashboard.render.com/ and click on New +
   * Select **PostgreSQL**
   * Fill out information for your DB (leave all other fields blank)
     * Name - the name of your application
     * Region - select US East (Ohio)
     * Instance Type - select Free
   * Select **Create Database**
   * Keep the created database page open. You will need the `Internal Database URL` value from this page for step 4. This URL will look follow this pattern:

```
postgresql://user:password@host/dbname
```

3. Deploy Your Express Server

* https://dashboard.render.com/ and click on New +
* Select **Web Service**
* Connect your GitHub account (if not connected already)
* Find your repository and select **Connect**
* Fill out the information for your Server
  * Name - the name of your app (it will appear in the URL that render gives you. For example: app-name-here.onrender.com)
  * Region - select US East (Ohio) - the important thing is that it matches the PostgreSQL region
  * Branch - main
  * Root Directory - if you used the React Express Auth Template, leave this blank
  * Runtime - Node
  * Build Command - if you used the React Express Auth Template, use `npm i && npm run migrate:rollback && npm run migrate && npm run seed`
  * Start Command - if you used the React Express Auth Template, use `npm start`
  * Instance Type - select Free
* Select **Create Web Service** (Note: The first build will fail because you need to set up environment variables)

4. Set up environment variables

* From the Web Service you just created, select Environment on the left side-menu
* Under Secret Files, select Add Secret File
  * Filename - .env
  * Contents
    * Look at your local .env file and copy over the `SESSION_SECRET` variable and value.
    * Add a second variable called `PG_CONNECTION_STRING`. Its value should be the `Internal Database URL` value from step 2e above.
    * Add a third variable called `NODE_ENV`. Its value should be `'production'`
    * Your file should look like this:

```
SESSION_SECRET='AS12FD42FKJ42FIE3WOIWEUR1283'
PG_CONNECTION_STRING='postgresql://user:password@host/dbname'
NODE_ENV='production'
```

* Click Save Changes

### Future changes to your code

If you followed these steps, your Render server will redeploy whenever the `main` branch is committed to. To update the deployed application, simply commit to `main`.

For frontend changes, make sure to run `npm run build` to update the `public/` folder contents and push.
