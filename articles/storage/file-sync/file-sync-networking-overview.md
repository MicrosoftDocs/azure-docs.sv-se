---
title: Azure File Sync nätverksöverväganden | Microsoft Docs
description: Lär dig hur du konfigurerar nätverk för Azure File Sync att cachelagra filer lokalt.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6c761edec571f404a538025c868750bc5712eced
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797302"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync nätverksöverväganden
Du kan ansluta till en Azure-filresurs på två sätt:

- Åtkomst till resursen direkt via SMB- eller FileREST-protokollen. Det här åtkomstmönstret används främst för att eliminera så många lokala servrar som möjligt.
- Skapa ett cacheminne för Azure-filresursen på en lokal server (eller på en virtuell Azure-dator) med Azure File Sync och komma åt filresursens data från den lokala servern med ditt val av protokoll (SMB, NFS, FTPS osv.) för ditt användningsfall. Det här åtkomstmönstret är praktiskt eftersom det kombinerar det bästa av både lokala prestanda och molnskala och serverlösa anslutningsbara tjänster, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användningsfall anropar för att använda Azure File Sync för att cachelagra filer lokalt i stället för att montera Azure-filresursen direkt över SMB. Mer information om nätverksöverväganden för en Azure Files-distribution finns i [Azure Files nätverksöverväganden](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

Nätverkskonfigurationen för Azure File Sync omfattar två olika Azure-objekt: en tjänst för synkronisering av lagring och ett Azure-lagringskonto. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer. En tjänst för synkronisering av lagring är en hanteringskonstruktion som representerar registrerade servrar, som är Windows-filservrar med en etablerad förtroenderelation med Azure File Sync och synkroniseringsgrupper, som definierar topologin för synkroniseringsrelationen. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Ansluta Windows-filserver till Azure med Azure File Sync 
Om du vill konfigurera Azure Files och Azure File Sync med en lokal Windows-filserver krävs inga särskilda nätverk för Azure utöver en grundläggande Internetanslutning. Om du Azure File Sync installera agenten Azure File Sync Windows-filserver som du vill synkronisera med Azure. Agenten Azure File Sync synkroniseras med en Azure-filresurs via två kanaler:

- FileREST-protokollet, som är ett HTTPS-baserat protokoll för åtkomst till din Azure-filresurs. Eftersom FileREST-protokollet använder standard-HTTPS för dataöverföring, måste endast port 443 vara tillgänglig för utgående trafik. Azure File Sync använder inte SMB-protokollet för att överföra data från dina lokala Windows-servrar till Azure-filresursen.
- Det Azure File Sync synkroniseringsprotokollet, vilket är ett HTTPS-baserat protokoll för utbyte av synkroniseringskunskaper, dvs. versionsinformation om filer och mappar i din miljö, mellan slutpunkter i din miljö. Det här protokollet används också för att utbyta metadata om filer och mappar i din miljö, till exempel tidsstämplar och åtkomstkontrollistor (ACL). 

Eftersom Azure Files erbjuder direkt SMB-protokollåtkomst på Azure-filresurser undrar kunderna ofta om de behöver konfigurera särskilda nätverk för att montera Azure-filresurser med SMB för att Azure File Sync agenten ska få åtkomst. Detta är inte bara obligatoriskt, utan rekommenderas inte heller, förutom för administratörsscenarier, på grund av bristen på snabb ändringsidentifiering för ändringar som görs direkt i Azure-filresursen (ändringar kanske inte identifieras under mer än 24 timmar beroende på storleken och antalet objekt i Azure-filresursen). Om du vill använda Azure-filresursen direkt, dvs. inte använda Azure File Sync för att cachelagra lokalt, kan du lära dig mer om nätverksöverväganden för direktåtkomst genom att läsa [översikten över Azure Files-nätverk.](../files/storage-files-networking-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

Även Azure File Sync inte kräver någon särskild nätverkskonfiguration, kan vissa kunder vilja konfigurera avancerade nätverksinställningar för att aktivera följande scenarier:

- Samverkan med din organisations proxyserverkonfiguration.
- Öppna organisationens lokala brandvägg till Azure Files och Azure File Sync tjänster.
- Tunnel Azure Files och Azure File Sync via ExpressRoute eller en VPN-anslutning.

### <a name="configuring-proxy-servers"></a>Konfigurera proxyservrar
Många organisationer använder en proxyserver som mellanhand mellan resurser i sina lokala nätverk och resurser utanför nätverket, till exempel i Azure. Proxyservrar är användbara för många program, till exempel nätverksisolering och säkerhet, samt övervakning och loggning. Azure File Sync kan fungera helt med en proxyserver, men du måste konfigurera inställningarna för proxyslutpunkten manuellt för din miljö med Azure File Sync. Detta måste göras via PowerShell med hjälp Azure File Sync server-cmdlets. 

Mer information om hur du konfigurerar Azure File Sync med en proxyserver finns i [Konfigurera Azure File Sync med en proxyserver.](file-sync-firewall-and-proxy.md)

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurera brandväggar och tjänsttaggar
Du kan isolera dina filservrar från de flesta Internetplatser av säkerhetsskäl. Om du Azure File Sync i din miljö måste du justera brandväggen för att öppna den och välja Azure-tjänster. Du kan göra detta genom att hämta IP-adressintervallen för de tjänster som du behöver via en mekanism som kallas [tjänsttaggar.](../../virtual-network/service-tags-overview.md)

Azure File Sync kräver IP-adressintervall för följande tjänster, som identifieras av deras tjänsttaggar:

| Tjänst | Beskrivning | Tjänsttagg |
|---------|-------------|-------------|
| Azure File Sync | Tjänsten Azure File Sync, som representeras av objektet Storage Sync Service, ansvarar för kärnaktiviteten för att synkronisera data mellan en Azure-filresurs och en Windows-filserver. | `StorageSyncService` |
| Azure Files | Alla data som synkroniseras via Azure File Sync lagras i Azure-filresursen. Filer som ändras på dina Windows-filservrar replikeras till din Azure-filresurs och filer som är nivåindelade på din lokala filserver laddas sömlöst ned när en användare begär dem. | `Storage` |
| Azure Resource Manager | Den Azure Resource Manager är hanteringsgränssnittet för Azure. Alla hanteringssamtal, inklusive Azure File Sync och pågående synkroniseringsserveraktiviteter, görs via Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory, eller Azure AD, innehåller de användarhuvudnamn som krävs för att auktorisera serverregistrering mot en tjänst för synkronisering av lagring, och de tjänsthuvudnamn som krävs för att Azure File Sync ha behörighet att komma åt dina molnresurser. | `AzureActiveDirectory` |

Om du använder Azure File Sync i Azure, även om det är en annan region, kan du använda namnet på tjänsttaggen direkt i nätverkssäkerhetsgruppen för att tillåta trafik till den tjänsten. Mer information om hur du gör detta finns i [Nätverkssäkerhetsgrupper.](../../virtual-network/network-security-groups-overview.md) 

Om du använder Azure File Sync lokalt kan du använda tjänsttagg-API:et för att hämta specifika IP-adressintervall för brandväggens lista över tillåtna adresser. Det finns två metoder för att hämta den här informationen:

- Den aktuella listan över IP-adressintervall för alla Azure-tjänster som stöder tjänsttaggar publiceras varje vecka på Microsoft Download Center i form av ett JSON-dokument. Varje Azure-moln har ett eget JSON-dokument med de IP-adressintervall som är relevanta för det molnet:
    - [Azure, offentlig](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
- Med API:et för identifiering av tjänsttaggar (förhandsversion) kan du hämta den aktuella listan med tjänsttaggar programmatiskt. I förhandsversionen kan API:et för identifiering av tjänsttaggar returnera information som är mindre aktuell än information som returneras från JSON-dokumenten som publicerats på Microsoft Download Center. Du kan använda API-ytan baserat på dina automatiseringsinställningar:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az_network_list_service_tags)

Mer information om hur du använder tjänsttagg-API:et för att hämta adresserna för dina tjänster finns i Listan Tillåt för [ip Azure File Sync-adresser](file-sync-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneltrafik över ett virtuellt privat nätverk eller ExpressRoute
Vissa organisationer kräver kommunikation med Azure för att gå via en nätverkstunnel, till exempel ett virtuellt privat nätverk (VPN) eller ExpressRoute, för ett extra säkerhetslager eller för att säkerställa att kommunikationen med Azure följer en deterministisk väg. 

När du upprättar en nätverkstunnel mellan ditt lokala nätverk och Azure peer-koppling mellan ditt lokala nätverk och ett eller flera virtuella nätverk i Azure. Ett [virtuellt](../../virtual-network/virtual-networks-overview.md)nätverk, eller VNet, liknar ett traditionellt nätverk som du använder lokalt. Precis som ett Azure Storage-konto eller en virtuell Azure-dator är ett virtuellt nätverk en Azure-resurs som distribueras i en resursgrupp. 

Azure Files och File Sync stöd för följande mekanismer för tunneltrafik mellan dina lokala servrar och Azure:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)En VPN-gateway är en specifik typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en alternativ plats (till exempel lokalt) via Internet. En Azure VPN Gateway är en Azure-resurs som kan distribueras i en resursgrupp tillsammans med ett lagringskonto eller andra Azure-resurser. Eftersom Azure File Sync är avsedd att användas med en lokal Windows-filserver använder du normalt en [plats-till-plats-VPN (S2S) VPN](../../vpn-gateway/design.md#s2smulti), även om det tekniskt sett är möjligt att använda en [punkt-till-plats-VPN (P2S) VPN.](../../vpn-gateway/point-to-site-about.md) 

    VPN-anslutningar för plats-till-plats (S2S) ansluter ditt virtuella Azure-nätverk och organisationens lokala nätverk. Med en VPN-anslutning mellan servrar kan du konfigurera en VPN-anslutning en gång, för en VPN-server eller enhet som finns i organisationens nätverk, i stället för att göra det för varje klientenhet som behöver åtkomst till din Azure-filresurs. Information om hur du förenklar distributionen av en S2S VPN-anslutning finns i Konfigurera ett [plats-till-plats-VPN (S2S) VPN](../files/storage-files-configure-s2s-vpn.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)för användning med Azure Files .

- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar Internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala datacenter och Azure kan ExpressRoute vara användbart när nätverksprestanda är ett övervägande. ExpressRoute är också ett bra alternativ när organisationens policy- eller regelkrav kräver en deterministisk väg till dina resurser i molnet.

### <a name="private-endpoints"></a>Privata slutpunkter
Förutom de offentliga standardslutpunkter som Azure Files och File Sync tillhandahåller via lagringskontot och tjänsten för synkronisering av lagring, ger Azure Files och File Sync möjlighet att ha en eller flera privata slutpunkter per resurs. När du skapar en privat slutpunkt för en Azure-resurs får den en privat IP-adress inifrån det virtuella nätverkets adressutrymme, ungefär som din lokala Windows-filserver har en IP-adress inom det dedikerade adressutrymmet för ditt lokala nätverk. 

> [!Important]  
> För att kunna använda privata slutpunkter på resursen för tjänsten för synkronisering av lagring måste du Azure File Sync version 10.1 eller senare. Agentversioner före 10.1 stöder inte privata slutpunkter i tjänsten för synkronisering av lagring. Alla tidigare agentversioner stöder privata slutpunkter på lagringskontoresursen.

En enskild privat slutpunkt är associerad med ett specifikt undernät för ett virtuellt Azure-nätverk. Lagringskonton och tjänster för synkronisering av lagring kan ha privata slutpunkter i mer än ett virtuellt nätverk.

Med privata slutpunkter kan du:
- Anslut säkert till dina Azure-resurser från lokala nätverk med hjälp av en VPN- eller ExpressRoute-anslutning med privat peering.
- Skydda dina Azure-resurser genom att inaktivera de offentliga slutpunkterna för Azure Files och File Sync. Som standard blockerar inte skapandet av en privat slutpunkt anslutningar till den offentliga slutpunkten.
- Öka säkerheten för det virtuella nätverket genom att blockera exfiltrering av data från det virtuella nätverket (och peeringgränser).

Information om hur du skapar en privat slutpunkt [finns i Konfigurera privata slutpunkter för Azure File Sync](file-sync-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privata slutpunkter och DNS
När du skapar en privat slutpunkt skapar vi som standard också en (eller uppdaterar en befintlig) privat DNS-zon som motsvarar `privatelink` underdomänen. För offentliga molnregioner är dessa DNS-zoner `privatelink.file.core.windows.net` för Azure Files och för `privatelink.afs.azure.net` Azure File Sync.

> [!Note]  
> I den här artikeln används lagringskontots DNS-suffix för de offentliga Azure-regionerna, `core.windows.net` . Den här kommentaren gäller även för nationella Azure-moln, till exempel Azure US Government-molnet och Azure China-molnet. Ersätt bara lämpliga suffix för din miljö. 

När du skapar privata slutpunkter för ett lagringskonto och en tjänst för synkronisering av lagring skapar vi A-poster för dem i deras respektive privata DNS-zoner. Vi uppdaterar också den offentliga DNS-posten så att de vanliga fullständigt kvalificerade domännamnen är CME:er för det relevanta privatelink-namnet. Detta gör att de fullständigt kvalificerade domännamnen kan peka på den privata slutpunktens IP-adress (es) när beställaren är inne i det virtuella nätverket och peka på den offentliga slutpunktens IP-adress (es) när beställaren är utanför det virtuella nätverket. 

För Azure Files har varje privat slutpunkt ett enda fullständigt kvalificerat domännamn, enligt mönstret , mappat till en privat `storageaccount.privatelink.file.core.windows.net` IP-adress för den privata slutpunkten. För Azure File Sync har varje privat slutpunkt fyra fullständigt kvalificerade domännamn för de fyra olika slutpunkter som Azure File Sync exponerar: hantering, synkronisering (primär), synkronisering (sekundär) och övervakning. De fullständigt kvalificerade domännamnen för dessa slutpunkter följer vanligtvis namnet på tjänsten för synkronisering av lagring såvida inte namnet innehåller icke-ASCII-tecken. Om namnet på tjänsten för synkronisering av lagring till exempel finns i regionen USA, västra 2 är motsvarande `mysyncservice` slutpunkter `mysyncservicemanagement.westus2.afs.azure.net` , , och `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` `mysyncservicemonitoring.westus2.afs.azure.net` . Varje privat slutpunkt för en tjänst för synkronisering av lagring innehåller fyra olika IP-adresser. 

Eftersom din privata Dns-zon i Azure är ansluten till det virtuella nätverk som innehåller den privata slutpunkten kan du observera DNS-konfigurationen när du anropar cmdleten från PowerShell på en virtuell `Resolve-DnsName` Azure-dator (alternativt `nslookup` i Windows och Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

I det här exemplet matchar `storageaccount.file.core.windows.net` lagringskontot den privata IP-adressen för den privata slutpunkten, vilket råkar vara `192.168.0.4` .

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


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

Om du kör samma kommando lokalt ser du att samma lagringskontonamn matchar den offentliga IP-adressen för lagringskontot i stället. `storageaccount.file.core.windows.net` är en CNAME-post för `storageaccount.privatelink.file.core.windows.net` , som i sin tur är en CNAME-post för Azure Storage-klustret som är värd för lagringskontot:

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

Detta återspeglar det faktum att Azure Files och Azure File Sync kan exponera både sina offentliga slutpunkter och en eller flera privata slutpunkter per resurs. För att säkerställa att de fullständigt kvalificerade domännamnen för dina resurser matchar de privata slutpunkterna privata IP-adresser måste du ändra konfigurationen på dina lokala DNS-servrar. Detta kan åstadkommas på flera olika sätt:

- Ändra värdfilen på klienterna så att de fullständigt kvalificerade domännamnen för dina lagringskonton och Storage Sync Services matchar de önskade privata IP-adresserna. Detta rekommenderas inte för produktionsmiljöer eftersom du behöver göra dessa ändringar för varje klient som behöver åtkomst till dina privata slutpunkter. Ändringar av dina privata slutpunkter/resurser (borttagningar, ändringar osv.) hanteras inte automatiskt.
- Skapa DNS-zoner på dina lokala servrar för och `privatelink.file.core.windows.net` med `privatelink.afs.azure.net` A-poster för dina Azure-resurser. Detta har fördelen att klienter i din lokala miljö automatiskt kan matcha Azure-resurser utan att behöva konfigurera varje klient, men den här lösningen är på liknande sätt skör när det gäller att ändra värdfilen eftersom ändringar inte återspeglas. Även om den här lösningen är spröd kan det vara det bästa valet för vissa miljöer.
- Vidarebefordra `core.windows.net` zonerna `afs.azure.net` och från dina lokala DNS-servrar till din privata Azure DNS-zon. Den privata Azure DNS-värden kan nås via en särskild IP-adress ( ) som endast är tillgänglig i virtuella nätverk som är länkade till den privata `168.63.129.16` DNS-zonen i Azure. För att lösa den här begränsningen kan du köra ytterligare DNS-servrar i ditt virtuella nätverk som vidarebefordrar och vidare till motsvarande `core.windows.net` `afs.azure.net` privata Azure DNS-zoner. För att förenkla den här konfigureringen har vi tillhandahållit PowerShell-cmdlets som automatiskt distribuerar DNS-servrar i ditt virtuella Azure-nätverk och konfigurerar dem efter behov. Information om hur du konfigurerar DNS-vidarebefordran finns i [Konfigurera DNS med Azure Files](../files/storage-files-networking-dns.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="encryption-in-transit"></a>Kryptering under överföring
Anslutningar som görs från Azure File Sync-agenten till din Azure-filresurs eller tjänsten för synkronisering av lagring krypteras alltid. Även om Azure-lagringskonton har en inställning för att inaktivera kryptering under överföring för kommunikation till Azure Files (och andra Azure-lagringstjänster som hanteras utanför lagringskontot), påverkar inte inaktiveringen av Azure File Sync-kryptering vid kommunikation med Azure Files. Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. 

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)