---
title: Azure Files nätverks överväganden | Microsoft Docs
description: En översikt över nätverks alternativ för Azure Files.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 02/22/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cec386b798b843a5badc9d52d9c71bd7df54b59a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601941"
---
# <a name="azure-files-networking-considerations"></a>Azure Files nätverks överväganden 
Du kan ansluta till en Azure-filresurs på två sätt:

- Åtkomst till resursen direkt via SMB (Server Message Block), NFS (Network File System) (för hands version) eller protokoll som har blockerats. Detta åtkomst mönster används i första hand för att eliminera så många lokala servrar som möjligt.
- Skapa ett cacheminne för Azure-filresursen på en lokal server (eller på en virtuell Azure-dator) med Azure File Sync och få åtkomst till fil resursens data från den lokala servern med ditt protokoll (SMB, NFS, FTPS osv.) för ditt användnings fall. Det här åtkomst mönstret är användbart eftersom det kombinerar det bästa av både lokala prestanda och moln skalning och tjänster som kan anslutas utan server, till exempel Azure Backup.

Den här artikeln fokuserar på hur du konfigurerar nätverk för när ditt användnings fall anropar åtkomst till Azure-filresursen direkt i stället för att använda Azure File Sync. För ytterligare information om nätverks överväganden för en Azure File Sync distribution, se [Azure File Sync nätverks överväganden](storage-sync-files-networking-overview.md).

Nätverks konfigurationen för Azure-filresurser görs på Azure Storage-kontot. Ett lagrings konto är en hanterings konstruktion som representerar en delad pool av lagring där du kan distribuera flera fil resurser, samt andra lagrings resurser, till exempel BLOB-behållare eller köer. Lagrings konton visar flera inställningar som hjälper dig att skydda nätverks åtkomsten till dina fil resurser: nätverks slut punkter, inställningar för lagrings konto brand vägg och kryptering under överföring. 

Vi rekommenderar att du läser [planering för en Azure Files distribution](storage-files-planning.md) innan du läser den här konceptuella guiden.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Den här videon är en guide och en demonstration av hur du på ett säkert sätt kan exponera Azure-filresurser direkt till informations arbetare och appar i fem enkla steg. I avsnitten nedan finns länkar och ytterligare kontext till den dokumentation som refereras i videon.
   :::column-end:::
:::row-end:::

## <a name="accessing-your-azure-file-shares"></a>Åtkomst till dina Azure-filresurser
När du distribuerar en Azure-filresurs inom ett lagrings konto är fil resursen omedelbart tillgänglig via lagrings kontots offentliga slut punkt. Det innebär att autentiserade begär Anden, till exempel begär Anden som har godkänts av en användares inloggnings identitet, kan komma från eller utanför Azure. 

I många kund miljöer kommer en första montering av Azure-filresursen på din lokala arbets Station att Miss lyckas, även om monteringar från virtuella Azure-datorer lyckas. Orsaken till detta är att många organisationer och Internet leverantörer (ISP) blockerar porten som SMB använder för att kommunicera, Port 445. NFS-resurser har inte det här problemet. Den här övningen kommer från säkerhets anvisningar om äldre och föråldrade versioner av SMB-protokollet. Även om SMB 3,0 är ett Internet-säkert protokoll, är äldre versioner av SMB, särskilt SMB 1,0. Azure-filresurser kan bara nås externt via SMB 3,0 och det fileraste protokollet (som också är ett säkert protokoll för Internet) via den offentliga slut punkten.

Eftersom det enklaste sättet att komma åt din Azure SMB-filresurs från lokalt är att öppna ditt lokala nätverk till port 445, rekommenderar Microsoft följande steg för att ta bort SMB 1,0 från din miljö:

