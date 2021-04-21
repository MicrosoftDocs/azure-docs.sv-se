---
title: Azure Files nätverksöverväganden | Microsoft Docs
description: En översikt över nätverksalternativ för Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1dce7795b8c62c36b80c51d5ba0dd8bc9b667e0e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759695"
---
# <a name="azure-files-networking-considerations"></a>Azure Files nätverksöverväganden 
Du kan ansluta till en Azure-filresurs på två sätt:

- Åtkomst till resursen direkt via Server Message Block (SMB), Network File System (NFS) (förhandsversion) eller FileREST-protokoll. Det här åtkomstmönstret används främst för att eliminera så många lokala servrar som möjligt.
- Skapa ett cacheminne för Azure-filresursen på en lokal server (eller på en virtuell Azure-dator) med Azure File Sync och komma åt filresursens data från den lokala servern med ditt val av protokoll (SMB, NFS, FTPS osv.) för ditt användningsfall. Det här åtkomstmönstret är praktiskt eftersom det kombinerar det bästa av både lokala prestanda och molnskala och serverlösa anslutningsbara tjänster, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användningsfall anropar för åtkomst till Azure-filresursen direkt i stället för att använda Azure File Sync. Mer information om nätverksöverväganden för en Azure File Sync-distribution finns [i Azure File Sync nätverksöverväganden](../file-sync/file-sync-networking-overview.md).

Nätverkskonfigurationen för Azure-filresurser görs på Azure-lagringskontot. Ett lagringskonto är en hanteringskonstruktion som representerar en delad lagringspool där du kan distribuera flera filresurser, samt andra lagringsresurser, till exempel blobcontainrar eller köer. Lagringskonton exponerar flera inställningar som hjälper dig att skydda nätverksåtkomsten till dina filresurser: nätverksslutpunkter, brandväggsinställningar för lagringskonton och kryptering under överföring. 

Vi rekommenderar att du [läser Planera för en Azure Files distribution innan](storage-files-planning.md) du läser den här konceptuella guiden.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Den här videon är en guide och demo för hur du på ett säkert sätt exponerar Azure-filresurser direkt för informationsarbetare och appar i fem enkla steg. Avsnitten nedan innehåller länkar och ytterligare kontext till den dokumentation som refereras i videon.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Åtkomst till dina Azure-filresurser
När du distribuerar en Azure-filresurs inom ett lagringskonto är filresursen omedelbart tillgänglig via lagringskontots offentliga slutpunkt. Det innebär att autentiserade begäranden, till exempel begäranden som auktoriserats av en användares inloggningsidentitet, kan komma inifrån eller utanför Azure på ett säkert sätt. 

I många kundmiljöer misslyckas en inledande montering av Azure-filresursen på din lokala arbetsstation, även om monteringar från virtuella Azure-datorer lyckas. Orsaken till detta är att många organisationer och Internetleverantörer blockerar porten som SMB använder för att kommunicera, port 445. NFS-resurser har inte det här problemet. Den här praxis kommer från säkerhetsvägledning om äldre och föråldrade versioner av SMB-protokollet. Även om SMB 3.0 är ett internetsäkert protokoll är äldre versioner av SMB, särskilt inte SMB 1.0. Azure-filresurser kan bara nås externt via SMB 3.0 och FileREST-protokollet (som också är ett internetsäkert protokoll) via den offentliga slutpunkten.

Eftersom det enklaste sättet att komma åt din Azure SMB-filresurs från den lokala miljön är att öppna ditt lokala nätverk till port 445 rekommenderar Microsoft följande steg för att ta bort SMB 1.0 från din miljö:

