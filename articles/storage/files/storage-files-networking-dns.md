---
title: Konfigurera DNS-vidarebefordran för Azure Files | Microsoft Docs
description: Lär dig hur du konfigurerar DNS-vidarebefordran för Azure Files.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/19/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9abe306668a4b20e42e45c498bf85b540dfaaee5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94630200"
---
# <a name="configuring-dns-forwarding-for-azure-files"></a>Konfigurera DNS-vidarebefordring för Azure Files
Med Azure Files kan du skapa privata slut punkter för lagrings kontona som innehåller dina fil resurser. Även om det är användbart för många olika program är privata slut punkter särskilt användbara för att ansluta till dina Azure-filresurser från ditt lokala nätverk med hjälp av en VPN-eller ExpressRoute-anslutning med privat peering. 

För att anslutningar till ditt lagrings konto ska gå över nätverks tunneln måste det fullständigt kvalificerade domän namnet (FQDN) för ditt lagrings konto matcha den privata IP-adressen för den privata slut punkten. För att uppnå detta måste du vidarebefordra Storage Endpoint-suffixet ( `core.windows.net` för offentliga moln regioner) till den Azure privata DNS-tjänst som är tillgänglig från det virtuella nätverket. Den här guiden visar hur du konfigurerar och konfigurerar DNS-vidarebefordran så att de matchar lagrings kontots privata slut punkts-IP-adress.

