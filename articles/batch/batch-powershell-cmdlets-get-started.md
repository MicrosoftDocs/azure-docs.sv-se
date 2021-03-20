---
title: Kom igång med PowerShell
description: En snabb introduktion till Azure PowerShell-cmdlets som du kan använda för att hantera Batch-resurserna.
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685518"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Hantera Batch-resurser med PowerShell-cmdletar

Med Azure Batch PowerShell-cmdletar kan du utföra och skripta många vanliga batch-uppgifter. Det här är en snabb introduktion till de cmdletar som du kan använda för att hantera Batch-konton och arbeta med Batch-resurser, t.ex. pooler, jobb och uppgifter.

En fullständig lista över alla Batch-cmdlets och en detaljerad cmdlet-syntax finns i [Cmdlet-referens för Azure Batch](/powershell/module/az.batch).

Vi rekommenderar att du uppdaterar Azure PowerShell-moduler ofta för att dra nytta av tjänstuppdateringar och förbättringar.

## <a name="prerequisites"></a>Förutsättningar

- [Installera och konfigurera Azure PowerShell-modulen](/powershell/azure/). Information om hur du installerar en viss Azure Batch-modul, till exempel en förhandsversion av en modul, finns i [PowerShell-galleriet](https://www.powershellgallery.com/packages/Az.Batch/).

- Kör cmdleten **Connect-AzAccount** för att ansluta till din prenumeration (Azure Batch-cmdletar medföljer Azure Resource Manager-modulen):

  ```powershell
  Connect-AzAccount
  ```

- **Registrera med Batch-leverantörens namnområde**. Du behöver bara utför den här åtgärden **en gång per prenumeration**.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Hantera Batch-konton och nycklar

### <a name="create-a-batch-account"></a>Skapa ett Batch-konto

**New-AzBatchAccount** skapar ett Batch-konto i en angiven resursgrupp. Om du inte redan har en resursgrupp skapar du en genom att köra cmdleten [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Ange ett av Azure-områdena i parametern **Plats**, till exempel "USA, centrala". Exempel:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Skapa sedan ett Batch-konto i resursgruppen. Ange ett namn för kontot i <*account_name*>, och platsen och namnet för resursgruppen. Det kan ta en stund innan skapandet av Batch-kontot har slutförts. Exempel:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Batch-kontonamnet måste vara unikt för Azure-regionen för resursgruppen, innehålla mellan 3 och 24 tecken och endast bestå av små bokstäver och siffror.

### <a name="get-account-access-keys"></a>Hämta kontoåtkomstnycklar

**Get-AzBatchAccountKeys** visar åtkomstnycklarna som är associerade med ett Azure Batch-konto. Kör till exempel följande för att hämta de primära och sekundära nycklarna för det konto som du skapade.

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Generera en ny åtkomstnyckel

**New-AzBatchAccountKey** genererar en ny primär eller sekundär kontonyckel för ett Azure Batch-konto. Om du till exempel vill skapa en ny primär nyckel för Batch-kontot skriver du:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Om du vill skapa en ny sekundär nyckel anger du ”Secondary” för parametern **KeyType**. Du måste återskapa de primära och sekundära nycklarna separat.

### <a name="delete-a-batch-account"></a>Ta bort ett Batch-konto

**Remove-AzBatchAccount** tar bort ett Batch-konto. Exempel:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

När du uppmanas att göra det bekräftar du att du vill ta bort kontot. Det kan ta en stund innan kontot tagits bort.

## <a name="create-a-batchaccountcontext-object"></a>Skapa ett BatchAccountContext-objekt

Du kan autentisera för att hantera Batch-resurser med hjälp av delad nyckel-autentisering eller Azure Active Directory-autentisering. Om du vill autentisera med hjälp av Batch PowerShell-cmdletar skapar du förs ett BatchAccountContext-objekt för att lagra dina kontouppgifter eller din identitet. Du skickar BatchAccountContext-objektet till cmdlets som använder parametern **BatchContext**.

### <a name="shared-key-authentication"></a>Autentisering med delad nyckel

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Som standard används kontots primära nyckel för autentisering, men du kan uttryckligen välja vilken nyckel som ska användas genom att ändra BatchAccountContext-objektets **KeyInUse**-egenskap: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Azure Active Directory-autentisering

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Skapa och ändra Batch-resurser

Använd cmdletar som **New-AzBatchPool**, **New-AzBatchJob** och **New-AzBatchTask** för att skapa resurser under ett Batch-konto. Det finns motsvarande cmdlets för **Get-** och **Set-** som du kan använda för att uppdatera egenskaperna för befintliga resurser, och cmdlets för **Remove-** som du använder om du vill ta bort resurser under ett Batch-konto.

När du använder många av dessa cmdletar måste du, förutom att skicka ett BatchContext-objekt, skapa eller skicka objekt som innehåller detaljerade resursinställningar, så som visas i följande exempel. Visa den detaljerade hjälpinformationen för respektive cmdlet om du vill ha fler exempel.

### <a name="create-a-batch-pool"></a>Skapa en Batch-pool

När du skapar eller uppdaterar en batch-pool anger du en [konfiguration](nodes-and-pools.md#configurations). Pooler bör normalt konfigureras med konfiguration av virtuell dator, vilket gör att du antingen kan ange någon av de virtuella Linux-eller Windows-avbildningar som anges i [Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)eller ange en anpassad avbildning som du har för berett. Cloud Services-mediepooler tillhandahåller endast Windows Compute-noder och har inte stöd för alla batch-funktioner.

När du kör **New-AzBatchPool** ska du skicka inställningarna för operativ systemet i ett PSVirtualMachineConfiguration-eller PSCloudServiceConfiguration-objekt. Till exempel skapar följande kodavsnitt en Batch-pool med beräkningsnoder i storleken Standard_A1 i konfigurationen för virtuella datorer, avbildade med Ubuntu Server 18.04-LTS. Här anger parametern **VirtualMachineConfiguration** variabeln *$configuration* som PSVirtualMachineConfiguration-objekt. Parametern **BatchContext** anger en tidigare definierad variabel, *$context*, som BatchAccountContext-objektet.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Beräkningsnodernas målantal i den nya poolen beräknas med en autoskalningsformel. I det här fallet är formeln helt enkelt **$TargetDedicated=4**, vilket indikerar att antalet beräkningsnoder i poolen som mest är 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Fråga avseende pooler, jobb, uppgifter och annan information

Använd cmdletar som **Get-AzBatchPool**, **Get-AzBatchJob** och **Get-AzBatchTask** för att fråga efter entiteter som skapats under ett Batch-konto.

### <a name="query-for-data"></a>Fråga efter data

Använd till exempel **Get-AzBatchPools** för att hämta dina pooler. Som standard returneras alla pooler i ditt konto, förutsatt att du redan har lagrat BatchAccountContext-objektet i *$context*:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Använda ett OData-filter

Du kan lägga till ett OData-filter med parametern **Filter** om du bara vill söka efter de objekt som du är intresserad av. Du kan till exempel hämta alla pooler med ID:n som börjar med ”myPool”:

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Den här metoden är inte lika flexibel som ”Where-Object”-metoden i en lokal pipeline. Frågan skickas dock direkt till Batch-tjänsten så att all filtrering utförs på serversidan, vilket sparar Internetbandbredd.

### <a name="use-the-id-parameter"></a>Använda Id-parametern

Ett alternativ till ett OData-filter är att använda parametern **Id**. Så här frågar du efter en specifik pool med ID:t ”myPool”:

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Parametern **Id** stöder endast sökningar efter fullständiga ID:n, inte jokertecken eller OData-filter.

### <a name="use-the-maxcount-parameter"></a>Använda parametern MaxCount

Som standard returnerar varje cmdlet högst 1 000 objekt. Om du når den här gränsen kan du antingen förfina filtret så att färre objekt returneras eller uttryckligen ställa in ett högsta antal med parametern **MaxCount**. Exempel:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Om du vill ta bort den övre gränsen anger du **MaxCount** till 0 eller mindre.

### <a name="use-the-powershell-pipeline"></a>Använd PowerShell pipeline

Batch-cmdletar använder PowerShell-pipelinen för att skicka data mellan cmdletar. Detta har samma effekt som om du anger en parameter men gör det enklare att arbeta med flera entiteter.

Exempelvis söka efter och visa alla aktiviteter på ditt konto:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Starta om varje beräkningsnod i en pool:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Hantera programpaket

[Programpaket](batch-application-packages.md) är ett förenklat sätt att distribuera program till Compute-noderna i dina pooler. Med Batch-PowerShell-cmdlet:ar kan du överföra och hantera programpaket i Batch-kontot och distribuera paketversioner för att beräkna noder.

> [!IMPORTANT]
> Du måste koppla ett Azure Storage-konto till Batch-kontot för att använda programpaket.

**Skapa** ett program:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Lägg till** ett programpaket:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Ange **standardversionen** för programmet:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Lista** ett programs paket

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Ta bort** ett programpaket

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Ta bort** ett program

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Du måste ta bort alla programpaketversioner innan du tar bort ett program. Du får ett konfliktfel om du försöker ta bort ett program som för närvarande har programpaket.

### <a name="deploy-an-application-package"></a>Distribuera ett programpaket

Du kan ange ett eller flera programpaket för distribution när du skapar en pool. När du anger ett paket när poolen skapas distribueras den till varje nod när noden ansluter till poolen. Paket distribueras också när en nod startas om eller när en avbildning återställs.

Ange alternativet `-ApplicationPackageReference` när du skapar en pool för att distribuera ett programpaket till poolen noder efterhand som de ansluts till poolen. Skapa först ett **PSApplicationPackageReference**-objekt och konfigurera det med program-ID:t och den paketversion som du vill distribuera till poolens beräkningsnoder:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Skapa nu poolen och ange paketreferensobjekt som argument till `ApplicationPackageReferences`-alternativet:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Mer information om programpaket finns i [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Distribuera program till beräkningsnoder med Batch-programpaket).

### <a name="update-a-pools-application-packages"></a>Uppdatera programpaket för en pool

Om du vill uppdatera program som är tilldelade till en befintlig pool skapar du ett PSApplicationPackageReference-objekt med önskade egenskaper (program-ID och paketversion):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Hämta sedan poolen från batch, ta bort alla befintliga paket, Lägg till den nya paket referensen och uppdatera batch-tjänsten med de nya inställningarna för poolen:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Poolens egenskaper i Batch-tjänsten har nu uppdaterats. Du måste starta om eller återställa avbildningen av noder om du vill distribuera nya programpaket för beräkningsnoder i poolen. Du kan starta om varje nod i en pool med det här kommandot:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Du kan distribuera flera programpaket till beräkningsnoder i poolen. Om du vill lägga till ett programpaket i stället för att ersätta de för närvarande distribuerade paketen, utelämnar du raden `$pool.ApplicationPackageReferences.Clear()` ovan.

## <a name="next-steps"></a>Nästa steg

- Granska [Azure Batch cmdlet-referens](/powershell/module/az.batch) för detaljerad cmdlet-syntax och exempel.
- Lär dig hur du [distribuerar program för att beräkna noder med batch-programpaket](batch-application-packages.md).
