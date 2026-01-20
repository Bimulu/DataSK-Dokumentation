# DataSK - Dansk Dokumentation

## 📋 Komplet Syntaks Reference på Dansk

---

## 🔌 **Database Forbindelse**

### Opret Forbindelse til Database
```skript
connect to [mysql] database with id %string% (at|on) host %string%[,] port %number%[,] database %string%[,] user[name] %string%[,] password %string%
```
**Eksempel:**
```skript
connect to mysql database with id "main" at host "localhost", port 3306, database "minecraft", username "root", password "kodeord123"
```

### Luk Database Forbindelse
```skript
close [database] [connection] %string%
close all [database] [connections]
```
**Eksempler:**
```skript
close database connection "main"
close all database connections
```

### Tjek om Database er Forbundet (Betingelse)
```skript
[database] %string% is connected
[database] %string% is(n't| not) connected
```
**Eksempel:**
```skript
if database "main" is connected:
    send "Forbundet!"
else:
    send "Ikke forbundet!"
```

---

## 🔍 **SQL Forespørgsler**

### Udfør SQL Forespørgsel
```skript
execute [sql] query %string% (in|on) [database] %string% [and store [result] in %-objects%]
execute [sql] update %string% (in|on) [database] %string%
```
**Eksempler:**
```skript
# Opret tabel
execute sql update "CREATE TABLE spillere (uuid VARCHAR(36), score INT)" in database "main"

# Indsæt data
execute sql update "INSERT INTO spillere VALUES ('uuid123', 100)" in database "main"

# Hent data med resultat
execute query "SELECT * FROM spillere" in database "main" and store result in {_resultater::*}
```

### Hent SQL Værdi (Første Resultat)
```skript
[the] [sql] value [of] %string% (from|in) [database] %string%
[sql] value %string% (from|in) [database] %string%
```
**Eksempel:**
```skript
set {_score} to sql value "SELECT score FROM spillere WHERE uuid='%player's uuid%'" from database "main"
send "Din score: %{_score}%"
```

### Hent SQL Kolonne
```skript
[sql] (column|value) %string% (from|of|in) [table] %string% (where|for) %string% (is|=) %string% (in|on) [database] %string%
[sql] [all] columns (from|of|in) [table] %string% (where|for) %string% (is|=) %string% (in|on) [database] %string%
```
**Eksempler:**
```skript
# Hent enkelt kolonne
set {_navn} to sql column "name" from table "spillere" where "uuid" is "%player's uuid%" in database "main"

# Hent alle kolonner
set {_data::*} to sql all columns from table "spillere" where "uuid" is "%player's uuid%" in database "main"
```

---

## 💾 **Variabel Lagring (NYT!)**

### Database Variabel (PropertyExpression) ⭐ DEN NEMME MÅDE!
```skript
[the] [database] var[iable] %string% (in|from) [database] %string%
[database] var[iable] %string% (in|from) [database] %string%
```

**Gem værdi:**
```skript
set database variable "score-%player's uuid%" in database "main" to 100
add 50 to database variable "score-%player's uuid%" in database "main"
```

**Hent værdi:**
```skript
set {_score} to database variable "score-%player's uuid%" in database "main"
send "Score: %database variable "score-%player's uuid%" from database "main"%"
```

**Slet værdi:**
```skript
delete database variable "score-%player's uuid%" in database "main"
```

### Gem Skript Variabel til Database
```skript
save [the] [skript] var[iable] %~objects% to [database] %string%
store [the] [skript] var[iable] %~objects% to [database] %string%
```
**Eksempler:**
```skript
# Gem enkelt variabel
save variable {spiller-score} to database "main"
save variable {kills::%player's uuid%} to database "main"

# Gem liste variabel
save variable {hjem::*} to database "main"
save variable {items::%player's uuid%::*} to database "main"
```

