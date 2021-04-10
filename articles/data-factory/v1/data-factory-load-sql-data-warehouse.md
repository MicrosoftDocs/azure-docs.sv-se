---
title: Läs in terabyte av data i Azure Synapse Analytics
description: Visar hur 1 TB data kan läsas in i Azure Synapse Analytics under 15 minuter med Azure Data Factory
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 5acae7c90efbf178fad199177fa6e0886e497fdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371217"
---
# <a name="load-1-tb-into-azure-synapse-analytics-under-15-minutes-with-data-factory"></a>Läs in 1 TB i Azure Synapse Analytics under 15 minuter med Data Factory
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av Data Factorys tjänsten, se [Kopiera data till eller från Azure Synapse Analytics med hjälp av Data Factory](../connector-azure-sql-data-warehouse.md).


[Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) är en molnbaserad, skalbar databas som kan bearbeta stora mängder data, både relationella och icke-relationella.  Azure Synapse Analytics bygger på en storskalig parallell bearbetnings arkitektur (MPP) och är optimerad för arbets belastningar för företags data lager.  Det ger moln elastiskhet med flexibiliteten att skala lagring och beräkning oberoende av varandra.

Att komma igång med Azure Synapse Analytics är nu enklare än att någonsin använda **Azure Data Factory**.  Azure Data Factory är en fullständigt hanterad molnbaserad data integrerings tjänst som kan användas för att fylla i Azure Synapse Analytics med data från ditt befintliga system, och spara värdefull tid samtidigt som du utvärderar Azure Synapse Analytics och skapar analys lösningar. Här är de viktigaste fördelarna med att läsa in data i Azure Synapse Analytics med hjälp av Azure Data Factory:

* **Enkelt att konfigurera**: 5-steg intuitiv guide utan skript krävs.
* **Stöd för omfattande data lager**: inbyggt stöd för en omfattande uppsättning lokala och molnbaserade data lager.
* **Säkert och kompatibelt**: data överförs via https eller ExpressRoute, och global tjänst närvaro garanterar att dina data aldrig lämnar den geografiska gräns
* **Oöverträffade prestanda med PolyBase** – som använder PolyBase är det mest effektiva sättet att flytta data till Azure Synapse Analytics. Med hjälp av funktionen för mellanlagring av BLOB kan du uppnå höga belastnings hastigheter från alla typer av data lager förutom Azure Blob Storage, som PolyBase stöder som standard.

Den här artikeln visar hur du använder Data Factory kopierings guiden för att läsa in 1 TB data från Azure Blob Storage till Azure Synapse Analytics på under 15 minuter med över 1,2 Gbit/s-genomflöde.

Den här artikeln innehåller steg-för-steg-instruktioner för att flytta data till Azure Synapse Analytics med hjälp av guiden Kopiera.

