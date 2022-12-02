---
title: "Batch delegation"
weight: -10
---

It is expected that OpenGov (formerly known as Gov2.0) will lead to much more referenda than before. **Delegation of your votes to an account whose voting behavior you trust is therefore a good option.**

Unfortunately, the user experience of delegating your votes is currently not very smooth. You can in principle issue an extrinsic on [Polkadot.js](https://polkadot.js.org/) or you could delegate on [SubSquare](https://kusama.subsquare.io). However, both interfaces require you to perform the action for *each referendum track individually*. Since there are 15 tracks, it would be better to issue one batch call.

Math Crypto has made an encoder for exactly such a batch call. Below are the instructions on how to use it:

{{< hint type=important >}}
The procedure below does not work with Ledger accounts (since the generated batch call is not supported by Ledger). 
{{< /hint >}}


1) Go to our [Delegation Encoder](https://backend-opengov.math-crypto.com/) page to generate your hash.


2) In another browser tab/window, go to *Polkadot.JS* ➔ *Developer* ➔ *Extrinsic* ➔ *Decode* (or use the [direct link](https://polkadot.js.org/apps/?rpc=wss%3A%2F%2Frpc.dotters.network%2Fkusama#/extrinsics/decode)). Copy the hash from the Delegation Encoder page (use the copy button!) and paste it in the ``hex-encoded call`` field.

![Submit hex-encoded call](encoder-hex-call.png)

3) You now see the content of the extrinsic. Check it for the account, balance, and conviction. If all correct, choose the *Submission* tab.

{{< hint type=tip >}}
The balance is in Planck units: the normal KSM amount times 1e12 (100...000 with 12 zeros). The example below corresponds to 0.7 KSM.
{{< /hint >}}


![Submitted hex-encoded call](encoder-hex-call-submitted.png)

4) Choose the account that you want to delegate from (your own account) in the ``using the selected account``. 

![Choose sender account](submit-from-account.png)

4) Now scroll to the bottom and choose ``Submit Transaction``. It will ask for your password of the selected account.

![Sign the message](submit-sign.png)

6) Submit password, choose *Sign and submit*. If there is no red icon in the top right corner, the extrinsic was successful. Your account is now delegating its votes for all referenda!


{{< hint type=tip >}}
You can repeat this procedure to update or correct the delegation. Having prior delegations or direct votes is not a problem.
{{< /hint >}}



