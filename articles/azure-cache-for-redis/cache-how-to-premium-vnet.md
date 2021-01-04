---
title: Konfigurera ett virtuellt nätverk – Premium-Tier Azure-cache för Redis-instans
description: Lär dig hur du skapar och hanterar stöd för virtuella nätverk för din Azure-cache på Premium-nivå för Redis-instanser.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 10/09/2020
ms.openlocfilehash: 9343bc424a0a38da173a56701528c4fd7549aabd
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734665"
---
# <a name="configure-virtual-network-support-for-a-premium-tier-azure-cache-for-redis-instance"></a>Konfigurera stöd för virtuella nätverk för en Azure-cache på Premium-nivå för Redis-instans

Azure cache för Redis har olika cache-erbjudanden, vilket ger flexibilitet i valet av cache-storlek och-funktioner. På Premium-nivå-funktionerna ingår kluster, persistence och stöd för virtuella nätverk. Ett virtuellt nätverk är ett privat nätverk i molnet. När ett Azure-cache för Redis-instans har kon figurer ATS med ett virtuellt nätverk, är det inte offentligt adresserat och kan endast nås från virtuella datorer och program i det virtuella nätverket. Den här artikeln beskriver hur du konfigurerar stöd för virtuella nätverk för en Azure-cache på Premium-nivå för Redis-instans.

> [!NOTE]
> Azure cache för Redis har stöd för både den klassiska distributions modellen och Azure Resource Manager virtuella nätverk.
> 

## <a name="why-virtual-network"></a>Varför Virtual Network?

[Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) -distribution ger förbättrad säkerhet och isolering för Azure cache för Redis-instans, tillsammans med undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst.

## <a name="virtual-network-support"></a>Stöd för virtuellt nätverk

Stöd för virtuella nätverk konfigureras i fönstret **ny Azure-cache för Redis** under skapandet av cache.

