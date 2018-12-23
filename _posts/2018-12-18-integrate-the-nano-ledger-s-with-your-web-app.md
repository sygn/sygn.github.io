---
layout: post
title: "Integrate the Nano Ledger S with your web application"
date: 2018-12-18 05:32:44
image: '/assets/img/post-image.png'
description: 'A simple guide to integrate the Nano ledger S with your web application'
tags:
- blockchain 
- ethereum 
- 'Nano ledger S'
- 'javascript'
categories:
- Blockchain
twitter_text: 'Integrate the Nano Ledger S with your web application'
---

##  About the Nano Ledger S
The Nano ledger S is an offline hardware wallet used for storing cryptocurrencies. It supports most of the different crypto currencies like bitcoin, ethereum and other altcoins. Lately I've been working on a web application for a blockhain based startup. The cool part about the app was that the user would need to sign specific blockchain transactions with their private wallets. A requirement was that the web application needed to support the Nano ledger S for authentication and message signing.

Through this post I will give a simple example how you can set up your application to be able to communicate with a Nano ledger S, get the basic information from the wallet and sign transactions.

##  Communication
There are different ways how you can set up the communication between the Nano ledger S and your application. 
Some of them are:
1. Node - with <a href="https://github.com/node-hid/node-hid" target="_blank">node-hid</a> (via USB)
2. Browser - using (<a href="https://github.com/grantila/u2f-api" target="_blank">U2F api</a>)
3. Bluetooth
And several others.

All supported communication options can be found on <a href="https://github.com/LedgerHQ/ledgerjs" target="_blank">Ledger github repo</a>.

In our project we chose the U2F communication as we were going to use the Nano ledger S directly in our web application.

##  U2F requirements
U2F itself has its own requirements which are:
- **U2F browser support enabled on the Ledger nano s**  
  Luckily the Nano Ledger S has that option enabled by default. If you are on another device, please check in the settings where you can enable browser support.    
- **Your browser needs to support U2F**
  The latest version of Chrome and Opera already support U2f, for older versions or browser please use an extension.  
  <a href="https://caniuse.com/#search=u2f" target="_blank">Check the current browser support for the U2f API</a>
- **HTTPS is required**

## Solution
Let's start by installing and adding the necessary libs to our project.
{% highlight shell %}
// terminal
yarn add @ledgerhq/hw-transport-u2f
yarn add @ledgerhq/hw-app-eth
{% endhighlight %}

{% highlight JavaScript %}

// app.js
import Transport from "@ledgerhq/hw-transport-u2f";
import AppEth from "@ledgerhq/hw-app-eth";

{% endhighlight %}

As mentioned before we decided to communicate with our Ledger nano S through the browser which is why we are using the `@ledgerhq/hw-transport-u2f` library for transport. As our blockchain is ethereum based we also needed the `@ledgerhq/hw-app-eth` library to be able to communicate with our wallet.

### Getting the address
To get the address we need to open a transport connection to the device and connect to our Ethereum app and pass the wallet BIP32 path. Once connected we can call the `getAddress` function on our wallet.

#### Getting the BIP32 path
You can get your wallet path with the <a href="" target="_blank">Ledger Live</a> app.

1. Go to your account in the app
2. Click on edit account
3. Toggle Advanced Logs
4. In the JSON object you will find your account path in the field `freshAddressPath`.

#### Getting the account
{% highlight JavaScript %}
const getAccount = async () => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  const result = await ethApp.getAddress(path);
  return result;
};
{% endhighlight %}

## Signing a transaction
To sign a specific transaction we can use the `signTransaction` function provided by the `@ledgerhq/hw-app-eth` app. The function expects again the BIP32 path of the account and the raw transaction to be signed

### Signing a raw transaction
{% highlight JavaScript %}
export const signTransaction = async (rawTx) => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  return ethApp.signTranscation(path, rawTx);
};

// example
signTransaction("e8018504e3b292008252089428ee52a8f3d6e5d15f8b131996950d7f296c7952872bd72a2487400080").then(result => {
  // do something with result
})
.catch(ex => {
  console.log(ex);
});
{% endhighlight %}

## Complete solution
{% highlight JavaScript %}
// ledger-api.js
import Transport from "@ledgerhq/hw-transport-u2f";
import AppEth from "@ledgerhq/hw-app-eth";

export const getAccount = async () => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  const result = await ethApp.getAddress(path);
  return result;
};

export const signTransaction = async (rawTx) => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  return ethApp.signTranscation(path, rawTx);
};
{% endhighlight %}