1. Se till att SMB 1.0 tas bort eller inaktiveras på organisationens enheter. Alla versioner av Windows och Windows Server som stöds för närvarande stöder borttagning eller inaktivering av SMB 1.0 och från och med Windows 10 version 1709 är SMB 1.0 inte installerat på Windows som standard. Mer information om hur du inaktiverar SMB 1.0 finns på våra OS-specifika sidor:
    - [Skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Skydda Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Se till att inga produkter i din organisation kräver SMB 1.0 och ta bort dem som gör det. Vi har ett [SMB1 Product Clearinghouse](https://aka.ms/stillneedssmb1)som innehåller alla första och tredje parts produkter som Microsoft känner till kräver SMB 1.0. 
1. (Valfritt) Använd en brandvägg från tredje part med organisationens lokala nätverk för att förhindra att SMB 1.0-trafik lämnar organisationens gräns.

Om din organisation kräver att port 445 blockeras enligt princip eller regelverk, eller om din organisation kräver att trafik till Azure följer en deterministisk väg, kan du använda Azure VPN Gateway eller ExpressRoute för att dirigera trafik till dina Azure-filresurser. NFS-resurser kräver inte något av detta eftersom de inte behöver port 445.

> [!Important]  
> Även om du väljer att använda en alternativ metod för att komma åt dina Azure-filresurser rekommenderar Microsoft fortfarande att du tar bort SMB 1.0 från din miljö.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunneltrafik över ett virtuellt privat nätverk eller ExpressRoute
När du upprättar en nätverkstunnel mellan ditt lokala nätverk och Azure peer-koppling mellan ditt lokala nätverk och ett eller flera virtuella nätverk i Azure. Ett [virtuellt](../../virtual-network/virtual-networks-overview.md)nätverk, eller VNet, liknar ett traditionellt nätverk som du använder lokalt. Precis som ett Azure Storage-konto eller en virtuell Azure-dator är ett virtuellt nätverk en Azure-resurs som distribueras i en resursgrupp. 

Azure Files har stöd för följande metoder för tunneltrafik mellan dina lokala arbetsstationer och servrar och Azure SMB/NFS-filresurser:

- [Azure VPN Gateway:](../../vpn-gateway/vpn-gateway-about-vpngateways.md)En VPN-gateway är en specifik typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en alternativ plats (till exempel lokalt) via Internet. En Azure VPN Gateway är en Azure-resurs som kan distribueras i en resursgrupp tillsammans med ett lagringskonto eller andra Azure-resurser. VPN-gatewayer exponerar två olika typer av anslutningar:
    - [P2S VPN-gatewayanslutningar (punkt-till-plats),](../../vpn-gateway/point-to-site-about.md) som är VPN-anslutningar mellan Azure och en enskild klient. Den här lösningen är främst användbar för enheter som inte ingår i organisationens lokala nätverk, till exempel distansarbetare som vill kunna montera sin Azure-filresurs hemifrån, ett kafé eller hotell när de är på vägen. Om du vill använda en P2S VPN-anslutning Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. Information om hur du förenklar distributionen av en P2S VPN-anslutning finns i Konfigurera en [punkt-till-plats-VPN (P2S) VPN](storage-files-configure-p2s-vpn-windows.md) på Windows för användning med Azure Files och Konfigurera ett [P2S-VPN (punkt-till-plats) på Linux](storage-files-configure-p2s-vpn-linux.md)för användning med Azure Files .
    - [Plats-till-plats(S2S) VPN](../../vpn-gateway/design.md#s2smulti), som är VPN-anslutningar mellan Azure och din organisations nätverk. Med en VPN-anslutning mellan servrar kan du konfigurera en VPN-anslutning en gång, för en VPN-server eller enhet som finns i organisationens nätverk, i stället för att göra det för varje klientenhet som behöver åtkomst till din Azure-filresurs. Information om hur du förenklar distributionen av en S2S VPN-anslutning finns i Konfigurera ett [plats-till-plats-VPN (S2S) VPN](storage-files-configure-s2s-vpn.md)för användning med Azure Files .
- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar Internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala datacenter och Azure kan ExpressRoute vara användbart när nätverksprestanda är ett övervägande. ExpressRoute är också ett bra alternativ när organisationens policy- eller regelkrav kräver en deterministisk väg till dina resurser i molnet.

Oavsett vilken tunnelmetod du använder för att komma åt dina Azure-filresurser behöver du en mekanism för att se till att trafiken till ditt lagringskonto går via tunneln i stället för din vanliga Internetanslutning. Tekniskt sett är det möjligt att dirigera till lagringskontots offentliga slutpunkt, men detta kräver hårdkodning av alla IP-adresser för Azure-lagringskluster i en region, eftersom lagringskonton kan flyttas mellan lagringskluster när som helst. Detta kräver också kontinuerlig uppdatering av IP-adressmappningarna eftersom nya kluster läggs till hela tiden.

I stället för att hårdkoda IP-adressen för dina lagringskonton till dina VPN-routningsregler rekommenderar vi att du använder privata slutpunkter, som ger ditt lagringskonto en IP-adress från adressutrymmet för ett virtuellt Azure-nätverk. Eftersom du skapar en tunnel till Azure och upprättar peering mellan ditt lokala nätverk och ett eller flera virtuella nätverk möjliggör detta korrekt routning på ett beständigt sätt.

### <a name="private-endpoints"></a>Privata slutpunkter
Förutom den offentliga standardslutpunkten för ett lagringskonto ger Azure Files alternativet att ha en eller flera privata slutpunkter. En privat slutpunkt är en slutpunkt som endast är tillgänglig i ett virtuellt Azure-nätverk. När du skapar en privat slutpunkt för ditt lagringskonto får ditt lagringskonto en privat IP-adress inifrån adressutrymmet för ditt virtuella nätverk, ungefär som hur en lokal filserver eller NAS-enhet tar emot en IP-adress inom det dedikerade adressutrymmet i ditt lokala nätverk. 

En enskild privat slutpunkt är associerad med ett specifikt undernät för ett virtuellt Azure-nätverk. Ett lagringskonto kan ha privata slutpunkter i mer än ett virtuellt nätverk.

Genom att använda privata slutpunkter Azure Files kan du:
- Anslut säkert till dina Azure-filresurser från lokala nätverk med hjälp av en VPN- eller ExpressRoute-anslutning med privat peering.
- Skydda dina Azure-filresurser genom att konfigurera brandväggen för lagringskontot så att den blockerar alla anslutningar på den offentliga slutpunkten. Som standard blockerar inte skapandet av en privat slutpunkt anslutningar till den offentliga slutpunkten.
- Öka säkerheten för det virtuella nätverket genom att blockera exfiltrering av data från det virtuella nätverket (och peeringgränser).

Information om hur du skapar en privat slutpunkt [finns i Konfigurera privata slutpunkter för Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privata slutpunkter och DNS
När du skapar en privat slutpunkt skapar vi som standard också en (eller uppdaterar en befintlig) privat DNS-zon som motsvarar `privatelink` underdomänen. Strikt sett behöver du inte skapa en privat DNS-zon för att använda en privat slutpunkt för ditt lagringskonto, men det rekommenderas i allmänhet och uttryckligen när du monterar din Azure-filresurs med ett Active Directory-användarhuvudnamn eller åtkomst från FileREST-API:et.

> [!Note]  
> I den här artikeln används lagringskontots DNS-suffix för de offentliga Azure-regionerna, `core.windows.net` . Den här kommentaren gäller även för nationella Azure-moln, till exempel Azure US Government-molnet och Azure China-molnet. Ersätt bara lämpliga suffix för din miljö. 

I din privata DNS-zon skapar vi en A-post för och en CNAME-post för lagringskontots `storageaccount.privatelink.file.core.windows.net` vanliga namn, som följer mönstret `storageaccount.file.core.windows.net` . Eftersom din privata Dns-zon i Azure är ansluten till det virtuella nätverk som innehåller den privata slutpunkten kan du observera DNS-konfigurationen när du anropar cmdleten från PowerShell på en virtuell `Resolve-DnsName` Azure-dator (alternativt `nslookup` i Windows och Linux):

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

I det här exemplet matchar `storageaccount.file.core.windows.net` lagringskontot den privata IP-adressen för den privata slutpunkten, som råkar vara `192.168.0.4` .

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

Om du kör samma kommando lokalt ser du att samma lagringskontonamn matchar den offentliga IP-adressen för lagringskontot i stället. `storageaccount.file.core.windows.net` är en CNAME-post för `storageaccount.privatelink.file.core.windows.net` , som i sin tur är en CNAME-post för Det Azure-lagringskluster som är värd för lagringskontot:

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

Detta återspeglar det faktum att lagringskontot kan exponera både den offentliga slutpunkten och en eller flera privata slutpunkter. För att säkerställa att namnet på lagringskontot matchar den privata slutpunktens privata IP-adress måste du ändra konfigurationen på dina lokala DNS-servrar. Detta kan åstadkommas på flera olika sätt:

- Ändra värdfilen på klienterna så att den `storageaccount.file.core.windows.net` matchar den önskade privata slutpunktens privata IP-adress. Detta rekommenderas inte för produktionsmiljöer, eftersom du behöver göra dessa ändringar för varje klient som vill montera dina Azure-filresurser och ändringar i lagringskontot eller den privata slutpunkten kommer inte att hanteras automatiskt.
- Skapa en A-post `storageaccount.file.core.windows.net` för på dina lokala DNS-servrar. Detta har fördelen att klienter i din lokala miljö automatiskt kan matcha lagringskontot utan att behöva konfigurera varje klient, men den här lösningen är på liknande sätt skör när det gäller att ändra värdfilen eftersom ändringarna inte återspeglas. Även om den här lösningen är spröd kan det vara det bästa valet för vissa miljöer.
- Vidarebefordra zonen `core.windows.net` från dina lokala DNS-servrar till din privata Azure DNS-zon. Den privata Azure DNS-värden kan nås via en särskild IP-adress ( ) som endast är tillgänglig i virtuella nätverk som är länkade till den privata `168.63.129.16` DNS-zonen i Azure. Du kan lösa den här begränsningen genom att köra ytterligare DNS-servrar i ditt virtuella nätverk som vidarebefordras `core.windows.net` till den privata DNS-zonen i Azure. För att förenkla den här konfigureringen har vi tillhandahållit PowerShell-cmdlets som automatiskt distribuerar DNS-servrar i ditt virtuella Azure-nätverk och konfigurerar dem efter behov. Information om hur du konfigurerar DNS-vidarebefordran finns i [Konfigurera DNS med Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Brandväggsinställningar för lagringskonto
En brandvägg är en nätverksprincip som styr vilka begäranden som får åtkomst till den offentliga slutpunkten för ett lagringskonto. Med hjälp av lagringskontots brandvägg kan du begränsa åtkomsten till lagringskontots offentliga slutpunkt till vissa IP-adresser eller intervall eller till ett virtuellt nätverk. I allmänhet begränsar de flesta brandväggsprinciper för ett lagringskonto nätverksåtkomst till ett eller flera virtuella nätverk. 

Det finns två sätt att begränsa åtkomsten till ett lagringskonto till ett virtuellt nätverk:
- Skapa en eller flera privata slutpunkter för lagringskontot och begränsa all åtkomst till den offentliga slutpunkten. Detta säkerställer att endast trafik som kommer inifrån de önskade virtuella nätverken kan komma åt Azure-filresurser i lagringskontot.
- Begränsa den offentliga slutpunkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket som kallas *tjänstslutpunkter.* När du begränsar trafiken till ett lagringskonto via en tjänstslutpunkt kommer du fortfarande åt lagringskontot via den offentliga IP-adressen.

> [!NOTE]
> NFS-resurser kan inte komma åt lagringskontots offentliga slutpunkt via den offentliga IP-adressen, de kan bara komma åt lagringskontots offentliga slutpunkt med hjälp av virtuella nätverk. NFS-resurser kan också komma åt lagringskontot med privata slutpunkter.

Mer information om hur du konfigurerar brandväggen för lagringskontot finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk.](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="encryption-in-transit"></a>Kryptering under överföring

> [!IMPORTANT]
> Det här avsnittet beskriver kryptering under överföringsinformation för SMB-resurser. Mer information om kryptering under överföring med NFS-resurser finns i [Säkerhet](storage-files-compare-protocols.md#security).

Som standard har alla Azure-lagringskonton kryptering under överföring aktiverat. Det innebär att när du monterar en filresurs via SMB eller kommer åt den via FileREST-protokollet (till exempel via Azure Portal, PowerShell/CLI eller Azure-SDK:er) tillåter Azure Files endast anslutningen om den görs med SMB 3.0+ med kryptering eller HTTPS. Klienter som inte stöder SMB 3.0 eller klienter som stöder SMB 3.0 men inte SMB-kryptering kan inte montera Azure-filresursen om kryptering under överföring är aktiverat. Mer information om vilka operativsystem som stöder SMB 3.0 med kryptering finns i vår detaljerade dokumentation för [Windows,](storage-how-to-use-files-windows.md) [macOS](storage-how-to-use-files-mac.md)och [Linux.](storage-how-to-use-files-linux.md) Alla aktuella versioner av PowerShell, CLI och SDK:er stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure Storage-konto. När kryptering har inaktiverats Azure Files även SMB 2.1, SMB 3.0 utan kryptering och okrypterade FileREST API-anrop via HTTP. Den främsta anledningen till att inaktivera kryptering under överföring är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure Files endast tillåter SMB 2.1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2.1-klient utanför Azure-regionen för Azure-filresursen, till exempel lokalt eller i en annan Azure-region, kan inte komma åt filresursen.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

## <a name="see-also"></a>Se även
- [Azure Files-översikt](storage-files-introduction.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)