Vi rekommenderar starkt att du läser [planering för en Azure Files distribution](storage-files-planning.md) och [Azure Files nätverks överväganden](storage-files-networking-overview.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="overview"></a>Översikt
Azure Files tillhandahåller två huvud typer av slut punkter för åtkomst till Azure-fil resurser: 
- Offentliga slut punkter som har en offentlig IP-adress och som kan nås från var som helst i världen.
- Privata slut punkter, som finns i ett virtuellt nätverk och har en privat IP-adress inom det virtuella nätverkets adress utrymme.

Offentliga och privata slut punkter finns på Azure Storage-kontot. Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer.

Varje lagrings konto har ett fullständigt kvalificerat domän namn (FQDN). I de offentliga moln regionerna följer detta FQDN mönstret `storageaccount.file.core.windows.net` där `storageaccount` är namnet på lagrings kontot. När du gör förfrågningar med det här namnet, t. ex. genom att montera resursen på din arbets station med hjälp av SMB, utför operativ systemet en DNS-sökning för att matcha det fullständigt kvalificerade domän namnet till en IP-adress som den kan använda för att skicka SMB-begäranden till.

Som standard `storageaccount.file.core.windows.net` matchas den offentliga slut punktens IP-adress. Den offentliga slut punkten för ett lagrings konto finns på ett Azure Storage-kluster som är värd för många andra lagrings kontots offentliga slut punkter. När du skapar en privat slut punkt länkas en privat DNS-zon till det virtuella nätverk som den lades till i, med en CNAME-Postmappning `storageaccount.file.core.windows.net` till en post post för den privata IP-adressen för lagrings kontots privata slut punkt. På så sätt kan du använda `storageaccount.file.core.windows.net` FQDN i det virtuella nätverket och matcha den privata slut punktens IP-adress.

Eftersom vårt slutgiltiga mål är att komma åt Azure-filresurser som finns i lagrings kontot från lokalt med hjälp av en nätverks tunnel, till exempel en VPN-eller ExpressRoute-anslutning, måste du konfigurera dina lokala DNS-servrar så att de vidarebefordrar begär Anden som görs till tjänsten Azure Files till Azures privata DNS-tjänst. För att åstadkomma detta måste du konfigurera *villkorlig vidarebefordran* av `*.core.windows.net` (eller lämpligt suffix för lagrings slut punkt för amerikanska myndigheter, Tyskland, eller Kinas nationella moln) till en DNS-server som finns i ditt virtuella Azure-nätverk. DNS-servern vidarebefordrar sedan begäran vidare till Azures privata DNS-tjänst som kommer att matcha det fullständigt kvalificerade domän namnet för lagrings kontot med en lämplig privat IP-adress.

Om du konfigurerar DNS-vidarebefordran för Azure Files måste du köra en virtuell dator som värd för en DNS-server för att vidarebefordra förfrågningarna, men det här är ett engångs steg för alla Azure-filresurser som finns i det virtuella nätverket. Dessutom är detta inte ett exklusivt krav på att Azure Files – vilken Azure-tjänst som helst som stöder privata slut punkter som du vill komma åt från den lokala tjänsten kan använda DNS-vidarebefordran som du konfigurerar i den här guiden: Azure Blob Storage, SQL Azure, Cosmos DB osv. 

I den här guiden visas stegen för att konfigurera DNS-vidarebefordran för Azure Storage-slutpunkten, så förutom Azure Files, kommer DNS-namn matchnings begär Anden för alla andra Azure Storage-tjänster (Azure Blob Storage, Azure Table Storage, Azure Queue Storage osv.) att vidarebefordras till Azures privata DNS-tjänst. Ytterligare slut punkter för andra Azure-tjänster kan också läggas till om du vill. DNS-vidarebefordring tillbaka till dina lokala DNS-servrar konfigureras också, vilket aktiverar moln resurser i det virtuella nätverket (till exempel en DFS-N-Server) för att lösa lokala dator namn. 

## <a name="prerequisites"></a>Förutsättningar
Innan du kan konfigurera DNS-vidarebefordran till Azure Files måste du utföra följande steg:

- Ett lagrings konto som innehåller en Azure-filresurs som du vill montera. Information om hur du skapar ett lagrings konto och en Azure-filresurs finns i [skapa en Azure-filresurs](storage-how-to-create-file-share.md).
- En privat slut punkt för lagrings kontot. Information om hur du skapar en privat slut punkt för Azure Files finns i [skapa en privat slut punkt](storage-files-networking-endpoints.md#create-a-private-endpoint).
- Den [senaste versionen](/powershell/azure/install-az-ps) av Azure PowerShell-modulen.

> [!Important]  
> Den här guiden förutsätter att du använder DNS-servern i Windows Server i din lokala miljö. Alla steg som beskrivs i den här guiden är möjliga med alla DNS-servrar, inte bara Windows DNS-servern.

## <a name="manually-configuring-dns-forwarding"></a>Konfigurera DNS-vidarebefordran manuellt
Om du redan har DNS-servrar på plats i ditt virtuella Azure-nätverk, eller om du bara föredrar att distribuera dina egna virtuella datorer som DNS-servrar av vilken metod organisationen använder, kan du konfigurera DNS manuellt med de inbyggda PowerShell-cmdletarna för DNS-servern.

På dina lokala DNS-servrar skapar du en villkorlig vidarebefordrare med hjälp av `Add-DnsServerConditionalForwarderZone` . Den här villkorliga vidarebefordraren måste distribueras på alla lokala DNS-servrar för att effektivt vidarebefordra trafik till Azure. Kom ihåg att ersätta `<azure-dns-server-ip>` med rätt IP-adresser för din miljö.

```powershell
$vnetDnsServers = "<azure-dns-server-ip>", "<azure-dns-server-ip>"

$storageAccountEndpoint = Get-AzContext | `
    Select-Object -ExpandProperty Environment | `
    Select-Object -ExpandProperty StorageEndpointSuffix

Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers $vnetDnsServers
```

På DNS-servrarna i ditt virtuella Azure-nätverk måste du också placera en vidarebefordrare på plats, så att begär Anden för lagrings kontots DNS-zon dirigeras till den privata Azure-DNS-tjänsten, som är övervakad av den reserverade IP-adressen `168.63.129.16` . (Kom ihåg att fylla i `$storageAccountEndpoint` om du kör kommandona i en annan PowerShell-session.)

```powershell
Add-DnsServerConditionalForwarderZone `
        -Name $storageAccountEndpoint `
        -MasterServers "168.63.129.16"
```

## <a name="using-the-azure-files-hybrid-module-to-configure-dns-forwarding"></a>Använda Azure Files hybrid-modulen för att konfigurera vidarebefordring av DNS
För att göra det så enkelt som möjligt att konfigurera DNS-vidarebefordran har vi tillhandahållit automatisering i Azure Files hybrid-modulen. De cmdletar som tillhandahålls för att manipulera DNS i den här modulen hjälper dig att distribuera DNS-servrar i det virtuella Azure-nätverket och uppdatera dina lokala DNS-servrar för att vidarebefordra till dem. 

Om du aldrig har använt Azure Files hybrid-modulen måste du först installera den på din arbets Station. Ladda ned den [senaste versionen](https://github.com/Azure-Samples/azure-files-samples/releases) av Azure Files hybrid PowerShell-modulen:

```PowerShell
# Unzip the downloaded file
Expand-Archive -Path AzFilesHybrid.zip

# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\AzFilesHybrid\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid
```

Att distribuera lösningen för DNS-vidarebefordran har två steg, som skapar en regel uppsättning för DNS-vidarebefordran, som definierar vilka Azure-tjänster som du vill vidarebefordra begär anden till och den faktiska distributionen av DNS-vidarebefordrarna. 

I följande exempel vidarebefordras begär anden till lagrings kontot, inklusive begär anden till Azure Files, Azure Blob Storage, Azure Table Storage och Azure Queue Storage. Om du vill kan du lägga till vidarebefordran för ytterligare Azure-tjänster till regeln via `-AzureEndpoints` parametern för `New-AzDnsForwardingRuleSet` cmdleten. Kom ihåg att ersätta `<virtual-network-resource-group>` , `<virtual-network-name>` och `<subnet-name>` med lämpliga värden för din miljö.

```PowerShell
# Create a rule set, which defines the forwarding rules
$ruleSet = New-AzDnsForwardingRuleSet -AzureEndpoints StorageAccountEndpoint

# Deploy and configure DNS forwarders
New-AzDnsForwarder `
        -DnsForwardingRuleSet $ruleSet `
        -VirtualNetworkResourceGroupName "<virtual-network-resource-group>" `
        -VirtualNetworkName "<virtual-network-name>" `
        -VirtualNetworkSubnetName "<subnet-name>"
```

Du kanske också tycker att det är användbart/nödvändigt att tillhandahålla flera ytterligare parametrar:

| Parameternamn | Typ | Beskrivning |
|----------------|------|-------------|
| `DnsServerResourceGroupName` | `string` | Som standard kommer DNS-servrarna att distribueras till samma resurs grupp som det virtuella nätverket. Om du inte vill använda den här parametern kan du välja en alternativ resurs grupp som du vill distribuera till. |
| `DnsForwarderRootName` | `string` | Som standard har DNS-servrarna som distribueras i Azure namn `DnsFwder-*` , där asterisken fylls med en iterator. Den här parametern ändrar roten för det namnet (t. ex. `DnsFwder` ). |
| `VmTemporaryPassword` | `SecureString` | Som standard väljs ett slumpmässigt lösen ord för det tillfälliga standard kontot som en virtuell dator har innan det är domänanslutna. När den är domänansluten är standard kontot inaktiverat. |
| `DomainToJoin` | `string` | Domänen som ska ansluta till de virtuella DNS-datorerna för anslutning. Som standard väljs den här domänen utifrån domänen på den dator där du kör cmdletarna. |
| `DnsForwarderRedundancyCount` | `int` | Antalet virtuella DNS-datorer som ska distribueras för det virtuella nätverket. Som standard `New-AzDnsForwarder` distribuerar två DNS-servrar i ditt virtuella Azure-nätverk i en tillgänglighets uppsättning för att säkerställa redundans. Det här antalet kan ändras efter behov. |
| `OnPremDnsHostNames` | `HashSet<string>` | En manuellt angiven lista med lokala DNS-värdnamn för att skapa vidarebefordrare på. Den här parametern är användbar när du inte vill tillämpa vidarebefordrare på alla lokala DNS-servrar, t. ex. När du har ett intervall med klienter med manuellt angivna DNS-namn. |
| `Credential` | `PSCredential` | En autentiseringsuppgift som ska användas vid uppdatering av DNS-servrarna. Detta är användbart när det användar konto som du har loggat in med inte har behörighet att ändra DNS-inställningar. |
| `SkipParentDomain` | `SwitchParameter` | Som standard tillämpas DNS-vidarebefordrare på den högsta domän som finns i din miljö. Om till exempel `northamerica.corp.contoso.com` är en underordnad domän till `corp.contoso.com` , kommer vidarebefordraren att skapas för de DNS-servrar som är kopplade till `corp.contoso.com` . Den här parametern gör att vidarebefordrare skapas i `northamerica.corp.contoso.com` . |

## <a name="confirm-dns-forwarders"></a>Bekräfta DNS-vidarebefordrare
Innan du testar för att se om DNS-vidarebefordrarna har tillämpats, rekommenderar vi att du rensar DNS-cachen på den lokala arbets stationen med `Clear-DnsClientCache` . Om du vill testa för att se om du kan lösa det fullständigt kvalificerade domän namnet för ditt lagrings konto använder du `Resolve-DnsName` eller `nslookup` .

```powershell
# Replace storageaccount.file.core.windows.net with the appropriate FQDN for your storage account.
# Note the proper suffix (core.windows.net) depends on the cloud your deployed in.
Resolve-DnsName -Name storageaccount.file.core.windows.net
```

Om namn matchningen lyckas bör du se den matchade IP-adressen matcha IP-adressen för ditt lagrings konto.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4
```

Om du redan har konfigurerat en VPN-eller ExpressRoute-anslutning kan du också använda `Test-NetConnection` för att se att en TCP-anslutning kan göras till ditt lagrings konto.

```PowerShell
Test-NetConnection -ComputerName storageaccount.file.core.windows.net -CommonTCPPort SMB
```

## <a name="see-also"></a>Se även
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Azure Files nätverks överväganden](storage-files-networking-overview.md)
- [Konfigurera Azure Files nätverks slut punkter](storage-files-networking-endpoints.md)