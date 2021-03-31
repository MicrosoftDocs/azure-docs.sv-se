---
title: Konfigurera Azure SQL Edge
description: Läs mer om hur du konfigurerar Azure SQL Edge.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392341"
---
# <a name="configure-azure-sql-edge"></a>Konfigurera Azure SQL Edge

Azure SQL Edge stöder konfiguration via något av följande två alternativ:

- Miljövariabler
- En MSSQL. conf-fil placerad i mappen/var/opt/MSSQL

> [!NOTE]
> Att ställa in miljövariabler åsidosätter inställningarna som anges i filen MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Konfigurera med hjälp av miljövariabler

Azure SQL Edge visar flera olika miljövariabler som kan användas för att konfigurera SQL Edge-behållaren. Dessa miljövariabler är en delmängd av de som är tillgängliga för SQL Server på Linux. Mer information om SQL Server på Linux miljövariabler finns i [miljövariabler](/sql/linux/sql-server-linux-configure-environment-variables/).

Följande nya miljövariabler har lagts till i Azure SQL Edge. 

| Miljövariabel | Beskrivning | Värden |     
|-----|-----| ---------- | 
| **PlanId** | Anger den Azure SQL Edge-SKU som ska användas under initieringen. Den här miljö variabeln krävs bara när du distribuerar Azure SQL Edge med Azure IoT Edge. | **asde-Developer-on-IoT-Edge** eller **asde-Premium-on-IoT-Edge** | 
| **MSSQL_TELEMETRY_ENABLED** | Aktivera eller inaktivera data insamling för användning och diagnostik. | TRUE eller FALSE |  
| **MSSQL_TELEMETRY_DIR** | Anger mål katalogen för gransknings filerna för användnings-och diagnostik-data insamling. | Mapplats i SQL Edge-behållare. Den här mappen kan mappas till en värd volym med hjälp av antingen monterings punkter eller data volymer. | 
| **MSSQL_PACKAGE** | Anger platsen för det DACPAC-eller BACPAC-paket som ska distribueras. | Mapp, fil eller SAS-URL som innehåller DACPAC-eller BACPAC-paket. Mer information finns [i distribuera SQL Database DACPAC-och BACPAC-paket i SQL Edge](deploy-dacpac.md). |


Följande SQL Server på Linux Environment-variabel stöds inte för Azure SQL Edge. Om det är definierat ignoreras den här miljövariabeln vid initiering av behållare.

| Miljövariabel | Beskrivning |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Aktivera tillgänglighets grupp. Till exempel **1** är aktive rad och **0** är inaktiverat. |

> [!IMPORTANT]
> **MSSQL_PID** -miljövariabeln för SQL Edge accepterar bara **Premium** och **Developer** som giltiga värden. Azure SQL Edge stöder inte initiering med en produkt nyckel.

### <a name="specify-the-environment-variables"></a>Ange miljövariabler

Ange miljövariabler för SQL Edge när du distribuerar tjänsten via [Azure Portal](deploy-portal.md). Du kan lägga till dem antingen i avsnittet **miljövariabler** i modulen distribution eller som en del av alternativen för att **skapa behållare**.

Lägg till värden i **miljövariabler**.

![Ange med hjälp av listan miljövariabler](media/configure/set-environment-variables.png)

Lägg till värden i **behållar skapande alternativ**.