1. Om du vill skapa ett cacheminne på Premium-nivå loggar du in på [Azure Portal](https://portal.azure.com) och väljer **skapa en resurs**. Förutom att skapa cacheminnen i Azure Portal kan du också skapa dem med hjälp av Resource Manager-mallar, PowerShell eller Azure CLI. Mer information om hur du skapar en Azure-cache för Redis-instansen finns i [skapa en cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Skärm bild som visar skapa en resurs.":::
   
1. Välj **databaser** på sidan **nytt** . Välj sedan **Azure cache för Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Skärm bild som visar hur du väljer Azure cache för Redis.":::

1. På sidan **ny Redis cache** konfigurerar du inställningarna för din nya cache på Premium-nivå.
   
   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-namn** | Ange ett globalt unikt namn. | Cache-namnet måste vara en sträng mellan 1 och 63 tecken som bara innehåller siffror, bokstäver eller bindestreck. Namnet måste börja och sluta med en siffra eller en bokstav och får inte innehålla flera bindestreck i rad. Din cacheposts *värdnamn* är *\<DNS name> . Redis.cache.Windows.net*. |
   | **Prenumeration** | Välj din prenumeration i den nedrullningsbara listan. | Den prenumeration som du vill skapa den här nya Azure-cache för Redis-instansen för. |
   | **Resursgrupp** | Välj en resurs grupp i den nedrullningsbara listan eller Välj **Skapa ny** och ange ett nytt resurs grupp namn. | Namnet på resurs gruppen där du vill skapa cachen och andra resurser. Genom att lägga till alla dina app-resurser i en resurs grupp kan du enkelt hantera eller ta bort dem tillsammans. |
   | **Plats** | Välj en plats i list rutan. | Välj en [region](https://azure.microsoft.com/regions/) nära andra tjänster som ska använda din cache. |
   | **Cachestorlek** |Välj en Premium-nivå-cache i list rutan för att konfigurera funktioner på Premium-nivå. Mer information finns i [Azure cache för Redis-priser](https://azure.microsoft.com/pricing/details/cache/). |  Prisnivån avgör storlek, prestanda och funktioner som är tillgängliga för cacheminnet. Mer information finns i [Azure cache för Redis-översikt](cache-overview.md). |

1. Välj fliken **nätverk** eller Välj knappen **nätverk** längst ned på sidan.

1. På fliken **nätverk** väljer du **virtuella nätverk** som anslutnings metod. Om du vill använda ett nytt virtuellt nätverk skapar du det först genom att följa stegen i [skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) eller [skapa ett virtuellt nätverk (klassisk) med hjälp av Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Gå sedan tillbaka till fönstret **ny Azure-cache för Redis** för att skapa och konfigurera din Premium-Tier-cache.

   > [!IMPORTANT]
   > När du distribuerar Azure cache för Redis till ett virtuellt Resource Manager-nätverk måste cachen finnas i ett dedikerat undernät som inte innehåller några andra resurser förutom Azure cache för Redis-instanser. Om du försöker distribuera en Azure-cache för Redis-instans till ett virtuellt nätverk i Resource Manager som innehåller andra resurser, Miss lyckas distributionen.
   > 
   > 

   | Inställning      | Föreslaget värde  | Beskrivning |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Virtuellt nätverk** | Välj ditt virtuella nätverk i list rutan. | Välj ett virtuellt nätverk som är i samma prenumeration och plats som din cache. |
   | **Undernät** | Välj ditt undernät i den nedrullningsbara listan. | Under nätets adress intervall ska vara i CIDR-notation (till exempel 192.168.1.0/24). Det måste finnas i det virtuella nätverkets adress utrymme. |
   | **Statisk IP-adress** | Valfritt Ange en statisk IP-adress. | Om du inte anger en statisk IP-adress väljs en IP-adress automatiskt. |

   > [!IMPORTANT]
   > Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Förutom de IP-adresser som används av Azure Virtual Network-infrastrukturen använder varje Azure cache för Redis-instans i under nätet två IP-adresser per Shard och ytterligare en IP-adress för belastningsutjämnaren. En ej klustrad cache anses ha en Shard.
   > 

1. Välj **Nästa: fliken Avancerat** eller Välj **Nästa: Avancerat** längst ned på sidan.

1. På fliken **Avancerat** för en cache-instans på Premium-nivå konfigurerar du inställningarna för icke-TLS-port, klustring och data beständighet.

1. Välj fliken **Nästa: Taggar** eller Välj knappen **Nästa: Taggar** längst ned på sidan.

1. Du kan också ange namn och värde om du vill kategorisera resursen på fliken **taggar** .

1. Välj **Granska + skapa**. Du kommer till fliken **Granska + skapa** där Azure verifierar konfigurationen.

1. När meddelandet grön **verifiering har skickats** visas väljer du **skapa**.

Det tar en stund innan cacheminnet skulle skapas. Du kan övervaka förloppet på **översikts** sidan för Azure-cache för Redis. När **statusen** är **igång** är cacheminnet redo att användas. När cachen har skapats kan du Visa konfigurationen för det virtuella nätverket genom att välja **Virtual Network** på **resurs** -menyn.

![Virtuellt nätverk][redis-cache-vnet-info]

Om du vill ansluta till Azure-cachen för Redis-instansen när du använder ett virtuellt nätverk anger du värd namnet för din cache i anslutnings strängen, som du ser i följande exempel:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Vanliga frågor och svar om Azure cache för Redis Virtual Network

Följande lista innehåller svar på vanliga frågor om Azure cache för Redis-skalning.

* Vilka är några vanliga problem med att konfigurera Azure cache för Redis och virtuella nätverk?
* [Hur kan jag kontrol lera att mitt cacheminne fungerar i ett virtuellt nätverk?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Varför får jag ett fel meddelande om att Fjärrcertifikatet är ogiltigt när jag försöker ansluta till Azure-cachen för Redis-instansen i ett virtuellt nätverk?
* [Kan jag använda virtuella nätverk med en standard-eller Basic-cache?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Varför Miss söker jag när en Azure-cache för Redis-instans skapas i vissa undernät, men inte med andra?
* [Vilka är kraven på under näts adress utrymme?](#what-are-the-subnet-address-space-requirements)
* [Fungerar alla cache-funktioner när en cache finns i ett virtuellt nätverk?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Vilka är några vanliga problem med att konfigurera Azure cache för Redis och virtuella nätverk?

När Azure cache för Redis finns i ett virtuellt nätverk används portarna i följande tabeller.

>[!IMPORTANT]
>Om portarna i följande tabeller är blockerade kanske inte cachen fungerar som den ska. Att ha en eller flera av dessa portar blockerade är det vanligaste problemet med fel konfiguration när du använder Azure cache för Redis i ett virtuellt nätverk.
> 

- [Krav för utgående portar](#outbound-port-requirements)
- [Krav för inkommande portar](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Krav för utgående portar

Det finns nio krav för utgående port. Utgående begär anden i dessa intervall är antingen utgående till andra tjänster som krävs för att cachen ska fungera eller internt till Redis-undernätet för kommunikation mellan noder. För geo-replikering finns ytterligare utgående krav för kommunikation mellan undernät i den primära cachen och repliken.

| Portar | Riktning | Transport protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Utgående |TCP |Redis-beroenden för Azure Storage/PKI (Internet) | (Redis-undernät) |* <sup>4</sup> |
| 443 | Utgående | TCP | Redis beroende av Azure Key Vault och Azure Monitor | (Redis-undernät) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Utgående |TCP/UDP |Redis-beroenden för DNS (Internet/Virtual Network) | (Redis-undernät) | 168.63.129.16 och 169.254.169.254 <sup>2</sup> och valfri anpassad DNS-server för under nätet <sup>3</sup> |
| 8443 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) | (Redis-undernät) |
| 10221-10231 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) | (Redis-undernät) |
| 20226 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 13000-13999 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 15000-15999 |Utgående |TCP |Intern kommunikation för Redis och geo-replikering | (Redis-undernät) |(Redis-undernät) (Geo-Replica-peer-undernät) |
| 6379-6380 |Utgående |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |

<sup>1</sup> du kan använda service tag-AzureKeyVault och AzureMonitor med Resource Manager Nätverks säkerhets grupper (NSG: er).

<sup>2</sup> de här IP-adresserna som ägs av Microsoft används för att hantera den virtuella värddatorn som hanterar Azure DNS.

<sup>3</sup> den här informationen behövs inte för undernät utan anpassade DNS-servrar eller nyare Redis-cacheer som ignorerar anpassad DNS.

<sup>4</sup> mer information finns i [ytterligare krav för virtuella nätverks anslutningar](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Krav på peerportar vid geo-replikering

Om du använder geo-replikering mellan cacheminnen i virtuella Azure-nätverk avblockerar du portarna 15000-15999 för hela under nätet i både inkommande *och* utgående riktning till båda cacheminnena. Med den här konfigurationen kan alla replik komponenter i under nätet kommunicera direkt med varandra även om det finns en framtida GEO-redundans.

#### <a name="inbound-port-requirements"></a>Krav för inkommande portar

Det finns åtta krav för ingående port intervall. Inkommande begär anden i dessa intervall är antingen inkommande från andra tjänster som finns i samma virtuella nätverk eller internt i Redis under nät kommunikation.

| Portar | Riktning | Transport protokoll | Syfte | Lokal IP | Fjärr-IP |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Inkommande |TCP |Klient kommunikation till Redis, Azure Load Balancing | (Redis-undernät) | (Redis-undernät), (klient-undernät), AzureLoadBalancer <sup>1</sup> |
| 8443 |Inkommande |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |
| 8500 |Inkommande |TCP/UDP |Belastningsutjämning i Azure | (Redis-undernät) | AzureLoadBalancer |
| 10221-10231 |Inkommande |TCP |Klient kommunikation till Redis-kluster, intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät), AzureLoadBalancer, (klient under nät) |
| 13000-13999 |Inkommande |TCP |Klient kommunikation till Redis-kluster, Azure Load Balancing | (Redis-undernät) | (Redis-undernät), (klient under nät), AzureLoadBalancer |
| 15000-15999 |Inkommande |TCP |Klient kommunikation till Redis-kluster, Azure Load Balancing och geo-replikering | (Redis-undernät) | (Redis-undernät), (klient-undernät), AzureLoadBalancer, (geo-Replica-peer-undernät) |
| 16001 |Inkommande |TCP/UDP |Belastningsutjämning i Azure | (Redis-undernät) | AzureLoadBalancer |
| 20226 |Inkommande |TCP |Intern kommunikation för Redis | (Redis-undernät) |(Redis-undernät) |

<sup>1</sup> du kan använda service tag-AzureLoadBalancer för Resource Manager eller AZURE_LOADBALANCER för den klassiska distributions modellen för redigering av NSG-reglerna.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Ytterligare krav för anslutning till virtuellt nätverk

Det finns krav på nätverks anslutning för Azure-cache för Redis som kanske inte inlednings vis uppfylls i ett virtuellt nätverk. Azure cache för Redis kräver att alla följande objekt fungerar korrekt när de används i ett virtuellt nätverk:

* Utgående nätverks anslutning till Azure Storage slut punkter över hela världen. Slut punkter som finns i samma region som Azure cache för Redis-instansen och lagrings slut punkter som finns i *andra* Azure-regioner ingår. Azure Storage slut punkter matchas enligt följande DNS-domäner: *Table.Core.Windows.net*, *blob.Core.Windows.net*, *Queue.Core.Windows.net* och *File.Core.Windows.net*.
* Utgående nätverks anslutning till *OCSP.DigiCert.com*, *crl4.DigiCert.com*, *OCSP.msocsp.com*, *mscrl.Microsoft.com*, *crl3.DigiCert.com*, *cacerts.DigiCert.com*, *oneocsp.Microsoft.com* och *CRL.Microsoft.com*. Den här anslutningen krävs för att stödja TLS/SSL-funktioner.
* DNS-konfigurationen för det virtuella nätverket måste kunna matcha alla slut punkter och domäner som nämns i de tidigare punkterna. Dessa DNS-krav kan uppfyllas genom att en giltig DNS-infrastruktur konfigureras och underhålls för det virtuella nätverket.
* Utgående nätverks anslutning till följande Azure Monitor slut punkter, som löses under följande DNS-domäner: *shoebox2-Black.shoebox2.Metrics.nsatc.net*, *North-prod2.prod2.Metrics.nsatc.net*, *azglobal-Black.azglobal.Metrics.nsatc.net*, *shoebox2-Red.shoebox2.Metrics.nsatc.net*, *East-prod2.prod2.Metrics.nsatc.net* och *azglobal-Red.azglobal.Metrics.nsatc.net*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Hur kan jag kontrol lera att mitt cacheminne fungerar i ett virtuellt nätverk?

>[!IMPORTANT]
>När du ansluter till en Azure-cache för Redis-instans som finns i ett virtuellt nätverk måste dina cache-klienter finnas i samma virtuella nätverk eller i ett virtuellt nätverk med peering för virtuella nätverk som är aktiverat i samma Azure-region. Global peering för virtuella nätverk stöds inte för närvarande. Detta krav gäller för alla test program eller diagnostiska Pinging-verktyg. Oavsett var klient programmet finns måste NSG: er eller andra nätverks lager konfigureras så att klientens nätverks trafik kan komma åt Azure-cachen för Redis-instansen.
>

När port kraven har kon figurer ATS enligt beskrivningen i föregående avsnitt kan du kontrol lera att din cache fungerar genom att följa dessa steg:

- [Starta om](cache-administration.md#reboot) alla cache-noder. Om alla nödvändiga cache-beroenden inte kan nås, enligt beskrivningen i krav för [inkommande port](cache-how-to-premium-vnet.md#inbound-port-requirements) och [krav på utgående port](cache-how-to-premium-vnet.md#outbound-port-requirements), kommer cacheminnet inte att kunna startas om korrekt.
- När du har startat om cache-noderna, enligt vad som rapporter ATS i cache-status i Azure Portal, kan du göra följande tester:
  - Pinga cache-slutpunkten genom att använda port 6380 från en dator som är inom samma virtuella nätverk som cachen med hjälp av [TCPing](https://www.elifulkerson.com/projects/tcping.php). Exempel:
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Om `tcping` verktyget rapporterar att porten är öppen är cachen tillgänglig för anslutning från klienter i det virtuella nätverket.

  - Ett annat sätt att testa är att skapa en app cache-klient (som kan vara ett enkelt konsol program med hjälp av StackExchange. Redis) som ansluter till cachen och lägger till och hämtar objekt från cachen. Installera exempel klient programmet på en virtuell dator som finns i samma virtuella nätverk som cachen. Kör det sedan för att kontrol lera anslutningen till cacheminnet.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Varför får jag ett fel meddelande om att Fjärrcertifikatet är ogiltigt när jag försöker ansluta till Azure-cachen för Redis-instansen i ett virtuellt nätverk?

När du försöker ansluta till en Azure-cache för Redis-instansen i ett virtuellt nätverk visas ett certifikat verifierings fel som detta:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

Orsaken kan vara att du ansluter till värden via IP-adressen. Vi rekommenderar att du använder värd namnet. Använd följande sträng med andra ord:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Undvik att använda IP-adressen som liknar följande anslutnings sträng:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Om du inte kan matcha DNS-namnet innehåller vissa klient bibliotek konfigurations alternativ som `sslHost` tillhandahålls av stackexchange. Redis-klienten. Med det här alternativet kan du åsidosätta värd namnet som används för certifikat verifiering. Exempel:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Kan jag använda virtuella nätverk med en standard-eller Basic-cache?

Virtuella nätverk kan bara användas med cacheminnen på Premium-nivå.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Varför Miss söker jag när en Azure-cache för Redis-instans skapas i vissa undernät, men inte med andra?

Om du distribuerar en Azure-cache för Redis-instansen till ett virtuellt nätverk måste cacheminnet finnas i ett dedikerat undernät som inte innehåller någon annan resurs typ. Om ett försök görs att distribuera en Azure-cache för Redis-instans till ett virtuellt nätverk i Resource Manager som innehåller andra resurser, t. ex. Azure Application Gateway-instanser och utgående NAT, kommer distributionen vanligt vis att Miss lyckas. Du måste ta bort befintliga resurser av andra typer innan du kan skapa en ny Azure-cache för Redis-instansen.

Du måste också ha tillräckligt med tillgängliga IP-adresser i under nätet.

### <a name="what-are-the-subnet-address-space-requirements"></a>Vilka är kraven på under näts adress utrymme?

Azure reserverar vissa IP-adresser i varje undernät och de här adresserna kan inte användas. De första och sista IP-adresserna i under näten är reserverade för protokoll överensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i finns [det några begränsningar för att använda IP-adresser i dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom de IP-adresser som används av Azure Virtual Network-infrastrukturen använder varje Azure cache för Redis-instans i under nätet två IP-adresser per kluster-Shard, plus ytterligare IP-adresser för ytterligare repliker, om det finns några. En ytterligare IP-adress används för belastningsutjämnaren. En ej klustrad cache anses ha en Shard.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Fungerar alla cache-funktioner när en cache finns i ett virtuellt nätverk?

När din cache ingår i ett virtuellt nätverk kan endast klienter i det virtuella nätverket komma åt cacheminnet. Därför fungerar inte följande hanterings funktioner för cachelagring just nu:

* **Redis-konsolen**: eftersom Redis-konsolen körs i din lokala webbläsare, som vanligt vis finns på en utvecklare som inte är ansluten till det virtuella nätverket, kan den inte ansluta till din cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Använda ExpressRoute med Azure cache för Redis

Kunder kan ansluta en [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) -krets till sin virtuella nätverks infrastruktur. På så sätt kan de utöka sitt lokala nätverk till Azure.

Som standard utför en nyligen skapad ExpressRoute-krets inte Tvingad tunnel trafik (annonsering av en standard väg, 0.0.0.0/0) i ett virtuellt nätverk. Det innebär att utgående Internet anslutning tillåts direkt från det virtuella nätverket. Klient program kan ansluta till andra Azure-slutpunkter, som innehåller en Azure-cache för Redis-instans.

En vanlig kund konfiguration är att använda Tvingad tunnel trafik (annonsera en standard väg), som framtvingar utgående Internet trafik till i stället för lokalt flöde. Detta trafikflöde bryter anslutningen till Azure cache för Redis om den utgående trafiken sedan blockeras lokalt så att Azure-cachen för Redis-instansen inte kan kommunicera med dess beroenden.

Lösningen är att definiera en eller flera användardefinierade vägar (UDR) på det undernät som innehåller Azure-cachen för Redis-instansen. En UDR definierar de undernät-/regionsspecifika vägar som ska användas i stället för standard vägen.

Använd om möjligt följande konfiguration:

* ExpressRoute-konfigurationen annonserar 0.0.0.0/0 och tvingar som standard tunnel all utgående trafik lokalt.
* UDR som tillämpas på det undernät som innehåller Azure-cachen för Redis-instansen definierar 0.0.0.0/0 med en fungerande väg för TCP/IP-trafik till det offentliga Internet. Till exempel anger den nästa hopp typ till *Internet*.

Den kombinerade påverkan av dessa steg är att UDR på under näts nivå har företräde framför ExpressRoute-Tvingad tunnel trafik, vilket garanterar utgående Internet åtkomst från Azure-cachen för Redis-instans.

Att ansluta till en Azure-cache för Redis-instans från ett lokalt program med hjälp av ExpressRoute är inte ett typiskt användnings scenario på grund av prestanda skäl. För bästa prestanda bör Azure cache för Redis-klienter finnas i samma region som Azure-cachen för Redis-instansen.

>[!IMPORTANT]
>Vägarna som definieras i en UDR *måste* vara tillräckligt bestämda för att prioriteras över alla vägar som annonseras av ExpressRoute-konfigurationen. I följande exempel används det breda adress intervallet 0.0.0.0/0 och det kan vara av misstag åsidosatt av väg annonseringar som använder mer exakta adress intervall.

>[!WARNING]
>Azure cache för Redis stöds inte med ExpressRoute-konfigurationer som *felaktigt korsar vägar från den offentliga peering-vägen till den privata peering-sökvägen*. ExpressRoute konfigurationer med offentlig peering konfigurerad ta emot väg annonseringar från Microsoft för en stor uppsättning Microsoft Azure IP-adressintervall. Om dessa adress intervall felaktigt annonseras i den privata peering-sökvägen är resultatet att alla utgående nätverks paket från Azure-cachen för Redis-instansens undernät felaktigt tvingas-tunnlad till en kunds lokala nätverks infrastruktur. Det här nätverks flödet delar upp Azure cache för Redis. Lösningen på det här problemet är att stoppa kors annonserings vägar från den offentliga peering-vägen till den privata peering-sökvägen.

Bakgrunds information om UDR finns i [trafik dirigering i virtuella nätverk](../virtual-network/virtual-networks-udr-overview.md).

Mer information om ExpressRoute finns i [teknisk översikt för ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure cache för Redis-funktioner.

* [Azure cache för Redis Premium service-nivåer](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
