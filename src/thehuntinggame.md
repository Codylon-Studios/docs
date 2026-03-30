# TheHuntingGame (deprecated)

last updated: 19th march 2025

> ⚠️ **Warning:** Documentation deprecated!!!

<aside>
1. Selfhost Server: See the docker install Guide for server deployment.
</aside>

## Server-Setup

<aside>
💡 It is not recommended to use the docker setup for backend or frontend development. 
You can follow the docker guide if you are going to deploy to a server.
Setup for google is mandatory in both cases.

</aside>

**Important:**

Change the DB_HOST value to postgres if you are switching from manual development to docker. DB_HOST value should be [localhost](http://localhost) for manual development.

### Manual development (non Docker)

#### GitHub Codespaces

<aside>
💡

Useful for backend-only development; If development involves Flutter frontend go to the OS specific setups.

</aside>

Install extension git graph

Install npm packages:

```bash
sudo npm install
(sudo npm i -g)
```

Install postgresql, log in, change password and create database thehuntinggame:

```bash
sudo apt-get update
sudo apt-get -y install postgresql
sudo service postgresql start
sudo su postgres
```

```bash
psql postgres
```

Change password:

```bash
\password
```

Create Database:

```bash
CREATE DATABASE thehuntinggame;
```

##### General Commands:

Start PostgreSQL Server:

```bash
sudo service postgresql start
```

Stop PostgreSQL Server:

```bash
sudo service postgresql stop
```

Start Redis Server:

```bash
sudo service redis-server start
```

Stop Redis Server:

```bash
sudo service redis-server stop
```

Start nodemon Server:

```bash
ctrl+c
```

#### MacOS

Install homebrew, 

[Homebrew](https://brew.sh/)

Open Code in VS Code

(install git graph extension)

Open Terminal (cmd+space, search for ‘Terminal’)

install postgresql, redis:

```bash
brew install postgresql@14
brew install redis
```

Start redis:

```bash
redis-server
```

Start Postgresql server (from now on it will auto start at login):

```bash
brew services start postgresql
```

go into postgres:

```bash
psql postgres
```

change password:

```bash
\password
```

If you changed the password to other than “postgres”, you have to update the .env and the docker-compose.yml file too:

```bash
DB_USER=postgres
DB_PASSWORD=postgres //Change this
DB_NAME=thehuntinggame
DB_HOST=localhost
JWT_SECRET=ekjbfjw
REFRESH_TOKEN_SECRET=melwmflw
NODE_ENV=DEVELOPMENT
REDIS_HOST=redis
REDIS_PORT=6379
EMAIL_USER=(google email)
CLIENT_ID=(google client id)
CLIENT_SECRET=(google client secret)
REFRESH_TOKEN=(google refresh token)
```

```bash
services:
  app:
    build: .
    container_name: thehuntinggamebackend
    ports:
      - "3000:3000"
    environment:
      - PORT=3000
      - DB_HOST=postgres
      - DB_PORT=5432
      - DB_USER=postgres
      - DB_PASSWORD=postgres #CHANGE
      - DB_NAME=thehuntinggame
      - REDIS_HOST=redis
      - REDIS_PORT=6379
    depends_on:
      - postgres
      - redis
    volumes:
      - .:/app
      - /app/node_modules 

  postgres:
    image: postgres:14
    container_name: postgres
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres #CHANGE
      POSTGRES_DB: thehuntinggame
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data

  redis:
    image: redis:6
    container_name: redis
    ports:
      - "6379:6379" 
    volumes:
      - redis-data:/data

volumes:
  postgres-data:
  redis-data:
```

Continue creating the database:

```bash
CREATE DATABASE thehuntinggame;
```

Exit posgresql using:

```bash
\q
```

##### General Commands:

Start the Dev Server using:

```bash
nodemon server.js
```

Stop the Server using:

```bash
ctrl+c
```

stop redis:

```bash
ctrl+c
//or
redis-cli shutdown
```

start redis:

```bash
redis-server
```

## Setup email verification sending

<aside>
💡

Create a google account an make sure 2FA is activated and setup.

Secure your account with a strong password and a passkey or similar.

</aside>

---

### **✅ Step 1: Make Sure OAuth Consent Screen is Set Up**

1. Go to [Google Cloud Console](https://console.cloud.google.com/).
2. Navigate to **APIs & Services** → **OAuth consent screen**.
3. If you haven’t set it up, **set the User Type to "External"** (required for non-Google Workspace accounts).
4. Fill in required fields:
    - **App name** (e.g., "TheHuntingGame").
    - **User support email**.
    - **Developer contact email**.
5. **Scopes**: Add `https://mail.google.com/` (if missing).
6. **Test Users**: If in "Testing" mode, **add your email** as a test user.
7. Click **Save and Continue**.

---

### **✅ Step 2: Enable Gmail API**

1. Go to [Gmail API](https://console.cloud.google.com/apis/library/gmail.googleapis.com).
2. If **not enabled**, click **Enable**.

---

### **✅ Step 3: Verify OAuth Client ID and Secret**

1. Go to **APIs & Services** → **Credentials**.
2. Under **OAuth 2.0 Client IDs**, create a new ClientID, make sure that:
    - **Application Type** is Web (as you are developing/deploying to a web server).
    - Redirect URI:
    
    https://developers.google.com/oauthplayground
    
    - **Client ID & Client Secret** match your `.env` file.

---

### **✅ Step 4: Use OAuth Playground to Get Refresh Token**

If you didn’t generate the **refresh token** correctly, redo these steps:

1. Open [OAuth 2.0 Playground](https://developers.google.com/oauthplayground).
2. Click **"OAuth 2.0 Configuration"** (gear icon).
3. Check **"Use your own OAuth credentials"**, enter:
    - **Client ID** (from Google Cloud).
    - **Client Secret** (from Google Cloud).
4. In **Step 1**, add this scope:
    
    ```
    <https://mail.google.com/>
    
    ```
    
5. Click **"Authorize APIs"** → Sign in with your Gmail account.
6. In **Step 2**, click **"Exchange authorization code for tokens"**.
7. Copy the **Refresh Token** (not the access token) and update your `.env` file:
    
    ```
    REFRESH_TOKEN=your-refresh-token
    ```
    

---

1. Add your email as USER_EMAIL  to the .env file

Template .env:

```bash
DB_USER=postgres
DB_PASSWORD=postgres //Whatever your password is
DB_NAME=thehuntinggame
DB_HOST=localhost
JWT_SECRET=ekjbfjw
REFRESH_TOKEN_SECRET=melwmflw
NODE_ENV=DEVELOPMENT
REDIS_HOST=redis
REDIS_PORT=6379
EMAIL_USER=(google email)
CLIENT_ID=(google client id)
CLIENT_SECRET=(google client secret)
REFRESH_TOKEN=(google refresh token)
```