![Ange med hjälp av alternativet för att skapa behållare](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> I det frånkopplade distributions läget kan miljövariabler anges med `-e` `--env` alternativet eller eller `--env-file` för `docker run` kommandot.

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurera med hjälp av en MSSQL. conf-fil

Azure SQL Edge innehåller inte det [konfigurations verktyg för MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) som SQL Server på Linux gör. Du måste konfigurera filen MSSQL. conf manuellt och placera den i den beständiga lagrings enhet som är mappad till mappen/var/opt/MSSQL/i SQL Edge-modulen. När du distribuerar SQL Edge från Azure Marketplace anges den här mappningen som alternativet **monteringar** i **behållaren skapa alternativ**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Följande nya MSSQL. conf-alternativ har lagts till för Azure SQL Edge. 

|Alternativ|Beskrivning|
|:---|:---|
|**customerfeedback** | Välj om SQL Server skicka feedback till Microsoft. Mer information finns i [inaktivera insamling av användnings data och diagnostikdata](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection)|      
|**userrequestedlocalauditdirectory** | Anger mål katalogen för gransknings filerna för användnings-och diagnostik-data insamling. Mer information finns i [lokal granskning av användning och insamling av diagnostikdata](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection) |        

Följande MSSQL. conf-alternativ gäller inte för SQL Edge:

|Alternativ|Beskrivning|
|:---|:---|
|**Kundfeedback** | Välj om SQL Server skicka feedback till Microsoft. |
|**Database mail-profil** | Ange e-postprofilen för standard databasen för SQL Server på Linux. |
|**Hög tillgänglighet** | Aktivera tillgänglighets grupper. |
|**Microsoft Distributed Transaction Coordinator** | Konfigurera och Felsök MSDTC i Linux. Ytterligare distribuerade transaktions-relaterade konfigurations alternativ stöds inte för SQL Edge. Mer information om dessa ytterligare konfigurations alternativ finns i [Configure MSDTC](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Service avtal för ML** | Godkänn R-och python-licensavtalet för Azure Machine Learning-paket. Gäller endast SQL Server 2019.|
|**outboundnetworkaccess** |Aktivera utgående nätverks åtkomst för [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R-, python-och Java-tillägg.|

Följande exempel på MSSQL. conf-filen fungerar för SQL Edge. Mer information om formatet för en MSSQL. conf-fil finns i [MSSQL. conf-format](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Kör Azure SQL Edge som icke-rot användare

Som standard körs Azure SQL Edge-behållare med en icke-rot användare/grupp. När den distribueras via Azure Marketplace (eller använder Docker-körning), om inte en annan användare/grupp anges, startar SQL Edge-behållare som MSSQL-användare (inte rot). Om du vill ange en annan icke-rot användare under distributionen lägger du till `*"User": "<name|uid>[:<group|gid>]"*` nyckel/värde-paret under behållare skapa alternativ. I exemplet nedan är SQL Edge konfigurerat att starta som användare `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Om du vill tillåta att icke-rot användaren har åtkomst till DB-filer som finns på monterade volymer, kontrollerar du att den användare/grupp som du kör behållaren under har Läs & Skriv behörigheter för lagring med beständig fil. I exemplet nedan ställer vi in den icke-rot användaren med user_id 10001 som ägare till filerna. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Uppgradera från tidigare CTP-versioner

Tidigare CTPs av Azure SQL Edge har kon figurer ATS för att köras som rot användare. Följande alternativ är tillgängliga när du uppgraderar från tidigare CTPs.

- Fortsätt att använda rot användaren – om du vill fortsätta använda rot användaren lägger du till `*"User": "0:0"*` nyckel/värde-paret under behållare skapa alternativ.
- Använd standard-MSSQL-användaren för att använda standard-MSSQL-användaren, Följ stegen nedan
  - Lägg till en användare med namnet MSSQL på Docker-värden. I exemplet nedan lägger vi till en användar-MSSQL med ID 10001. Den här användaren läggs också till i rot gruppen.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Ändra behörigheten för den katalog/monterings volym där databas filen finns 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Använd ett annat icke-rot användar konto – om du vill använda ett annat konto än rot användare
  - Uppdatera behållaren skapa alternativ för att ange Lägg till `*"User": "user_name | user_id*` nyckel/värde-par under behållare skapa alternativ. Ersätt user_name eller user_id med en faktisk user_name eller user_id från Docker-värden. 
  - Ändra behörigheter för katalogen/monterings volymen.

## <a name="persist-your-data"></a>Spara dina data

Dina ändringar i Azure SQL Edge-konfigurationen och databasfiler sparas i behållaren även om du startar om behållaren med `docker stop` och `docker start` . Men om du tar bort behållaren med `docker rm` , tas allt i behållaren bort, inklusive Azure SQL Edge och dina databaser. I följande avsnitt beskrivs hur du använder **data volymer** för att spara dina databasfiler även om de tillhör ande behållarna tas bort.

> [!IMPORTANT]
> För Azure SQL Edge är det viktigt att du förstår data persistens i Docker. Förutom diskussionen i det här avsnittet finns Docker-dokumentation om [hur du hanterar data i Docker-behållare](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Montera en värd katalog som data volym

Det första alternativet är att montera en katalog på värden som en data volym i din behållare. Det gör du genom att använda `docker run` kommandot med `-v <host directory>:/var/opt/mssql` flaggan. Detta gör att data kan återställas mellan behållar körningar.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Med den här metoden kan du också dela och Visa filer på värden utanför Docker.

> [!IMPORTANT]
> Värd volym mappning för **Docker på Windows** stöder för närvarande inte mappning av den fullständiga `/var/opt/mssql` katalogen. Du kan dock mappa en under katalog, t. ex `/var/opt/mssql/data` . till värddatorn.

> [!IMPORTANT]
> Värd volym mappning för **Docker på Mac** med Azure SQL Edge-avbildningen stöds inte för tillfället. Använd data volym behållare i stället. Den här begränsningen är speciell för `/var/opt/mssql` katalogen. Läsning från en monterad katalog fungerar bra. Du kan till exempel montera en värd katalog med-v på Mac och återställa en säkerhets kopia från en. bak-fil som finns på värden.

### <a name="use-data-volume-containers"></a>Använd data volym behållare

Det andra alternativet är att använda en data volym behållare. Du kan skapa en data volym behållare genom att ange ett volym namn i stället för en värd katalog med `-v` parametern. I följande exempel skapas en delad data volym med namnet **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Den här tekniken för att implicit skapa en data volym i körnings kommandot fungerar inte med äldre versioner av Docker. I så fall använder du de specifika stegen som beskrivs i Docker-dokumentationen, [skapar och monterar en data volym behållare](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

Även om du stoppar och tar bort den här behållaren kvarstår data volymen. Du kan visa den med `docker volume ls` kommandot.

```bash
docker volume ls
```

Om du sedan skapar en annan behållare med samma volym namn använder den nya behållaren samma Azure SQL Edge-data som finns i volymen.

Om du vill ta bort en data volym behållare använder du `docker volume rm` kommandot.

> [!WARNING]
> Om du tar bort data volym behållaren tas alla Azure SQL Edge-data i behållaren bort *permanent* .


## <a name="next-steps"></a>Nästa steg

- [Ansluta till Azure SQL Edge](connect.md)
- [Skapa en IoT-lösning från slutpunkt till slutpunkt med SQL Edge](tutorial-deploy-azure-resources.md)