### Indlæs Skript Variabel fra Database
```skript
load [the] [skript] var[iable] %~objects% from [database] %string%
```
**Eksempler:**
```skript
# Indlæs enkelt variabel
load variable {spiller-score} from database "main"
load variable {kills::%player's uuid%} from database "main"

# Indlæs liste variabel
load variable {hjem::*} from database "main"
load variable {items::%player's uuid%::*} from database "main"
```

### Slet Skript Variabel fra Database
```skript
delete [the] [skript] var[iable] %~objects% from [database] %string%
remove [the] [skript] var[iable] %~objects% from [database] %string%
```
**Eksempler:**
```skript
delete variable {spiller-score} from database "main"
remove variable {kills::%player's uuid%} from database "main"
```

### Tjek om Variabel Findes i Database (Betingelse)
```skript
[the] [skript] var[iable] %~objects% (exists|is stored) in [database] %string%
[the] [skript] var[iable] %~objects% (doesn't|does(n't| not)|is(n't| not)) exist in [database] %string%
```
**Eksempler:**
```skript
if variable {spiller-score} exists in database "main":
    send "Fundet!"

if variable {kills::%player's uuid%} doesn't exist in database "main":
    send "Ikke fundet!"
```

---

## 📊 **Komplette Eksempler**

### 1. Grundlæggende Opsætning
```skript
on script load:
    # Opret forbindelse til MySQL
    connect to mysql database with id "main" at host "localhost", port 3306, database "minecraft", username "root", password "kodeord"
    
    # Tjek forbindelse
    if database "main" is connected:
        broadcast "&aDatabasen er forbundet!"
    else:
        broadcast "&cKunne ikke forbinde til databasen!"

on script unload:
    # Luk forbindelser når serveren lukker
    close all database connections
    broadcast "&7Database forbindelser lukket."
```

### 2. Simpel Score System
```skript
command /sætscore <number>:
    trigger:
        # Gem score til database
        set {score::%player's uuid%} to arg-1
        save variable {score::%player's uuid%} to database "main"
        send "&aDin score er nu %arg-1%!"

command /hentscore:
    trigger:
        # Indlæs score fra database
        load variable {score::%player's uuid%} from database "main"
        
        if {score::%player's uuid%} is set:
            send "&aDin score: %{score::%player's uuid%}%"
        else:
            send "&cDu har ingen score endnu!"
```

### 3. PropertyExpression (Den Nemmere Måde!)
```skript
command /penge <number>:
    trigger:
        # Sæt penge direkte uden separat gem-kommando!
        set database variable "penge-%player's uuid%" in database "main" to arg-1
        send "&aDine penge er nu %arg-1%kr!"

command /tjekpenge:
    trigger:
        # Hent penge direkte
        set {_penge} to database variable "penge-%player's uuid%" in database "main"
        
        if {_penge} is set:
            send "&aDu har %{_penge}%kr"
        else:
            send "&cDu har ingen penge endnu!"
```

### 4. Auto-Indlæs ved Join, Auto-Gem ved Quit
```skript
on join:
    # Indlæs spillerens point når de joiner
    load variable {point::%player's uuid%} from database "main"
    
    # Hvis ny spiller, sæt standard
    if {point::%player's uuid%} is not set:
        set {point::%player's uuid%} to 0
    
    send "&eVelkommen! Du har %{point::%player's uuid%}% point."

on quit:
    # Gem spillerens point når de forlader
    save variable {point::%player's uuid%} to database "main"

command /tilføjpoint:
    trigger:
        add 1 to {point::%player's uuid%}
        send "&aDu har nu %{point::%player's uuid%}% point!"
```

