---
title: Azure File Sync nätverks överväganden | Microsoft Docs
description: Lär dig hur du konfigurerar nätverk att använda Azure File Sync för att cachelagra filer lokalt.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 76d97e3312c1df51193d8a881f3ee07fcd155d75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629367"
---
# <a name="azure-file-sync-networking-considerations"></a>Azure File Sync nätverks överväganden
Du kan ansluta till en Azure-filresurs på två sätt:

- Åtkomst till resursen direkt via SMB eller fileraste protokollen. Detta åtkomst mönster används i första hand för att eliminera så många lokala servrar som möjligt.
- Skapa ett cacheminne för Azure-filresursen på en lokal server (eller på en virtuell Azure-dator) med Azure File Sync och få åtkomst till fil resursens data från den lokala servern med ditt protokoll (SMB, NFS, FTPS osv.) för ditt användnings fall. Det här åtkomst mönstret är användbart eftersom det kombinerar det bästa av både lokala prestanda och moln skalning och tjänster som kan anslutas utan server, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användnings fall anropar för att använda Azure File Sync cachelagra filer lokalt i stället för att direkt montera Azure-filresursen över SMB. För ytterligare information om nätverks överväganden för en Azure Files distribution, se [Azure Files nätverks överväganden](storage-files-networking-overview.md).

Nätverks konfiguration för Azure File Sync sträcker sig över två olika Azure-objekt: en synkroniseringstjänst för lagring och ett Azure Storage-konto. Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. En tjänst för synkronisering av lagring är en hanterings konstruktion som representerar registrerade servrar, som är Windows-filservrar med en upprättad förtroende relation med Azure File Sync, och synkronisera grupper som definierar topologin för synkroniseringsrelation. 

## <a name="connecting-windows-file-server-to-azure-with-azure-file-sync"></a>Ansluta Windows-filserver till Azure med Azure File Sync 
Om du vill konfigurera och använda Azure Files och Azure File Sync med en lokal Windows-filserver krävs inget särskilt nätverk till Azure utöver en grundläggande Internet anslutning. Om du vill distribuera Azure File Sync installerar du Azure File Sync-agenten på Windows-filservern som du vill synkronisera med Azure. Azure File Sync agenten uppnår synkronisering med en Azure-filresurs via två kanaler:

- Det filbaserade protokollet, som är ett HTTPS-baserat protokoll för åtkomst till Azure-filresursen. Eftersom det fileraste protokollet använder standard-HTTPS för data överföring, måste endast port 443 vara tillgänglig utgående. Azure File Sync använder inte SMB-protokollet för att överföra data från dina lokala Windows-servrar till Azure-filresursen.
- Azure File Sync Sync-protokollet, som är ett HTTPS-baserat protokoll för att utväxla kunskap om synkronisering, d.v.s. versions informationen om filerna och mapparna i din miljö, mellan slut punkter i din miljö. Det här protokollet används också för att byta metadata om filerna och mapparna i din miljö, till exempel tidsstämplar och åtkomst kontrol listor (ACL: er). 

Eftersom Azure Files ger direkt åtkomst till SMB-protokollet på Azure-filresurser, undrar kunderna ofta om de behöver konfigurera särskilda nätverk för att montera Azure-filresurser med SMB för att Azure File Sync agenten ska få åtkomst. Detta är inte bara nödvändigt men rekommenderas inte, förutom i administratörs scenarier, på grund av bristen på snabb ändrings identifiering av ändringar som görs direkt till Azure-filresursen (ändringar kanske inte kan identifieras i mer än 24 timmar beroende på storleken på och antalet objekt i Azure-filresursen). Om du vill använda Azure-filresursen direkt, d.v.s. inte använder Azure File Sync för att cachelagra lokalt, kan du lära dig mer om nätverks överväganden för direkt åtkomst genom att konsultera [Azure Files nätverks översikt](storage-files-networking-overview.md).

Även om Azure File Sync inte kräver någon speciell nätverks konfiguration, kan vissa kunder vilja konfigurera avancerade nätverks inställningar för att möjliggöra följande scenarier:

- Samar beta med din organisations konfiguration av proxyservern.
- Öppna din organisations lokala brand vägg för Azure Files-och Azure File Sync-tjänsterna.
- Tunnel Azure Files och Azure File Sync över ExpressRoute eller VPN-anslutning.

### <a name="configuring-proxy-servers"></a>Konfigurera proxyservrar
Många organisationer använder en proxyserver som en mellanhand mellan resurser i det lokala nätverket och resurser utanför nätverket, till exempel i Azure. Proxyservrar är användbara för många program, till exempel nätverks isolering och säkerhet, samt övervakning och loggning. Azure File Sync kan samverka fullständigt med en proxyserver, men du måste manuellt konfigurera inställningarna för proxy-slutpunkten för din miljö med Azure File Sync. Detta måste göras via PowerShell med hjälp av Azure File Sync Server-cmdletar. 

