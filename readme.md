# Set Up a Node.js App for a Website With Apache on Ubuntu 16.04

## How integrate https with nodejs + apache2 server

I assume server setup done mith ubuntu-16.04 and instlled nodejs and apache2

> Create a Sample Node.js Application
```
sudo mkdir /var/www/html/nodejs
sudo nano /var/www/html/nodejs/hello.js
```
> Put the following content into the file:
```
#!/usr/bin/env nodejs
var http = require('http');
http.createServer(function (request, response) {
   response.writeHead(200, {'Content-Type': 'text/plain'});
   response.end('Hello World! Node.js is working correctly.\n');
}).listen(8080);
console.log('Server running at http://127.0.0.1:8080/');
```

> Make the file executable:
```
sudo chmod 755 hello.js
```

> Install PM2
```
sudo npm install -g pm2
```

Start the hello.js example script with the command:

```
sudo pm2 start hello.js
```

> As root add PM2 to the startup scripts, so that it will automatically restart if the server is rebooted:
```
sudo pm2 startup systemd
```

## Configure Apache2
> To access the Node.js script from the web, install the Apache modules proxy and proxy_http with the commands:

```
sudo a2enmod proxy
sudo a2enmod proxy_http

sudo service apache2 restart
```

> Next, you will need to add the Apache proxy configurations. These directives need to be inserted into the VirtualHost command block in the site's main Apache configuration file.

> By common convention, this Apache configuration file is usually /etc/apache2/sites-available/example.com.conf on Ubuntu.

> Note: The location and filename of a site's Apache configuration file can vary.

> Edit this file with your editor of choice, for example with the command:

```
sudo nano /etc/apache2/sites-available/example.com.conf
```
Scroll through the file until you find the VirtualHost command block, which will look like:

```
<VirtualHost *:80>
ServerName example.com

   ProxyRequests Off
   ProxyPreserveHost On
   ProxyVia Full
   <Proxy *>
      Require all granted
   </Proxy>

   <Location /nodejs>
      ProxyPass http://127.0.0.1:8080
      ProxyPassReverse http://127.0.0.1:8080
   </Location>

    <Directory "/var/www/example.com/html">
    AllowOverride All
    </Directory>
</VirtualHost>
```

> Save and exit the file, then restart Apache for the changes to take effect:

```
sudo services apache2 restart
```