### 5. Gem Lister
```skript
command /tilføjitem <text>:
    trigger:
        # Tilføj item til spillerens liste
        if {items::%player's uuid%::*} is not set:
            set {_index} to 1
        else:
            set {_index} to size of {items::%player's uuid%::*} + 1
        
        set {items::%player's uuid%::%{_index}%} to arg-1
        
        # Gem listen
        save variable {items::%player's uuid%::*} to database "main"
        send "&aTilføjede '%arg-1%' til dine items!"

command /mitems:
    trigger:
        # Indlæs listen
        load variable {items::%player's uuid%::*} from database "main"
        
        if {items::%player's uuid%::*} is set:
            send "&6Dine Items:"
            loop {items::%player's uuid%::*}:
                send "&e- %loop-value%"
        else:
            send "&cDu har ingen items!"
```

### 6. Økonomi System
```skript
on join:
    load variable {saldo::%player's uuid%} from database "main"
    if {saldo::%player's uuid%} is not set:
        set {saldo::%player's uuid%} to 100

on quit:
    save variable {saldo::%player's uuid%} to database "main"

command /saldo:
    aliases: /penge, /bal
    trigger:
        send "&aDin saldo: &e%{saldo::%player's uuid%}%kr"

command /betal <offline player> <number>:
    trigger:
        if arg-2 <= 0:
            send "&cBeløbet skal være positivt!"
            stop
        
        if arg-1 is player:
            send "&cDu kan ikke betale dig selv!"
            stop
        
        if {saldo::%player's uuid%} < arg-2:
            send "&cDu har ikke nok penge!"
            stop
        
        # Indlæs modtagers saldo
        load variable {saldo::%arg-1's uuid%} from database "main"
        if {saldo::%arg-1's uuid%} is not set:
            set {saldo::%arg-1's uuid%} to 0
        
        # Overfør penge
        remove arg-2 from {saldo::%player's uuid%}
        add arg-2 to {saldo::%arg-1's uuid%}
        
        # Gem begge saldi
        save variable {saldo::%player's uuid%} to database "main"
        save variable {saldo::%arg-1's uuid%} to database "main"
        
        send "&aDu sendte %arg-2%kr til %arg-1%!"
        if arg-1 is online:
            send "&aDu modtog %arg-2%kr fra %player%!" to arg-1
```

### 7. Hjem System (Gem Koordinater Separat)
```skript
command /sæthjem [<text>]:
    trigger:
        if arg-1 is not set:
            set {_navn} to "hjem"
        else:
            set {_navn} to arg-1
        
        # Gem koordinater separat (bedre end Location objekt!)
        set database variable "hjem-%player's uuid%-%{_navn}%-verden" in database "main" to "%player's world%"
        set database variable "hjem-%player's uuid%-%{_navn}%-x" in database "main" to player's x-coordinate
        set database variable "hjem-%player's uuid%-%{_navn}%-y" in database "main" to player's y-coordinate
        set database variable "hjem-%player's uuid%-%{_navn}%-z" in database "main" to player's z-coordinate
        
        send "&aHjem '%{_navn}%' sat på din position!"

command /hjem [<text>]:
    trigger:
        if arg-1 is not set:
            set {_navn} to "hjem"
        else:
            set {_navn} to arg-1
        
        # Indlæs koordinater
        set {_verden} to database variable "hjem-%player's uuid%-%{_navn}%-verden" in database "main"
        set {_x} to database variable "hjem-%player's uuid%-%{_navn}%-x" in database "main"
        set {_y} to database variable "hjem-%player's uuid%-%{_navn}%-y" in database "main"
        set {_z} to database variable "hjem-%player's uuid%-%{_navn}%-z" in database "main"
        
        if {_verden} is not set:
            send "&cHjem '%{_navn}%' ikke fundet!"
            stop
        
        # Opret lokation og teleporter
        set {_lok} to location at {_x}, {_y}, {_z} in world {_verden}
        teleport player to {_lok}
        send "&aTeleporteret til hjem '%{_navn}%'!"

command /slethjem [<text>]:
    trigger:
        if arg-1 is not set:
            set {_navn} to "hjem"
        else:
            set {_navn} to arg-1
        
        # Slet alle koordinater
        delete database variable "hjem-%player's uuid%-%{_navn}%-verden" in database "main"
        delete database variable "hjem-%player's uuid%-%{_navn}%-x" in database "main"
        delete database variable "hjem-%player's uuid%-%{_navn}%-y" in database "main"
        delete database variable "hjem-%player's uuid%-%{_navn}%-z" in database "main"
        
        send "&aHjem '%{_navn}%' slettet!"
```

