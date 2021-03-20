---
title: Kända problem/migrerings begränsningar med hybrid läge
description: Läs om kända problem/migrerings begränsningar med hjälp av Azure Database Migration Service i hybrid läge.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 819fe5ced6c91819c817065305a31fca456ea5c0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91291851"
---
# <a name="known-issuesmigration-limitations-with-using-hybrid-mode"></a>Kända problem/migrerings begränsningar med hybrid läge

Kända problem och begränsningar som är associerade med att använda Azure Database Migration Service i hybrid läge beskrivs i följande avsnitt.

## <a name="installer-fails-to-authenticate"></a>Installations programmet kan inte autentisera

När du har laddat upp certifikatet till din AdApp, finns det en fördröjning på upp till ett par minuter innan det kan autentiseras med Azure. Installations programmet försöker försöka igen med en fördröjning, men det är möjligt att spridningen blir längre än återförsöket och du ser ett **FailedToGetAccessTokenException** -meddelande. Om certifikatet har laddats upp till rätt AdApp och rätt AppId angavs i dmsSettings.jspå, kan du försöka köra installations kommandot igen.

## <a name="service-offline-after-successful-installation"></a>Tjänsten offline efter att installationen har slutförts

Om tjänsten visas som offline när installationen har slutförts kan du prova med följande steg.

1. I Azure Portal Azure Database Migration Service navigerar du till fliken **hybrid** inställningar och kontrollerar sedan att arbetaren är registrerad genom att kontrol lera rutnätet för registrerade arbetare.

    Status för den här arbetaren bör vara **online**, men den kan visas som **offline** om det uppstår ett problem.

2. Kontrol lera statusen för tjänsten på arbets datorn genom att köra följande PowerShell-kommando:

    ```
    Get-Service Scenario*
    ```

    Med det här kommandot får du statusen för Windows-tjänsten som kör arbetaren. Det får bara finnas ett enda resultat. Om arbetaren har stoppats kan du försöka starta om den med hjälp av följande PowerShell-kommando:

    ```
    Start-Service Scenario*
    ```

    Du kan också kontrol lera tjänsten i användar gränssnittet för Windows-tjänster.

3. Om Windows-tjänsten växlar mellan att köra och stoppas, påträffade arbets rutinen problem med att starta. Kontrol lera Azure Database Migration Service hybrid Worker-loggar för att fastställa problemet.

    - Installations process loggar lagras i mappen "loggar" i mappen där installations programmet för körbara filer kördes.
    - Azure Database Migration Service hybrid Worker-loggar lagras i mappen **WorkerLogs** i mappen där Worker installeras. Standard platsen för Hybrid Worker-loggfilerna är **C:\Program Files\DatabaseMigrationServiceHybrid\WorkerLogs**.

## <a name="using-your-own-signed-certificate"></a>Använda ditt eget signerade certifikat

Certifikatet som genereras av åtgärden GenerateCert är ett självsignerat certifikat som kanske inte kan godkännas utifrån dina interna säkerhets principer. I stället för att använda det här certifikatet kan du ange ditt eget certifikat och ange tumavtrycket i dmsSettings.js. Certifikatet måste laddas upp till din AdApp och installeras på den dator där du installerar Azure Database Migration Service hybrid Worker. Installera sedan certifikatet med den privata nyckeln i den lokala datorns certifikat arkiv.

## <a name="running-the-worker-service-as-a-low-privilege-account"></a>Köra Worker-tjänsten som ett konto med låg behörighet

Som standard körs tjänsten Azure Database Migration Service hybrid Worker som det lokala system kontot. Du kan ändra det konto som används för den här tjänsten så länge det konto som du använder har nätverks behörighet. Använd följande process för att ändra tjänstens kör som-konto.

1. Stoppa tjänsten, antingen via Windows-tjänster eller med hjälp av Stop-Service kommandot i PowerShell.

2. Uppdatera tjänsten för att använda ett annat inloggnings konto.

3. I certmgr för lokala dator certifikat ger du privata nyckel behörigheter till det nya kontot för **DMS hybrid app-nyckeln** och certifikat för **DMS-scenariots motor nyckel par** .

    a. Öppna certmgr för att visa följande nycklar:

    - DMS hybrid app-nyckel
    - Installations nyckel för DMS Hybrid Worker
    - Nyckel par för DMS scenario motor

    b. Högerklicka på posten **DMS hybrid app Key** , peka på **alla aktiviteter** och välj sedan **hantera privata nycklar**.

    c. På fliken **säkerhet** väljer du **Lägg till** och anger sedan namnet på kontot.

    d. Använd samma steg för att bevilja behörighet för privat nyckel för det nya kontot till det nya kontot i **DMS-scenariot motor nyckel par** .

## <a name="unregistering-the-worker-manually"></a>Avregistrera arbetaren manuellt

Om du inte längre har åtkomst till arbets datorn kan du avregistrera arbets tagaren och återanvända Azure Database Migration Service-instansen genom att utföra följande steg:

1. I Azure Portal Azure Database Migration Service instansen och navigera sedan till sidan **hybrid** inställningar.

   Din arbets post visas i listan med statusen visas som **offline**.

2. Längst till höger om listan över Worker-poster väljer du ellipserna och väljer sedan **avregistrera**.

## <a name="addressing-issues-for-specific-migration-scenarios"></a>Adresserings problem för specifika migreringar

I avsnitten nedan beskrivs problem som är specifika för scenarier som rör användning av Azure Database Migration Service hybrid läge för att utföra en online-migrering.

### <a name="online-migrations-to-azure-sql-managed-instance"></a>Online-migreringar till Azure SQL-hanterad instans

**Hög CPU-användning**

**Problem**: vid online-migrering till SQL-hanterad instans kommer datorn som kör hybrid Worker att uppleva hög CPU-användning om det finns för många säkerhets kopior eller om säkerhets kopieringarna är för stora.

Lösning **: lös** problemet genom att använda komprimerade säkerhets kopior, dela migreringen så att den använder flera resurser eller skala upp datorn som kör hybrid Worker.
