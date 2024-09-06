# Deploying a Vite Project with Github Pages

So, you've built an app - congrats! You can run it locally, but wouldn't it be sweet if everyone on the internet could use it??

This resource covers deploying a Vanilla JS Vite app using Github Pages. 

> Note: An alternate (and more complex, but potentially smoother) approach can be found on [Vite's own docs](https://vitejs.dev/guide/static-deploy).

**Table of Contents**
- [Prerequisites](#prerequisites)
- [What is Github Pages?](#what-is-github-pages)
- [Configure Vite for Deployment on Github Pages](#configure-vite-for-deployment-on-github-pages)
- [Publish on Github Pages](#publish-on-github-pages)

{% embed url="https://youtu.be/0Iw6SZpnHKY" %}

## Prerequisites

In order to deploy to Github using this guide, you will need 
* A project built using Vite
* A Github repo with that Vite project inside.

## What is Github Pages?

Github Pages is ([according to their website](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages))

> _... a static site hosting service that takes HTML, CSS, and JavaScript files straight from a repository on GitHub, optionally runs the files through a build process, and publishes a website._

There are a few ways to utilize Github Pages but the most straightforward is **to have an `index.html` in the root of our repo** that Github Pages can easily find and serve.

With a few VERY easy settings, we can publish our repo as long as that `index.html` file is in the root.

**To test this out**:
* add an `index.html` file to the root of your repo
* commit and push it
* follow the instructions below in the [Publish On Github Page section](#publish-on-github-pages). 
* delete the `index.html` file once you've tested it.

## Configure Vite for Deployment on Github Pages

**Objective(s)**: Build the production version of the app in the root of the directory.

> If you made an `index.html` file for testing above, delete it now.

Assuming you built that app using Vite, the first step is to make the **production version** of the application. To do this, run the command:

```
npm run build
```

This will create the **production version** of your app in a folder called `dist/` (short for "distribution"). Take a look inside! It will have an `index.html` file and an `assets/` folder with your JavaScript and CSS.

**However, we need that `index.html` to be in the root of the repo:**

In order to do that, we'll need to configure Vite to create that version in the right location.

Create a Vite configuration file

```sh
touch vite.config.js
```

And put this inside:

```js
import { defineConfig } from 'vite'

export default defineConfig({
  // GitHub Pages expects an index.html in the root directory
  // so just run npm build before pushing to GitHub and this will rebuild our assets to the root
  build: { outDir: '..' },
  // needed for github pages just put the repo name here
  base: '/your-repo-name-here/', 
});
```

This does two things:
* `build` and `outDir` determine where to put your compiled application. Normally it would put it in a folder called `dist/` but in order for Github pages to work, we want to build in the root of the repo.
* `base` determines how the `index.html` file connects to the `.js` and `.css` files in your `assets/` folder

Now, run the command

```sh
npm run build
```

This will **compile** the code you've written in your `app/` folder into optimized static files that can quickly be served by Github pages. It will put those files in the root directory of your repo, where Github expects to find an `index.html` file and any associated `assets`.

You can see what this "deployed" version will look like by running the command...

```sh
npm run preview
```

...which will serve the application at http://localhost:4173/

Finally, **commit and push** your new compiled version to Github!

> Note: each time your `npm run build`, new versions of your `assets` will be created and you will need to manually delete them.

## Publish on Github Pages

**Objective(s)**: Publish your web app!

Deploying your application on Github Pages is about as easy as it gets:

1. Open the repo on Github.com
2. Go to the <kbd>Settings</kbd> tab
3. Find the <kbd>Pages</kbd> section
4. Make sure that **Source** is **Deploy from a branch**
5. Below, set **Branch** to `main` (or whatever branch you're using)
6. Click **Save** and wait a few minutes! You should see the URL of your application.
7. Each time you push a new commit to `main`, your page will redeploy!