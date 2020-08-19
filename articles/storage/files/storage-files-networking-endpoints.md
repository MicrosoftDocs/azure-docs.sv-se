---
title: Konfigurera Azure Files nätverks slut punkter | Microsoft Docs
description: En översikt över nätverks alternativ för Azure Files.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/17/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c48c48ef438f99f3b144c3300cb2415e4d387e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586689"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurera Azure Files nätverks slut punkter

Azure Files tillhandahåller två huvud typer av slut punkter för åtkomst till Azure-fil resurser: 
- Offentliga slut punkter som har en offentlig IP-adress och som kan nås från var som helst i världen.
- Privata slut punkter, som finns i ett virtuellt nätverk och har en privat IP-adress inom det virtuella nätverkets adress utrymme.

Offentliga och privata slut punkter finns på Azure Storage-kontot. Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer.

Den här artikeln fokuserar på hur du konfigurerar ett lagrings kontos slut punkter för åtkomst till Azure-filresursen direkt. De flesta Detaljer som tillhandahålls i det här dokumentet gäller också hur Azure File Sync interagerar med offentliga och privata slut punkter för lagrings kontot, men för mer information om nätverks överväganden för en Azure File Sync distribution, se [konfigurera Azure File Sync proxy-och brand Väggs inställningar](storage-sync-files-firewall-and-proxy.md).

Vi rekommenderar att du läser [Azure Files nätverks överväganden](storage-files-networking-overview.md) innan du läser den här guiden.

## <a name="prerequisites"></a>Förutsättningar

- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- I den här artikeln förutsätts att du redan har skapat en Azure-filresurs i ett lagrings konto som du vill ansluta till lokalt. Information om hur du skapar en Azure-filresurs finns i [skapa en Azure-fil resurs](storage-how-to-create-file-share.md).
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="endpoint-configurations"></a>Endpoint-konfigurationer

Du kan konfigurera dina slut punkter för att begränsa nätverks åtkomsten till ditt lagrings konto. Det finns två sätt att begränsa åtkomsten till ett lagrings konto till ett virtuellt nätverk:

- [Skapa en eller flera privata slut punkter för lagrings kontot](#create-a-private-endpoint)  och begränsa all åtkomst till den offentliga slut punkten. Detta säkerställer att endast trafik som härstammar från de önskade virtuella nätverken kan komma åt Azure-filresurserna i lagrings kontot.
- [Begränsa den offentliga slut punkten till ett eller flera virtuella nätverk](#restrict-public-endpoint-access). Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. När du begränsar trafiken till ett lagrings konto via en tjänst slut punkt har du fortfarande åtkomst till lagrings kontot via den offentliga IP-adressen, men åtkomst är bara möjlig från de platser som du anger i konfigurationen.

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Om du skapar en privat slut punkt för ditt lagrings konto leder det till att följande Azure-resurser distribueras:

- **En privat slut punkt**: en Azure-resurs som representerar lagrings kontots privata slut punkt. Du kan tänka på detta som en resurs som ansluter ett lagrings konto och ett nätverks gränssnitt.
- **Ett nätverks gränssnitt (NIC)**: det nätverks gränssnitt som upprätthåller en privat IP-adress inom det angivna virtuella nätverket/under nätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas en virtuell dator, ägs den av den privata slut punkten.
- **En privat DNS-zon**: om du aldrig har distribuerat en privat slut punkt för det här virtuella nätverket tidigare, kommer en ny privat DNS-zon att distribueras för det virtuella nätverket. En DNS A-post kommer också att skapas för lagrings kontot i den här DNS-zonen. Om du redan har distribuerat en privat slut punkt i det här virtuella nätverket kommer en ny A-post för lagrings kontot att läggas till i den befintliga DNS-zonen. Distribution av en DNS-zon är valfritt, rekommenderas, och krävs om du monterar dina Azure-filresurser med ett huvud namn för AD-tjänsten eller med det fileraste API: et.

> [!Note]  
> I den här artikeln används DNS-suffixet för lagrings kontot för Azures offentliga regioner `core.windows.net` . Den här kommentarer gäller också för Azures suveräna moln, till exempel Azure-molnet för amerikanska myndigheter och molnet i molnet, och ersätter bara de nödvändiga suffixen för din miljö. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt genom att köra följande kommandon från PowerShell, kommando raden eller terminalen (fungerar för Windows, Linux eller MacOS). Du måste ersätta `<storage-account-name>` med lämpligt lagrings konto namn:

```
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket (utdata som visas för Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]

Om du har en virtuell dator i det virtuella nätverket, eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i [Konfigurera DNS-vidarebefordran för Azure Files](storage-files-networking-dns.md), kan du testa att den privata slut punkten har kon figurer ATS korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat korrekt bör du se följande utdata, där `192.168.0.5` är den privata IP-adressen för den privata slut punkten i det virtuella nätverket. Du bör fortfarande använda storageaccount.file.core.windows.net för att montera fil resursen i stället för `privatelink` sökvägen.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```

---

### <a name="restrict-public-endpoint-access"></a>Begränsa offentlig slut punkts åtkomst

Genom att begränsa åtkomsten till den offentliga slut punkten måste du först inaktivera allmän åtkomst till den offentliga slut punkten. Att inaktivera åtkomst till den offentliga slut punkten påverkar inte privata slut punkter. När den offentliga slut punkten har inaktiverats kan du välja vissa nätverk eller IP-adresser som kan fortsätta att komma åt den. I allmänhet begränsar de flesta brand Väggs principer för ett lagrings konto nätverks åtkomst till ett eller flera virtuella nätverk.

#### <a name="disable-access-to-the-public-endpoint"></a>Inaktivera åtkomst till den offentliga slut punkten

När åtkomst till den offentliga slut punkten är inaktive rad kan lagrings kontot fortfarande nås via dess privata slut punkter. Annars avvisas giltiga begär anden till lagrings kontots offentliga slut punkt, om de inte kommer från [en särskilt tillåten källa](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks). 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till den offentliga slut punkten till vissa virtuella nätverk

När du begränsar lagrings kontot till specifika virtuella nätverk kan du tillåta begär anden till den offentliga slut punkten från de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. Detta kan användas med eller utan privata slut punkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Se även

- [Azure Files nätverks överväganden](storage-files-networking-overview.md)
- [Konfigurera DNS-vidarebefordring för Azure Files](storage-files-networking-dns.md)
- [Konfigurerar S2S VPN för Azure Files](storage-files-configure-s2s-vpn.md)
