---
title: Så här tar du bort och exporterar personliga data från Azure DevTest Labs
description: Lär dig hur du tar bort och exporterar personliga data från Azure DevLast Labs-tjänsten för att stödja dina skyldigheter enligt allmän dataskyddsförordning (GDPR).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2c44b2f3aa6f2dfad18ed53804842a5dad8bd94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483524"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportera eller ta bort personliga data från Azure DevTest Labs
Den här artikeln innehåller anvisningar för att ta bort och exportera personliga data från Azure DevTest Labss tjänsten. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Vilka person uppgifter samlar in DevTest Labs?
DevTest Labs samlar in två huvud delar av person uppgifter från användaren. De är: användarens e-postadress och ID för användar objekt. Den här informationen är viktig för tjänsten för att tillhandahålla funktioner i tjänsten för labb administratörer och labb användare.

### <a name="user-email-address"></a>Användarens e-postadress
DevTest Labs använder användarens e-postadress för att skicka e-postmeddelanden för automatisk avstängning till labb användare. E-postmeddelandet meddelar användare av datorn att de stängs av. Användarna kan antingen skjuta upp eller hoppa över avstängningen om de vill göra det. Du konfigurerar e-postadressen på labb nivån eller på VM-nivå.

**Ställa in e-post i labbet:**

![Ställa in e-post på labb nivån](./media/personal-data-delete-export/lab-user-email.png)

**Ställer in e-post på den virtuella datorn:**

