---
title: Flytta Azure Analysis Services till en annan region | Microsoft Docs
description: Beskriver hur du flyttar en Azure Analysis Services-resurs till en annan region.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 049ff6d14c3967481eb73037814082fa261154e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96497936"
---
# <a name="move-analysis-services-to-a-different-region"></a>Flytta Analysis Services till en annan region

Den här artikeln beskriver hur du flyttar en Analysis Services server resurs till en annan Azure-region. Du kan flytta servern till en annan region av olika anledningar, till exempel för att dra nytta av en Azure-region närmare användare, för att använda Service planer som stöds i vissa regioner eller för att uppfylla interna krav och styrnings krav. 

I det här och tillhör ande länkade artiklar lär du dig att:

> [!div class="checklist"]
> 
> * Säkerhetskopiera en käll server modell databas till [Blob Storage](../storage/blobs/storage-blobs-introduction.md).
> * Exportera en [resurs mal len](../azure-resource-manager/templates/overview.md)för käll servern.
> * Hämta en [signatur för delad åtkomst för lagring (SAS)](../storage/common/storage-sas-overview.md).
> * Ändra resurs mal len.
> * Distribuera mallen för att skapa en ny mål server.
> * Återställ en modell databas till den nya mål servern.
> * Verifiera den nya mål servern och databasen.
> * Ta bort käll servern.

Den här artikeln beskriver hur du använder en mall för att migrera en enskild Analysis Services-server med en **grundläggande konfiguration** till en annan region *och* resurs grupp i samma prenumeration. Om du använder en mall behåller du konfigurerade Server egenskaper som garanterar att mål servern är konfigurerad med samma egenskaper, förutom region och resurs grupp, som käll Server. Den här artikeln beskriver inte hur du flyttar associerade resurser som kan ingå i samma resurs grupp, till exempel data källa, lagring och gateway-resurser. 

Innan du flyttar en server till en annan region rekommenderar vi att du skapar en detaljerad plan. Överväg ytterligare resurser, till exempel gateways och lagring som kan behöva flyttas. Med en plan är det viktigt att slutföra en eller flera utvärderings åtgärder med test servrar innan du flyttar en produktions server.

> [!IMPORTANT]
> Klient program och anslutnings strängar ansluter till Analysis Services med hjälp av det fullständiga Server namnet, som är en URI som innehåller den region som servern finns i. Till exempel `asazure://westcentralus.asazure.windows.net/advworks01`. När du flyttar en server till en annan region skapar du effektivt en ny server resurs i en annan region, vilket kommer att ha en annan region i Server namns-URI: n. Klient program och anslutnings strängar som används i skript måste ansluta till den nya servern med hjälp av den nya server namns-URI: n. Användning av ett [Server namns Ali Aset](analysis-services-server-alias.md) kan minska antalet platser som server namn-URI: n måste ändras, men måste implementeras innan en region flyttas.

> [!IMPORTANT]
> Azure-regioner använder olika IP-adressintervall. Om du har konfigurerat brand Väggs undantag för regionen som servern och/eller lagrings kontot finns i, kan det vara nödvändigt att konfigurera ett annat IP-adressintervall. Mer information finns i [vanliga frågor och svar om Analysis Services nätverks anslutning](analysis-services-network-faq.md).

> [!NOTE]
> Den här artikeln beskriver hur du återställer en säkerhets kopia av en databas till en mål server från en lagrings behållare i käll serverns region. I vissa fall kan återställning av säkerhets kopior från en annan region ha dåliga prestanda, särskilt för stora databaser. För bästa prestanda under återställning av databasen, migrera eller skapa en ny lagrings behållare i mål server regionen. Kopiera. ABF-säkerhetskopierade filer från lagrings behållaren för käll regionen till lagrings behållaren för mål regionen innan du återställer databasen till mål servern. Även om det finns utanför räckvidden för den här artikeln, i vissa fall, särskilt med stora databaser, skript för en databas från käll servern, återskapa och sedan bearbetning på mål servern för att läsa in databas data kan vara mer kostnads effektivt än att använda säkerhets kopiering/återställning.

