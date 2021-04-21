---
title: Flytta Azure Analysis Services till en annan region | Microsoft Docs
description: Beskriver hur du flyttar en Azure Analysis Services resurs till en annan region.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: 2b698ffaddb4bc818eaabda34022ab58ff05fe5f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786359"
---
# <a name="move-analysis-services-to-a-different-region"></a>Flytta Analysis Services till en annan region

I den här artikeln beskrivs hur du flyttar Analysis Services en serverresurs till en annan Azure-region. Du kan flytta servern till en annan region av flera olika skäl, till exempel för att dra nytta av en Azure-region närmare användarna, för att använda tjänstplaner som stöds endast i specifika regioner eller för att uppfylla interna princip- och styrningskrav. 

I den här och associerade länkade artiklar får du lära dig att:

> [!div class="checklist"]
> 
> * Säkerhetskopiera en källservermodelldatabas till [Blob Storage](../storage/blobs/storage-blobs-introduction.md).
> * Exportera en [källserverresursmall](../azure-resource-manager/templates/overview.md).
> * Hämta en signatur [för delad åtkomst för lagring (SAS).](../storage/common/storage-sas-overview.md)
> * Ändra resursmallen.
> * Distribuera mallen för att skapa en ny målserver.
> * Återställ en modelldatabas till den nya målservern.
> * Kontrollera den nya målservern och databasen.
> * Ta bort källservern.

I den här artikeln beskrivs hur du använder en resursmall för att migrera en enskild Analysis Services-server med en grundläggande *konfiguration* till en annan region och resursgrupp i samma prenumeration.  Om du använder en mall behålls konfigurerade serveregenskaper som säkerställer att målservern är konfigurerad med samma egenskaper, förutom region och resursgrupp, som källservern. Den här artikeln beskriver inte flytt av associerade resurser som kan ingå i samma resursgrupp, till exempel datakälla, lagring och gatewayresurser. 

Innan du flyttar en server till en annan region rekommenderar vi att du skapar en detaljerad plan. Överväg ytterligare resurser, till exempel gatewayer och lagring, som också kan behöva flyttas. Med alla planer är det viktigt att utföra en eller flera försöksflyttningsåtgärder med hjälp av testservrar innan du flyttar en produktionsserver.

> [!IMPORTANT]
> Klientprogram och anslutningssträngar ansluter till Analysis Services med det fullständiga servernamnet, vilket är en URI som innehåller den region där servern finns. Till exempel `asazure://westcentralus.asazure.windows.net/advworks01`. När du flyttar en server till en annan region skapar du i praktiken en ny serverresurs i en annan region, som har en annan region i servernamnets URI. Klientprogram och anslutningssträngar som används i skript måste ansluta till den nya servern med hjälp av den nya servernamnets URI. Genom att [använda ett servernamnsalias](analysis-services-server-alias.md) kan du minska antalet platser som servernamnets URI måste ändras på, men måste implementeras innan en region flyttas.

> [!IMPORTANT]
> Azure-regioner använder olika IP-adressintervall. Om du har konfigurerat brandväggundantag för den region där servern och/eller lagringskontot finns kan det vara nödvändigt att konfigurera ett annat IP-adressintervall. Mer information finns i [Vanliga frågor och svar om Analysis Services nätverksanslutning.](analysis-services-network-faq.md)

> [!NOTE]
> I den här artikeln beskrivs hur du återställer en säkerhetskopia av databasen till en målserver från en lagringscontainer i källserverns region. I vissa fall kan återställning av säkerhetskopior från en annan region ha sämre prestanda, särskilt för stora databaser. För bästa prestanda under databasåterställning bör du migrera eller skapa en ny lagringscontainer i målserverregionen. Kopiera .abf-säkerhetskopieringsfilerna från lagringscontainern för källregionen till målregionens lagringscontainer innan du återställer databasen till målservern. Även om det ligger utanför omfånget för den här artikeln kan det i vissa fall, särskilt med mycket stora databaser, vara mer kostnadseffektivt att skriva ut en databas från källservern, återskapa och sedan bearbeta på målservern för att läsa in databasdata än att använda säkerhetskopiering/återställning.

