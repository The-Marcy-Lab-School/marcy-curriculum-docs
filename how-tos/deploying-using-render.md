# How to Deploy on Render

**Table of Contents:**

- [Overview](#overview)
  - [Render vs. Github Pages](#render-vs-github-pages)
  - [Create An Account](#create-an-account)
- [Deploy A Server](#deploy-a-server)
  - [What Do the Build and Start Commands Do?](#what-do-the-build-and-start-commands-do)
- [Deploy A Database](#deploy-a-database)
- [Future changes to your code](#future-changes-to-your-code)

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

![The Render Dashboard](img/dashboard.png)

## Deploy A Server

1. Make sure you are signed in using your GitHub account
2. https://dashboard.render.com/ and click on **New +**
3. Select **Web Service**
4. Choose **Git Provider** to find a repository on your account. It may take some time for your repositories to load. 
    * If your repository is public, you can provide a link to the repository but it will not be able to auto-deploy on future commits. As such, this is NOT the preferred method.
5. Fill out the information for your Server

   * Name - the name of your app (it will appear in the URL that render gives you. For example: `app-name-here.onrender.com`)
   * Language - Node
   * Branch - `draft` for assignments, `main` for portfolio projects
   * Region - select US East (Ohio)
   * Root Directory - Leave blank (will default to the root of your repo)
   * Build Command:
     * If your application has a database, see the next section
     * If your application has a Vite frontend: 
        
        ```sh
        cd [vite_folder_name] && npm i && npm run build && cd ../server && npm i
        ```

     * If neither: 
        ```sh
        cd server && npm i
        ```
   * Start Command (assuming your `index.js` file is in `server/`):
     
     ```sh
     cd server && node index.js
     ```
 
   * Instance Type - select **Free**

6. Add Any environment variables your application may need:

    ![Add environment variables individually or paste multiple values at a time from a .env file.](img/environment-variables-configuration-render.png)

7. Select **Deploy Web Service**

This should take you to your web service's dashboard where you can see the latest build information and the URL. In a few minutes your server will be up and running!

Any time that you want to make an update to your deployed server, just commit and push the change to your repo! The deployment process will automatically run your "Build" and "Start" commands (unless you used a public git URL to setup your server in which cause auto-deployments are disabled).

![alt text](img/web-service-dashboard.png)

### What Do the Build and Start Commands Do?

The "Build" and "Start" commands are executed before every new deploy as a part of the "Auto Deploy" process. The server will re-deploy your application on every new commit.

Projects built using Vite cannot be served as they are stored in the repository. They need to have static assets created via the `npm run build` command. Those static assets are then stored in the `dist/` folder and served by our server. In the event that we change the frontend in a new commit, we want to rebuild those assets before restarting the server. Therefore, the "Build" command above is executed every time the server is deployed for a new commit (even when the frontend doesn't change).

After the "Build" command runs, the "Start" command runs to start the server. To ensure that our server works properly, we just need to make sure the server dependencies are installed and then run the `index.js` file with `node`:

![If your project had a `vite-project` folder for the frontend, and a `server` folder for the backend, your configuration would look like this](img/render-deploying-static-build-start.png)

As a result, the "continuous deployment" process would look like this:
1. A commit is made with changes to the project
2. Render detects the commit and begins a new deployment
3. The "build" command is executed, generating updated static assets
4. The "start" command is executed, starting the server
5. The deployment completes and the server is live!

## Deploy A Database

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

3. Deploy Your Express Server. Follow the instructions above for deploying a server with the following changes:

   * Build command: Okay this part is going to be a bit wonky because we are going to be using a Free instance type which means we don't have access to a "Pre-Deploy Command"
     * The very first time that you deploy your server, use the following as your build command to set up migrations, seeds, and build the frontend:

        ```sh
        cd frontend && npm i && npm run build && cd ../server && npm i && npm run migrate:rollback && npm run migrate && npm run seed
        ```
      
     * Immediately after the first deploy is successful, go back into your settings and change this build command to the following (we're removing the `migrate:rollback`, `migrate`, and `seed` portions as these will wipe your database!):

        ```sh
        cd frontend && npm i && npm run build && cd ../server && npm i
        ```

   * Add your environment variables:
     * Add a `SESSION_SECRET` variable and a long, random value. It can literally be just a random jumble of characters. You can also use [https://randomkeygen.com/](https://randomkeygen.com/) to generate a random key.
     * Add a `PG_CONNECTION_STRING` variable. Its value should be the `Internal Database URL` value from step 2e above.
     * Add a `NODE_ENV` variable set to `production`
     * Your values should look like this: (No quotations needed!)

        ```
        SESSION_SECRET=AS12FD42FKJ42FIE3WOIWEUR1283
        PG_CONNECTION_STRING=postgresql://user:password@host/dbname
        NODE_ENV=production
        ```

4. Click **Save Changes**

## Future changes to your code

If you followed these steps, your Render server will redeploy whenever the `main` branch is committed to. To update the deployed application, simply commit to `main`.