Mer information om hur du konfigurerar Azure File Sync med en proxyserver finns i [konfigurera Azure File Sync med en proxyserver](storage-sync-files-firewall-and-proxy.md).

### <a name="configuring-firewalls-and-service-tags"></a>Konfigurera brand väggar och service märken
Du kan isolera dina fil servrar från den flesta Internet-platsen av säkerhets synpunkt. Om du vill använda Azure File Sync i din miljö måste du justera brand väggen för att öppna den för att välja Azure-tjänster. Du kan göra detta genom att hämta IP-adressintervall för de tjänster som du behöver via en mekanism som kallas [service märken](../../virtual-network/service-tags-overview.md).

Azure File Sync kräver IP-adressintervall för följande tjänster, som identifieras av deras tjänst koder:

| Tjänst | Beskrivning | Tjänsttagg |
|---------|-------------|-------------|
| Azure File Sync | Den Azure File Sync tjänsten, som representeras av objektet Storage Sync service, ansvarar för kärn aktiviteten för synkronisering av data mellan en Azure-filresurs och en Windows-filserver. | `StorageSyncService` |
| Azure Files | Alla data som synkroniseras via Azure File Sync lagras i Azure-filresursen. Filer som har ändrats på dina Windows-filservrar replikeras till Azure-filresursen och filer som skiktas på din lokala fil Server laddas sömlöst ned när en användare begär dem. | `Storage` |
| Azure Resource Manager | Azure Resource Manager är hanterings gränssnittet för Azure. Alla hanterings anrop, inklusive Azure File Sync Server registrering och pågående Sync Server-aktiviteter, görs via Azure Resource Manager. | `AzureResourceManager` |
| Azure Active Directory | Azure Active Directory eller Azure AD innehåller de användar huvud konton som krävs för att auktorisera Server registrering mot en tjänst för synkronisering av lagring och de tjänst huvud namn som krävs för att Azure File Sync ska få åtkomst till dina moln resurser. | `AzureActiveDirectory` |

Om du använder Azure File Sync i Azure, även om det är en annan region, kan du använda namnet på service tag gen direkt i nätverks säkerhets gruppen för att tillåta trafik till tjänsten. Mer information om hur du gör detta finns i [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md). 

Om du använder Azure File Sync lokalt kan du använda API: et för service tag för att hämta vissa IP-adressintervall för brand väggens lista över tillåtna. Det finns två metoder för att hämta den här informationen:

