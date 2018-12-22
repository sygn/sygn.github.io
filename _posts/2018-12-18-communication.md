---
layout: post
title: "Set up your react app to be able to communicate with the Nano ledger S"
date: 2018-12-18 05:32:44
image: '/assets/img/post-image.png'
description: 'How to setup your react app to be able to communicate with a Nano Ledger S wallet'
tags:
- blockchain 
- ethereum 
- 'Nano ledger S'
categories:
- Blockchain
twitter_text: 'How to install and use this template'
---

##  About the Nano Ledger S
The Nano ledger S is an offline hardware wallet. It packs with it rich support for a lot different crypto currencies like bitcoin, ethereum and other altcoins. Lately I've been working on a web application for a blockhain based startup. The cool part about the app is that user would need to sign specific blockchain transactions with their private wallets. A solution we introduced was the usage of the Nano ledger S.

Through this post I will give a simple example how you can set up your application to be able to communicate with a Nano ledger S.


##  Communication
There are different ways how you set up the communication between the Nano ledger S and your application. 
Some of the are:
1. node - with node-hid (via USB)
2. browser - using (<a href="https://github.com/grantila/u2f-api" target="_blank">U2F api</a>)
3. Bluetooth
And several others.

All supported communication options can be found on <a href="https://github.com/LedgerHQ/ledgerjs" target="_blank">Ledgers github repo</a>.

In our we wanted to communicate via the U2F option, because we needed to communicate with Nano direclty from the browser.

##  U2F requirements
U2F itself has its own requirements which are:
- **U2F browser support is needed for Ledger**  
  Luckily the Nano Ledger S has that option enabled by default. If you are on another device, please check in the settings where you can enable browser support.    
- **Your browser needs to support U2F**
  The latest version of Chrome and Opera already support U2f, for older versions or browser please use an extension.  
  <a href="https://caniuse.com/#search=u2f" target="_blank">Check the current browser support for the U2f API</a>
- **HTTPS is required**

## Solution
Let's start by adding the necessary libs to our project.
{% highlight javscript %}
import Transport from "@ledgerhq/hw-transport-u2f";
import AppEth from "@ledgerhq/hw-app-eth";

{% endhighlight %}

As mentioned before we decided to communicate with our Ledger nano S through the browser which is why we are using the `@ledgerhq/hw-transport-u2f` librrary for transport. As our blockchain is ethereum based wealso needed the `@ledgerhq/hw-app-eth` library to be able to communicate with our wallet.


### Getting the address
To get the address we need to open a transport conneciton to the device and connect to our Ethereum app and pass the wallet BIP44 path. Once connected we can call the `getAddress` function on our wallet.

{% highlight javscript %}
const getBtcAddress = async () => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  const result = await ethApp.getAddress(path);
  return result;
};
{% endhighlight %}


### Complete solution
{% highlight javscript %}
import Transport from "@ledgerhq/hw-transport-u2f";
import AppEth from "@ledgerhq/hw-app-eth";

const getBtcAddress = async () => {
  const path = "44'/60'/0'/0/0";
  const transport = await Transport.create();
  const ethApp = new AppEth(transport);
  const result = await ethApp.getAddress(path);
  return result;
};

getBtcAddress().then(a => {
  console.log(a);
})
.catch(ex => {
  console.log(ex);
});
{% endhighlight %}