### 8. Statistik System
```skript
on death of player:
    # Forøg dødsfald
    add 1 to {deaths::%victim's uuid%}
    save variable {deaths::%victim's uuid%} to database "main"

on death:
    if attacker is a player:
        # Forøg drab
        add 1 to {kills::%attacker's uuid%}
        add 10 to {score::%attacker's uuid%}
        
        # Gem til database
        save variable {kills::%attacker's uuid%} to database "main"
        save variable {score::%attacker's uuid%} to database "main"
        
        send "&aDrab! +10 score. Total: %{score::%attacker's uuid%}%" to attacker

command /stats [<offline player>]:
    trigger:
        if arg-1 is not set:
            set {_target} to player
        else:
            set {_target} to arg-1
            # Indlæs targets data
            load variable {score::%{_target}'s uuid%} from database "main"
            load variable {kills::%{_target}'s uuid%} from database "main"
            load variable {deaths::%{_target}'s uuid%} from database "main"
        
        send "&6=== %{_target}%'s Statistikker ==="
        send "&eScore: &a%{score::%{_target}'s uuid% ? 0}%"
        send "&eDrab: &a%{kills::%{_target}'s uuid% ? 0}%"
        send "&eDødsfald: &c%{deaths::%{_target}'s uuid% ? 0}%"
```

### 9. Auto-Gem System
```skript
every 5 minutes:
    set {_antal} to 0
    loop all players:
        save variable {saldo::%loop-player's uuid%} to database "main"
        save variable {score::%loop-player's uuid%} to database "main"
        save variable {kills::%loop-player's uuid%} to database "main"
        add 1 to {_antal}
    
    if {_antal} > 0:
        broadcast "&7[Auto-Gem] Gemte data for %{_antal}% spiller(e)!"
```

### 10. Globale Server Variabler
```skript
command /sætbesked <text>:
    permission: admin
    trigger:
        set database variable "server-besked" in database "main" to arg-1
        broadcast "&6[Server] Besked opdateret!"

command /besked:
    trigger:
        set {_besked} to database variable "server-besked" in database "main"
        if {_besked} is set:
            send "&6=== Dagens Besked ==="
            send "&e%{_besked}%"
        else:
            send "&cIngen besked sat!"

# Vis besked ved join
on join:
    wait 2 seconds
    set {_besked} to database variable "server-besked" in database "main"
    if {_besked} is set:
        send "&6=== Dagens Besked ===" to player
        send "&e%{_besked}%" to player
```

---

## 🎯 **Understøttede Variabeltyper**

| Type | Support | Anbefaling |
|------|---------|------------|
| Tekst (String) | ✅ Perfekt | ✅ Brug gerne |
| Tal (Number) | ✅ Perfekt | ✅ Brug gerne |
| Boolean | ✅ Perfekt | ✅ Brug gerne |
| Lister | ✅ God | ✅ Brug gerne |
| ItemStack | ⚠️ Virker | ⚠️ Kun kortvarigt |
| Location | ⚠️ Virker | ⚠️ Gem koordinater separat |
| Spiller | ❌ Nej | ❌ Brug UUID i stedet |

---

## 💡 **Bedste Praksis**

### ✅ GØR DETTE:
```skript
# Gem simple datatyper
save variable {score} to database "main"

# Brug UUID for spillerdata
set {uuid} to "%player's uuid%"
save variable {uuid} to database "main"

# Gem lister
set {scores::1} to 100
set {scores::2} to 200
save variable {scores::*} to database "main"

# Brug PropertyExpression for simple værdier
set database variable "score" in database "main" to 100
```