1. Se till att SMB 1,0 tas bort eller inaktive ras på din organisations enheter. Alla Windows-och Windows Server-versioner som stöds för närvarande stöder borttagning eller inaktive ring av SMB 1,0 och från och med Windows 10, version 1709, SMB 1,0 installeras inte i Windows som standard. Mer information om hur du inaktiverar SMB 1,0 finns i våra OS-/regionsspecifika sidor:
    - [Skydda Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server)
    - [Skydda Linux](storage-how-to-use-files-linux.md#securing-linux)
1. Se till att inga produkter i organisationen kräver SMB 1,0 och ta bort de som gör. Vi underhåller en [SMB1 produkt-Clearinghouse](https://aka.ms/stillneedssmb1)som innehåller alla de första och tredje parts produkter som är kända för att Microsoft ska kräva SMB 1,0. 
1. Valfritt Använd en brand vägg från en tredje part med din organisations lokala nätverk för att förhindra att SMB 1,0-trafik lämnar din organisations gränser.

Om din organisation kräver att port 445 ska blockeras per princip eller förordning, eller om din organisation kräver trafik till Azure för att följa en deterministisk sökväg kan du använda Azure VPN Gateway eller ExpressRoute för tunnel trafik till dina Azure-filresurser. NFS-resurser kräver inte något av detta eftersom de inte behöver port 445.

> [!Important]  
> Även om du väljer att använda en alternativ metod för att få åtkomst till dina Azure-filresurser rekommenderar Microsoft fortfarande att du tar bort SMB 1,0 från din miljö.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>Tunnel trafik över ett virtuellt privat nätverk eller ExpressRoute
När du upprättar en nätverks tunnel mellan ditt lokala nätverk och Azure, peering du ditt lokala nätverk med ett eller flera virtuella nätverk i Azure. Ett [virtuellt nätverk](../../virtual-network/virtual-networks-overview.md), eller VNet, liknar ett traditionellt nätverk som du kommer att använda lokalt. Precis som ett Azure Storage-konto eller en virtuell Azure-dator är ett VNet en Azure-resurs som distribueras i en resurs grupp. 

Azure Files stöder följande mekanismer för tunnel trafik mellan dina lokala arbets stationer och servrar och Azure SMB/NFS-fil resurser:

- [Azure VPN gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md): en VPN-gateway är en speciell typ av virtuell nätverksgateway som används för att skicka krypterad trafik mellan ett virtuellt Azure-nätverk och en annan plats (till exempel lokalt) via Internet. En Azure-VPN Gateway är en Azure-resurs som kan distribueras i en resurs grupp tillsammans med ett lagrings konto eller andra Azure-resurser. VPN-gatewayer exponerar två olika typer av anslutningar:
    - [Punkt-till-plats (P2s) VPN gateway-](../../vpn-gateway/point-to-site-about.md) anslutningar, som är VPN-anslutningar mellan Azure och en enskild klient. Den här lösningen är främst användbar för enheter som inte är en del av organisationens lokala nätverk, t. ex. kunder som vill kunna montera sin Azure-filresurs från hemmet, ett kafé eller ett hotell på resan. Om du vill använda en P2S VPN-anslutning med Azure Files måste en P2S VPN-anslutning konfigureras för varje klient som vill ansluta. För att förenkla distributionen av en P2S VPN-anslutning, se [Konfigurera en punkt-till-plats (P2s) VPN i Windows för användning med Azure Files](storage-files-configure-p2s-vpn-windows.md) och [Konfigurera en punkt-till-plats (P2s) VPN på Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [VPN för plats-till-plats (S2S)](../../vpn-gateway/design.md#s2smulti), som är VPN-anslutningar mellan Azure och din organisations nätverk. Med en S2S VPN-anslutning kan du konfigurera en VPN-anslutning en gång, för en VPN-server eller en enhet som finns i din organisations nätverk, i stället för att göra för varje klient enhet som behöver åtkomst till Azure-filresursen. Information om hur du fören klar distributionen av en S2S VPN-anslutning finns i [Konfigurera en plats-till-plats (S2S) VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
- [ExpressRoute](../../expressroute/expressroute-introduction.md), som gör att du kan skapa en definierad väg mellan Azure och ditt lokala nätverk som inte passerar Internet. Eftersom ExpressRoute tillhandahåller en dedikerad sökväg mellan ditt lokala data Center och Azure, kan ExpressRoute vara användbart när nätverks prestanda är ett övervägande. ExpressRoute är också ett användbart alternativ när din organisations policy eller myndighets krav kräver en deterministisk sökväg till dina resurser i molnet.

Oavsett vilken tunnel metod du använder för att få åtkomst till dina Azure-filresurser behöver du en mekanism för att säkerställa att trafiken till ditt lagrings konto går via tunneln i stället för din vanliga Internet anslutning. Det är tekniskt möjligt att dirigera till den offentliga slut punkten för lagrings kontot, men detta kräver en hård kodning av alla IP-adresser för Azure Storage-kluster i en region, eftersom lagrings konton kan flyttas mellan lagrings kluster när som helst. Detta kräver också konstant uppdatering av IP-adress mappningar eftersom nya kluster läggs till hela tiden.

I stället för att hårdkoda IP-adressen för dina lagrings konton till reglerna för VPN-routning rekommenderar vi att du använder privata slut punkter, vilket ger ditt lagrings konto en IP-adress från adress utrymmet för ett virtuellt Azure-nätverk. Eftersom du skapar en tunnel till Azure skapar peering mellan ditt lokala nätverk och ett eller flera virtuella nätverk, så att detta möjliggör rätt routning på ett hållbart sätt.

### <a name="private-endpoints"></a>Privata slutpunkter
Förutom den offentliga standard slut punkten för ett lagrings konto tillhandahåller Azure Files alternativet att ha en eller flera privata slut punkter. En privat slut punkt är en slut punkt som endast är tillgänglig i ett virtuellt Azure-nätverk. När du skapar en privat slut punkt för ditt lagrings konto hämtar ditt lagrings konto en privat IP-adress från det virtuella nätverkets adress utrymme, ungefär som hur en lokal fil server eller NAS-enhet tar emot en IP-adress inom det lokala nätverkets dedikerade adress utrymme. 

En privat privat slut punkt är associerad med ett specifikt Azure Virtual Network-undernät. Ett lagrings konto kan ha privata slut punkter i fler än ett virtuellt nätverk.

Med hjälp av privata slut punkter med Azure Files kan du:
- Anslut säkert till dina Azure-filresurser från lokala nätverk med hjälp av en VPN-eller ExpressRoute-anslutning med privat peering.
- Skydda dina Azure-filresurser genom att konfigurera lagrings kontots brand vägg för att blockera alla anslutningar på den offentliga slut punkten. Som standard blockeras inte anslutningar till den offentliga slut punkten genom att skapa en privat slut punkt.
- Öka säkerheten för det virtuella nätverket genom att göra det möjligt att blockera exfiltrering av data från det virtuella nätverket (och peering-gränser).

Information om hur du skapar en privat slut punkt finns i [Konfigurera privata slut punkter för Azure Files](storage-files-networking-endpoints.md).

### <a name="private-endpoints-and-dns"></a>Privata slut punkter och DNS
När du skapar en privat slut punkt skapar vi som standard också en (eller uppdaterar en befintlig) privat DNS-zon som motsvarar under `privatelink` domänen. Det krävs inget krav för att skapa en privat DNS-zon för att använda en privat slut punkt för ditt lagrings konto, men det är starkt rekommenderat i allmänhet och uttryckligen krävs när du monterar Azure-filresursen med en Active Directory användarens huvud namn eller åtkomst från det arkivaste API: et.

> [!Note]  
> I den här artikeln används DNS-suffixet för lagrings kontot för Azures offentliga regioner `core.windows.net` . Den här kommentarer gäller också för Azures suveräna moln, till exempel Azure-molnet för amerikanska myndigheter och molnet i molnet, och ersätter bara de nödvändiga suffixen för din miljö. 

I din privata DNS-zon skapar vi en A-post för `storageaccount.privatelink.file.core.windows.net` och en CNAME-post för det reguljära namnet på lagrings kontot, som följer mönstret `storageaccount.file.core.windows.net` . Eftersom din privata Azure-DNS-zon är ansluten till det virtuella nätverket som innehåller den privata slut punkten kan du se DNS-konfigurationen när du anropar `Resolve-DnsName` cmdleten från PowerShell på en virtuell Azure-dator (alternativt `nslookup` i Windows och Linux):

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

Detta återspeglar det faktum att lagrings kontot kan exponera både den offentliga slut punkten och en eller flera privata slut punkter. För att säkerställa att lagrings konto namnet matchas mot den privata IP-adressen för den privata slut punkten måste du ändra konfigurationen på dina lokala DNS-servrar. Detta kan åstadkommas på flera sätt:

- Ändra hosts-filen på klienterna för att `storageaccount.file.core.windows.net` matcha den önskade privata IP-adressen för den privata slut punkten. Detta rekommenderas inte för produktions miljöer eftersom du behöver göra dessa ändringar för varje klient som vill montera dina Azure-filresurser och ändringar av lagrings kontot eller den privata slut punkten hanteras inte automatiskt.
- Skapa en post för `storageaccount.file.core.windows.net` i dina lokala DNS-servrar. Detta har fördelen att klienter i din lokala miljö kan lösa lagrings kontot automatiskt utan att behöva konfigurera varje klient, men den här lösningen är också sårbar för att ändra värd filen eftersom ändringar inte återspeglas. Även om den här lösningen är sårbar kan det vara det bästa valet för vissa miljöer.
- Vidarebefordra `core.windows.net` zonen från dina lokala DNS-servrar till din Azures privata DNS-zon. Den privata Azure-DNS-värden kan nås via en särskild IP-adress ( `168.63.129.16` ) som endast är tillgänglig i virtuella nätverk som är länkade till Azures privata DNS-zon. För att lösa den här begränsningen kan du köra ytterligare DNS-servrar i det virtuella nätverket som kommer att vidarebefordras `core.windows.net` till Azures privata DNS-zon. För att förenkla den här konfigurationen har vi tillhandahållit PowerShell-cmdletar som automatiskt distribuerar DNS-servrar i det virtuella Azure-nätverket och konfigurerar dem efter behov. Information om hur du konfigurerar DNS-vidarebefordran finns i [Konfigurera DNS med Azure Files](storage-files-networking-dns.md).

## <a name="storage-account-firewall-settings"></a>Brand Väggs inställningar för lagrings konto
En brand vägg är en nätverks princip som styr vilka begär Anden som kan få åtkomst till den offentliga slut punkten för ett lagrings konto. Med hjälp av lagrings kontots brand vägg kan du begränsa åtkomsten till lagrings kontots offentliga slut punkt till vissa IP-adresser eller intervall eller till ett virtuellt nätverk. I allmänhet kommer de flesta brand Väggs principer för ett lagrings konto att begränsa nätverks åtkomsten till ett eller flera virtuella nätverk. 

Det finns två sätt att begränsa åtkomsten till ett lagrings konto till ett virtuellt nätverk:
- Skapa en eller flera privata slut punkter för lagrings kontot och begränsa all åtkomst till den offentliga slut punkten. Detta säkerställer att endast trafik som härstammar från de önskade virtuella nätverken kan komma åt Azure-filresurserna i lagrings kontot.
- Begränsa den offentliga slut punkten till ett eller flera virtuella nätverk. Detta fungerar med hjälp av en funktion i det virtuella nätverket med namnet *tjänst slut punkter*. När du begränsar trafiken till ett lagrings konto via en tjänst slut punkt har du fortfarande åtkomst till lagrings kontot via den offentliga IP-adressen.

> [!NOTE]
> NFS-resurser kan inte komma åt lagrings kontots offentliga slut punkt via den offentliga IP-adressen. de kan bara komma åt lagrings kontots offentliga slut punkt med virtuella nätverk. NFS-resurser kan också komma åt lagrings kontot med hjälp av privata slut punkter.

Mer information om hur du konfigurerar lagrings kontots brand vägg finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="encryption-in-transit"></a>Kryptering under överföring

> [!IMPORTANT]
> Det här avsnittet beskriver kryptering i överförings information för SMB-resurser. Mer information om kryptering i överföring med NFS-resurser finns i [säkerhet](storage-files-compare-protocols.md#security).

Som standard har alla Azure Storage-konton kryptering under överföring aktiverat. Det innebär att när du monterar en fil resurs över SMB eller åtkomst till den via det fileraste protokollet (till exempel via Azure Portal, PowerShell/CLI eller Azure SDK: er), tillåter Azure Files bara anslutningen om den görs med SMB 3.0 + med kryptering eller HTTPS. Klienter som inte har stöd för SMB 3,0 eller klienter som stöder SMB 3,0 men inte SMB-kryptering kommer inte att kunna montera Azure-filresursen om kryptering i överföring är aktiverat. Mer information om vilka operativ system som stöder SMB 3,0 med kryptering finns i vår detaljerade dokumentation för [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)och [Linux](storage-how-to-use-files-linux.md). Alla aktuella versioner av PowerShell, CLI och SDK: er stöder HTTPS.  

Du kan inaktivera kryptering under överföring för ett Azure Storage-konto. När krypteringen är inaktive rad kan Azure Files också tillåta SMB 2,1, SMB 3,0 utan kryptering och okrypterade filer för API-anrop via HTTP. Den främsta anledningen till att inaktivera kryptering vid överföring är att stödja ett äldre program som måste köras på ett äldre operativ system, till exempel Windows Server 2008 R2 eller äldre Linux-distribution. Azure Files tillåter endast SMB 2,1-anslutningar inom samma Azure-region som Azure-filresursen. en SMB 2,1-klient utanför Azure-filresursens region, till exempel lokalt eller i en annan Azure-region, kommer inte att kunna komma åt fil resursen.

Mer information om kryptering i överföring finns i [krav på säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="see-also"></a>Se även
- [Azure Files-översikt](storage-files-introduction.md)
- [Planera för en Azure Files-distribution](storage-files-planning.md)