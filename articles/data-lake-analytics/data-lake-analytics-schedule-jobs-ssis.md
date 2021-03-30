---
title: Schemalägg Azure Data Lake Analytics U-SQL-jobb med SSIS
description: Lär dig hur du använder SQL Server Integration Services för att schemalägga U-SQL-jobb med infogat skript eller från Query-filer i U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/17/2018
ms.openlocfilehash: b080b433f5af49e970faba02003fb68e21a08365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221459"
---
# <a name="schedule-u-sql-jobs-using-sql-server-integration-services-ssis"></a>Schemalägg U-SQL-jobb med SQL Server Integration Services (SSIS)

I det här dokumentet får du lära dig hur du dirigerar och skapar U-SQL-jobb med hjälp av SQL Server integration service (SSIS). 

## <a name="prerequisites"></a>Förutsättningar

[Azure Feature Pack för integrations tjänster](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud) tillhandahåller [Azure Data Lake Analytics aktivitet](/sql/integration-services/control-flow/azure-data-lake-analytics-task) och [Azure Data Lake Analytics anslutnings hanteraren](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager) som hjälper till att ansluta till Azure Data Lake Analytics-tjänsten. Kontrol lera att du använder den här uppgiften för att installera:

- [Hämta och installera SQL Server Data Tools (SSDT) för Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt)
- [Installera Azure Feature Pack för integrerings tjänster (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis)

## <a name="azure-data-lake-analytics-task"></a>Azure Data Lake Analytics aktivitet

Med Azure Data Lake Analytics-aktiviteten kan användarna skicka U-SQL-jobb till Azure Data Lake Analytics-kontot. 

[Lär dig hur du konfigurerar Azure Data Lake Analytics-aktivitet](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

![Azure Data Lake Analytics aktivitet i SSIS](./media/data-lake-analytics-schedule-jobs-ssis/data-lake-analytics-azure-data-lake-analytics-task-in-ssis.png)

Du kan hämta U-SQL-skriptet från olika platser med hjälp av inbyggda funktioner och uppgifter i SSIS, under scenarier visar hur du kan konfigurera U-SQL-skripten för olika användar fall.

## <a name="scenario-1-use-inline-script-call-tvfs-and-stored-procs"></a>Scenario 1 – använda infogade skript anrop tvfs och lagrade processer

I Azure Data Lake Analytics aktivitets redigeraren konfigurerar du **SourceType** som **DirectInput** och sätter U-SQL-uttrycken i **USQLStatement**.

För enkel underhåll och kod hantering är det bara att använda korta U-SQL-skript som infogade skript, till exempel kan du anropa befintliga tabell värdes funktioner och lagrade procedurer i dina U-SQL-databaser. 

![Redigera infogade U-SQL-skript i SSIS-aktiviteten](./media/data-lake-analytics-schedule-jobs-ssis/edit-inline-usql-script-in-ssis.png)

Relaterad artikel: [så här skickar du parameter till lagrade procedurer](#scenario-6-pass-parameters-to-u-sql-script)

## <a name="scenario-2-use-u-sql-files-in-azure-data-lake-store"></a>Scenario 2 – Använd U-SQL-filer i Azure Data Lake Store

Du kan också använda U-SQL-filer i Azure Data Lake Store med hjälp av **åtgärden Azure Data Lake Store fil system** i Azure Feature Pack. Med den här metoden kan du använda skripten som lagras i molnet.

Följ stegen nedan för att konfigurera anslutningen mellan Azure Data Lake Store fil system aktivitet och Azure Data Lake Analytics aktivitet.

### <a name="set-task-control-flow"></a>Ange flöde för aktivitets kontroll

I SSIS-paketets designvy lägger du till en **Azure Data Lake Store fil system aktivitet**, ett **förgrunds sling-behållare** och en **Azure Data Lake Analytics uppgift** i behållaren för förgrunds slingor. Med åtgärden Azure Data Lake Store fil system kan du hämta U-SQL-filer i ditt ADLS-konto till en tillfällig mapp. Den förgrunds sling-behållaren och Azure Data Lake Analytics aktiviteten hjälper till att skicka varje U-SQL-fil under den tillfälliga mappen till Azure Data Lake Analytics-kontot som ett U-SQL-jobb.

![Diagram som visar en Azure Data Lake Store fil system aktivitet som läggs till i en behållare för förgrunds slingor.](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-data-lake-store.png)

### <a name="configure-azure-data-lake-store-file-system-task"></a>Konfigurera Azure Data Lake Store fil system aktivitet

1. Ange **åtgärden** till **CopyFromADLS**.
2. Konfigurera **AzureDataLakeConnection**, Läs mer om [Azure Data Lake Store anslutnings hanteraren](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).
3. Ange **AzureDataLakeDirectory**. Peka på mappen som lagrar U-SQL-skripten. Använd relativ sökväg som är relativ till rotmappen för Azure Data Lake Store-kontot.
4. Ange **mål** till en mapp som cachelagrar de hämtade U-SQL-skripten. Den här mappsökvägen används i förgrunds-slingan för att skicka U-SQL-jobb. 

![Konfigurera Azure Data Lake Store fil system aktivitet](./media/data-lake-analytics-schedule-jobs-ssis/configure-azure-data-lake-store-file-system-task.png)

[Läs mer om hur du Azure Data Lake Store fil system](/sql/integration-services/control-flow/azure-data-lake-store-file-system-task).

### <a name="configure-foreach-loop-container"></a>Konfigurera behållare för förgrunds slingor

1. På sidan **samling** anger du **uppräknare** till **förgrunds fil uppräkna ren**.

2. Ange **mappen** under **uppräknings konfigurations** grupp till den tillfälliga mappen som innehåller de hämtade U-SQL-skripten.

3. Ange **filer** under **uppräknings konfiguration** så att `*.usql` loop-behållaren bara fångar filerna som slutar med `.usql` .

    ![Skärm bild som visar förgrunds slingan med "samling" vald och konfigurations avsnitten uppräknare och uppräknare är markerade.](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-collection.png)

4. På sidan **variabel mappningar** lägger du till en användardefinierad variabel för att hämta fil namnet för varje U-SQL-fil. Ange **indexet** till 0 för att hämta fil namnet. I det här exemplet definierar du en variabel som kallas `User::FileName` . Den här variabeln används för att dynamiskt hämta U-SQL skript fil anslutning och ange U-SQL-jobbnamn i Azure Data Lake Analytics aktiviteten.

    ![Konfigurera en upprepnings behållare för att hämta fil namn](./media/data-lake-analytics-schedule-jobs-ssis/configure-foreach-loop-container-variable-mapping.png)

### <a name="configure-azure-data-lake-analytics-task"></a>Konfigurera Azure Data Lake Analytics aktivitet 

1. Ange **SourceType** till **FileConnection**.

2. Ange **FileConnection** till fil anslutningen som pekar på fil objekt som returneras från förgrunds sling-behållaren.
    
    Så här skapar du den här fil anslutningen:

   1. Välj **\<New Connection...>** i FileConnection-inställningen.
   2. Ange **användnings typ** till en **befintlig fil** och ange **filen** till en befintlig fils sökväg.

       ![Skärm bild som visar fil anslutnings hanterarens redigerare med "befintlig fil" vald för "användnings typ".](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

   3. I vyn **anslutnings hanterare** högerklickar du på fil anslutningen som skapats just nu och väljer **Egenskaper**.

   4. I fönstret **Egenskaper** expanderar du **uttryck** och ställer in **ConnectionString** till variabeln som definierats i förgrunds sling-behållaren, till exempel `@[User::FileName]` .

       ![Konfigurera behållare för förgrunds slingor](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-property-for-foreach-loop-container.png)

3. Ange **AzureDataLakeAnalyticsConnection** till det Azure Data Lake Analytics konto som du vill skicka jobb till. Läs mer om [Azure Data Lake Analytics Connection Manager](/sql/integration-services/connection-manager/azure-data-lake-analytics-connection-manager).

4. Ange andra jobb konfigurationer. [Läs mer](/sql/integration-services/control-flow/azure-data-lake-analytics-task).

5. Använd **uttryck** för att dynamiskt ange U-SQL-jobbets namn:

    1. På sidan **uttryck** lägger du till ett nytt uttrycks nyckel par för **JobName**.
    2. Ange värdet för JobName till variabeln som definierats i förgrunds slinga-behållaren, till exempel `@[User::FileName]` .
    
        ![Konfigurera SSIS-uttryck för U-SQL-jobbnamn](./media/data-lake-analytics-schedule-jobs-ssis/configure-expression-for-u-sql-job-name.png)

## <a name="scenario-3-use-u-sql-files-in-azure-blob-storage"></a>Scenario 3 – Använd U-SQL-filer i Azure Blob Storage

Du kan använda U-SQL-filer i Azure Blob Storage med hjälp av **Azure Blob Download Task** i Azure Feature Pack. Med den här metoden kan du använda skripten i molnet.

Stegen påminner om [Scenario 2: Använd U-SQL-filer i Azure Data Lake Store](#scenario-2-use-u-sql-files-in-azure-data-lake-store). Ändra åtgärden Azure Data Lake Store fil system till Azure Blob Download. [Läs mer om hämtnings uppgiften för Azure Blob](/sql/integration-services/control-flow/azure-blob-download-task).

Kontroll flödet ser ut så här.

![Använd U-SQL-filer i Azure Data Lake Store](./media/data-lake-analytics-schedule-jobs-ssis/use-u-sql-files-in-azure-blob-storage.png)

## <a name="scenario-4-use-u-sql-files-on-the-local-machine"></a>Scenario 4 – Använd U-SQL-filer på den lokala datorn

Förutom att använda U-SQL-filer som lagras i molnet kan du också använda filer på din lokala dator eller filer som distribueras med dina SSIS-paket.

1. Högerklicka på **anslutnings hanterare** i SSIS-projekt och välj **ny anslutnings hanterare**.

2. Välj **filtyp** och klicka på **Lägg till.**...

3. Ange **användnings typ** till **befintlig fil** och ange **filen** till filen på den lokala datorn.

    ![Lägg till fil anslutning till den lokala filen](./media/data-lake-analytics-schedule-jobs-ssis/configure-file-connection-for-foreach-loop-container.png)

4. Lägg till **Azure Data Lake Analytics** uppgift och:
    1. Ange **SourceType** till **FileConnection**.
    2. Ange **FileConnection** till fil anslutningen som skapats just nu.

5. Slutför andra konfigurationer för Azure Data Lake Analytics aktivitet.

## <a name="scenario-5-use-u-sql-statement-in-ssis-variable"></a>Scenario 5 – använda U-SQL-instruktionen i SSIS-variabeln

I vissa fall kan du behöva generera U-SQL-uttrycken dynamiskt. Du kan använda **variabeln SSIS** med **SSIS-uttryck** och andra SSIS-uppgifter, som skript aktivitet, för att hjälpa dig att generera U-SQL-instruktionen dynamiskt.

1. Öppna verktyg-fönstret med **SSIS > variabler** på översta nivån.

2. Lägg till en SSIS-variabel och ange värdet direkt eller Använd **uttryck** för att generera värdet.

3. Lägg till **Azure Data Lake Analytics uppgift** och:
    1. Ange **SourceType** till **Variable**.
    2. Ange **SourceVariable** till SSIS-variabeln som skapats just nu.

4. Slutför andra konfigurationer för Azure Data Lake Analytics aktivitet.

## <a name="scenario-6-pass-parameters-to-u-sql-script"></a>Scenario 6 – skicka parametrar till U-SQL-skript

I vissa fall kanske du vill ange värdet U-SQL-variabeln i U-SQL-skriptet dynamiskt. **Parameter mappnings** funktion i Azure Data Lake Analytics uppgifts hjälp med det här scenariot. Det finns vanligt vis två vanliga användar fall:

- Ange Path-variablerna för indata och utdata dynamiskt baserat på aktuellt datum och aktuell tid.
- Ange parametern för lagrade procedurer.

[Läs mer om hur du ställer in parametrar för U-SQL-skriptet](/sql/integration-services/control-flow/azure-data-lake-analytics-task#parameter-mapping-page-configuration).

## <a name="next-steps"></a>Nästa steg

- [Kör SSIS-paket i Azure](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)
- [Azure Feature Pack för integrerings tjänster (SSIS)](/sql/integration-services/azure-feature-pack-for-integration-services-ssis#scenario-managing-data-in-the-cloud)
- [Schemalägg U-SQL-jobb med Azure Data Factory](../data-factory/transform-data-using-data-lake-analytics.md)