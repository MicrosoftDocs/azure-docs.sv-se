---
title: Skapa en Azure HPC Cache
description: Så här skapar du en Azure HPC Cache instans
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 02934a1943ef37d282dd2a2e7862c5695bbd6ecb
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862713"
---
# <a name="create-an-azure-hpc-cache"></a>Skapa en Azure HPC Cache

Använd den Azure Portal eller Azure CLI för att skapa din cache.

![skärmbild av cacheöversikten i Azure Portal, med knappen Skapa längst ned](media/hpc-cache-home-page.png)

Klicka på bilden nedan om du vill se [en videodemonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagringsmål.

[![videominiatyr: Azure HPC Cache: Installation (klicka för att besöka videosidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärmbild av projektinformationssidan i Azure Portal](media/hpc-cache-create-basics.png)

I **Projektinformation** väljer du den prenumeration och resursgrupp som ska vara värd för cachen.

I **Tjänstinformation** anger du cachenamnet och dessa andra attribut:

* Plats – Välj en av de [regioner som stöds.](hpc-cache-overview.md#region-availability)
* Virtuellt nätverk – Du kan välja ett befintligt eller skapa ett nytt virtuellt nätverk.
* Undernät – Välj eller skapa ett undernät med minst 64 IP-adresser (/24). Det här undernätet får endast användas för den här Azure HPC Cache instansen.

## <a name="set-cache-capacity"></a>Ange cachekapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På **sidan Cache** måste du ange cachens kapacitet. Värdena som anges här avgör hur mycket data som cacheminnet kan innehålla och hur snabbt det kan användas för klientbegäranden.

Kapaciteten påverkar även cachens kostnad.

Välj kapacitet genom att ange dessa två värden:

* Maximal dataöverföringshastighet för cachen (dataflöde) i GB/sekund
* Mängden allokerat lagringsutrymme för cachelagrade data, i TB

Välj ett av de tillgängliga dataflödesvärdena och cachelagringsstorlekarna.

Tänk på att den faktiska dataöverföringshastigheten beror på arbetsbelastning, nätverkshastigheter och typen av lagringsmål. De värden som du väljer anger det maximala dataflödet för hela cachesystemet, men vissa av dem används för overhead-uppgifter. Om en klient till exempel begär en fil som inte redan lagras i cacheminnet, eller om filen är markerad som inaktuell, använder cacheminnet en del av sitt dataflöde för att hämta den från backend-lagringen.

Azure HPC Cache hanterar vilka filer som cachelagras och förinstalleras för att maximera träfffrekvensen för cachen. Cacheinnehållet utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de används mindre ofta. Välj en cachelagringsstorlek som kan innehålla den aktiva uppsättningen arbetsfiler, plus ytterligare utrymme för metadata och annat omkostnader.

![skärmbild av sidan för cacheändring](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Aktivera Azure Key Vault kryptering (valfritt)

Sidan **Diskkrypteringsnycklar** visas mellan **flikarna Cache** **och** Taggar.<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Om du vill hantera krypteringsnycklarna som används för cachelagringen, Azure Key Vault information på sidan **Diskkrypteringsnycklar.** Nyckelvalvet måste finnas i samma region och i samma prenumeration som cachen.

Du kan hoppa över det här avsnittet om du inte behöver kund hanterade nycklar. Azure krypterar data med Microsoft-hanterade nycklar som standard. Läs [Azure Storage-kryptering om](../storage/common/storage-service-encryption.md) du vill veta mer.

> [!NOTE]
>
> * Du kan inte ändra mellan Microsoft-hanterade nycklar och kund hanterade nycklar när du har skapat cachen.
> * När cachen har skapats måste du ge den behörighet att komma åt nyckelvalvet. Klicka på **knappen Aktivera** kryptering på cachens **översiktssida för** att aktivera kryptering. Ta det här steget inom 90 minuter efter att du har skapat cacheminnet.
> * Cachediskar skapas efter den här auktoriseringen. Det innebär att den första tiden för att skapa cacheminnet är kort, men cachen är inte redo att användas under minst tio minuter efter att du har auktoriserat åtkomst.

En fullständig förklaring av den kund hanterade nyckelkrypteringsprocessen finns i [Använda kund hanterade krypteringsnycklar för Azure HPC Cache](customer-keys.md).

![skärmbild av sidan med krypteringsnycklar med "kund hanterad" valt och nyckelvalvsfält som visar](media/create-encryption.png)

Välj **Kund-hanterad** för att välja kund hanterad nyckelkryptering. Specifikationsfälten för nyckelvalvet visas. Välj den Azure Key Vault ska använda och välj sedan den nyckel och version som ska användas för den här cachen. Nyckeln måste vara en 2 048-bitars RSA-nyckel. Du kan skapa ett nytt nyckelvalv, nyckel eller nyckelversion från den här sidan.

När du har skapat cachen måste du auktorisera den att använda nyckelvalvstjänsten. Läs [Auktorisera Azure Key Vault kryptering från cacheminnet](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) för mer information.

## <a name="add-resource-tags-optional"></a>Lägga till resurstaggar (valfritt)

På **sidan Taggar** kan du lägga till [resurstaggar](../azure-resource-manager/management/tag-resources.md) i din Azure HPC Cache instans.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cachen

När du har konfigurerat den nya cachen klickar du **på fliken Granska +** skapa. Portalen verifierar dina val och låter dig granska dina val. Om allt är korrekt klickar du på **Skapa**.

Det tar cirka 10 minuter att skapa cachen. Du kan följa förloppet i Azure Portal aviseringspanelen.

![skärmbild av skapande av "distribution pågår" och "meddelanden"-sidor i portalen](media/hpc-cache-deploy-status.png)

När skapandet är klart visas ett meddelande med en länk till den nya Azure HPC Cache instansen och cachen visas i prenumerationens **resurslista.**

![skärmbild av Azure HPC Cache instans i Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Om ditt cacheminne använder kund hanterade krypteringsnycklar kan cachen visas i resurslistan innan distributionsstatusen ändras för att slutföras. Så snart cachens status är Väntar på nyckel **kan du** auktorisera [den att](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) använda nyckelvalvet.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Skapa cachen med Azure CLI

[Konfigurera Azure CLI för Azure HPC Cache](./az-cli-prerequisites.md).

> [!NOTE]
> Azure CLI har för närvarande inte stöd för att skapa ett cacheminne med kund hanterade krypteringsnycklar. Använd Azure-portalen.

Använd kommandot [az hpc-cache create](/cli/azure/hpc-cache#az_hpc_cache_create) för att skapa en ny Azure HPC Cache.

Ange följande värden:

* Namn på cacheresursgrupp
* Cachenamn
* Azure-region
* Cacheundernät i det här formatet:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Cacheundernätet behöver minst 64 IP-adresser (/24) och det kan inte lagra några andra resurser.

* Cachelagringskapacitet. Två värden anger det maximala dataflödet för Azure HPC Cache:

  * Cachestorleken (i GB)
  * SKU:n för de virtuella datorer som används i cacheinfrastrukturen

  [az hpc-cache skus list](/cli/azure/hpc-cache/skus) visar tillgängliga SKU:er och giltiga alternativ för cachestorlek för var och en. Alternativen för cachestorlek sträcker sig från 3 TB till 48 TB, men endast vissa värden stöds.

  Det här diagrammet visar vilken cachestorlek och vilka SKU-kombinationer som är giltiga när dokumentet förbereds (juli 2020).

  | Cachestorlek | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nej          | nej          |
  | 6144 GB    | ja         | ja         | nej          |
  | 12288 GB   | ja         | ja         | ja         |
  | 24576 GB   | nej          | ja         | ja         |
  | 49152 GB   | nej          | nej          | ja         |

  Läs avsnittet **Ange cachekapacitet** på fliken med portalinstruktioner för viktig information om priser, dataflöde och hur du gör rätt storlek på cacheminnet för arbetsflödet.

Exempel på cacheskapande:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Det tar flera minuter att skapa cacheminnet. Om det lyckas returnerar create-kommandot utdata så här:

```azurecli
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },
  "id": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619",
  "location": "eastus",
  "mountAddresses": [
    "10.3.0.17",
    "10.3.0.18",
    "10.3.0.19"
  ],
  "name": "my-cache-0619",
  "provisioningState": "Succeeded",
  "resourceGroup": "doc-demo-rg",
  "sku": {
    "name": "Standard_2G"
  },
  "subnet": "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default",
  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.42",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-04-01T15:19:54.068299+00:00",
    "pendingFirmwareVersion": null
  }
}
```

Meddelandet innehåller användbar information, inklusive följande objekt:

* Klientmonteringsadresser – Använd dessa IP-adresser när du är redo att ansluta klienter till cachen. Läs [Montera Azure HPC Cache mer](hpc-cache-mount.md) information.
* Uppgraderingsstatus – När en programuppdatering släpps ändras det här meddelandet. Du kan [uppgradera cacheprogramvaran](hpc-cache-manage.md#upgrade-cache-software) manuellt vid en lämplig tidpunkt, eller så tillämpas den automatiskt efter flera dagar.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> PowerShell-modulen Az.HPCCache finns för närvarande i offentlig förhandsversion. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsade funktioner. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell finns [mer information i Översikt Azure Cloud Shell](../cloud-shell/overview.md) information.

> [!IMPORTANT]
> När **PowerShell-modulen Az.HPCCache** är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När den här PowerShell-modulen blir allmänt tillgänglig, kommer den att vara en del av framtida Versioner av Az PowerShell-modulen och vara tillgänglig Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Skapa cachen med Azure PowerShell

> [!NOTE]
> Azure PowerShell för närvarande inte stöd för att skapa ett cacheminne med kund hanterade krypteringsnycklar. Använd Azure-portalen.

Använd [cmdleten New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) för att skapa en ny Azure HPC Cache.

Ange följande värden:

* Namn på cacheresursgrupp
* Cachenamn
* Azure-region
* Cacheundernät i det här formatet:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Cacheundernätet behöver minst 64 IP-adresser (/24) och det kan inte lagra några andra resurser.

* Cachekapacitet. Två värden anger det maximala dataflödet för Azure HPC Cache:

  * Cachestorleken (i GB)
  * SKU:n för de virtuella datorer som används i cacheinfrastrukturen

  [Get-AzHpcCacheSku visar tillgängliga](/powershell/module/az.hpccache/get-azhpccachesku) SKU:er och giltiga alternativ för cachestorlek för var och en. Alternativen för cachestorlek sträcker sig från 3 TB till 48 TB, men endast vissa värden stöds.

  Det här diagrammet visar vilken cachestorlek och vilka SKU-kombinationer som är giltiga när dokumentet förbereds (juli 2020).

  | Cachestorlek | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nej          | nej          |
  | 6144 GB    | ja         | ja         | nej          |
  | 12 288 GB   | ja         | ja         | ja         |
  | 24 576 GB   | nej          | ja         | ja         |
  | 49 152 GB   | nej          | nej          | ja         |

  Läs avsnittet **Ange cachekapacitet** på fliken med portalinstruktioner för viktig information om priser, dataflöde och hur du gör rätt storlek på cacheminnet för arbetsflödet.

Exempel på cacheskapande:

```azurepowershell-interactive
$cacheParams = @{
  ResourceGroupName = 'doc-demo-rg'
  CacheName = 'my-cache-0619'
  Location = 'eastus'
  cacheSize = '3072'
  SubnetUri = "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default"
  Sku = 'Standard_2G'
}
New-AzHpcCache @cacheParams
```

Det tar flera minuter att skapa cacheminnet. Om det lyckas returnerar create-kommandot följande utdata:

```Output
cacheSizeGb       : 3072
health            : @{state=Healthy; statusDescription=The cache is in Running state}
id                : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.StorageCache/caches/my-cache-0619
location          : eastus
mountAddresses    : {10.3.0.17, 10.3.0.18, 10.3.0.19}
name              : my-cache-0619
provisioningState : Succeeded
resourceGroup     : doc-demo-rg
sku               : @{name=Standard_2G}
subnet            : /subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default
tags              :
type              : Microsoft.StorageCache/caches
upgradeStatus     : @{currentFirmwareVersion=5.3.42; firmwareUpdateDeadline=1/1/0001 12:00:00 AM; firmwareUpdateStatus=unavailable; lastFirmwareUpdate=4/1/2020 10:19:54 AM; pendingFirmwareVersion=}
```

Meddelandet innehåller användbar information, inklusive följande objekt:

* Klientmonteringsadresser – Använd dessa IP-adresser när du är redo att ansluta klienter till cachen. Läs [Montera Azure HPC Cache mer](hpc-cache-mount.md) information.
* Uppgraderingsstatus – När en programuppdatering släpps ändras det här meddelandet. Du kan [uppgradera cacheprogramvaran](hpc-cache-manage.md#upgrade-cache-software) manuellt vid en lämplig tidpunkt, eller så tillämpas den automatiskt efter flera dagar.

---

## <a name="next-steps"></a>Nästa steg

När ditt cacheminne visas **i listan** Resurser kan du gå vidare till nästa steg.

* [Definiera lagringsmål för](hpc-cache-add-storage.md) att ge cacheåtkomst till dina datakällor.
* Om du använder kund hanterade krypteringsnycklar måste du auktorisera [Azure Key Vault](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) kryptering från cachens översiktssida för att slutföra konfigurationen av cachen. Du måste göra det här steget innan du kan lägga till lagring. Mer [information finns i Använda kund hanterade](customer-keys.md) krypteringsnycklar.
