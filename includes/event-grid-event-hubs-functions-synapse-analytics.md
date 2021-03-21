---
title: ta med fil
description: ta med fil
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 12/07/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d6b8b35b36830568d6ff8c46a381fec0cfc57e84
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854439"
---
:::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/overview.png" alt-text="Programöversikt":::

Diagrammet visar arbetsflödet för lösningen som du skapar i självstudien: 

1. Data som skickas till en Azure-händelsehubb samlas i en Azure-blobblagring.
2. När datainsamlingen är klar skapas en händelse som skickas till ett Azure-händelserutnät. 
3. Händelserutnätet vidarebefordrar händelsedatan till en Azure-funktionsapp.
4. Funktionsappen använder blobb-URL:en i händelsedatan till att hämta blobben från lagringen. 
5. Function-appen migrerar BLOB-data till en Azure Synapse-analys. 

I den här självstudien gör du följande:

> [!div class="checklist"]
> - Distribuera den infrastruktur som krävs för självstudien
> - Publicera kod till en Functions-app
> - Skapa en Event Grid-prenumeration 
> - Strömma exempel data till Event Hubs
> - Verifiera insamlade data i Azure Synapse Analytics

## <a name="prerequisites"></a>Förutsättningar
Du behöver följande för att kunna slutföra den här självstudiekursen:

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- [Visual studio 2019](https://www.visualstudio.com/vs/) med arbets belastningar för: .net Skriv bords utveckling, Azure-utveckling, ASP.net och webb utveckling, Node.js utveckling och python-utveckling.
- Ladda ned [exempelprojektet EventHubsCaptureEventGridDemo](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) till din dator.
    - WindTurbineDataGenerator – en enkel utgivare som skickar exempel vinds turbin-data till en Capture-aktiverad händelsehubben
    - FunctionDWDumper – en Azure-funktion som tar emot ett Event Grid-meddelande när en Avro-fil hämtas till blob-lagring i Azure Storage. Den tar emot blobens URI-sökväg, läser innehållet och pushar dessa data till Azure Synapse Analytics (dedikerad SQL-pool).

## <a name="deploy-the-infrastructure"></a>Distribuera infrastrukturen
I det här steget distribuerar du den infrastruktur som krävs med en [Resource Manager-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/event-grid/EventHubsDataMigration.json). När du distribuerar mallen skapas följande resurser:

* Händelsehubb med Capture-funktionen aktiverad.
* Lagringskonto för hämtade filer. 
* Apptjänstplan som är värd för funktionsappen
* Funktionsapp för bearbetning av händelsen
* SQL Server som värd för informationslagret
* Azure Synapse Analytics (dedikerad SQL-pool) för att lagra migrerade data

### <a name="use-azure-cli-to-deploy-the-infrastructure"></a>Använd Azure CLI för att distribuera infrastrukturen

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj **Cloud Shell**-knappen överst.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/azure-portal.png" alt-text="Azure-portalen":::
3. Du ser att Cloud Shell öppnas längst ned i webbläsaren.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/launch-cloud-shell.png" alt-text="Cloud Shell":::
4. Om du i Cloud Shell ser ett alternativ för att välja mellan **Bash** och **PowerShell**, väljer du **Bash**. 
5. Om du använder Cloud Shell för första gången, skapar du ett lagringskonto genom att välja **Skapa lagring**. Det måste finnas ett Azure-lagringskonto i Azure Cloud Shell för att vissa filer ska kunna lagras. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/create-storage-cloud-shell.png" alt-text="Skapa lagrings utrymme för Cloud Shell":::
6. Vänta tills Cloud Shell har initierats. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/cloud-shell-initialized.png" alt-text="Cloud Shell initierad":::
1. Skapa en Azure-resursgrupp genom att köra följande CLI-kommando: 
    1. Kopiera och klistra in följande kommando i Cloud Shell-fönstret. Ändra resurs gruppens namn och plats om du vill.

        ```azurecli
        az group create -l eastus -n rgDataMigration
        ```
    2. Tryck på **RETUR**. 

        Här är ett exempel:
    
        ```azurecli
        user@Azure:~$ az group create -l eastus -n rgDataMigration
        {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/rgDataMigration",
          "location": "eastus",
          "managedBy": null,
          "name": "rgDataMigration",
          "properties": {
            "provisioningState": "Succeeded"
          },
          "tags": null
        }
        ```
2. Distribuera alla resurser som nämns i föregående avsnitt (händelsehubben, lagrings konto, Functions-app, Azure Synapse Analytics) genom att köra följande CLI-kommando: 
    1. Kopiera och klistra in kommandot i Cloud Shell-fönstret. Alternativt kan du kopiera och klistra in i ett redigeringsprogram, ange värden och sedan kopiera kommandot till Cloud Shell. 

        > [!IMPORTANT]
        > Ange värden för följande entiteter innan du kör kommandot: 
        > - Namn på den resursgrupp som du skapade tidigare.
        > - Namn på händelsehubbens namnområde. 
        > - Namn på händelsehubben. Du kan lämna värdet som det är (hubdatamigration).
        > - Namn på SQL-servern.
        > - Namn på SQL-användaren och lösenordet. 
        > - Namn för databasen.
        > - Namn på lagringskontot. 
        > - Namn på funktionsappen. 


        ```azurecli
        az deployment group create \
            --resource-group rgDataMigration \
            --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/event-grid/EventHubsDataMigration.json \
            --parameters eventHubNamespaceName=<event-hub-namespace> eventHubName=hubdatamigration sqlServerName=<sql-server-name> sqlServerUserName=<user-name> sqlServerPassword=<password> sqlServerDatabaseName=<database-name> storageName=<unique-storage-name> functionAppName=<app-name>
        ```
    3.  Tryck på **Retur-tangenten** i Cloud Shell-fönstret för att köra kommandot. Den här processen kan ta en stund eftersom du skapar många resurser. Kontrollera att det inte finns några fel i kommandoresultatet. 
1. Stäng Cloud Shell genom att välja knappen **Cloud Shell** i Portal (eller) **X** -knappen i det övre högra hörnet i Cloud Shells fönstret. 

### <a name="verify-that-the-resources-are-created"></a>Kontrollera att resurserna har skapats

1. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn. 
2. Filtrera listan över resursgrupper genom att ange namnet på resursgruppen i sökrutan. 
3. Välj din resursgrupp i listan.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-resource-group.png" alt-text="Välj din resursgrupp":::
4. Kontrollera att du ser följande resurser i resursgruppen:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png" alt-text="Resurser i resursgruppen" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/resources-in-resource-group.png":::

### <a name="create-a-table-in-azure-synapse-analytics"></a>Skapa en tabell i Azure Synapse Analytics
Skapa en tabell i informationslagret genom att köra skriptet [CreateDataWarehouseTable.sql](https://github.com/Azure/azure-event-hubs/blob/master/samples/e2e/EventHubsCaptureEventGridDemo/scripts/CreateDataWarehouseTable.sql). Du kan köra skriptet med Visual Studio eller Frågeredigeraren i portalen. Följande steg visar hur du använder Frågeredigeraren: 

1. I listan över resurser i resurs gruppen väljer du din **dedikerade SQL-pool**. 
2. På sidan **dedikerad SQL-pool** i avsnittet **vanliga uppgifter** på den vänstra menyn väljer du **Frågeredigeraren (för hands version)**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/sql-data-warehouse-page.png" alt-text="Azure Synapse Analytics-sida":::
2. Ange namnet på **användare** och **lösenord** för SQL-servern och välj **OK**. Följ dessa steg om du ser ett meddelande om att klienten får åtkomst till SQL Server:
    1. Välj länken: **ange serverns brand vägg**. 
    2. På sidan **brand Väggs inställningar** väljer du **Lägg till klient-IP** i verktygsfältet och väljer sedan **Spara** i verktygsfältet. 
    3. Välj **OK** i meddelandet lyckades.
    4. Gå tillbaka till sidan för **dedikerad SQL-pool** och välj **Frågeredigeraren (för hands version)** på den vänstra menyn. 
    5. Ange **användare** och **lösen ord** och välj sedan **OK**. 
1. Kopiera och kör följande SQL-skript i frågefönstret: 

    ```sql
    CREATE TABLE [dbo].[Fact_WindTurbineMetrics] (
        [DeviceId] nvarchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL, 
        [MeasureTime] datetime NULL, 
        [GeneratedPower] float NULL, 
        [WindSpeed] float NULL, 
        [TurbineSpeed] float NULL
    )
    WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    ```

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/run-sql-query.png" alt-text="Köra SQL-fråga":::
5. Låt den här fliken eller fönstret vara öppet så att du kan kontrollera att data har skapats i slutet av självstudien. 

### <a name="update-the-function-runtime-version"></a>Uppdatera funktionens körnings version

1. Öppna en annan flik i webbläsaren och navigera till [Azure Portal](https://portal.azure.com).
1. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn.
1. Välj den resurs grupp där Function-appen finns. 
1. Välj **Function-appen** i listan över resurser i resurs gruppen.
1. Välj **konfiguration** under **Inställningar** på den vänstra menyn. 
1. Växla till fliken **funktionens körnings inställningar** i den högra rutan. 
1. Uppdatera **Kör tids versionen** till **~ 3**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-runtime-version.png" alt-text="Uppdatera funktion runtime-version":::
6. Välj **Spara** i verktygsfältet. 
1. På popup-menyn **Spara ändringar** väljer du **Fortsätt**. 

## <a name="publish-the-azure-functions-app"></a>Publicera Azure Functions-appen

1. Starta Visual Studio.
2. Öppna lösningen **EventHubsCaptureEventGridDemo.sln** som du laddade ned från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) som en del av förutsättningarna. Du hittar den i `/samples/e2e/EventHubsCaptureEventGridDemo` mappen. 
3. I Solution Explorer högerklickar du på **FunctionEGDWDumper** -projekt och väljer **publicera**.
4. Om du ser följande skärm väljer du **Starta**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/start-publish-button.png" alt-text="Start-knappen i publicerings avsnittet.":::
5. I dialog rutan **publicera** väljer du **Azure** for **target** och **sedan nästa**. 
6. Välj **Azure-Funktionsapp (Windows)** och välj **Nästa**.
7. På fliken **Functions-instans** väljer du din Azure-prenumeration, expanderar resurs gruppen och väljer sedan Function-appen och väljer sedan **Slutför**. Du måste logga in på ditt Azure-konto om du inte redan har gjort det. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-select-function-app.png" alt-text="Välj din Function-app":::
8. På sidan **publicera** i avsnittet **tjänst beroenden** väljer du **Konfigurera** för **lagring**. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/publish-storage-configure-link.png" alt-text="Välj Konfigurera länk för lagrings tjänst beroende":::
1. Följ de här stegen på sidan **Konfigurera beroende** : 
    1. Välj det **lagrings konto** som du skapade tidigare och välj sedan **Nästa**. 

        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-dependency-storage.png" alt-text="Välj lagrings konto":::
    10. Ange ett **namn för anslutnings strängen** och välj **ingen** för alternativet **Spara anslutnings sträng** och välj sedan **Nästa**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-connection-string.png" alt-text="Ange namn på anslutnings sträng":::      
    1. Avmarkera alternativet **C#-kod fil** och **hemligheter-Arkiv** och välj sedan **Slutför**.  
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/dependency-storage-changes-summary.png" alt-text="Granska Sammanfattning av ändringar":::
1. När profilen har konfigurerats i Visual Studio väljer du **Publicera**.

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/select-publish.png" alt-text="Välj publicera":::
2. På fliken där **Azure Function** -sidan är öppen väljer du  **funktioner** på den vänstra menyn. Bekräfta att funktionen **EventGridTriggerMigrateData** visas i listan. Om du inte ser det kan du prova att publicera från Visual Studio igen och sedan uppdatera sidan i portalen. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-function-creation.png" alt-text="Bekräfta skapande av funktion":::    

När du har publicerat funktionen är du redo att prenumerera på händelsen.

## <a name="subscribe-to-the-event"></a>Prenumerera på händelsen

1. I en ny flik eller ett nytt fönster med en webbläsare går du till [Azure Portal](https://portal.azure.com).
2. I Azure Portal väljer du **Resursgrupper** i den vänstra menyn. 
3. Filtrera listan över resursgrupper genom att ange namnet på resursgruppen i sökrutan. 
4. Välj din resursgrupp i listan.
1. Välj **Event Hubs namn området** i listan över resurser.
1. På sidan **Event Hubs namn område** väljer du **händelser** på den vänstra menyn och väljer sedan **+ händelse prenumeration** i verktygsfältet. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-add-subscription-link.png" alt-text="Länken Lägg till händelse prenumeration på sidan händelser för ett Event Hubs-namnområde":::
1. Följ dessa steg på sidan **Skapa händelse prenumeration** :
    1. Ange ett namn för **händelse prenumerationen**. 
    1. Ange ett namn för **system-ämnet**. Ett system ämne innehåller en slut punkt för avsändaren att skicka händelser. Mer information finns i [system avsnitt](../articles/event-grid/system-topics.md)
    1. I **typ av slut punkt** väljer du **Azure Function**.
    1. För **slut punkt** väljer du länken.
    1. På sidan **Välj Azure-funktion** följer du dessa steg om de inte fylls i automatiskt.
        1. Välj den Azure-prenumeration som innehåller Azure-funktionen. 
        1. Välj resurs grupp för funktionen. 
        1. Välj Function-appen.
        1. Välj distributions plats. 
        1. Välj funktionen **EventGridTriggerMigrateData**. 
    1. På sidan **Välj Azure-funktion** väljer du **Bekräfta markering**.
    1. Gå sedan tillbaka till sidan **Skapa händelse prenumeration** och välj **skapa**. 
    
        :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png" alt-text="Skapa en händelse prenumeration med hjälp av funktionen" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/event-subscription-select-function.png":::
1. Kontrol lera att händelse prenumerationen har skapats. Växla till fliken **händelse prenumerationer** på sidan **händelser** för Event Hubs namn området. 
    
    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png" alt-text="Bekräfta händelse prenumeration" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/confirm-event-subscription.png":::
1. Välj App Service plan (inte App Service) i listan över resurser i resurs gruppen. 

## <a name="run-the-app-to-generate-data"></a>Kör appen för att generera data
Du är klar med konfigurationen av händelsehubben, dedikera SQL-pool (tidigare SQL Data Warehouse), Azure Function-app och händelse prenumeration. Innan du kör ett program som genererar data för händelsehubben måste du konfigurera några värden.

1. Gå till resursgruppen som du gjorde tidigare i Azure Portal. 
2. Välj Event Hubs-namnområde.
3. På sidan **Event Hubs-namnområde** väljer du **Principer för delad åtkomst** i den vänstra menyn.
4. Välj **RootManageSharedAccessKey** i listan med principer. 

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/event-hub-namespace-shared-access-policies.png" alt-text="Sidan principer för delad åtkomst för ett Event Hubs-namnområde":::    
1. Välj kopieringsknappen intill textrutan **Primärnyckel för anslutningssträng**. 
1. Gå tillbaka till Visual Studio-lösningen. 
1. Högerklicka på **WindTurbineDataGenerator** Project och välj **Set as Startup Project (ange som start projekt**). 
1. Gå till projektet WindTurbineDataGenerator och öppna **program.cs**.
1. Ersätt `<EVENT HUBS NAMESPACE CONNECTION STRING>` med den anslutnings sträng som du kopierade från portalen. 
1. Ersätt `<EVENT HUB NAME>` med namnet på händelsehubben. 

   ```cs
   private const string EventHubConnectionString = "Endpoint=sb://demomigrationnamespace.servicebus.windows.net/...";
   private const string EventHubName = "hubdatamigration";
   ```
6. Skapa lösningen. Kör **WindTurbineGenerator.exe** -programmet. 
7. Efter ett par minuter, i fliken annan webbläsare där du har frågefönstret öppet, frågar du tabellen i informations lagret för migrerade data.

    ```sql
    select * from [dbo].[Fact_WindTurbineMetrics]    
    ```

    ![Frågeresultat](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

## <a name="monitor-the-solution"></a>Övervaka lösningen
Det här avsnittet hjälper dig att övervaka eller felsöka lösningen. 

### <a name="view-captured-data-in-the-storage-account"></a>Visa insamlade data i lagrings kontot
1. Navigera till resurs gruppen och välj det lagrings konto som används för att samla in händelse data. 
1. På sidan **lagrings konto** väljer du **Storage Explorer (för hands version**) på den vänstra menyn.
1. Expandera **BLOB-behållare** och välj **windturbinecapture**. 
1. Öppna mappen som heter samma som **Event Hubs namn område** i den högra rutan. 
1. Öppna mappen som heter samma som Event Hub (**hubdatamigration**). 
1. Gå igenom mapparna och se AVRO-filerna. Här är ett exempel:

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png" alt-text="Insamlad fil i lagringen" lightbox="media/event-grid-event-hubs-functions-synapse-analytics/storage-captured-file.png":::
    

### <a name="verify-that-the-event-grid-trigger-invoked-the-function"></a>Kontrol lera att den Event Grid utlösaren anropade funktionen
1. Navigera till resurs gruppen och välj Function-appen. 
1. Välj **funktioner** på den vänstra menyn.
1. Välj funktionen **EventGridTriggerMigrateData** i listan. 
1. På **funktions** sidan väljer du **övervaka** på den vänstra menyn. 
1. Välj **Konfigurera** för att konfigurera Application Insights för att avbilda anrops loggar. 
1. Skapa en ny **Application Insights** resurs eller Använd en befintlig resurs. 
1. Gå tillbaka till **Monitor** -sidan för funktionen. 
1. Bekräfta att klient programmet (**WindTurbineDataGenerator**) som skickar händelserna fortfarande körs. Annars kör du appen. 
1. Vänta några minuter (5 minuter eller mer) och välj knappen **Uppdatera** för att se funktions anrop.    

    :::image type="content" source="media/event-grid-event-hubs-functions-synapse-analytics/function-invocations.png" alt-text="Funktions anrop":::
1. Välj ett anrop för att se information.

    Event Grid distribuerar händelsedata till prenumeranterna. I följande exempel visar genererade händelsedata när data som strömmas via en händelsehubb hämtas i en blobb. Observera särskilt att egenskapen `fileUrl` i `data`-objektet pekar på blobben i lagringen. Funktionsappen använder den här URL:en för att hämta blobbfilen med insamlade data.

    ```json
    {
        "topic": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourcegroups/rgDataMigration/providers/Microsoft.EventHub/namespaces/spehubns1207",
        "subject": "hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "id": "4538f1a5-02d8-4b40-9f20-36301ac976ba",
        "data": {
            "fileUrl": "https://spehubstorage1207.blob.core.windows.net/windturbinecapture/spehubns1207/hubdatamigration/0/2020/12/07/21/49/12.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "0",
            "sizeInBytes": 473444,
            "eventCount": 2800,
            "firstSequenceNumber": 55500,
            "lastSequenceNumber": 58299,
            "firstEnqueueTime": "2020-12-07T21:49:12.556Z",
            "lastEnqueueTime": "2020-12-07T21:50:11.534Z"
        },
        "dataVersion": "1",
        "metadataVersion": "1",
        "eventTime": "2020-12-07T21:50:12.7065524Z"
    }
    ```

### <a name="verify-that-the-data-is-stored-in-the-dedicated-sql-pool"></a>Verifiera att data lagras i den dedikerade SQL-poolen
I webbläsare-fliken där du har frågefönstret öppet frågar du tabellen i din dedikerade SQL-pool för migrerade data.

![Frågeresultat](media/event-grid-event-hubs-functions-synapse-analytics/query-results.png)

