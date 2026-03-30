# TaskMinder

last updated: 19th march 2025

> ⚠️ **Warning:** Documentation deprecated, new docs here: [TaskMinder Docs](https://docs.taskminder.de).

## DEVELOPMENT

1. Start an editor of you choice (preferrably VS Code), install extension “git graph”

<aside>
💡 Ensure node and npm are installed before continung.

</aside>

1. Open Terminal, execute

```bash
sudo npm install
```

to install all dependencies.

Installation of redis:

Follow the guide of your OS (see link below):

For GitHub Codespaces, follow the guide for Ubuntu (Linux).

[Install Redis](https://redis.io/docs/latest/operate/oss_and_stack/install/install-redis/)

start Redis:

```bash
individuell for OS

GitHub Codespaces:
sudo service redis-server start
```

For the next step, you need to install the database, PostgreSQL, which is individuell for every OS:

[PostgreSQL: Downloads](https://www.postgresql.org/download/)

For GitHub Codespaces, execute the following commands to install PostgreSQL start and create the database:

```bash
sudo apt-get update
sudo apt-get -y install postgresql
sudo service postgresql start
sudo su postgres
psql postgres
\password
CREATE DATABASE haplaner;
```

For the other OS, please get into pqsl (SQL Shell) as user postgres and change the password using \password and lastly, create the database:

```bash
psql -U postgres
\password
CREATE DATABASE haplaner;
```

Create an .env file in the root folder, which holds all your enviroment variables:

For DB_PASSWORD, set the value to your password set for the postgres user ealier in this guide.

.env:

```bash
DB_USER=postgres
DB_PASSWORD=your_postgres_password
DB_NAME=haplaner
DB_HOST=localhost
NODE_ENV=DEVELOPMENT
REDIS_HOST=redis
REDIS_PORT=6379
SESSION_SECRET=your_session_secret
DSB_USER=your_dsb_user
DSB_PASSWORD=your_passcode
CLASSCODE=your_classcode
DB_CONTAINER=taskminder-postgres
BACKUP_DIR=/var/taskminder/backups
```

Before starting the server, you still need to create two tables:

```bash
node ./src/initTables/eventType.js
node ./src/initTables/team.js
```

Finally, you can start the server:

```jsx
nodemon server.js
```

Visit http://localhost:3000 to view the website.

Stopping the server:

```markdown
control+c
```

Stopping redis and postgresql:

See inidividual guides for redis and postgresql on thier offical websites:

```bash
GitHub Codespaces:
sudo service postgresql stop
sudo service redis-server stop
```

---

## Old SQL cmds (not used anymore)

1. (Listet alle Datenbanken auf):

```bash
\\l

```

1. (Verbindet sich mit Datenbank haplaner):

```bash
\\c haplaner

```

1. (Erstellt Tabelle users):

```sql
CREATE TABLE users (
id SERIAL PRIMARY KEY,
username VARCHAR(255) UNIQUE NOT NULL,
password VARCHAR(255) NOT NULL,
-- school VARCHAR(200) NOT NULL,
class VARCHAR(255) NOT NULL
);

```

1. Erstellt Tabelle hausaufgaben10d:
(wenn bereits erstellt wurde: → ALTER TABLE hausaufgaben10d(…….);)

```sql
CREATE TABLE homework10d (
   homeworkId SERIAL PRIMARY KEY,
   content TEXT NOT NULL,
   subjectId INT NOT NULL,
   assignmentDate BIGINT NOT NULL,
   submissionDate BIGINT NOT NULL
   -- lastChanged DATE NOT NULL,
   -- author VARCHAR(50) NOT NULL,
   -- FOREIGN KEY (author) REFERENCES users(username)
);

```

1. Erstellt Tabelle ereignisse10d

```markdown
(leer)

```

1. Erstellt Tabelle user_ha10d (JOIN TABLE)

```sql
CREATE TABLE homework10dCheck (
   checkId SERIAL PRIMARY KEY,
   username VARCHAR(255) NOT NULL,
   homeworkId INT NOT NULL,
   checked BOOLEAN NOT NULL DEFAULT FALSE,
   FOREIGN KEY (username) REFERENCES users(username) ON DELETE CASCADE,
   FOREIGN KEY (homeworkId) REFERENCES homework10d(homeworkid) ON DELETE CASCADE,
   CONSTRAINT unique_user_homework UNIQUE (username, homeworkId) -- ensure a user can only check each homework once
);

```

1. Erstellt Tabelle anmerkungen10d

```markdown
(leer)

```

1. (Verlassen psql Terminal Oberfläche):

```bash
\\q

```

1. Terminal (nur dieses) schließen und neu öffnen

---

## PRODUCTION (Docker Compose)

<aside>
💡

This guide assumes that Ubuntu Server LTS ver. > 20 is installed.

</aside>

# **1. Set Up Server Environment**

Update and Upgrade Packages:

```bash
sudo apt update && sudo apt upgrade -y
```

1. Ensure you have bought a domain name from a domain registrar (Squarespace, namecheap etc.)
2. Ensure git is installed and you have a working terminal text editor (either nano or vi).

```sql
git --version
vi --version
nano --version
```

1. Ensure you have changed the password for the root user for safety reasons.
2. Find out public IP address of server.
3. Update the DNS Configuration on the website of your domain registrar.

## **Configure Domain Name**

**DNS Settings**: Log in to your domain registrar’s control panel and set an A record for www.codylon.de pointing to your Server’s public IP address.

Should look like this:

• **Host/Name**: www

• **Type**: A

• **IP Address**: The public IP of your server

• **TTL**: 3600 seconds (default or 1 hour is fine)

**Dynamic DNS (If Applicable)**: If your IP address is dynamic, consider setting up a Dynamic DNS service to keep your domain pointing to the correct IP.

You can check the propagation of your DNS records using tools like DNS Checker. Search for your domain and verify that it resolves to your public IP.

# **2. Continue Setting Up the Environment**

1. install and activate the firewall (ufw), allow 80 and 443 (HTTP and HTTPS) to pass.
2. Ensure docker, docker-cli and docker compose are installed:

[Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

1. **Clone GitHub repository**

**Navigate to the Desired Directory**: Choose a directory where you want to store your project. (create one with mkdir)

```bash
cd /pathtodirectory 
```

**Clone the Repository**: Use Git to clone the repository:

```bash
git clone https://github.com/Codylon-Studios/TaskMinder.git
```

## Create Docker Secrets

In the root folder of your application, create a new folder named docker_secrets.

Inside, create 8 .txt files:

db_name.txt

db_password.txt

db_user.txt

redis_port.txt

session_secret.txt

class_code.txt

dsb_password.txt

dsb_user.txt

backup_dir.txt

db_container.txt

## Set Up the Proxy (Nginx)

1. **Install Nginx**:
    
    ```bash
    sudo apt install nginx
    ```
    
2. **Create a configuration file** for your domain (replace [codylon.de](http://codylin.de) with your domain name) :
    
    ```bash
    sudo nano /etc/nginx/sites-available/codylon.de
    ```
    
3. **Copy your configuration** into that file (the one that is provided in the root folder of the cloned repo). Make sure to replace [codylon.de](http://codylon.de/) with your domain name in the marked places in the Nginx configuration.

<aside>
💡

If your server is behind a router, e.g. on a Pi behind a router at home, you have to Port Forward 80 and 443 in your router’s settings before continung with the next steps.

</aside>

1. **Set up SSL certificates** using Let's Encrypt, again replacing [codylon.de](http://codylon.de) with your domain name in the command.:
    
    ```bash
    sudo apt install certbot python3-certbot-nginx
    sudo certbot --nginx -d codylon.de
    ```
    
    This will automatically create the SSL certificates at the path specified in your config.
    
2. **Create a symbolic link** to enable the site:

(Replace [codylon.de](http://codylon.de) with your actual domain name)

```bash
sudo ln -s /etc/nginx/sites-available/codylon.de /etc/nginx/sites-enabled/
```

1. **Test the configuration** for syntax errors:
    
    ```bash
    sudo nginx -t
    
    ```
    
2. **Restart Nginx** to apply the changes:
    
    ```bash
    sudo systemctl restart nginx
    
    ```
    
3. **Make sure your application** is running on port 3000 (as specified in `proxy_pass http://localhost:3000`). 

<aside>
💡

Remember to replace "codylon.de" with your actual domain name in both the Nginx configuration and the certbot command if you're using a different domain.

</aside>

Don’t forget to init the tables:

```bash
node ./src/initTable/eventType.js
node ./src/initTable/team.js
```

1. Start the docker Compose

```bash
docker compose up -d --build
```

**8. Test the Deployment**

**Access the Site**: Navigate to https://www.codylon.de in your browser to verify that the site is live and the SSL certificate is functioning correctly.

**Check for Errors**: Monitor the Nginx logs for any issues.

sudo tail -f /var/log/nginx/error.log