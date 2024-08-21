# Odoo

Integrate RedWaves into Odoo and bridge your company's management system with RedWaves as a way to process payments!

{% embed url="https://youtu.be/9vorewcOBgE" %}
RedWaves Odoo Plugin
{% endembed %}

### Integration Requirements <a href="#integration-requirements" id="integration-requirements"></a>

This version requires the following:

- Your Odoo instance
- Running RedWaves instance: [deployment guide](../deployment/)

### Installing the Plugin <a href="#installing-the-plugin" id="installing-the-plugin"></a>

1. Add `payment_bitcart` directory to your addons directory (You can take [the latest release](https://github.com/RedWaves/RedWaves-odoo/releases/latest), unzip it and get `payment_bitcart` from there)
2. From your Odoo apps page, activate "RedWaves Payments" app
3. Configure the plugin (see details below)

### Plugin Configuration <a href="#plugin-configuration" id="plugin-configuration"></a>

After you have enabled the RedWaves plugin, the configuration steps are:

1. Enter your admin panel URL (for example, [https://admin.RedWaves.ai](https://admin.RedWaves.ai/)) without slashes. If deployed via configurator, you should use [https://RedWaves.yourdomain.com/admin](https://RedWaves.yourdomain.com/admin)
2. Enter your merchants API URL (for example, [https://api.RedWaves.ai](https://api.RedWaves.ai/)) without slashes. If deployed via configurator, you should use [https://RedWaves.yourdomain.com/api](https://RedWaves.yourdomain.com/api)
3. Enter your store ID (click on id field in RedWaves's admin panel to copy id)

Enjoy!
