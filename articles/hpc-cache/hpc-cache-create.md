---
title: Skapa en Azure HPC-cache
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 50fdab1fa8def1b4e3b9a9d59d3b2593e5526c65
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103472036"
---
# <a name="create-an-azure-hpc-cache"></a>Skapa en Azure HPC-cache

Använd Azure Portal eller Azure CLI för att skapa din cache.

![skärm bild av cache-översikten i Azure Portal med knappen Skapa längst ned](media/hpc-cache-home-page.png)

Klicka på bilden nedan om du vill titta på en [video demonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagrings mål.

[![video miniatyr: Azure HPC cache: Setup (Klicka för att besöka video sidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärm bild av sidan projekt information i Azure Portal](media/hpc-cache-create-basics.png)

I **projekt information** väljer du den prenumeration och resurs grupp som ska vara värd för cachen.

I **tjänst information** anger du cache-namn och följande attribut:

* Plats – Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk – du kan välja ett befintligt namn eller skapa ett nytt virtuellt nätverk.
* Undernät – Välj eller skapa ett undernät med minst 64 IP-adresser (/24). Det här under nätet får endast användas för den här Azure HPC-instansen.

## <a name="set-cache-capacity"></a>Ange cache-kapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På sidan **cache** måste du ange kapaciteten för din cache. Värdena som anges här avgör hur mycket data ditt cacheminne kan innehålla och hur snabbt det kan betjäna klient begär Anden.

Kapaciteten påverkar också cachens kostnad.

Välj kapacitet genom att ange följande två värden:

* Maximal data överförings hastighet för cachen (data flöde), i GB/SEK
* Mängden lagrings utrymme som allokerats för cachelagrade data i TB

Välj något av de tillgängliga data flödes värdena och cache Storage-storlekarna.

Tänk på att den faktiska data överförings hastigheten är beroende av arbets belastning, nätverks hastigheter och typen av lagrings mål. De värden du väljer ställer in maximalt data flöde för hela cache-systemet, men vissa av dem används för omkostnader. Om till exempel en klient begär en fil som inte redan finns lagrad i cacheminnet, eller om filen har marker ATS som inaktuell, använder cacheminnet en del av data flödet för att hämta den från backend-lagringen.

Azure HPC cache hanterar vilka filer som cachelagras och förinstalleras för att maximera träffar i cacheträffar. Innehållet i cachen utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de inte används ofta. Välj en lagrings storlek för cachen som kan vara den aktiva uppsättningen arbetsfiler, plus ytterligare utrymme för metadata och andra kostnader.

![skärm bild av sidan cache-storlek](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Aktivera Azure Key Vault kryptering (valfritt)

Sidan **disk krypterings nycklar** visas mellan flikarna **cache** och **taggar** .<!-- Read [Regional availability](hpc-cache-overview.md#region-availability) to learn more about region support. -->

Om du vill hantera krypterings nycklarna som används för cache-lagringen, anger du Azure Key Vault information på sidan **disk krypterings nycklar** . Nyckel valvet måste finnas i samma region och i samma prenumeration som cachen.

Du kan hoppa över det här avsnittet om du inte behöver Kundhanterade nycklar. Azure krypterar data med Microsoft-hanterade nycklar som standard. Läs mer i [Azure Storage-kryptering](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Du kan inte ändra mellan Microsoft-hanterade nycklar och Kundhanterade nycklar när du har skapat cacheminnet.
> * När cachen har skapats måste du ge den åtkomst till nyckel valvet. Klicka på knappen **Aktivera kryptering** på sidan för cachens **Översikt** för att aktivera kryptering. Ta det här steget inom 90 minuter när du skapar cacheminnet.
> * Cache-diskar skapas efter den här auktoriseringen. Det innebär att tiden för att skapa första cache är kort, men cachen är inte redo att användas i tio minuter eller mer efter att du har auktoriserat åtkomst.

En fullständig förklaring av den kund hanterade nyckel krypterings processen finns i [använda Kundhanterade krypterings nycklar för Azure HPC cache](customer-keys.md).

![skärm bild av sidan med krypterings nycklar med valt kundhanterat och nyckel valv fält visas](media/create-encryption.png)

Välj **kund hanterat** för att välja kundhanterad nyckel kryptering. Fält för nyckel valvs specifikation visas. Välj den Azure Key Vault som du vill använda och välj sedan den nyckel och version som ska användas för cacheminnet. Nyckeln måste vara en 2048-bitars RSA-nyckel. Du kan skapa ett nytt nyckel valv, nyckel eller nyckel version från den här sidan.

När du har skapat cachen måste du godkänna att den använder Key Vault-tjänsten. Mer information finns [i auktorisera Azure Key Vault kryptering från cachen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Lägg till resurs koder (valfritt)

På sidan **taggar** kan du lägga till [resurs Taggar](../azure-resource-manager/management/tag-resources.md) till Azure HPC-instansen.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa** . Portalen verifierar dina val och gör att du kan granska dina val. Om allt stämmer klickar du på **skapa**.

Det tar cirka 10 minuter att skapa cacheminnet. Du kan följa förloppet i Azure Portal meddelande panel.

![skärm bild av sidorna för att skapa "distribution pågår" och "Notifications" i portalen](media/hpc-cache-deploy-status.png)

När en avisering har skapats visas ett meddelande med en länk till den nya Azure HPC cache-instansen och cachen visas i prenumerationens lista över **resurser** .

![skärm bild av Azure HPC cache-instansen i Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Om cachen använder Kundhanterade krypterings nycklar kan cachen visas i resurs listan innan distributionens status ändras till slutförd. Så snart cachens status **väntar på nyckel** kan du [ge den](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) behörighet att använda nyckel valvet.

## <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="create-the-cache-with-azure-cli"></a>Skapa cachen med Azure CLI

[Konfigurera Azure CLI för Azure HPC-cache](./az-cli-prerequisites.md).

> [!NOTE]
> Azure CLI har för närvarande inte stöd för att skapa en cache med Kundhanterade krypterings nycklar. Använd Azure-portalen.

Använd kommandot [AZ HPC-cache Create](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-create) för att skapa en ny Azure HPC-cache.

Ange följande värden:

* Namn på resurs grupp för cache
* Cache-namn
* Azure-region
* Cache-undernät, i det här formatet:

  ``--subnet "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"``

  Under nätet för cache måste ha minst 64 IP-adresser (/24) och det får inte innehålla några andra resurser.

* Cache-kapacitet. Två värden anger maximalt data flöde för Azure HPC-cachen:

  * Cachestorlek (i GB)
  * SKU för de virtuella datorer som används i cache-infrastrukturen

  [AZ HPC – cache SKU-lista](/cli/azure/ext/hpc-cache/hpc-cache/skus) visar tillgängliga SKU: er och giltiga alternativ för cachestorlek för var och en. Storleks alternativ för cache mellan 3 TB och 48 TB, men endast vissa värden stöds.

  Det här diagrammet visar vilken cachestorlek och vilka SKU-kombinationer som är giltiga vid den tidpunkt då dokumentet förbereds (2020 juli).

  | Cachestorlek | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nej          | nej          |
  | 6144 GB    | ja         | ja         | nej          |
  | 12288 GB   | ja         | ja         | ja         |
  | 24576 GB   | nej          | ja         | ja         |
  | 49152 GB   | nej          | nej          | ja         |

  Läs avsnittet **Ange cache-kapacitet** i fliken Portal instruktioner för viktig information om priser, data flöde och hur du ändrar cacheminnet för ditt arbets flöde.

Exempel på att skapa cache:

```azurecli
az hpc-cache create --resource-group doc-demo-rg --name my-cache-0619 \
    --location "eastus" --cache-size-gb "3072" \
    --subnet "/subscriptions/<subscription-ID>/resourceGroups/doc-demo-rg/providers/Microsoft.Network/virtualNetworks/vnet-doc0619/subnets/default" \
    --sku-name "Standard_2G"
```

Det tar flera minuter att skapa cacheminnet. Vid lyckad returnerar kommandot CREATE följande utdata:

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

Meddelandet innehåller viss användbar information, inklusive följande objekt:

* Klient monterings adresser – Använd de här IP-adresserna när du är redo att ansluta klienter till cacheminnet. Mer information finns i [montera Azure HPC cache](hpc-cache-mount.md) .
* Uppgraderings status – när en program uppdatering släpps ändras det här meddelandet. Du kan [Uppgradera cache-programvaran](hpc-cache-manage.md#upgrade-cache-software) manuellt vid en lämplig tidpunkt, eller så kommer den att tillämpas automatiskt efter flera dagar.

## <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

> [!CAUTION]
> PowerShell-modulen AZ. HPCCache är för närvarande en offentlig för hands version. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsade funktioner. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Krav

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps). Om du väljer att använda Cloud Shell, se [Översikt över Azure Cloud Shell](../cloud-shell/overview.md) för mer information.

> [!IMPORTANT]
> Även om **AZ. HPCCache** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När den här PowerShell-modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av AZ PowerShell-modulen och vara tillgängliga internt från Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HPCCache
```

## <a name="create-the-cache-with-azure-powershell"></a>Skapa cachen med Azure PowerShell

> [!NOTE]
> Azure PowerShell har för närvarande inte stöd för att skapa en cache med Kundhanterade krypterings nycklar. Använd Azure-portalen.

Använd cmdleten [New-AzHpcCache](/powershell/module/az.hpccache/new-azhpccache) för att skapa en ny Azure HPC-cache.

Ange följande värden:

* Namn på resurs grupp för cache
* Cache-namn
* Azure-region
* Cache-undernät, i det här formatet:

  `-SubnetUri "/subscriptions/<subscription_id>/resourceGroups/<cache_resource_group>/providers/Microsoft.Network/virtualNetworks/<virtual_network_name>/sub
nets/<cache_subnet_name>"`

  Under nätet för cache måste ha minst 64 IP-adresser (/24) och det får inte innehålla några andra resurser.

* Cache-kapacitet. Två värden anger maximalt data flöde för Azure HPC-cachen:

  * Cachestorlek (i GB)
  * SKU för de virtuella datorer som används i cache-infrastrukturen

  [Get-AzHpcCacheSku](/powershell/module/az.hpccache/get-azhpccachesku) visar tillgängliga SKU: er och giltiga alternativ för cachestorlek för var och en. Storleks alternativ för cache mellan 3 TB och 48 TB, men endast vissa värden stöds.

  Det här diagrammet visar vilken cachestorlek och vilka SKU-kombinationer som är giltiga vid den tidpunkt då dokumentet förbereds (2020 juli).

  | Cachestorlek | Standard_2G | Standard_4G | Standard_8G |
  |------------|-------------|-------------|-------------|
  | 3072 GB    | ja         | nej          | nej          |
  | 6144 GB    | ja         | ja         | nej          |
  | 12 288 GB   | ja         | ja         | ja         |
  | 24 576 GB   | nej          | ja         | ja         |
  | 49 152 GB   | nej          | nej          | ja         |

  Läs avsnittet **Ange cache-kapacitet** i fliken Portal instruktioner för viktig information om priser, data flöde och hur du ändrar cacheminnet för ditt arbets flöde.

Exempel på att skapa cache:

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

Det tar flera minuter att skapa cacheminnet. Vid lyckad returnerar kommandot CREATE följande utdata:

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

Meddelandet innehåller viss användbar information, inklusive följande objekt:

* Klient monterings adresser – Använd de här IP-adresserna när du är redo att ansluta klienter till cacheminnet. Mer information finns i [montera Azure HPC cache](hpc-cache-mount.md) .
* Uppgraderings status – när en program uppdatering publiceras ändras meddelandet. Du kan [Uppgradera cache-programvaran](hpc-cache-manage.md#upgrade-cache-software) manuellt vid en lämplig tidpunkt, eller så används den automatiskt efter flera dagar.

---

## <a name="next-steps"></a>Nästa steg

När din cache visas i listan **resurser** kan du gå vidare till nästa steg.

* [Definiera lagrings mål](hpc-cache-add-storage.md) för att ge cache åtkomst till dina data källor.
* Om du använder Kundhanterade krypterings nycklar måste du [auktorisera Azure Key Vault kryptering](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) från sidans översikts sida för att slutföra cache-installationen. Du måste göra det här steget innan du kan lägga till lagrings utrymme. Läs [Använd kund hanterade krypterings nycklar](customer-keys.md) för mer information.