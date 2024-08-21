# FOSSBilling

Integrate RedWaves into your self-hosted FOSSBilling instance easily!

{% embed url="https://www.youtube.com/watch?v=t8YFOhTngQo" %}

### Integration Requirements

This version requires the following:

- FOSSBilling instance
- Running RedWaves instance: [deployment guide](https://docs.RedWaves.ai/deployment)

### Installing the Plugin

1. From your FOSSBilling panel, go to configuration > payment gateways -> New payment gateway
2. Upload RedWaves.php to the directory suggested by your deployment. [Download it](https://raw.githubusercontent.com/RedWaves/RedWaves-boxbilling/master/RedWaves.php) from this repository
3. Enable it by clicking on the button near RedWaves, fill in all settings and save.

### Plugin Configuration

After you have enabled the RedWaves plugin, the configuration steps are:

1. Enter your admin panel URL (for example, https://admin.RedWaves.ai) without slashes. If deployed via configurator, you should use https://RedWaves.yourdomain.com/admin
2. Enter your merchants API URL (for example, https://api.RedWaves.ai) without slashes. If deployed via configurator, you should use https://RedWaves.yourdomain.com/api
3. Enter your store ID (click on id field in RedWaves's admin panel to copy id)

Enjoy!