- Den aktuella listan över IP-adressintervall för alla tjänster som stöder service taggar för Azure-tjänster publiceras varje vecka på Microsoft Download Center i form av ett JSON-dokument. Varje Azure-moln har sitt eget JSON-dokument med de IP-adressintervall som är relevanta för det molnet:
    - [Offentlig Azure-](https://www.microsoft.com/download/details.aspx?id=56519)
    - [Azure US Government](https://www.microsoft.com/download/details.aspx?id=57063)
    - [Azure Kina](https://www.microsoft.com/download/details.aspx?id=57062)
    - [Azure Tyskland](https://www.microsoft.com/download/details.aspx?id=57064)
- API: et för identifiering av service tag (för hands version) möjliggör program mässig hämtning av den aktuella listan över service märken. I för hands versionen kan service tag Discovery API returnera information som är mindre aktuell än information som returneras från JSON-dokumenten som publicerats på Microsoft Download Center. Du kan använda API-ytan baserat på inställningarna för Automation:
    - [REST-API](/rest/api/virtualnetwork/servicetags/list)
    - [Azure PowerShell](/powershell/module/az.network/Get-AzNetworkServiceTag)
    - [Azure CLI](/cli/azure/network#az-network-list-service-tags)

Mer information om hur du använder service tag-API: et för att hämta adresserna för dina tjänster finns i [listan över tillåtna Azure File Sync IP-adresser](storage-sync-files-firewall-and-proxy.md#allow-list-for-azure-file-sync-ip-addresses).

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunnel trafik över ett virtuellt privat nätverk eller ExpressRoute
Vissa organisationer kräver kommunikation med Azure för att gå över en nätverks tunnel, till exempel ett virtuellt privat nätverk (VPN) eller ExpressRoute, för ett extra säkerhets lager eller för att säkerställa kommunikation med Azure efter en deterministisk väg. 

När du upprättar en nätverks tunnel mellan ditt lokala nätverk och Azure, peering du ditt lokala nätverk med ett eller flera virtuella nätverk i Azure. Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md), eller VNet, liknar ett traditionellt nätverk som du kommer att använda lokalt. Precis som ett Azure Storage-konto eller en virtuell Azure-dator är ett VNet en Azure-resurs som distribueras i en resurs grupp. 

Azure Files och File Sync stödja följande mekanismer för tunnel trafik mellan dina lokala servrar och Azure:

- [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): en VPN-gateway är en speciell typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en annan plats (till exempel lokalt) via Internet. En Azure-VPN Gateway är en Azure-resurs som kan distribueras i en resurs grupp tillsammans med ett lagrings konto eller andra Azure-resurser. Eftersom Azure File Sync är avsedd att användas med en lokal Windows-filserver, använder du normalt en [plats-till-plats (S2S) VPN](../../vpn-gateway/design.md#s2smulti), även om det är tekniskt möjligt att använda en P2s- [VPN (punkt-till-plats)](../../vpn-gateway/point-to-site-about.md). 

    VPN-anslutningar för plats-till-plats (S2S) ansluter ditt virtuella Azure-nätverk och organisationens lokala nätverk. Med en S2S VPN-anslutning kan du konfigurera en VPN-anslutning en gång, för en VPN-server eller en enhet som finns i din organisations nätverk, i stället för att göra för varje klient enhet som behöver åtkomst till Azure-filresursen. Information om hur du fören klar distributionen av en S2S VPN-anslutning finns i [Konfigurera en plats-till-plats (S2S) VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).

- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar Internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala data Center och Azure, kan ExpressRoute vara användbart när nätverks prestanda är ett övervägande. ExpressRoute är också ett användbart alternativ när din organisations policy eller myndighets krav kräver en deterministisk sökväg till dina resurser i molnet.

### <a name="private-endpoints"></a>Privata slutpunkter
Förutom standard-offentliga slut punkter Azure Files och File Sync tillhandahålla lagrings kontot och tjänsten för synkronisering av lagring, Azure Files och File Sync ger möjlighet att ha en eller flera privata slut punkter per resurs. När du skapar en privat slut punkt för en Azure-resurs får den en privat IP-adress från det virtuella nätverkets adress utrymme, ungefär som hur din lokala Windows-filserver har en IP-adress inom det dedikerade adress utrymmet i ditt lokala nätverk. 

> [!Important]  
> Du måste använda Azure File Sync agent version 10,1 eller senare för att kunna använda privata slut punkter i resursens synkroniseringstjänst för lagring. Agent versioner före 10,1 stöder inte privata slut punkter på tjänsten för synkronisering av lagring. Alla tidigare agent versioner stöder privata slut punkter på lagrings konto resursen.

En privat privat slut punkt är associerad med ett specifikt Azure Virtual Network-undernät. Lagrings konton och lagrings tjänster för synkronisering kan ha privata slut punkter i fler än ett virtuellt nätverk.

Med privata slut punkter kan du:
- Anslut säkert till dina Azure-resurser från lokala nätverk med en VPN-eller ExpressRoute-anslutning med privat peering.
- Skydda dina Azure-resurser genom att inaktivera de offentliga slut punkterna för Azure Files och File Sync. Som standard blockeras inte anslutningar till den offentliga slut punkten genom att skapa en privat slut punkt.
- Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket (och peering-gränser).

Information om hur du skapar en privat slut punkt finns i [Konfigurera privata slut punkter för Azure File Sync](storage-sync-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privata slut punkter och DNS
När du skapar en privat slut punkt skapar vi som standard också en (eller uppdaterar en befintlig) privat DNS-zon som motsvarar under `privatelink` domänen. För offentliga moln regioner är dessa DNS-zoner `privatelink.file.core.windows.net` för Azure Files och `privatelink.afs.azure.net` för Azure File Sync.

> [!Note]  
> I den här artikeln används DNS-suffixet för lagrings kontot för Azures offentliga regioner `core.windows.net` . Den här kommentarer gäller också för Azures suveräna moln, till exempel Azure-molnet för amerikanska myndigheter och molnet i molnet, och ersätter bara de nödvändiga suffixen för din miljö. 

När du skapar privata slut punkter för ett lagrings konto och en tjänst för synkronisering av lagring, skapar vi en post för dem i sina respektive privata DNS-zoner. Vi uppdaterar också den offentliga DNS-posten så att de vanliga fullständigt kvalificerade domän namnen är CNAME för det relevanta privatelink-namnet. Detta gör att de fullständigt kvalificerade domän namnen kan peka på den privata slut punktens IP-adress (er) när beställaren finns i det virtuella nätverket och för att peka på den offentliga slut punktens IP-adress (er) när beställaren är utanför det virtuella nätverket. 

För Azure Files har varje privat slut punkt ett enda fullständigt kvalificerat domän namn, efter mönstret `storageaccount.privatelink.file.core.windows.net` , som mappas till en privat IP-adress för den privata slut punkten. För Azure File Sync har varje privat slut punkt fyra fullständigt kvalificerade domän namn för de fyra olika slut punkter som Azure File Sync exponerar: hantering, synkronisering (primär), synkronisering (sekundär) och övervakning. De fullständigt kvalificerade domän namnen för dessa slut punkter följer vanligt vis namnet på tjänsten Storage Sync, om inte namnet innehåller icke-ASCII-tecken. Om ditt lagrings namn för synkroniseringstjänsten till exempel finns `mysyncservice` i regionen USA, västra 2, blir motsvarande slut punkter,, `mysyncservicemanagement.westus2.afs.azure.net` `mysyncservicesyncp.westus2.afs.azure.net` `mysyncservicesyncs.westus2.afs.azure.net` , och `mysyncservicemonitoring.westus2.afs.azure.net` . Varje privat slut punkt för en synkroniseringstjänst för lagring kommer att innehålla 4 distinkta IP-adresser. 

Eftersom din privata Azure-DNS-zon är ansluten till det virtuella nätverket som innehåller den privata slut punkten kan du se DNS-konfigurationen när du anropar `Resolve-DnsName` cmdleten från PowerShell på en virtuell Azure-dator (alternativt `nslookup` i Windows och Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

I det här exemplet matchar lagrings kontot den privata `storageaccount.file.core.windows.net` IP-adressen för den privata slut punkten, vilket sker `192.168.0.4` .

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

Om du kör samma kommando från en lokal plats ser du att samma lagrings konto namn matchar den offentliga IP-adressen för lagrings kontot i stället. `storageaccount.file.core.windows.net` är en CNAME-post för `storageaccount.privatelink.file.core.windows.net` , vilket i sin tur är en CNAME-post för Azure Storage-kluster som är värd för lagrings kontot:

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

Detta återspeglar det faktum att Azure Files och Azure File Sync kan exponera både sina offentliga slut punkter och en eller flera privata slut punkter per resurs. För att säkerställa att de fullständigt kvalificerade domän namnen för dina resurser matchar privata IP-adresser för privata slut punkter måste du ändra konfigurationen på dina lokala DNS-servrar. Detta kan åstadkommas på flera sätt:

- Att ändra hosts-filen på klienterna så att de fullständigt kvalificerade domän namnen för dina lagrings konton och lagrings tjänster för synkronisering matchas mot önskade privata IP-adresser. Detta rekommenderas inte för produktions miljöer eftersom du behöver göra dessa ändringar på varje klient som behöver åtkomst till dina privata slut punkter. Ändringar i dina privata slut punkter/resurser (borttagningar, ändringar osv.) hanteras inte automatiskt.
- Skapa DNS-zoner på dina lokala servrar för `privatelink.file.core.windows.net` och `privatelink.afs.azure.net` med en post för dina Azure-resurser. Detta har fördelen att klienter i din lokala miljö automatiskt kan lösa Azure-resurser utan att behöva konfigurera varje klient, men den här lösningen är också sårbar för att ändra värd filen eftersom ändringar inte återspeglas. Även om den här lösningen är sårbar kan det vara det bästa valet för vissa miljöer.
- Vidarebefordra `core.windows.net` zonerna och `afs.azure.net` från dina lokala DNS-servrar till din Azures privata DNS-zon. Den privata Azure-DNS-värden kan nås via en särskild IP-adress ( `168.63.129.16` ) som endast är tillgänglig i virtuella nätverk som är länkade till Azures privata DNS-zon. För att lösa den här begränsningen kan du köra ytterligare DNS-servrar i ditt virtuella nätverk som kommer att vidarebefordra `core.windows.net` och `afs.azure.net` till motsvarande Azure privata DNS-zoner. För att förenkla den här konfigurationen har vi tillhandahållit PowerShell-cmdletar som automatiskt distribuerar DNS-servrar i det virtuella Azure-nätverket och konfigurerar dem efter behov. Information om hur du konfigurerar DNS-vidarebefordran finns i [Konfigurera DNS med Azure Files](storage-files-networking-dns.md).

## <a name="encryption-in-transit"></a>Kryptering under överföring
Anslutningar som görs från Azure File Sync agent till Azure-filresursen eller lagrings tjänsten för synkronisering krypteras alltid. Även om Azure Storage-konton har en inställning för att inaktivera krav på kryptering vid överföring av kommunikation till Azure Files (och de andra Azure Storage-tjänster som hanteras utanför lagrings kontot), kommer inaktive ring av den här inställningen inte att påverka Azure File Syncens kryptering när den kommunicerar med Azure Files. Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. 

Mer information om kryptering i överföring finns i [krav på säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)