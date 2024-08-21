# Manual Deployment

The process is basically the following:

1. Install OS required libraries
2. Install python3 (3.9+)
3. Install nodejs (20) and yarn
4. Install postgresql
5. Install redis (6.2.0+)
6. Clone and run all parts of RedWaves
7. (Optional) Open Firewall Ports and Access the Sites

## Warning: Not recommended to use in production <a href="#warning-not-recommended-to-use-in-production" id="warning-not-recommended-to-use-in-production"></a>

Manual installation is NOT recommended in production. It should be only used for learning purpose.

Instead you should use the [docker deployment](docker.md).

The docker deployment will provide you easy update system and make sure that all moving parts are wired correctly without any technical knowledge. It will also setup HTTPS for you.

## Typical manual installation <a href="#typical-manual-installation" id="typical-manual-installation"></a>

This steps have been done on ubuntu 22.04, adapt for your own install.

### 1) Install OS required libraries

```bash
sudo apt install libsecp256k1-dev
```

> Or the equivalent for your os package manager

More info on libsecp256k1 in [electrum docs](https://github.com/spesmilo/electrum-docs/blob/master/libsecp256k1-linux.rst) or [bitcoin core docs](https://github.com/bitcoin-core/secp256k1#build-steps)

### 2) Install Python 3

Usually it might have already been installed, but we also need pip3 and dev packages, so:

```bash
sudo apt install python3 python3-pip python3-dev
```

### 3) Install Node.JS and Yarn

```bash
sudo apt install nodejs
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update && sudo apt install yarn
```

{% hint style="info" %}
If nodejs from your distro is not at least the version we require, then you should install it via [nodesource](https://github.com/nodesource/distributions)
{% endhint %}

{% hint style="warning" %}
Using a nodejs version higher than specified way work, but we officially support only 1 release series (usually current LTS)
{% endhint %}

### 4) Install PostgresSQL

Note, replace `REPLACEME` with your new postgres password.

```bash
sudo apt install postgresql postgresql-contrib
sudo -u postgres createdb RedWaves
sudo -u postgres psql -U postgres -d postgres -c "alter user postgres with password 'REPLACEME';"
```

### 5) Install Redis

```bash
sudo apt install redis-server
```

{% hint style="info" %}
Ensure that your redis version is > 6.2.0. Check with `redis-server -v`.

If redis from your distro is too old, install from [official redis repository](https://redis.io/docs/getting-started/installation/install-redis-on-linux/#install-on-ubuntudebian)
{% endhint %}

### 6) Clone and prepare RedWaves components

#### RedWaves core(daemons) & Merchants API:

<pre class="language-bash"><code class="lang-bash">git clone https://github.com/RedWaves/RedWaves
cd RedWaves
<strong># Optional: Create virtual environment instead of using the global python environment
</strong>python3 -m venv env
source env/bin/activate
# continue installation
sudo pip3 install -r requirements.txt
sudo pip3 install -r requirements/production.txt
sudo pip3 install -r requirements/daemons/btc.txt
</code></pre>

For any other daemon(coin) you want to use, run:

```bash
sudo pip3 install -r requirements/daemons/coin_name.txt
```

Where coin\_name is coin code(btc, ltc, etc.).

Create a file `conf/.env` It contains all the settings. For now, we just need to set database password and enabled cryptos.

```bash
# Replace REPLACEME with your database password
# specify used cryptocurrencies with BITCART_CRYPTOS

cat > conf/.env << EOF
DB_PASSWORD=REPLACEME
BITCART_CRYPTOS=btc,ltc
EOF
```

Apply database migrations:

```bash
alembic upgrade head
```

#### RedWaves admin panel

```bash
git clone https://github.com/RedWaves/RedWaves-admin
cd RedWaves-admin
yarn
yarn build
```

#### RedWaves store

```bash
git clone https://github.com/RedWaves/RedWaves-store
cd RedWaves-store
yarn
yarn build
```

## Run everything

#### RedWaves core(daemons) & Merchants API:

Start daemons from the `RedWaves` repo directory:

```bash
python3 daemons/btc.py
```

For any other coin, do the similar procedure:

```bash
python3 daemons/coin_name.py
```

Start api:

```bash
gunicorn -c gunicorn.conf.py main:app
```

Start background worker:

```bash
python3 worker.py
```

> If you want to run a specific coin on a test network or change other environment settings you can update the `.env` file in the [RedWaves `conf/` directory](https://github.com/RedWaves/RedWaves/tree/master/conf)

#### RedWaves admin panel

```bash
cd RedWaves-admin
yarn start
```

#### RedWaves store

```bash
cd RedWaves-store
NUXT_PORT=4000 yarn start
```

#### Default ports

* The RedWaves API runs on port `8000`.
* Daemons on ports `5000-500X`
* The RedWaves Admin panel runs on port `3000`
* The RedWaves Store runs on port `4000`.

## (Optional) Open Firewall Ports and Access the Sites

If you are running RedWaves on your local machine - you will not need to do these steps. You can go ahead and access the system with:

* RedWaves Admin Panel: `http://127.0.0.1:3000/`
* RedWaves Store: `http://127.0.0.1:4000/`
* RedWaves Merchants API: `http://127.0.0.1:8000/`

If you are running RedWaves on a remote machine, you will need to do additional things to access them.

#### Option 1: Nginx proxy (Recommended)

This option is recommended to proxy secure incoming requests to the correct RedWaves process.

Install Nginx

```bash
sudo apt install nginx
```

Add configuration for each component: RedWaves-store, RedWaves and RedWaves-admin

```nginx
vim /etc/nginx/sites-available/RedWaves-admin.conf

server {
    server_name RedWaves-admin.<mysite>.com;
    access_log /var/log/nginx/RedWaves-admin.access.log;
    error_log /var/log/nginx/RedWaves-admin.error.log;

    location / {
        proxy_pass http://localhost:3000;
    }
}
```

Enable the config

```bash
sudo ln -s /etc/nginx/sites-available/RedWaves-admin.conf /etc/nginx/sites-enabled
```

> Add DNS records for your server names to point to your VM's ip

Check the config and reload nginx

```bash
sudo nginx -t
sudo systemctl reload nginx
```

Add TLS certificates with the letsencrypt CA for the sites

```bash
sudo apt install certbot
sudo certbot --nginx
```

Now you should be able to access the components over TLS. You can then also enable `http2` in your nginx configuration if you want.

> You might want to look at the [FAQ for more detailed info on the Nginx configuration options](../support-and-community/faq/deployment-faq/#can-i-use-an-existing-nginx-server-as-a-reverse-proxy-with-ssl-termination)

#### Option 2: No proxy

If you have a firewall, you will want to open ports `3000`, `4000` and `8000`. Using `ufw` as an example:

```bash
sudo ufw allow 3000
sudo ufw allow 4000
sudo ufw allow 8000
```

> `yarn` is listening on localhost `127.0.0.1` by default and you won't be able to access it over the internet unless you reverse proxy it with `nginx`. If you want to expose it without reverse proxy, use the environment variable: `NUXT_HOST=0.0.0.0` to listen on all interfaces.

The store and admin site need **public** access to the RedWaves api (URL should be resolvable both client and server side).

Using the manual method you need to set that with environment variables. The complete setup of the RedWaves Admin Panel and Store may look like this:

```bash
# RedWaves-admin
NUXT_HOST="0.0.0.0" BITCART_ADMIN_API_URL="http://RedWaves-api-ip:8000" yarn start
# RedWaves-store
NUXT_PORT=4000 NUXT_HOST="0.0.0.0" BITCART_STORE_API_URL="http://RedWaves-api-ip:8000" yarn start
```

> Note: The above is the minimum to make it work and not a production grade solution. We still recommend to use docker deployment unless you really know what you're doing.

**Access the site remotely**

* RedWaves Admin Panel: `http://my-RedWaves-admin-ip:3000/`
* RedWaves Store: `http://my-RedWaves-store-ip:4000/`
* RedWaves Merchants API: `http://my-RedWaves-store-ip:8000/`

Continue with: [Your first invoice](../your-first-invoice/)

## (Optional) Managing Processes

If you want the procaesses: RedWaves api, daemons, worker and frontend (RedWaves admin and RedWaves store) to be managed with automatic startup, error reporting etc then consider using [supervisord](http://supervisord.org/) or [systemd](https://systemd.io/) to manage the processes.

## Upgrading manual deployment

Note: it is recommended to use docker deployment for easy upgrades.

To upgrade manually, follow the following steps:

### 1) Stop everything already running

Merchants API, workers, daemons, Admin panel and Store should be stopped

### 2) Pull latest changes

Run :

```
git pull
```

For every RedWaves component directory (Merchants API, Admin Panel, Store).

### 3) Upgrade dependencies

#### RedWaves core(daemons) & Merchants API:

```bash
sudo pip3 install -r requirements.txt
sudo pip3 install -r requirements/production.txt
sudo pip3 install -r requirements/daemons/btc.txt
```

**RedWaves admin**

```
yarn
```

**RedWaves store**

```
yarn
```

### 4) Apply new database migrations

In RedWaves core(daemons) & Merchants API directory, run:

```
alembic upgrade head
```

### 5) Rebuild store and admin

For RedWaves Admin Panel and Store, run:

```
yarn build
```

### 6) Start everything again

Follow instructions [here](manual.md#run-everything)
