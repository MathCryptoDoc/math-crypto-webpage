---
title: "Batch delegation"
weight: -10
---

It is expected that OpenGov (formerly known as Gov2.0) will lead to much more referenda than before. **Delegation of your votes to an account whose voting behavior you trust is therefore a good option.**

Unfortunately, the user experience of delegating your votes is currently not very smooth. You can in principle issue an extrinsic on [Polkadot.js](https://polkadot.js.org/) or you could delegate on [SubSquare](https://kusama.subsquare.io) or on [Polkassembly](https://kusama.polkassembly.io/). However, both interfaces require you to perform the action for *each referendum track individually*. Since there are 15 tracks, it would be better to issue one batch call.

**Math Crypto has made an encoder for exactly such a batch call.**

{{< hint type=important >}}
In you want to delegate from a **Ledger account** (the sending account in step 4 below), you first need to have a **Governance** or Any proxy for it.  Read this [support article](https://support.polkadot.network/support/solutions/articles/65000182179-how-to-create-a-proxy-account) by the W3F on how to create and remove a proxy.
{{< /hint >}}

{{< hint type=tip >}}
You can also check out this [short explanation on YouTube](https://youtu.be/ZcqOTNzmGr0?t=728) by [Leemo](https://twitter.com/leemoyoutube).
{{< /hint >}}

{{< hint type=important >}}
If you have **voted or delegated already on OpenGov** with the sending account (in step 4 below), you need to send two extrinsics: one for removing the existing votes/delegations and one for setting up the new delegations. If unsure, do both extrinsics.
{{< /hint >}}


Instructions on how to send the delegate extrinsic (the remove extrinsic is very similar):
1) Go to our [Delegation Encoder](https://backend-opengov.math-crypto.com/) page to generate your hash.


2) In another browser tab/window, go to *Polkadot.JS* ➔ *Developer* ➔ *Extrinsic* ➔ *Decode* (or use the [direct link](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc.dotters.network%2Fkusama#/extrinsics/decode)). Copy the hash from the Delegation Encoder page (use the copy button!) and paste it in the ``hex-encoded call`` field.

![Submit hex-encoded call](encoder-hex-call.png)

3) You now see the content of the extrinsic. Check it for the account, balance, and conviction. If all correct, choose the *Submission* tab.

{{< hint type=tip >}}
The balance is in Planck units: the normal KSM amount times 1e12 (100...000 with 12 zeros). The example below corresponds to 0.7 KSM.
{{< /hint >}}


![Submitted hex-encoded call](encoder-hex-call-submitted.png)

4) Choose the account that you want to delegate from (your own account) in the ``using the selected account``. This can be a Ledger account as long as you have a **Governance** or Any proxy for it.

![Choose sender account](submit-from-account.png)

5) Now scroll to the bottom and choose ``Submit Transaction``. It will ask for your password of the selected account.

![Sign the message](submit-sign.png)

6) Submit password, choose *Sign and submit*. If you have a proxy for the account, you can choose it here. Remember to only use **Governance** or Any proxies. 

7) If you get a green icon in the top-right corner, the extrinsic was successful. Your account is now delegating its votes for all referenda! A yellow icon might indicate errors or warnings. Please check your delegation.





