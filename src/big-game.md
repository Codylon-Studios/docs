# Big Game

last updated: 18th jan 2025

## Github Codespaces

> ⚠️ **Warning:** FÜR CODESPACES (EXPLIZIT DAFÜR/NICHT FÜR ANDERE OS)

1. Codespaces starten, Erweiterung git graph installieren
2. In Kommandozeile ausführen: (Installiert alle dependencies)

```bash
sudo npm install
```

3. (PostgreSQL (Datenbank-Management) installieren):

```bash
sudo apt-get update
```

```bash
sudo apt-get -y install postgresql
```

4. (Start von postgresql server):

```bash
sudo service postgresql start
```

5. (Einloggen in Terminal vom Server):

```bash
sudo su postgres
```

6. (Anmelden als postgres user (Standard-user)):

```bash
psql postgres
```

7. (Passwort ändern und merken!):

```bash
\password
```

8. (Erstellt eine Datenbank namens accounts):

```bash
CREATE DATABASE accounts;
```

9. (Listet alle Datenbanken auf):

```bash
\l
```

10. (Verbindet sich mit Datenbank accounts):

```bash
\c accounts
```

11. (Erstellt Tabelle accounts):

```sql
CREATE TABLE accounts (
id SERIAL PRIMARY KEY,
username VARCHAR(255) UNIQUE NOT NULL,
password VARCHAR(255) NOT NULL
);
```

12. (erstellt Tabelle chessgames):

```sql
CREATE TABLE chessgames (
    game_id SERIAL PRIMARY KEY,                 
    player_white_id INT NOT NULL,                
    player_black_id INT NOT NULL,                
    start_time TIMESTAMP NOT NULL,          
    end_time TIMESTAMP,                          
    status ENUM('ongoing', 'white_won', 'black_won', 'draw') NOT NULL,                          
    moves TEXT,                                  
    is_rated BOOLEAN DEFAULT FALSE,              
    time_control VARCHAR(20),                    
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP, 
    FOREIGN KEY (player_white_id) REFERENCES accounts(id) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (player_black_id) REFERENCES accounts(id) ON DELETE CASCADE ON UPDATE CASCADE
);
```

13. (Verlassen psql Terminal Oberfläche, danch Terminal schließen und neu öffnen):

```bash
\q
```

14. (Für datenbankverbindung noch config_db.json anlegen!):

your_password beim password parameter soll dabei den Wert tragen, der in Schritt 7 festgelegt wurde.

db_config.json:

```json
{
"host": "localhost",
"port": 5432,
"database": "accounts",
"user": "postgres",
"password": "your_password",
}
```

15. Start von Server:

```jsx
nodemon server.js
```

16. Besuchen der Website:

```jsx
localhost:3000
```

17. Jetzt kann man programmieren! Veränderungen im Code werden automatisch erkannt und der Server wird daraufhin neu gestartet.
18. Ausführen von Git-Befehlen (wenn nötig)
19. Stoppen des Servers (windows, mac, linux)

```markdown
control+c
```

20. Stoppen der Postgres Datenbank:

```bash
sudo service postgresql stop
```

Bei allen weiteren Malen können die Schritte 1 bis inklusive 15 übersprungen werden, nur die Schritte 16-20 werden für den normalen workflow benötigt. Jedoch muss man vor den 16. Schritt noch

```bash
sudo service postgresql start
```

ausführen, denn dies startet den postgres server (datenbank).

---

## Error/Troubleshooting

1️⃣: 

> Error: **listen** EADDREINUSE: address already in **use** :::3000
> 

(zeigt alle offenen Anwendungen für port 3000 an):

```markdown
sudo lsof -i :3000
```

Dies listet alle offenen Anwendungen für port 3000 an, jede enthält eine PID, nun kann man jede ungenutzte Anwendungen durch ihre PID beenden:

(beendet die PID):

```markdown
kill -9 <PID> 
```

2️⃣:

> `error: Error: Cannot find module 'bcrypt'`
> 

(Wenn bycrpt Fehler gibt):

```markdown
npm uninstall bcrypt
```

```markdown
npm i bcrypt
```

3: when running psql postgres

> 
> 
> 
> psql: error: connection to server on socket "/tmp/.s.PGSQL.5432" failed: No such file or directory
> Is the server running locally and accepting connections on that socket?
> 

Auf mac: (gibt das Ende der log-file aus:)

```bash
tail -n 10 /opt/homebrew/var/log/postgres.log
```

bzw: 

```bash
tail -n 10 /opt/homebrew/var/log/postgresql@{version}.log
```

If error = 

data directory "/opt/homebrew/var/postgresql@14" has invalid permissions

Permissions should be u=rwx (0700) or u=rwx,g=rx (0750).

⇒

```bash
sudo chmod -R 0700 /opt/homebrew/var/postgresql@14
```

bzw.:

```bash
sudo chmod -R 0700 /opt/homebrew/var/postgres
```

---

## Git

Under this section, some Git merging strategies will be introduced. Always ask other People before merging branches.

**Rebase-Merge (Preferred)**

Rewrites the feature branch history to make it appear as if it was developed on top of the current develop branch. This creates a linear history for the feature branch commits and a merge commit when the feature branch is finally merged.

```markdown
git checkout develop
git pull
git checkout <branch>
git rebase develop
git push --force
git checkout develop
git merge --no-ff <branch>
git push
git branch -d <branch>
```

> You can call `git config merge.ff false` to change config. Then you can call `git merge <branch>`instead of `git merge --no-ff <branch>` for future uses.
> 

**Merge (Alternative)**

Retains the original commit history and creates a merge commit. The history will show exactly where the branches diverged and merged.

```markdown
git checkout develop
git pull
git merge <branch>
git push
```