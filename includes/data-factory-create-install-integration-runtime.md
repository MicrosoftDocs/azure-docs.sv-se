---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510285"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Skapa en lokal Integration Runtime

I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. Integration runtime med egen värd är den komponent som kopierar data från SQL Server på din dator till Azure SQL Database. 

1. Skapa en variabel för namnet på din Integration Runtime. Använd ett unikt namn och anteckna det. Du använder det senare i den här självstudien. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Skapa Integration Runtime med egen värd. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Här är exempel på utdata:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Kör följande kommando för att hämta statusen för din skapade Integration Runtime. Kontrollera att värdet för egenskapen **State** är inställd på **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Här är exempel på utdata:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Kör följande kommando som används för att hämta autentiseringsnycklarna för att registrera en lokal Integration Runtime med Azure Data Factory-tjänsten i molnet: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Här är exempel på utdata:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Kopiera en av nycklarna (uteslut de dubbla citattecknen) för att registrera den lokala installation av Integration Runtime som du installerar på datorn i följande steg.  

## <a name="install-the-integration-runtime-tool"></a>Installera integration runtime-verktyget

1. Om du redan har Integration Runtime på din dator ska du avinstallera det med **Lägg till eller ta bort program**. 

2. [Ladda ned](https://www.microsoft.com/download/details.aspx?id=39717) Integration Runtime med egen värd på en lokal Windows-dator. Kör installationen.

3. På sidan **Välkommen till Microsoft Integration Runtime** klickar du på **Nästa**.

4. På sidan med **licensavtalet för slutanvändare** godkänner du villkoren och väljer du **Nästa**.

5. På sidan **Målmapp** väljer du **Nästa**.

6. På sidan **Klar att installera Microsoft Integration Runtime** väljer du **Installera**.

7. På sidan **Slutfört installationen av Microsoft Integration Runtime** väljer du **Slutför**.

8. Klistra in den nyckel som du sparade i föregående avsnitt på sidan **Registrera Integration Runtime (lokal installation)** och välj **Registrera**. 

    ![Registrera Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. På sidan **ny integration Runtime (lokal installation) nod** väljer du **Slutför**. 

10. När integration runtime med egen värd har registrerats ser du följande meddelande:

    ![Registered successfully (Registrerat)](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. På sidan **Registrera Integration Runtime (lokal installation)** väljer du **Starta konfigurationshanteraren**.

15. När noden är ansluten till molntjänsten ser du följande sida:

    ![Sidan Noden är ansluten](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Testa nu anslutningen till din SQL Server-databasen.

    ![Fliken Diagnostik](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. I fönstret **Konfigurationshanteraren** växlar du till fliken **Diagnostik**.

    b. Välj **SqlServer** som typ av datakälla.

    c. Ange servernamnet.

    d. Ange namnet på databasen.

    e. Välj autentiseringsläge.

    f. Ange användarnamnet.

    ex. Ange lösen ordet som är kopplat till för användar namnet.

    h. Klicka på **Test** för att bekräfta att Integration Runtime kan ansluta till SQL Server. Du ser en grön bockmarkering om anslutningen är klar. Om anslutningen inte lyckats får du ett felmeddelande. Åtgärda eventuella problem och se till att Integration Runtime kan ansluta till SQL Server.    

    > [!NOTE]
    > Anteckna värdena för autentiseringstyp, server, databas, användare och lösenord. Du använder det senare i den här självstudien.
