---
title: Konfigurera Azure Files nätverksslutpunkter | Microsoft Docs
description: Lär dig hur du konfigurerar Azure File-nätverksslutpunkter.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 42e83facec7817b6588bf69977fea5ab74b6b10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759887"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurera Azure Files nätverksslutpunkter

Azure Files två huvudtyper av slutpunkter för åtkomst till Azure-filresurser: 
- Offentliga slutpunkter, som har en offentlig IP-adress och kan nås var som helst i världen.
- Privata slutpunkter, som finns i ett virtuellt nätverk och har en privat IP-adress inifrån det virtuella nätverkets adressutrymme.

Offentliga och privata slutpunkter finns på Azure Storage-kontot. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer.

Den här artikeln fokuserar på hur du konfigurerar slutpunkter för ett lagringskonto för att komma åt Azure-filresursen direkt. Merparten av informationen i det här dokumentet gäller även för hur Azure File Sync fungerar med offentliga och privata slutpunkter för lagringskontot. Mer information om nätverksöverväganden för en Azure File Sync-distribution finns i Konfigurera [Azure File Sync-proxy](../file-sync/file-sync-firewall-and-proxy.md)och brandväggsinställningar.

Vi rekommenderar att [du Azure Files nätverksöverväganden](storage-files-networking-overview.md) innan du läser den här guiden.

## <a name="prerequisites"></a>Förutsättningar

- Den här artikeln förutsätter att du redan har skapat en Azure-prenumeration. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- Den här artikeln förutsätter att du redan har skapat en Azure-filresurs i ett lagringskonto som du vill ansluta till från en lokal plats. Information om hur du skapar en Azure-filresurs finns i [Skapa en Azure-filresurs.](storage-how-to-create-file-share.md)
- Om du tänker använda Azure PowerShell installerar [du den senaste versionen](/powershell/azure/install-az-ps).
- Om du tänker använda Azure CLI installerar [du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Slutpunktskonfigurationer

Du kan konfigurera dina slutpunkter för att begränsa nätverksåtkomsten till ditt lagringskonto. Det finns två sätt att begränsa åtkomsten till ett lagringskonto till ett virtuellt nätverk:

- [Skapa en eller flera privata slutpunkter för lagringskontot och](#create-a-private-endpoint)  begränsa all åtkomst till den offentliga slutpunkten. Detta säkerställer att endast trafik som kommer inifrån de önskade virtuella nätverken kan komma åt Azure-filresurser i lagringskontot.
- [Begränsa den offentliga slutpunkten till ett eller flera virtuella nätverk](#restrict-public-endpoint-access). Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter*. När du begränsar trafiken till ett lagringskonto via en tjänstslutpunkt kommer du fortfarande åt lagringskontot via den offentliga IP-adressen, men åtkomst är bara möjlig från de platser som du anger i konfigurationen.

### <a name="create-a-private-endpoint"></a>Skapa en privat slutpunkt

Om du skapar en privat slutpunkt för ditt lagringskonto distribueras följande Azure-resurser:

- **En privat slutpunkt:** En Azure-resurs som representerar lagringskontots privata slutpunkt. Du kan tänka på detta som en resurs som ansluter ett lagringskonto och ett nätverksgränssnitt.
- **Ett nätverksgränssnitt (NIC): Nätverksgränssnittet** som upprätthåller en privat IP-adress i det angivna virtuella nätverket/undernätet. Det här är exakt samma resurs som distribueras när du distribuerar en virtuell dator, men i stället för att tilldelas till en virtuell dator ägs den av den privata slutpunkten.
- **En privat DNS-zon:** Om du aldrig har distribuerat en privat slutpunkt för det här virtuella nätverket tidigare distribueras en ny privat DNS-zon för det virtuella nätverket. En DNS A-post skapas också för lagringskontot i den här DNS-zonen. Om du redan har distribuerat en privat slutpunkt i det här virtuella nätverket läggs en ny A-post för lagringskontot till i den befintliga DNS-zonen. Det är valfritt att distribuera en DNS-zon, men rekommenderas starkt och krävs om du monterar dina Azure-filresurser med ett AD-tjänsthuvudnamn eller använder FileREST-API:et.

> [!Note]  
> I den här artikeln används lagringskontots DNS-suffix för de offentliga Azure-regionerna, `core.windows.net` . Den här inställningen gäller även för nationella Azure-moln, till exempel Azure US Government-molnet och Azure China-molnet. Du ersätter bara lämpliga suffix för din miljö. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Verifiera anslutningen

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du har en virtuell dator i ditt virtuella nätverk eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](storage-files-networking-dns.md)kan du testa att din privata slutpunkt har konfigurerats korrekt genom att köra följande kommandon från PowerShell, kommandoraden eller terminalen (fungerar för Windows, Linux eller macOS). Du måste ersätta `<storage-account-name>` med lämpligt lagringskontonamn:

```
nslookup <storage-account-name>.file.core.windows.net
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata IP-adressen för den privata slutpunkten i ditt virtuella nätverk `192.168.0.5` (utdata visas för Windows):

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du har en virtuell dator i ditt virtuella nätverk eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](storage-files-networking-dns.md)kan du testa att din privata slutpunkt har konfigurerats korrekt med följande kommandon:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata `192.168.0.5` IP-adressen för den privata slutpunkten i det virtuella nätverket:

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

Om du har en virtuell dator i ditt virtuella nätverk eller om du har konfigurerat DNS-vidarebefordran enligt beskrivningen i Konfigurera DNS-vidarebefordran för [Azure Files](storage-files-networking-dns.md)kan du testa att din privata slutpunkt har konfigurerats korrekt med följande kommandon:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Om allt har fungerat korrekt bör du se följande utdata, där är den privata IP-adressen för `192.168.0.5` den privata slutpunkten i ditt virtuella nätverk. Du bör fortfarande använda storageaccount.file.core.windows.net för att montera filresursen i stället för `privatelink` sökvägen.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Begränsa åtkomst till offentliga slutpunkter

Om du vill begränsa åtkomsten till offentliga slutpunkter måste du först inaktivera allmän åtkomst till den offentliga slutpunkten. Inaktivering av åtkomst till den offentliga slutpunkten påverkar inte privata slutpunkter. När den offentliga slutpunkten har inaktiverats kan du välja specifika nätverk eller IP-adresser som kan fortsätta att komma åt den. I allmänhet begränsar de flesta brandväggsprinciper för ett lagringskonto nätverksåtkomst till ett eller flera virtuella nätverk.

#### <a name="disable-access-to-the-public-endpoint"></a>Inaktivera åtkomst till den offentliga slutpunkten

När åtkomsten till den offentliga slutpunkten är inaktiverad kan lagringskontot fortfarande nås via dess privata slutpunkter. Annars avvisas giltiga begäranden till lagringskontots offentliga slutpunkt, såvida de inte kommer från [en specifikt tillåten källa.](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks) 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Begränsa åtkomsten till den offentliga slutpunkten till specifika virtuella nätverk

När du begränsar lagringskontot till specifika virtuella nätverk tillåter du begäranden till den offentliga slutpunkten från de angivna virtuella nätverken. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter*. Detta kan användas med eller utan privata slutpunkter.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Se även

- [Azure Files nätverksöverväganden](storage-files-networking-overview.md)
- [Konfigurera DNS-vidarebefordring för Azure Files](storage-files-networking-dns.md)
- [Konfigurera S2S VPN för Azure Files](storage-files-configure-s2s-vpn.md)