# etherpad
My own etherpad configuration

### Instructions
##### MariaDB
Follow MariaDB instructions [here](https://www.howtoforge.com/tutorial/ubuntu-etherpad-editor-server-installation/)
1. `apt update -y`
2. `apt install gnupg2 git unzip libssl-dev pkg-config gcc g++ make build-essential -y`
3. `apt install mariadb-server` 
4. `mysql`
   1. `create database etherpad;`
   2. `grant all privileges on etherpad.* to etherpad@localhost identified by 'PASSWORD';` with your own, secure password in place of `PASSWORD` 
    3. flush privileges;
    4. exit;
##### Node.js 
Follow Node.js instructions from [here.](https://www.howtoforge.com/tutorial/ubuntu-etherpad-editor-server-installation/)
1. `curl -sL https://deb.nodesource.com/setup_17.x -o nodesource_setup.sh` Note the updated version. If facing issues, 17 and 14 both worked for me previously. 
2. `bash nodesource_setup.sh`
3. `apt install nodejs` returns something like `v17...`
##### Etherpad
Install and configure Etherpad via instructions from same site above
1. `adduser --home /opt/etherpad --shell /bin/bash etherpad` and set a secure password when prompted
2. `install -d -m 755 -o etherpad -g etherpad /opt/etherpad`
3. `su - etherpad`
4. `git clone --branch master https://github.com/ether/etherpad-lite.git`
5. `cd etherpad-lite`
6. `bin/run.sh`
7. `vim settings.json`
8. Remove the following lines
  ```
  "dbType" : "dirty",
  "dbSettings" : {
                   "filename" : "var/dirty.db"
                 },
  ```
9. Change the MySQL settings to match previous set up
  ```
  "dbType" : "mysql",
  "dbSettings" : {
    "user":     "etherpad",
    "host":     "localhost",
    "port":     3306,
    "password": "PASSWORD", #change this to match your secure password!
    "database": "etherpad",
    "charset":  "utf8mb4"
  },
  ```
10. Change the `trustProxy` line: `"trustProxy": true,`
11. Define a password for admin user:
  ```
  "users": {
    "admin": {
      "password": "SUPERSECUREPASSWORD#CHANGEME",
      "is_admin": true
    },
  ```
12. `./bin/installDeps.sh` and ignore any errors. They shouldn't cause any real issues for a personal use server, although you'll want to fix the security issues if this is used in a more sensitive context
13. `exit`
##### Systemd Service
Create systemd service.
1. Use [file](etherpad.service) and follow instructions inside.
##### Nginx
Set up nginx
1. `sudo apt install nginx`
2. `sudo ufw allow "Nginx Full"`
3. Set up [etherpad.conf] file as instructed in file. Make sure to replace `e.mysterylie.xyz` with your own preferred domain, for example `etherpad.example.com`
4. `sudo ln -s /etc/nginx/sites-available/etherpad.conf /etc/nginx/sites-enabled/`
5. `sudo nginx -t` to test your configuration file and look for "ok" and "successful" at end of output
6. `sudo systemctl reload nginx`
##### Certbot and SSL
Install certbot and SSL
1. sudo apt install certbot python3-certbot-nginx      
2. `sudo certbot --nginx -d e.mysterylie.xyz` (but update the domain to match the one you used in the previous step)
3. When asked, choose option `2` to redirect all traffic to HTTPS. You can change this back if you run into issues later.
4. **Reload your site and make sure you see the lock!!** If you see the lock, you're done!
