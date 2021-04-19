---
title: Montera Azure Blob Storage med hjälp av NFS 3.0-protokollet (förhandsversion) | Microsoft Docs
description: Lär dig hur du monterar en container i Blob Storage från en virtuell Azure-dator (VM) eller en klient som körs lokalt med hjälp av NFS 3.0-protokollet.
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 08/04/2020
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: bf6b06ba7cc7f547f752ffa7877fca186ba4465e
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713794"
---
# <a name="mount-blob-storage-by-using-the-network-file-system-nfs-30-protocol-preview"></a>Montera Blob Storage med hjälp av protokollet Network File System (NFS) 3.0 (förhandsversion)

Du kan montera en container i Blob Storage från en Linux-baserad virtuell Azure-dator (VM) eller ett Linux-system som körs lokalt med hjälp av NFS 3.0-protokollet. Den här artikeln innehåller stegvisa anvisningar. Mer information om stöd för NFS 3.0-protokoll i Blob Storage finns i [Network File System (NFS) 3.0-protokollstöd i Azure Blob Storage (förhandsversion).](network-file-system-protocol-support.md)

## <a name="step-1-register-the-nfs-30-protocol-feature-with-your-subscription"></a>Steg 1: Registrera NFS 3.0-protokollfunktionen med din prenumeration

1. Öppna ett PowerShell-kommandofönster. 

2. Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

   ```powershell
   Connect-AzAccount
   ```

3. Om din identitet är associerad med mer än en prenumeration anger du din aktiva prenumeration.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```
   
   Ersätt `<subscription-id>` platshållarvärdet med ID:t för din prenumeration.

4. Registrera `AllowNFSV3` funktionen med hjälp av följande kommando.

   ```powershell
   Register-AzProviderFeature -FeatureName AllowNFSV3 -ProviderNamespace Microsoft.Storage 
   ```

5. Registrera resursprovidern med hjälp av följande kommando.
    
   ```powershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage   
   ```

## <a name="step-2-verify-that-the-feature-is-registered"></a>Steg 2: Kontrollera att funktionen är registrerad 

Registreringsgodkännande kan ta upp till en timme. Använd följande kommandon för att kontrollera att registreringen är klar.

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNFSV3
```

## <a name="step-3-create-an-azure-virtual-network-vnet"></a>Steg 3: Skapa en Azure Virtual Network (VNet)

Ditt lagringskonto måste finnas i ett VNet. Med ett VNet kan klienter ansluta till ditt lagringskonto på ett säkert sätt. Mer information om VNet och hur du skapar ett finns i Virtual Network [dokumentationen](../../virtual-network/index.yml).

