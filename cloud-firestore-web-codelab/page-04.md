## 4. Install the Firebase Command Line Interface

The Firebase Command Line Interface (CLI) allows you to serve your web app locally and deploy your web app to Firebase Hosting.

> **Note:** To install the CLI, you need to install [npm](https://www.npmjs.com/) which typically comes with [NodeJS](https://nodejs.org/en/).

To install the CLI, run the following npm command:

```
npm -g install firebase-tools
```

> Doesn't work? You may need to [change npm permissions](https://docs.npmjs.com/getting-started/fixing-npm-permissions).

To verify that the CLI has been installed correctly open a console, then run:

```
firebase --version
```

Make sure the version of the Firebase CLI is v4.1.0 or later.

Authorize the Firebase CLI by running:

```
firebase login
```

Once authorized, go back to the command line and make sure that you're still in your app's directory.

Our web app template is configured to automatically pull your project's configuration from the Firebase Hosting environment. However, we still need to associate your project with your Firebase project. Run the following command:

```
firebase use --add
```

You'll be prompted to select your Project ID and then to give this Firebase project an alias. An alias is useful if you have multiple environments (production, staging, etc). However, for this codelab, let's just use the alias of "default". Follow the instructions in your terminal.