> [!NOTE]
> Om du använder en lokal datagateway för att ansluta till datakällor måste du också flytta gatewayresursen till målserverregionen. Mer information finns i [Installera och konfigurera en lokal datagateway.](analysis-services-gateway-install.md)

## <a name="prerequisites"></a>Förutsättningar

- **Azure Storage-konto:** Krävs för att lagra en .abf-säkerhetskopia.
- **SQL Server Management Studio (SSMS): Krävs** för att säkerhetskopiera och återställa modelldatabaser.
- **Azure PowerShell**. Krävs endast om du väljer att slutföra den här uppgiften med hjälp av PowerShell.

## <a name="prepare"></a>Förbereda

### <a name="backup-model-databases"></a>Säkerhetskopiera modelldatabaser

Om **lagringsinställningarna** inte redan har konfigurerats för källservern följer du stegen i [Konfigurera lagringsinställningar.](analysis-services-backup.md#configure-storage-settings)

När lagringsinställningarna har konfigurerats följer du stegen i Säkerhetskopiering [för](analysis-services-backup.md#backup) att skapa en .abf-säkerhetskopia av modelldatabasen i lagringscontainern. Du kommer senare att återställa ABF-säkerhetskopian till den nya målservern.


### <a name="export-template"></a>Exportera mall

Mallen innehåller konfigurationsegenskaper för källservern.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här exporterar du en mall med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **Alla resurser** och välj sedan din Analysis Services server.

3. Välj > **Inställningar Exportera**  >  **mall**.

4. Välj **Ladda** ned på **bladet Exportera** mall.

5. Leta upp ZIP-filen som du laddade ned från portalen och packa upp filen till en mapp.

   ZIP-filen innehåller de .json-filer som utgör mallen och de parametrar som krävs för att distribuera en ny server.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Så här exporterar du en mall med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration [med kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med fler än en prenumeration anger du din aktiva prenumeration till prenumerationen på den serverresurs som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för källservern. De här kommandona sparar en json-mall med ResourceGroupName som filnamn i din aktuella katalog.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <server-account-name> `
     -ResourceType Microsoft.AnalysisServices/servers
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```
---

### <a name="get-storage-shared-access-signature-sas"></a>Hämta signatur för delad åtkomst för lagring (SAS)

När du distribuerar en målserver från en mall krävs en SAS-token för användardelegering (som en URI) för att ange lagringscontainern som innehåller säkerhetskopian av databasen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här hämtar du en signatur för delad åtkomst med hjälp av portalen:

1. I portalen väljer du det lagringskonto som används för att säkerhetskopiera serverdatabasen.

2. Välj **Storage Explorer** och expandera sedan **BLOBCONTAINRAR.** 

3. Högerklicka på lagringscontainern och välj sedan **Hämta signatur för delad åtkomst.**

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Hämta SAS":::

4. I **Signatur för delad åtkomst** väljer du **Skapa**. Sas upphör som standard att gälla om 24 timmar.

5. Kopiera och spara **URI:en**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill hämta en signatur för delad åtkomst med hjälp av PowerShell följer du stegen i Skapa en SAS för användardelegering för en [container eller blob med PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Ändra mallen

Använd en textredigerare för att ändra template.jspå filen du exporterade och ändra egenskaperna för region och blobcontainer. 

Så här ändrar du mallen:

1. I en textredigerare går du till **platsegenskapen** och anger den nya målregionen. I egenskapen **backupBlobContainerUri klistrar** du in lagringscontainerns URI med SAS-nyckeln. 

    I följande exempel anges målregionen för servern advworks1 till och `South Central US` lagringscontainerns URI med signatur för delad åtkomst: 

    ```json
    "resources": [
        {
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "name": "[parameters('servers_advworks1_name')]",
            "location": "South Central US",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "asadmins@adventure-works.com"
                    ]
                },
                "backupBlobContainerUri": "https://storagenorthcentralus.blob.core.windows.net/backup?sp=rl&st=2020-06-01T19:30:42Z&se=2020-06-02T19:30:42Z&sv=2019-10-10&sr=c&sig=PCQ4s9RujJkxu89gO4tiDTbE3%2BFECx6zAdcv8x0cVUQ%3D",
                "querypoolConnectionMode": "All"
            }
        }
    ]         
    ```
2. Spara mallen.

#### <a name="regions"></a>Regioner

Information om hur du hämtar Azure-regioner [finns i Azure-platser.](https://azure.microsoft.com/global-infrastructure/locations/) Kör kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation) för att hämta regioner med hjälp av PowerShell.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Flytta

Om du vill distribuera en ny serverresurs i en annan region använder dutemplate.js **på** filen som du exporterade och ändrade i föregående avsnitt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I portalen väljer du **Skapa en resurs**.

2. I **Sök på Marketplace** skriver du **malldistribution och** trycker sedan på **RETUR.**

3. Välj **Malldistribution**.

4. Välj **Skapa**.

5. Välj **Skapa en egen mall i redigeraren**.

6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa intemplate.jspå **filen** som du exporterade och ändrade.

7. Kontrollera att mallredigeraren visar rätt egenskaper för den nya målservern.

8. Välj **Spara**.

9. Ange eller välj egenskapsvärdena:

    - **Prenumeration**: Välj Azure-prenumerationen.
    
    - **Resursgrupp:** Välj **Skapa ny** och ange sedan ett resursgruppnamn. Du kan välja en befintlig resursgrupp förutsatt att den inte redan innehåller Analysis Services server med samma namn.
    
    - **Plats:** Välj samma region som du angav i mallen.

10. Välj **Granska och skapa.**

11. Granska villkoren och grunderna och välj sedan **Skapa.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd de här kommandona för att distribuera mallen:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Hämta målserverns URI

För att kunna ansluta till den nya målservern från SSMS för att återställa modelldatabasen måste du hämta den nya målserverns URI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här hämtar du server-URI:en i portalen:

1. I portalen går du till den nya målserverresursen.

2. På sidan **Översikt** kopierar du **Servernamns-URI.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommandon för att hämta serverns URI med hjälp av PowerShell:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Kopiera **ServerFullName** från utdata.

---

### <a name="restore-model-database"></a>Återställa modelldatabasen

Följ stegen som beskrivs i [Återställa](analysis-services-backup.md#restore) för att återställa modelldatabasen .abf-säkerhetskopia till den nya målservern.

Valfritt: När du har återställt modelldatabasen bearbetar du modellen och tabellerna för att uppdatera data från datakällor. Bearbeta modellen och tabellen med hjälp av SSMS:

1. I SSMS högerklickar du på modelldatabasen > **Process Database**.

2. Expandera **Tabeller** och högerklicka på en tabell. I **Processtabeller väljer** du alla tabeller och sedan **OK.**

## <a name="verify"></a>Verifiera

1. I portalen går du till den nya målservern.

2. På sidan Översikt går du till **Modeller på Analysis Services server** och kontrollerar att återställda modeller visas.

3. Använd ett klientprogram som Power BI eller Excel för att ansluta till modellen på den nya servern. Kontrollera att modellobjekt som tabeller, mått och hierarkier visas. 

4. Kör eventuella automatiseringsskript. Kontrollera att de har körts.

Valfritt: [ALM Toolkit](http://alm-toolkit.com/) är *ett* verktyg med öppen källkod för att jämföra och hantera Power BI datauppsättningar *och* Analysis Services-tabellmodelldatabaser. Använd verktygslådan för att ansluta till både käll- och målserverdatabaser och jämföra. Om databasmigrering lyckas kommer modellobjekten att ha samma definition. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har verifierat att klientprogrammen kan ansluta till den nya servern och alla automatiseringsskript körs korrekt tar du bort källservern. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort källservern från portalen:

På källserverns **översiktssida väljer** du Ta **bort**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort källservern med hjälp av PowerShell använder du Remove-AzAnalysisServicesServer kommandot.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> När du har slutfört en regionflyttning rekommenderar vi att den nya målservern använder en lagringscontainer i samma region för säkerhetskopieringar, i stället för lagringscontainern i källserverregionen.