> [!NOTE]
> Klienter i samma virtuella nätverk kan montera containrar i ditt konto. Du kan också montera en container från en klient som körs i ett lokalt nätverk, men du måste först ansluta ditt lokala nätverk till ditt VNet. Se [Nätverksanslutningar som stöds.](network-file-system-protocol-support.md#supported-network-connections)

## <a name="step-4-configure-network-security"></a>Steg 4: Konfigurera nätverkssäkerhet

Det enda sättet att skydda data i ditt konto är att använda ett VNet och andra nätverkssäkerhetsinställningar. Andra verktyg som används för att skydda data, inklusive auktorisering av kontonyckel, Azure Active Directory-säkerhet (AD) och åtkomstkontrollistor (ACL:er) stöds ännu inte i konton som har stöd för NFS 3.0-protokollet aktiverat.

Information om hur du skyddar data i ditt konto finns i följande rekommendationer: [Nätverkssäkerhetsrekommendationer för Blob Storage.](security-recommendations.md#networking)

## <a name="step-5-create-and-configure-a-storage-account"></a>Steg 5: Skapa och konfigurera ett lagringskonto

Om du vill montera en container med hjälp av NFS 3.0 måste du skapa ett lagringskonto när **du** har registrerat funktionen med din prenumeration. Du kan inte aktivera konton som fanns innan du registrerade funktionen.

I förhandsversionen av den här funktionen stöds NFS 3.0-protokollet för standardkonton för generell användning v2-lagring och för premium-blockbloblagringskonton. Mer information om dessa typer av lagringskonton finns i [Översikt över lagringskonton.](../common/storage-account-overview.md)

När du konfigurerar kontot väljer du följande värden:

|Inställning | Premium-prestanda | Standardprestanda  
|----|---|---|
|Location|Alla tillgängliga regioner |En av följande regioner: Australien, östra, Sydkorea, centrala, USA, östra och USA, södra centrala   
|Prestanda|Premium| Standard
|Typ av konto|BlockBlobStorage| Generell användning V2
|Replikering|Lokalt redundant lagring (LRS)| Lokalt redundant lagring (LRS)
|Anslutningsmetod|Offentlig slutpunkt (valda nätverk) eller privat slutpunkt |Offentlig slutpunkt (valda nätverk) eller privat slutpunkt
|Säker överföring krävs|Inaktiverad|Inaktiverad
|Hierarkisk namnrymd|Enabled|Enabled
|NFS V3|Enabled |Enabled 

Du kan acceptera standardvärdena för alla andra inställningar. 

## <a name="step-6-create-a-container"></a>Steg 6: Skapa en container

Skapa en container i ditt lagringskonto med hjälp av något av följande verktyg eller SDK:er:

|Verktyg|SDK:er|
|---|---|
|[Azure-portalen](https://portal.azure.com)|[.NET](data-lake-storage-directory-file-acl-dotnet.md#create-a-container)|
|[AzCopy](../common/storage-use-azcopy-v10.md#transfer-data)|[Java](data-lake-storage-directory-file-acl-java.md)|
|[PowerShell](data-lake-storage-directory-file-acl-powershell.md#create-a-container)|[Python](data-lake-storage-directory-file-acl-python.md#create-a-container)|
|[Azure CLI](data-lake-storage-directory-file-acl-cli.md#create-a-container)|[JavaScript](data-lake-storage-directory-file-acl-javascript.md)|
||[REST](/rest/api/storageservices/create-container)|

## <a name="step-7-mount-the-container"></a>Steg 7: Montera containern

Skapa en katalog i Linux-systemet och montera sedan en container i lagringskontot.

1. Skapa en katalog i ett Linux-system.

   ```
   mkdir -p /mnt/test
   ```

2. Montera en container med hjälp av följande kommando.

   ```
   mount -o sec=sys,vers=3,nolock,proto=tcp <storage-account-name>.blob.core.windows.net:/<storage-account-name>/<container-name>  /mnt/test
   ```

   - Ersätt `<storage-account-name>` platshållaren som visas i det här kommandot med namnet på ditt lagringskonto.  

   - Ersätt `<container-name>` platshållaren med namnet på din container.

---

## <a name="resolve-common-issues"></a>Lösa vanliga problem

|Problem/fel | Lösning|
|---|---|
|`Access denied by server while mounting`|Se till att klienten körs i ett undernät som stöds. Se [nätverksplatser som stöds.](network-file-system-protocol-support.md#supported-network-connections)|
|`No such file or directory`| Se till att containern du monterar skapades efter att du verifierade att funktionen hade registrerats. Se [Steg 2: Kontrollera att funktionen är registrerad.](#step-2-verify-that-the-feature-is-registered) Se också till att skriva monteringskommandot och dess parametrar direkt i terminalen. Om du kopierar och klistrar in någon del av kommandot i terminalen från ett annat program kan det finnas dolda tecken i den inklistrade informationen som orsakar felet.|

## <a name="see-also"></a>Se även

[Protokollstöd för Network File System (NFS) 3.0 i Azure Blob Storage (förhandsversion)](network-file-system-protocol-support.md)