> [!NOTE]
> Om du använder en lokal datagateway för att ansluta till data källor måste du också flytta Gateway-resursen till mål server regionen. Mer information finns i [Installera och konfigurera en lokal datagateway](analysis-services-gateway-install.md).

## <a name="prerequisites"></a>Förutsättningar

- **Azure Storage-konto**: krävs för att lagra en. ABF säkerhets kopierings fil.
- **SQL Server Management Studio (SSMS)**: krävs för att säkerhetskopiera och återställa modell databaser.
- **Azure PowerShell**. Krävs endast om du väljer att slutföra den här uppgiften med hjälp av PowerShell.

## <a name="prepare"></a>Förbereda

### <a name="backup-model-databases"></a>Säkerhetskopiera modell databaser

Om **lagrings inställningarna** inte redan har kon figurer ATS för käll servern följer du stegen i [Konfigurera lagrings inställningar](analysis-services-backup.md#configure-storage-settings).

När lagrings inställningarna har kon figurer ATS följer du stegen i [säkerhets kopieringen](analysis-services-backup.md#backup) för att skapa en modell databas. ABF säkerhets kopia i din lagrings behållare. Du kan senare återställa. ABF-säkerhetskopian till den nya mål servern.


### <a name="export-template"></a>Exportera mall

Mallen innehåller konfigurations egenskaper för käll servern.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här exporterar du en mall med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla resurser** och välj sedan din Analysis Services-server.

3. Välj   >  **Exportera mall** för > inställningar.

4. Välj **Hämta** på bladet **Exportera mall** .

5. Leta upp zip-filen som du laddade ned från portalen och packa sedan upp filen i en mapp.

   Zip-filen innehåller JSON-filerna som utgör den mall och de parametrar som krävs för att distribuera en ny server.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Exportera en mall med hjälp av PowerShell:

1. Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) och följ anvisningarna på skärmen:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Om din identitet är associerad med fler än en prenumeration ställer du in din aktiva prenumeration på prenumerationen på den server resurs som du vill flytta.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Exportera mallen för käll servern. De här kommandona sparar en JSON-mall med ResourceGroupName som fil namn i din aktuella katalog.

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

När du distribuerar en mål server från en mall krävs en SAS-token för användaren (som en URI) för att ange den lagrings behållare som innehåller säkerhets kopian av databasen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här hämtar du en signatur för delad åtkomst med hjälp av portalen:

1. I portalen väljer du det lagrings konto som används för att säkerhetskopiera Server databasen.

2. Välj **Storage Explorer** och expandera sedan **BLOB-behållare**. 

3. Högerklicka på lagrings behållaren och välj sedan **Hämta signatur för delad åtkomst**.

    :::image type="content" source="media/move-between-regions/get-sas.png" alt-text="Hämta SAS":::

4. I **signaturen för delad åtkomst** väljer du **skapa**. Som standard upphör SAS att gälla om 24 timmar.

5. Kopiera och spara **URI: n**. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill få en signatur för delad åtkomst med hjälp av PowerShell följer du stegen i [skapa en användar Delegerings-SAS för en behållare eller BLOB med PowerShell](../storage/blobs/storage-blob-user-delegation-sas-create-powershell.md#create-a-user-delegation-sas-for-a-blob).

---

### <a name="modify-the-template"></a>Ändra mallen

Använd en text redigerare för att ändra template.jspå filen som du har exporterat, ändra egenskaperna för region och blob-behållare. 

Ändra mallen:

1. I en text redigerare, i egenskapen **location** , anger du den nya mål regionen. I egenskapen **backupBlobContainerUri** klistrar du in URI för lagrings behållare med SAS-nyckel. 

    I följande exempel anges mål regionen för servern advworks1 till `South Central US` och anger URI för lagrings behållare med signaturen för delad åtkomst: 

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

Information om hur du skaffar Azure-regioner finns i [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/). Kör kommandot [Get-AzLocation](/powershell/module/az.resources/get-azlocation) för att hämta regioner med hjälp av PowerShell.

```azurepowershell-interactive
   Get-AzLocation | format-table 
```

## <a name="move"></a>Flytta

Om du vill distribuera en ny server resurs i en annan region använder du **template.js** filen som du exporterade och ändrade i föregående avsnitt.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I portalen väljer du **skapa en resurs**.

2. I **Sök på Marketplace** skriver du **mall distribution** och trycker sedan på **RETUR**.

3. Välj **malldistribution**.

4. Välj **Skapa**.

5. Välj **Bygg en egen mall i redigeraren**.

6. Välj **Läs in fil** och följ sedan anvisningarna för att läsa in **template.jspå** filen som du exporterade och ändrade.

7. Kontrol lera att rätt egenskaper för den nya mål servern visas i mal Lav redigeraren.

8. Välj **Spara**.

9. Ange eller Välj egenskaps värden:

    - **Prenumeration**: Välj Azure-prenumerationen.
    
    - **Resurs grupp**: Välj **Skapa ny** och ange sedan ett resurs grupps namn. Du kan välja en befintlig resurs grupp förutsatt att den inte redan innehåller en Analysis Services-server med samma namn.
    
    - **Plats**: Välj samma region som du angav i mallen.

10. Välj **Granska och skapa**.

11. Granska de allmänna villkoren och välj sedan **skapa**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommandon för att distribuera mallen:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. South Central US)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<local template file>"  
   ```
---

### <a name="get-target-server-uri"></a>Hämta mål serverns URI

För att kunna ansluta till den nya mål servern från SSMS för att återställa modell databasen måste du hämta den nya mål serverns URI.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här hämtar du Server-URI i portalen:

1. I portalen går du till den nya mål server resursen.

2. På sidan **Översikt** kopierar du **Server namn** -URI: n.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd följande kommandon för att hämta server-URI med hjälp av PowerShell:

```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $serverName = Read-Host -Prompt "Enter the server name (i.e. advworks2)"

   Get-AzAnalysisServicesServer -ResourceGroupName $resourceGroupName -Name "$serverName"
```
Kopiera **ServerFullName** från utdata.

---

### <a name="restore-model-database"></a>Återställ modell databas

Följ stegen som beskrivs i [restore](analysis-services-backup.md#restore) för att återställa modell databasen. ABF säkerhets kopiering till den nya mål servern.

Valfritt: när du har återställt modell databasen bearbetar du modellen och tabellerna för att uppdatera data från data källor. För att bearbeta modellen och tabellen med SSMS:

1. I SSMS högerklickar du på modell databasen > **process databas**.

2. Expandera **tabeller**, högerklicka på en tabell. I **process tabeller**, väljer du alla tabeller och väljer sedan **OK**.

## <a name="verify"></a>Verifiera

1. I portalen går du till den nya mål servern.

2. På sidan Översikt går du till **modeller på Analysis Services server** och kontrollerar att återställda modeller visas.

3. Använd ett klient program som Power BI eller Excel för att ansluta till modellen på den nya servern. Kontrol lera att modell objekt som tabeller, mått och hierarkier visas. 

4. Kör eventuella Automation-skript. Verifiera att de har körts.

Valfritt: [Alm Toolkit](http://alm-toolkit.com/) är ett verktyg med *öppen källkod* som används för att jämföra och hantera Power BI data uppsättningar *och* Analysis Services tabell modell databaser. Använd verktygs verktyget för att ansluta till både käll-och mål Server databaser och jämför. Om migreringen av databasen lyckas, är modell objekt samma definition. 

:::image type="content" source="media/move-between-regions/alm-toolkit.png" alt-text="ALM Toolkit":::

## <a name="clean-up-resources"></a>Rensa resurser

När du har verifierat att klient program kan ansluta till den nya servern och eventuella automatiserade skript körs på rätt sätt, tar du bort käll servern. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Ta bort käll servern från portalen:

På sidan **Översikt** för käll servern väljer du **ta bort**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort käll servern med hjälp av PowerShell använder du kommandot Remove-AzAnalysisServicesServer.

```azurepowershell-interactive
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

---

> [!NOTE]
> När du har flyttat en region rekommenderar vi att den nya mål servern använder en lagrings behållare i samma region för säkerhets kopiering, i stället för lagrings behållaren i käll Server regionen.