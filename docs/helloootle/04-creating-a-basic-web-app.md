# Creating your Web App Project
At the end of following the instructions, you will have a React project set up with TypeScript support using Vite as the development tool. This project will serve as the foundation for a web app that integrates with the Tari protocol. Specifically, you'll have:

- A new React application
- The various dependencies for the Tari protocol and by extension, the Ootle

Start by creating a folder for your project. Call it `Hello Ootle Project`

Next, launch VS Code and select the `Open Folder` option from the `File` menu. Open the folder you just created.

Next, select the `Terminal` menu and choose `New Terminal`. This will open a terminal at the bottom of your VS Code window.

## Create a React project

The quickest way to set up a new React project is to leverage `vite` to do so.

In the terminal, run the following command below, and select `Y` to continue

```bash
npm create vite@latest
```

When you run this command, you'll be likely asked to install the following. Select `Y` to continue

```bash
Need to install the following packages:
create-vite@6.3.1
Ok to proceed? (y)
```

You'll be asked to enter a project name. You can call it what you like but to make it easy to follow along, let's call it the `'helloootle-app`. 

```bash
> npx
> create-vite

✔ Project name: … helloootle-app
```
Next, select `React` from the list of options:

```bash
? Select a framework: › - Use arrow-keys. Return to submit.
    Vanilla
    Vue
❯   React
    Preact
    Lit
    Svelte
    Solid
    Qwik
    Angular
    Others
```

Lastly, choose the `Typescript` variant:

```bash
? Select a variant: › - Use arrow-keys. Return to submit.
❯   TypeScript
    TypeScript + SWC
    JavaScript
    JavaScript + SWC
    React Router v7 ↗
```

Once this is done, you'll be instructed to enter the following commands. Do so in the same terminal:

```bash
  cd latest
  npm install
  npm run dev
```

The above will install all the necessary dependencies and then run the template Vite + React application. When running the application, you'll see the following message:

```bash
  VITE v6.2.1  ready in 123 ms

  ➜  Local:   http://localhost:5173/
  ➜  Network: use --host to expose
  ➜  press h + enter to show help
```

The `localhost:port` address indicates that you have the app running locally at that address. Entering the address in your browser will open up the stock app. You'll see something equivalent to the below:

You can view the project and its associated files on the left-hand side of VS Code

We'll be modifying this app to get your Hello Ootle app up and running. For now, you can proceed to the next step.

## Installing the dependencies

Next, you're going to want to update your `package.json` file in the project to include the following items under the `dependencies` section.

Fortunately, you can just run the following command to do so:

```bash
npm install @tari-project/tari-permissions @tari-project/tarijs @tari-project/typescript-bindings @tari-project/wallet_jrpc_client @tari-project/wallet-daemon-provider @tari-project/tari-universe-signer @tari-project/wallet-connect-signer
```

Now, if you're new to VS Code, you'll see on the left-hand side, under the `Explorer` tab, a list of files that have been created. You can skip to the next section if you're already familiar with VS Code, but if not, you can start getting a feel for VS Code by locating the `package.json` and opening it.

Below is what you should be seeing in the package.json file following the above:

```json
    "@tari-project/tari-permissions": "^0.4.0",
    "@tari-project/tarijs": "^0.4.0",
    "@tari-project/typescript-bindings": "^1.4.0",
    "@tari-project/wallet_jrpc_client": "^1.4.0",
    "@tari-project/wallet-daemon-provider": "^0.4.0"
```