> [!NOTE]
>  Allmän information om funktioner i Data Factory att flytta data till/från Azure Synapse Analytics finns i avsnittet [Flytta data till och från Azure Synapse Analytics med hjälp av Azure Data Factory](data-factory-azure-sql-data-warehouse-connector.md) artikeln.
>
> Du kan också bygga pipelines med Visual Studio, PowerShell osv. Se [självstudie: kopiera data från Azure blob till Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) för en snabb genom gång med stegvisa instruktioner för att använda kopierings aktiviteten i Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Förutsättningar
* Azure Blob Storage: med det här experimentet används Azure Blob Storage (GRS) för att lagra data uppsättningen TPC-H-test.  Om du inte har ett Azure Storage-konto kan du läsa om [hur du skapar ett lagrings konto](../../storage/common/storage-account-create.md).
* [TPC-h-](http://www.tpc.org/tpch/) data: vi ska använda TPC-h som test data uppsättning.  Om du vill göra det måste du använda `dbgen` från TPC-H Toolkit, som hjälper dig att skapa data uppsättningen.  Du kan antingen ladda ned käll koden för `dbgen` från [TPC-verktyg](http://www.tpc.org/tpc_documents_current_versions/current_specifications5.asp) och kompilera den själv eller ladda ned den kompilerade binärfilen från [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Kör dbgen.exe med följande kommandon för att generera 1 TB platt fil för `lineitem` tabell uppslag över 10 filer:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopiera nu de genererade filerna till Azure-blobben.  Se [Flytta data till och från ett lokalt fil system med hjälp av Azure Data Factory](data-factory-onprem-file-system-connector.md) för hur du gör detta med hjälp av ADF-kopiering.    
* Azure Synapse Analytics: det här experimentet läser in data i Azure Synapse Analytics som skapats med 6 000 DWU: er

    Mer information om hur du skapar en Azure Synapse Analytics-databas hittar du i [skapa en Azure Synapse-analys](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md) .  För att få bästa möjliga inläsnings prestanda i Azure Synapse Analytics med PolyBase väljer vi det maximala antalet data lager enheter (DWU: er) som tillåts i prestanda inställningen, som är 6 000 DWU: er.

  > [!NOTE]
  > När du läser in från Azure Blob är data inläsnings prestanda direkt proportionella till antalet DWU: er som du konfigurerar för Azure Synapse Analytics:
  >
  > Inläsning av 1 TB i 1 000 DWU Azure Synapse-analys tar 87 minuter (~ 200 Mbit/s) inläsning 1 TB till 2 000 DWU Azure Synapse Analytics tar 46 minuter (~ 380 Mbit/s) inläsning 1 TB i 6 000 DWU Azure Synapse Analytics tar 14 minuter (~ 1,2 Gbit/s)
  >
  >

    Om du vill skapa en dedikerad SQL-pool med 6 000 DWU: er flyttar du skjutreglaget för prestanda på vägen till höger:

    ![Skjutreglage för prestanda](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    För en befintlig databas som inte är konfigurerad med 6 000 DWU: er kan du skala upp den med Azure Portal.  Navigera till databasen i Azure Portal och det finns en **skalnings** knapp på **översikts** panelen som visas i följande bild:

    ![Knappen skala](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klicka på knappen **skala** för att öppna följande panel, flytta skjutreglaget till maximalt värde och klicka på knappen **Spara** .

    ![Skalnings dialog ruta](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Det här experimentet läser in data i Azure Synapse Analytics med hjälp av `xlargerc` resurs klassen.

    För att uppnå bästa möjliga data flöde måste kopieringen utföras med hjälp av en Azure Synapse Analytics-användare som tillhör `xlargerc` resurs klassen.  Lär dig hur du gör detta genom att följa [exemplet på Ändra en användar resurs klass](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md).  
* Skapa mål tabell schema i Azure Synapse Analytics-databasen genom att köra följande DDL-instruktion:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  När de nödvändiga stegen har slutförts är nu nu dags att konfigurera kopierings aktiviteten med hjälp av guiden Kopiera.

## <a name="launch-copy-wizard"></a>Använda guiden Kopiera
1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **skapa en resurs** i det övre vänstra hörnet, klicka på **information + analys** och klicka på **Data Factory**.
3. I fönstret **ny data fabrik** :

   1. Ange **LoadIntoSQLDWDataFactory** som **namn**.
       Namnet på Azure Data Factory måste vara globalt unikt. Om du får felet: **data fabriks namnet "LoadIntoSQLDWDataFactory" är inte tillgängligt**, ändrar du namnet på data fabriken (till exempel yournameLoadIntoSQLDWDataFactory) och försöker skapa igen. Se artikeln [Data Factory – namnregler](data-factory-naming-rules.md) för namnregler för Data Factory-artefakter.  
   2. Välj din Azure- **prenumeration**.
   3. För resursgruppen utför du något av följande steg:
      1. Välj **Använd befintlig** och välj en befintlig resursgrupp.
      2. Välj **Skapa nytt** och ange ett namn för en resursgrupp.
   4. Välj **plats** för datafabriken.
   5. Välj kryssrutan **PIN-kod för instrumentpanelen** längst ned på bladet.  
   6. Klicka på **Skapa**.
4. När den har skapats visas bladet **Data Factory** som du ser i följande bild:

   ![Datafabrikens startsida](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. På startsidan Datafabrik klickar du på ikonen **Kopiera data** för att starta **guiden Kopiera**.

   > [!NOTE]
   > Om du ser att webbläsaren har fastnat på ”Auktoriserar...”, inaktiverar/avmarkerar du inställningen **Blockera cookies från tredje part och platsdata** (eller) behåller den aktiverad och skapar ett undantag för **login.microsoftonline.com**. Försök sedan starta guiden igen.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Steg 1: konfigurera schema för data inläsning
Det första steget är att konfigurera schemat för data inläsning.  

På sidan **Egenskaper**:

1. Ange **CopyFromBlobToAzureSqlDataWarehouse** som **uppgifts namn**
2. Välj **kör en gång nu** .   
3. Klicka på **Nästa**.  

    ![Guiden Kopiera – sidan Egenskaper](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Steg 2: Konfigurera källa
I det här avsnittet visas hur du konfigurerar källan: Azure blob som innehåller 1 – TB TPC-H-filer med rad objekt.

1. Välj **Azure-Blob Storage** som data lager och klicka på **Nästa**.

    ![Guiden Kopiera – sidan Välj källa](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Fyll i anslutnings informationen för Azure Blob Storage-kontot och klicka på **Nästa**.

    ![Guiden Kopiera – information om käll anslutning](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Välj den **mapp** som innehåller filerna för TPC-H-raden och klicka på **Nästa**.

    ![Guiden Kopiera – Välj mapp för indatamängd](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. När du klickar på **Nästa** identifieras fil format inställningarna automatiskt.  Kontrol lera att kolumn avgränsaren är | i stället för standard kommatecknet,.  Klicka på **Nästa** när du har förvisat data.

    ![Guiden Kopiera – fil format inställningar](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Steg 3: Konfigurera målet
Det här avsnittet visar hur du konfigurerar målet: `lineitem` tabellen i Azure Synapse Analytics-databasen.

1. Välj **Azure Synapse Analytics** som mål Arkiv och klicka på **Nästa**.

    ![Guiden Kopiera – Välj mål data lager](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Fyll i anslutnings informationen för Azure Synapse Analytics.  Se till att du anger den användare som är medlem i rollen `xlargerc` (se avsnittet **krav** för detaljerade instruktioner) och klicka på **Nästa**.

    ![Guiden Kopiera – information om mål anslutning](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Välj mål tabellen och klicka på **Nästa**.

    ![Kopiera guiden – tabell mappnings sida](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. På sidan schema mappning lämnar du alternativet "Använd kolumn mappning" omarkerat och klickar på **Nästa**.

## <a name="step-4-performance-settings"></a>Steg 4: prestanda inställningar

**Tillåt PolyBase** är markerat som standard.  Klicka på **Nästa**.

![Kopierings guiden – sidan schema mappning](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Steg 5: Distribuera och övervaka inläsnings resultat
1. Klicka på **Slutför** om du vill distribuera.

    ![Kopierings guiden – sammanfattnings sida 1](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. När distributionen är klar klickar du på `Click here to monitor copy pipeline` för att övervaka kopierings körnings förloppet. Välj den kopia pipeline som du skapade i listan **aktivitets fönster** .

    ![Kopierings guiden – sammanfattnings sida 2](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    Du kan visa information om kopierings körningen i **aktivitets fönstrets Utforskare** i den högra panelen, inklusive data volymen som läses från källan och skrivs till mål, varaktighet och genomsnittligt data flöde för körningen.

    Som du ser i följande skärm bild kan du kopiera 1 TB från Azure Blob Storage till Azure Synapse Analytics tog 14 minuter, vilket effektivt uppnår data flödet på 1,22 Gbit/s!

    ![Guiden Kopiera – dialog rutan har slutförts](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Bästa praxis
Här följer några exempel på metod tips för att köra Azure Synapse Analytics-databasen:

* Använd en större resurs klass vid inläsning till ett GRUPPERat COLUMNSTORE-INDEX.
* För effektivare kopplingar bör du överväga att använda hash-distribution av en SELECT-kolumn i stället för standard resursallokering-distribution.
* Överväg att använda heap för tillfälliga data för snabbare belastnings hastigheter.
* Skapa statistik när du har slutfört inläsningen till Azure Synapse Analytics.

Mer information finns i [metod tips för Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) .

## <a name="next-steps"></a>Nästa steg
* [Guiden Data Factory kopiering](data-factory-copy-wizard.md) – den här artikeln innehåller information om guiden Kopiera.
* [Prestanda-och justerings guide för kopierings aktivitet](data-factory-copy-activity-performance.md) – den här artikeln innehåller måtten för referens prestanda och justerings guiden.