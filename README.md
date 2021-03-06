# Blocker
---

Blocker is a javascript/svelte project for learning the basics of Svelte. It is converted to javascript/svelte from an early 2000 program.
Not wanting to write another "Hello World" app, this app serve the purpose to learn a new framework and as a start to utilize different functions.
The game itself is a simple timed avoidance game where you drag a box/smiley trying to avoid 4 rectangles and the boundary.

Feel free to modify and use the code as you wish. Learn by **doing and experimenting**.

A working demo is located at [Blocker](https:jsblocker.netlify.app/).
---

## Get started

Clone this repo or download the zip.
Install the dependencies.

```bash
cd blocker
npm install
```

then start [Rollup](https://rollupjs.org):

```bash
npm run dev
```

Navigate to [localhost:5000](http://localhost:5000). You should see the app running. Edit the App or a component file in `src`, save it, and reload the page to see your changes.

By default, the server will only respond to requests from localhost. To allow connections from other computers, edit the `sirv` commands in package.json to include the option `--host 0.0.0.0`.

## Building and running in production mode

To create an optimised version of the app:

```bash
npm run build
```

You can run the newly built app with `npm run start`. This uses [sirv](https://github.com/lukeed/sirv), which is included in your package.json's `dependencies` so that the app will work when you deploy to platforms like [Netlify](https://www.netlify.com/) or other similar platforms.

[![Netlify Status](https://api.netlify.com/api/v1/badges/adacc8f2-d6bf-4620-87d2-1c828a6164ff/deploy-status)](https://app.netlify.com/sites/happy-jones-2a6de0/deploys)