![Ställa in e-post på VM-nivå](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Användar objekt-ID
DevTest Labs använder user Object ID för att Visa månads månads kostnads trender och kostnad per resurs information till labb administratörer. Det gör det möjligt för dem att spåra kostnader och hantera tröskelvärden för deras labb. 

**Beräknad kostnads trend för den aktuella kalender månaden:** 
 ![ Uppskattad kostnads trend för den aktuella kalender månaden](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Beräknad månad till dags kostnad per resurs:** 
 ![ Beräknad månad till dags kostnad per resurs](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Varför behöver vi dessa personliga data?
DevTest Labs-tjänsten använder personliga data i drift syfte. Dessa data är viktiga för att tjänsten ska tillhandahålla viktiga funktioner. Om du anger en bevarande princip för användarens e-postadress får Lab-användare inte tid för automatisk avstängning av e-postmeddelanden när deras e-postadress tas bort från systemet. På samma sätt kan inte labb administratören Visa månads månads kostnads trender och kostnad per resurs för datorer i sina labb om användar objekt-ID: n tas bort baserat på en bevarande princip. Dessa data måste därför behållas så länge som användarens resurs är aktiv i labbet.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Hur kan jag glömma att systemet glömmer mina personliga data?
Om du vill ha dessa personliga data borttagna som labb användare kan du göra det genom att ta bort motsvarande resurs i labbet. DevTest Labs-tjänsten anonymiseras uppgifterna den borttagna personliga informationen 30 dagar efter att den tagits bort av användaren.

Om du till exempel tar bort din virtuella dator eller tar bort din e-postadress, anonymiseras uppgifterna DevTest Labs-tjänsten dessa data 30 dagar efter att resursen har tagits bort. Lagrings principen på 30 dagar efter borttagningen är att se till att vi tillhandahåller en riktig kostnads projektion månads månad för labb administratören.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Hur kan jag begära en export av mina personliga data?
Du kan exportera personliga och labb användnings data med hjälp av Azure Portal eller PowerShell. Data exporteras som två olika CSV-filer:

- **disks.csv** -innehåller information om diskarna som används av de olika virtuella datorerna
- **virtualmachines.csv** -innehåller information om de virtuella datorerna i labbet.

### <a name="azure-portal"></a>Azure Portal
Som labb användare kan du begära en export av de personliga data som DevTest Labs-tjänsten lagrar. Om du vill begära en export navigerar du till alternativet **personliga data** på sidan **Översikt** i labbet. Om du väljer knappen för att **Exportera** filer på din labb administratörs lagrings konto. Du kan sedan kontakta din labb administratör för att visa dessa data.

1. Välj **personliga data** på den vänstra menyn. 

    ![Sidan personliga data](./media/personal-data-delete-export/personal-data-page.png)
2. Välj den **resurs grupp** som innehåller labbet.

    ![Välj resursgrupp](./media/personal-data-delete-export/select-resource-group.png)
3. Välj **lagrings kontot** i resurs gruppen.
4. På sidan **lagrings konto** väljer du **blobbar**.

    ![Panelen Välj blobbar](./media/personal-data-delete-export/select-blobs-tile.png)
5. Välj behållaren med namnet **labresourceusage** i listan över behållare.

    ![Välj BLOB-behållare](./media/personal-data-delete-export/select-blob-container.png)
6. Välj **mappen** som heter efter ditt labb. Du hittar **CSV** -filer för **diskar** och **virtuella datorer** i labbet i den här mappen. Du kan hämta dessa CSV-filer, filtrera innehållet för labb användaren som begär en åtkomst och dela den med dem.

    ![Ladda ned CSV-fil](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell"></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage="The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Huvud komponenterna i exemplet ovan är:

- Kommandot Invoke-AzureRmResourceAction.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Två åtgärds parametrar
    - **blobStorageAbsoluteSasUri** – lagrings kontots URI med SAS-token (signatur för delad åtkomst). I PowerShell-skriptet kan det här värdet skickas i stället för lagrings nyckeln.
    - **usageStartDate** – start datum för hämtning av data, med slutdatumet det aktuella datumet då åtgärden körs. Granularitet är på dags nivå, så även om du lägger till tidsinformation kommer den att ignoreras.

### <a name="exported-data---a-closer-look"></a>Exporterade data – ett närmare utseende
Nu ska vi ta en närmare titt på exporterade data. Som tidigare nämnts kommer det att finnas två CSV-filer när data har exporter ATS. 

**virtualmachines.csv** innehåller följande data kolumner:

| Kolumnnamn | Description |
| ----------- | ----------- | 
| SubscriptionId | Prenumerations-ID: n som labbet finns i. |
| LabUId | Unikt GUID-ID för labbet. |
| LabName | Labbets namn. |
| LabResourceId | Fullständigt kvalificerat labb resurs-ID. |
| ResourceGroupName | Namnet på den resurs grupp som innehåller den virtuella datorn | 
| ResourceId | Fullständigt kvalificerat resurs-ID för den virtuella datorn. |
| ResourceUId | GUID för den virtuella datorn |
| Name | Namn på virtuell dator. |
| CreatedTime | Datum/tid då den virtuella datorn skapades. |
| DeletedDate | Datum/tid då den virtuella datorn togs bort. Om den är tom har borttagningen ännu inte skett. |
| ResourceOwner | Den virtuella datorns ägare. Om värdet är tomt, är det antingen en virtuell dator som kan ha skapats eller skapats av ett huvud namn för tjänsten. |
| PricingTier | Den virtuella datorns pris nivå |
| ResourceStatus | Tillgänglighets status för den virtuella datorn. Aktiv, om den virtuella datorn har tagits bort, om den fortfarande finns eller är inaktiv. |
| ComputeResourceId | Fullständigt kvalificerat resurs-ID för beräkning av virtuella datorer. |
| Ledig | Ange som sant om den virtuella datorn är en virtuell dator med anspråk | 
| EnvironmentId | Den miljö resurs identifierare inom vilken den virtuella datorn skapades. Den är tom när den virtuella datorn inte skapades som en del av en miljö resurs. |
| ExpirationDate | Förfallo datum för den virtuella datorn. Den har angetts till Tom, om ett förfallo datum inte har angetts.
| GalleryImageReferenceVersion |  Version av bas avbildningen för virtuella datorer. |
| GalleryImageReferenceOffer | Erbjudande för bas avbildningen för virtuella datorer. |
| GalleryImageReferencePublisher | Utgivare av bas avbildningen för virtuella datorer. |
| GalleryImageReferenceSku | SKU för den virtuella datorns bas avbildning |
| GalleryImageReferenceOsType | OS-typ för bas avbildningen för virtuella datorer |
| CustomImageId | Fullständigt kvalificerat ID för den anpassade VM-avbildningen. |

De data kolumner som finns i **disks.csv** visas nedan:

| Kolumnnamn | Description | 
| ----------- | ----------- | 
| SubscriptionId | ID för den prenumeration som innehåller labbet |
| LabUId | GUID för labbet |
| LabName | Labbets namn | 
| LabResourceId | Fullständigt kvalificerat resurs-ID för labbet | 
| ResourceGroupName | Namnet på den resurs grupp som innehåller labbet | 
| ResourceId | Fullständigt kvalificerat resurs-ID för den virtuella datorn. |
| ResourceUId | GUID för den virtuella datorn |
 |Name | Namnet på den anslutna disken |
| CreatedTime |Datum och tid då data disken skapades. |
| DeletedDate | Datum och tid då data disken togs bort. |
| ResourceStatus | Status för resursen. Aktiv, om resursen finns. Inaktiv, vid borttagning. |
| DiskBlobName | BLOB-namn för data disken. |
| DiskSizeGB | Data diskens storlek. |
| DiskType | Typ av datadisk. 0 för standard 1 för Premium. |
| LeasedByVmId | Resurs-ID för den virtuella dator som data disken har kopplats till. |


> [!NOTE]
> Om du hanterar flera labb och vill hämta allmän information, är de två nyckel kolumnerna **LabUID** och **ResourceUId**, som är unika ID: n för prenumerationer.

Exporterade data kan manipuleras och visualiseras med hjälp av verktyg som SQL Server, Power BI osv. Den här funktionen är särskilt användbar när du vill rapportera användningen av ditt labb till hanterings teamet som kanske inte använder samma Azure-prenumeration som du gör.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar: 

- [Ange principer för ett labb](devtest-lab-set-lab-policy.md)
- [Vanliga frågor och svar](devtest-lab-faq.md)
