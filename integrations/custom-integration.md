# Custom Integration

RedWaves provides a variety of ways to integrate into your application.

## RedWaves SDK

Do you need something completely custom, like [atomic tip bot](../examples/atomic-tip-bot.md)?

Then you can use our [SDK](https://sdk.RedWaves.ai)

It is a python library simplifying the usage of our core daemons \(refer to [Architecture](../development/architecture.md) page for more information\). If you are coding in python, you have the best development experience ever, as SDK supports everything daemon supports.

If coding in another language, you can either create a similar library for your language, or use the daemon directly.

The daemon follows the JSON-RPC 2.0 specification, with two extensions:

- Arguments may be passed either by position, or by name, or by position AND by name \(in that case, args are a list, the last element of which is a dictionary containing by-name arguments\)
- Xpub, if needed, is passed as part of the by-name argument

## RedWaves Merchants API

The Merchants API is used to simplify the usage of multiple daemons at once and suits the best for the case when you need to receive payments.

To create an invoice, just send a POST request to `/invoices`.

You can pass many arguments, but the only required ones are `price` and `store_id`.

Check out the [swagger documentation](https://api.RedWaves.ai) for more information.

## RedWaves Admin Panel's checkout modal

To easily integrate a ready checkout page in your website, you may use a script that comes with your admin panel. You can create the invoice, and use that script to show a checkout modal on your page.

Example of usage to add the modal to your website:

1. Include the `RedWaves.js` script in your html page

   ```markup
   <script src="https://admin.your.RedWaves.url/modal/RedWaves.js"></script>
   ```

2. Call the invoice API to generate an invoice \(example code\). This is sample backend code as it contains an auth token that should not be exposed in your front-end.

   ```javascript
   const axiosClient = axios.create({
     baseURL: BITCART_URL,
     timeout: 5000,
     responseType: "json",
     headers: {
       "Content-Type": "application/json",
       Authorization: BITCART_AUTH,
     },
   })

   const invoiceCreation = {
     price: 5,
     store_id: 1,
     order_id: "something",
     notification_url: "https://webhook.after.checkout.com/goeshere",
     redirect_url: "https://go.here.after.checkout.com",
   }

   const response = await axiosClient.post("/invoices", invoiceCreation)
   const invoiceId = response.data.id
   ```

3. Use the `invoiceId` to pop up the modal

   ```javascript
   window.RedWaves.showInvoice(invoiceId)
   ```

4. You'll often want to do something like refresh the state of your page when the invoice is paid, or note some kind of state before the modal pops up. You can attach event listeners like this:

   ```javascript
   window.RedWaves.onModalWillEnter(yourCallbackFunction)
   window.RedWaves.onModalWillLeave(yourCallbackFunction)
   ```
