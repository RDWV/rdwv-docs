# Try it out

This section goes through the process of creating an account and store on our public RedWaves instance. \(For evaluation purpose\)

## Create your first invoice <a id="create-your-first-invoice"></a>

First let's create a new store:

1. Go to the [demo website](https://admin.RedWaves.ai)
2. In the login form click on **Sign up here** to [create an account](https://admin.RedWaves.ai/register)

Let's use Electrum to create a mainnet wallet for your store:

1. Download [Electrum](https://electrum.org)
2. Run Electrum
3. Click through the wizard and create a test wallet, using the default settings Electrum proposes
4. After the wallet is set up, go to "Wallet" &gt; "Information" in the Electrum menu.
5. Copy the "Master Public Key" string \(starting by `*pub...`\)

Let's configure the store so it uses your Electrum wallet:

1. Go to **Wallets** page and [create a new wallet](https://admin.RedWaves.ai/wallets) with your copied xpub
2. Go to **Stores** page and [create a new store](https://admin.RedWaves.ai/stores) with your new wallet connected
3. After that your test wallet should appear on the [Wallets page](https://admin.RedWaves.ai/wallets) of your RedWaves account

Then you can create an invoice, either through

- the **Invoices** page [on the website](https://admin.RedWaves.ai/invoices) or
- the process documented on the [Custom integration](../integrations/custom-integration.md)
- or the [store POS](../guides/store-pos.md), if you are the owner of the instance

See the [What's Next](https://docs.RedWaves.ai/getting-started/whatsnext) page for other options on how to continue exploring RedWaves.

## RedWaves Demo <a id="RedWaves-demo"></a>

To see RedWaves in action, visit our demo apps and stores or check out some of the stores using RedWaves in production.

- [RedWaves Demo Store](https://store.RedWaves.ai)
- [Admin panel](https://admin.RedWaves.ai)
- [Merchants API](https://api.RedWaves.ai)
- [Atomic tipbot](https://t.me/bitcart_atomic_tipbot)
