# Hackathon Introduction & Instructions (WIP)

Welcome to this internal Tari Hackathon! The purpose of today's session is to allow you to start interacting with the Ootle, developing contracts and apps and generally running the Ootle through it's paces.

We've focused on making sure that it's easy to get set up and started, so we've done some pre-setup on our side. However, you're still going to need to configure a couple of items before you can get going:

* Ideally, you'll need to set up Rust in your desired Integrated Development Environment (IDE). If you're unsure where to start, our [Setting Up Your Development Environment Guide](https://tari.com/lessons/08_setting_up_development_environment) is a good starting point
* The Ootle Wallet Daemon. This is your wallet on the second layer and how you will be interacting with ContractNet via Tari.js (more on this in a bit).
* Once set up, you'll need to grab some free test tokens so you can submit transactions, deploy templates and more.
* The Tari.js library, [located here](https://github.com/tari-project/tari.js/). This typescript library should help you perform some of the common functions and calls for the wallet easily via jRPC. A detailed guide for the library can be found here: [Tari.JS Library Introduction](tarijs_library_intro.md)
* **Optional** We have created a sample project (A decentralized exchange smart contract on the Tari network. The main functions include swapping tokens, adding liquidity, and removing liquidity.) that leverages Tari.js, so you have some practical working examples. 

In addition, we'll cover some basic implementations and actions you can perform with code examples so you can get started.

## But firstly - what is the Ootle?

If you're unfamiliar with the Ootle, we'd like to encourage you to read the [Tari Introduction](tari-introduction.md), which gives a quick, high-level overview of Tari, the Ootle and it's various components.

## Set up the Ootle Wallet Daemon

While there are a couple of ways a developer can set up a working environment for Ootle development, we're going to be using the easiest setup, which is to use the Ootle Wallet Daemon for a local wallet and then connect to ContractNet via the wallet.

The binary you require is ```tari_dan_wallet_daemon```

You'll be able to find the latest binaries [here](https://github.com/tari-project/tari-dan/releases). Alternatively, you can grab the repo via git in your repos folder:

```
git clone https://github.com/tari-project/tari-dan.git
```

And build the project to obtain the required executable.

## Run the wallet and create an account

To begin, run the following command in the folder in which the ```tari_dan_wallet_daemon``` is installed:

```
./tari_dan_wallet_daemon --network igor
```

This will start the wallet, and you should see the following below:

```bash
15:18 INFO  🌐 JSON-RPC listening on 127.0.0.1:9000
15:18 INFO  💤 Stopping JSON-RPC
15:18 INFO  🕸️ HTTP UI started at 127.0.0.1:5100
15:18 INFO  🕸️ HTTP UI listening on 127.0.0.1:5100
```
Navigating to the HTTP URL shown will allow you to access the Ootle wallet's web interface. At this point, you will not have an account (if you're wondering, but wait, what's an account - we'll be explaining some of these items in the following sections.), so you will need to create one. Enter the Account Name and select **"Create Account"** to create the associated account and proceed to the main wallet interface.

![Alt text](/docs/assets/images/tari_dan_wallet_account_creation.png)

This will be your primary wallet to use with either your unique app or the sample app provided.

## Digging into the sample project