### ❌ UNDGÅ DETTE:
```skript
# Gem ikke komplekse objekter langsigtet
save variable {spiller-objekt} to database "main"

# Gem ikke datoer direkte
save variable {nuværende-tid} to database "main"

# Gem ikke spillernavne (brug UUID)
save variable {navn} to database "main"
```

---

## 🔧 **Almindelige Fejl**

### Fejl: "Can't understand this condition/effect"
**Løsning:**
1. Tjek at DataSK er installeret
2. Genstart serveren efter installation
3. Sørg for at Skript er installeret
4. Kontroller syntaksen er korrekt

### Fejl: "Database not connected"
**Løsning:**
1. Tjek MySQL er startet: `systemctl status mysql`
2. Verificer login oplysninger
3. Tjek firewall: `sudo ufw allow 3306`
4. Test forbindelse: `mysql -u root -p`

### Fejl: Variabler gemmes ikke
**Løsning:**
1. Tjek console for SQL fejl
2. Verificer MySQL bruger har skriverettigheder
3. Tjek diskplads

### Fejl: Liste variabler virker ikke
**Løsning:**
Husk stjernen: `save variable {liste::*}`

---

## 📝 **Hurtig Reference**

```skript
# === FORBINDELSE ===
connect to mysql database with id "main" at host "localhost", port 3306, database "minecraft", username "root", password "kode"

# === VARIABLER ===
save variable {var} to database "main"          # Gem
load variable {var} from database "main"        # Indlæs
delete variable {var} from database "main"      # Slet
if variable {var} exists in database "main"     # Tjek

# === PROPERTY EXPRESSION (NEMMERE) ===
set database variable "key" in database "main" to 100   # Gem
set {_val} to database variable "key" in database "main" # Hent
delete database variable "key" in database "main"       # Slet

# === SQL ===
execute sql update "CREATE TABLE..." in database "main"
set {_val} to sql value "SELECT..." from database "main"
```

---

## 🎓 **Læringssti**

1. **Start her:** `simple-beginner-example.sk` - Lær det grundlæggende
2. **Derefter:** `complete-example.sk` - Se et fuldt system
3. **Avanceret:** `sql-query-examples.sk` - SQL forespørgsler
4. **Byg selv:** Lav dit eget system!

---

## ⚠️ **Vigtige Tips**

- 🔑 Brug **altid UUID** for spillerdata: `{data::%player's uuid%}`
- 💾 Gem **ofte** (hver 5-10 minutter)
- 📥 Indlæs data når spillere **joiner**
- 📤 Gem data når spillere **forlader**
- 🔒 Luk forbindelser når **serveren lukker**
- 💿 Tag **backup** af din database regelmæssigt

---

## 🆘 **Få Hjælp**

Hvis du har problemer:

1. **Tjek MySQL kører**
   ```bash
   systemctl status mysql
   ```

2. **Test forbindelse**
   ```bash
   mysql -u root -p
   ```

3. **Tjek plugin er installeret**
   ```
   /plugins
   ```

4. **Se console for fejl**
   Se i `logs/latest.log`

5. **Test simpelt script først**
   Start med det simple eksempel før du laver komplekse systemer

---

## 📚 **Yderligere Ressourcer**

- 📖 **README.md** - Engelsk dokumentation
- 📋 **SYNTAX_REFERENCE.md** - Fuld syntaks reference
- 🔤 **VARIABLE_TYPES.md** - Variabeltype guide
- 💻 **examples/** - Eksempel scripts

---

## 🎉 **Held og Lykke!**

Du har nu alt hvad du behøver for at bygge avancerede database-drevne systemer i Skript!

Start småt, test ofte, og byg derfra. God fornøjelse! 🚀
