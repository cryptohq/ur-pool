## Open Source UR Mining Pool

The following build instructions are based on Ubuntu 16.04 LTS.

### Dependencies

* [go-ur](https://github.com/urcapital/go-ur)
* redis-server
* npm
* nginx

  apt update
  apt install -g -y build-essential libgmp3-dev golang git
  apt install -g -y redis-server npm nginx
  npm install -g ember-cli@2.4.3 bower

### Building backend

  git clone https://github.com/urcapital/go-ur.git
  cd ur-pool
  make
  mv config.example.json config.json

### Starting backend

  ./build/bin/ur-pool config.json

### Building UI on Linux

  cd www
  npm i
  bower i --allow-root
  ./build.sh

If there is a problem finding node run,
  sudo ln -s /usr/bin/nodejs /usr/bin/node

### Running pool

1. Start go-ur with RPC on port 9595
2. Run

  ./build/bin/ur-pool config.json

### Setup nginx

1. Run

  cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.$(date "+%b_%d_%Y_%H.%M.%S")

2. Configure nginx to serve API on <code>/api</code> subdirectory.
3. Configure nginx to serve <code>www/dist</code> as static website.

### Running Locally

  ember server --port 8082 --environment development

### Configuration

I recommend this deployment strategy:

  * Mining instance - 1x (it depends, you can run one node for EU, one for US, one for Asia)
  * Unlocker and payouts instance - 1x each (strict!)
  * API instance - 1x

If you are distributing your pool deployment to several servers or processes,
create several configs and disable unneeded modules on each server.

### Notes

* Unlocking and payouts are sequential, 1st tx go, 2nd waiting for 1st to confirm and so on. You can disable that in code. Carefully read `docs/PAYOUTS.md`.
* Also, keep in mind that **unlocking and payouts will halt in case of backend or node RPC errors**. In that case check everything and restart.
* You must restart module if you see errors with the word *suspended*.
* Don't run payouts and unlocker modules as part of mining node. Create separate configs for both, launch independently and make sure you have a single instance of each module running.
* If `poolFeeAddress` is not specified all pool profit will remain on coinbase address. If it specified, make sure to periodically send some dust back required for payments.

### Credits

Thanks to sammy007 for creating open-ethereum-pool!
