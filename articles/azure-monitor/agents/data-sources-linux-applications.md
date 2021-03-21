---
title: Samla in Linux-programmets prestanda i Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller information om hur du konfigurerar Log Analytics agent för Linux för att samla in prestanda räknare för MySQL och Apache HTTP server.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2017
ms.openlocfilehash: d8bcc4b68082fdf0d1f86dc341c3313f9d8788c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102050689"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Samla in prestanda räknare för Linux-program i Azure Monitor 

Den här artikeln innehåller information om hur du konfigurerar [Log Analytics agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux) att samla in prestanda räknare för vissa program i Azure Monitor.  Programmen som ingår i den här artikeln är:  

- [MySQL](#mysql)
- [Apache HTTP-Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Om MySQL-servern eller MariaDB-servern identifieras på datorn när Log Analytics-agenten har installerats, installeras en provider för prestanda övervakning för MySQL-servern automatiskt. Den här providern ansluter till den lokala MySQL/MariaDB-servern för att visa prestanda statistik. MySQL-användarautentiseringsuppgifter måste konfigureras så att providern kan komma åt MySQL-servern.

### <a name="configure-mysql-credentials"></a>Konfigurera MySQL-autentiseringsuppgifter
MySQL OMI-providern kräver en förkonfigurerad MySQL-användare och installerade MySQL-klientcertifikat för att fråga om prestanda-och hälso information från MySQL-instansen.  Autentiseringsuppgifterna lagras i en autentiseringsnyckel som är lagrad på Linux-agenten.  Verifierings filen anger vilken bindnings adress och vilken port som MySQL-instansen lyssnar på och vilka autentiseringsuppgifter som ska användas för att samla in mått.  

Under installationen av Log Analytics agent för Linux kommer MySQL OMI-providern att söka igenom MySQL My. cnf konfigurationsfiler (standard platser) för bind-address och port och delvis ange den MySQL OMI-autentiseringsprocessen.

MySQL-autentiseringsprocessen lagras på `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth` .


### <a name="authentication-file-format"></a>Fil format för autentisering
Följande är formatet för filen MySQL OMI-autentisering

> [Port] = [bind-address], [username], [Base64-kodat lösen ord]  
> (Port) = (bind-Address), (användar namn), (Base64-kodat lösen ord)  
> (Port) = (bind-Address), (användar namn), (Base64-kodat lösen ord)  
> AutoUpdate = [TRUE | FALSE]  

Posterna i filen i autentiseringen beskrivs i följande tabell.

| Egenskap | Beskrivning |
|:--|:--|
| Port | Representerar den aktuella porten som MySQL-instansen lyssnar på. Port 0 anger att egenskaperna som följer används för standard instansen. |
| Bind-Address| Aktuell MySQL-bindning-adress. |
| användarnamn| MySQL-användare som används för att övervaka MySQL-serverinstansen. |
| Base64-kodat lösen ord| Lösen ordet för MySQL Monitoring-användaren kodad i base64. |
| Autoupdater| Anger om du vill söka efter ändringar i filen My. cnf och skriva över MySQL-OMI när MySQL OMI-providern har uppgraderats. |

### <a name="default-instance"></a>Standardinstans
Filen MySQL OMI-autentisering kan definiera en standard instans och port nummer som gör det enklare att hantera flera MySQL-instanser på en Linux-värd.  Standard instansen betecknas av en instans med Port 0. Alla ytterligare instanser ärver egenskaper från standard instansen om de inte anger olika värden. Om t. ex. MySQL-instansen som lyssnar på porten "3308" läggs till, används standard instansens bind-Address, username och base64-kodat lösen ord för att testa och övervaka instansen som lyssnar på 3308. Om instansen på 3308 är bunden till en annan adress och använder samma MySQL-användarnamn och lösen ord, så behövs bara bindnings adressen och de andra egenskaperna kommer att ärvas.

Följande tabell innehåller exempel på instans inställningar 

| Beskrivning | Fil |
|:--|:--|
| Standard instans och instans med port 3308. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standard instans och instans med port 3308 och annat användar namn och lösen ord. | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>Fil program för MySQL OMI-autentisering
Ingår i installationen av MySQL OMI-providern är ett fil program för MySQL-OMI som kan användas för att redigera MySQL OMI-autentiseringsprocessen. Du hittar fil programmet för autentisering på följande plats.

`/opt/microsoft/mysql-cimprov/bin/mycimprovauth`

> [!NOTE]
> Filen med autentiseringsuppgifter måste vara läsbar av omsagent-kontot. Att köra mycimprovauth-kommandot som omsgent rekommenderas.

Följande tabell innehåller information om syntaxen för att använda mycimprovauth.

| Åtgärd | Exempel | Beskrivning
|:--|:--|:--|
| uppdatera *Falskt eller sant* | mycimprovauth AutoUpdate false | Anger om autentiseringsprocessen ska uppdateras automatiskt vid omstart eller uppdatering. |
| standard *lösen ord för bind-Address username* | mycimprovauth standard 127.0.0.1 rot-PWD | Ställer in standard instansen i OMI-autentiseringsprocessen för MySQL.<br>Fältet lösen ord ska anges i oformaterad text – lösen ordet i MySQL-OMI är 64 Base64-kodat. |
| ta bort *standard eller port_num* | mycimprovauth 3308 | Tar bort den angivna instansen antingen via standard eller port nummer. |
| Hjälp | mycimprov-hjälp | Visar en lista med kommandon som ska användas. |
| skriva ut | mycimprov utskrift | Skriver ut en enkel att läsa MySQL OMI-autentiseringsprocessen. |
| uppdatera port_num *BIND-Address username Password* | mycimprov uppdatering 3307 127.0.0.1 rot PWD | Uppdaterar den angivna instansen eller lägger till instansen om den inte finns. |

Följande exempel kommandon definierar ett standard användar konto för MySQL-servern på localhost.  Fältet lösen ord ska anges i klartext – lösen ordet i MySQL-OMI-autentiseringsprocessen kommer att vara bas 64-kodat

```console
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
sudo /opt/omi/bin/service_control restart
```

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Databas behörigheter som krävs för resurs prestanda räknare för MySQL
MySQL-användaren måste ha åtkomst till följande frågor för att samla in prestanda data för MySQL-servern. 

```sql
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

MySQL-användaren måste också välja åtkomst till följande standard tabeller.

- information_schema
- MySQL. 

De här behörigheterna kan beviljas genom att köra följande Grant-kommandon.

```sql
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

> [!NOTE]
> För att bevilja behörighet till en MySQL-övervakning måste användaren ha behörigheten "beviljande alternativ" och behörigheten beviljad.

### <a name="define-performance-counters"></a>Definiera prestanda räknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor måste du konfigurera de prestanda räknare som ska samlas in.  Använd proceduren i [prestanda data källor i Windows och Linux i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objekt namn | Räknarens namn |
|:--|:--|
| MySQL-databas | Disk utrymme i byte |
| MySQL-databas | Tables |
| MySQL-Server | Avbruten anslutning PCT |
| MySQL-Server | Anslutnings användning PCT |
| MySQL-Server | Användning av disk utrymme i byte |
| MySQL-Server | Fullständig tabells ökning PCT |
| MySQL-Server | InnoDB buffer-pool, träff |
| MySQL-Server | InnoDB buffer-pool Använd PCT |
| MySQL-Server | InnoDB buffer-pool Använd PCT |
| MySQL-Server | Key cache träff PCT |
| MySQL-Server | Nyckel-cache Använd PCT |
| MySQL-Server | Skriv PCT för nyckel-cache |
| MySQL-Server | Fråga cache träff PCT |
| MySQL-Server | Rensning av frågekörning i cacheminnet |
| MySQL-Server | Fråga cache Använd PCT |
| MySQL-Server | Table cache träff PCT |
| MySQL-Server | Table cache Använd PCT |
| MySQL-Server | Tabell lås konkurrens proc |

## <a name="apache-http-server"></a>Apache HTTP-Server 
Om Apache HTTP server identifieras på datorn när omsagent-paketet installeras, installeras en provider för prestanda övervakning för Apache HTTP Server automatiskt. Den här providern förlitar sig på en Apache-modul som måste läsas in i Apache HTTP-server för att få åtkomst till prestanda data. Modulen kan läsas in med följande kommando:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Om du vill inaktivera Apache Monitoring-modulen kör du följande kommando:

```console
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definiera prestanda räknare

När du har konfigurerat Log Analytics-agenten för Linux för att skicka data till Azure Monitor måste du konfigurera de prestanda räknare som ska samlas in.  Använd proceduren i [prestanda data källor i Windows och Linux i Azure Monitor](data-sources-performance-counters.md) med räknarna i följande tabell.

| Objekt namn | Räknarens namn |
|:--|:--|
| Apache HTTP-Server | Upptagna arbetare |
| Apache HTTP-Server | Inaktiva arbetare |
| Apache HTTP-Server | PCT-upptagna arbetare |
| Apache HTTP-Server | Totalt PCT-CPU |
| Apache virtuell värd | Fel per minut-klient |
| Apache virtuell värd | Fel per minut-Server |
| Apache virtuell värd | KB per begäran |
| Apache virtuell värd | Begär anden i KB per sekund |
| Apache virtuell värd | Begär Anden per sekund |



## <a name="next-steps"></a>Nästa steg
* [Samla in prestanda räknare](data-sources-performance-counters.md) från Linux-agenter.
* Lär dig mer om [logg frågor](../logs/log-query-overview.md) för att analysera data som samlas in från data källor och lösningar.