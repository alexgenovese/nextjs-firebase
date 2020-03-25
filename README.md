
<div align="center">

# Nextjs & Firebase Starter

[Next.js][nextjs] SSR React on Firebase Hosting with Cloud Functions

</div>

Cloud Functions and Next.js can combine to provide a low-cost auto-scaling, SSR app experience.

Firebase Hosting can [rewrite routes to a Cloud Function][function-rewrites] that serves our server-side rendered Next.js React app. Each individual `page` bundle is served in a new call to the Cloud Function which performs the initial server render. 

This results in our app being served on our Hosting URL instead of the Cloud Function URL:

`<project-name>.firebaseapp.com/` & `<project-name>.web.app/`

instead of:

`https://us-central1-<project-name>.cloudfunctions.net/<function-name>`

## Usage

```shell
# install
yarn install
# setup: setup firebase config to deploy
yarn setup
# watch
yarn watch
# build
yarn build
# locally test
yarn serve
# deploy to firebase
yarn deploy
```

- the `src/` and `dist/` folder are deployed alongside one another
- no need to randomly copy files between places
- `yarn serve` works out of the box

[function-rewrites]: https://firebase.google.com/docs/hosting/full-config#section-rewrites
[fb-route-priority]: https://firebase.google.com/docs/hosting/full-config#hosting_priority_order
[medium-nextjs]: https://medium.com/@jthegedus/table-of-contents-ec337953b39b
[nextjs]: https://nextjs.org/


## Set up firebase

- install Firebase Tools: `npm i -g firebase-tools`
- create a project through the [firebase web console](https://console.firebase.google.com/)
- grab the projects ID from the web consoles URL: `https://console.firebase.google.com/project/<projectId>`
- update the `.firebaserc` default project ID to the newly created project
- login to the Firebase CLI tool with `firebase login`

### Customization

Next App and Next Server development are separated into two different folders:

- app - `src/client/`
- server - `src/server/`

If you wish to modify any configuration of the Next App, you should only modify the contents of `src/app`.

For instance, the `.babelrc` in `src/server` is used only to compile the Firebase Cloud Functions code, which is our the Next Server code. If you wish to customize the `.babelrc` for the Next App compilation, then you should create one at `src/app/.babelrc` and follow the [customization guide](https://github.com/zeit/next.js#customizing-babel-config).

## Important

- The empty `placeholder.html` file is so Firebase Hosting does not error on an empty `public/` folder and still hosts at the Firebase project URL.
- `firebase.json` outlines the catchall rewrite rule for our Cloud Function.
- Specifying [`"engines": {"node": "8"}`](package.json#L5-L7) in the `package.json` is required for firebase functions
  to be deployed on Node 8 rather than Node 6
  ([Firebase Blog Announcement](https://firebase.googleblog.com/2018/08/cloud-functions-for-firebase-config-node-8-timeout-memory-region.html))
  . This is matched in [`src/functions/.babelrc`](src/functions/.babelrc) so that babel output somewhat compacter and moderner code.

### \_app.js

If using `_app.js` you may receive the following error on your deployed Cloud Function:

```
{ Error: Cannot find module '@babel/runtime/regenerator'...
```

Despite next.js having `@babel/runtime` as a dependency, you must install it as a dependency directly in this project.
