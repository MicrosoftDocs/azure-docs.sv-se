---
title: Alternativ för DNS-namnmatchning för virtuella Linux-datorer
description: Namn matchnings scenarier för virtuella Linux-datorer i Azure IaaS, inklusive tillhandahållna DNS-tjänster, hybrid extern DNS och att ta med din egen DNS-server.
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556799"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Alternativ för DNS-namnmatchning för virtuella Linux-datorer i Azure
Azure tillhandahåller DNS-namnmatchning som standard för alla virtuella datorer som finns i ett enda virtuellt nätverk. Du kan implementera din egen lösning för DNS-namnmatchning genom att konfigurera dina egna DNS-tjänster på dina virtuella datorer som Azure-värdar. Följande scenarier bör hjälpa dig att välja den som fungerar för din situation.

* [Namn matchning som Azure tillhandahåller](#name-resolution-that-azure-provides)
* [Namn matchning med hjälp av en egen DNS-Server](#name-resolution-using-your-own-dns-server)

Vilken typ av namn matchning du använder beror på hur dina virtuella datorer och roll instanser måste kommunicera med varandra.

I följande tabell visas scenarier och motsvarande namn matchnings lösningar:

| **Scenario** | **Lösning** | **Huvudnamnssuffix** |
| --- | --- | --- |
| Namn matchning mellan roll instanser eller virtuella datorer i samma virtuella nätverk |Namn matchning som Azure tillhandahåller |värdnamn eller fullständigt kvalificerat domän namn (FQDN) |
| Namn matchning mellan roll instanser eller virtuella datorer i olika virtuella nätverk |Kundhanterade DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Matchning av lokala datorer och tjänst namn från roll instanser eller virtuella datorer i Azure |Kundhanterade DNS-servrar (till exempel en lokal domänkontrollant, en lokal skrivskyddad domänkontrollant eller en sekundär DNS-sekundär som synkroniseras med hjälp av zon överföringar). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Matchning av Azure-värdnamn från lokala datorer |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i motsvarande virtuella nätverk. Proxyservern vidarebefordrar frågor till Azure för lösning. Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-using-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser |[Namn matchning med hjälp av en egen DNS-Server](#name-resolution-using-your-own-dns-server) |saknas |

## <a name="name-resolution-that-azure-provides"></a>Namn matchning som Azure tillhandahåller
Tillsammans med matchning av offentliga DNS-namn tillhandahåller Azure intern namn matchning för virtuella datorer och roll instanser i samma virtuella nätverk. I virtuella nätverk som baseras på Azure Resource Manager är DNS-suffixet konsekvent i det virtuella nätverket. FQDN krävs inte. DNS-namn kan tilldelas till både nätverkskort och virtuella datorer. Även om namn matchningen som Azure tillhandahåller inte kräver någon konfiguration, är det inte det lämpligaste alternativet för alla distributions scenarier som visas i tabellen ovan.

### <a name="features-and-considerations"></a>Funktioner och överväganden
**Egenskaper**

* Ingen konfiguration krävs för att använda namn matchning som Azure tillhandahåller.
* Den namn matchnings tjänst som Azure tillhandahåller har hög tillgänglighet. Du behöver inte skapa och hantera kluster av dina egna DNS-servrar.
* Namn matchnings tjänsten som Azure tillhandahåller kan användas tillsammans med dina egna DNS-servrar för att matcha både lokala och Azure-värdnamn.
* Namn matchning tillhandahålls mellan virtuella datorer i virtuella nätverk utan att det fullständiga domän namnet krävs.
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererade namn.

**Angående**

* Det DNS-suffix som Azure skapar kan inte ändras.
* Du kan inte registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte.
* Värdnamn måste vara DNS-kompatibla.
    Namn får endast innehålla 0-9, a-z och "-", och de får inte börja eller sluta med "-". Se RFC 3696 avsnitt 2.
* DNS-frågans trafik begränsas för varje virtuell dator. Begränsningen påverkar inte de flesta program.  Om begränsningen för begäran observeras kontrollerar du att cachelagring på klient sidan är aktiverat.  Mer information finns i [få ut mesta möjliga av namn matchningen som Azure tillhandahåller](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Få ut mesta möjliga av namn matchningen som Azure tillhandahåller
**Cachelagring på klient sidan:**

Vissa DNS-frågor skickas inte över nätverket. Cachelagring på klient sidan bidrar till att minska svars tiden och förbättra återhämtningen till nätverks inkonsekvenser genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller ett TTL-värde (Time-to-Live) som gör det möjligt för cache att lagra posten så länge som möjligt utan att det går att påverka posternas aktualitet. Därför är cachelagring på klient sidan lämplig för de flesta situationer.

Vissa Linux-distributioner omfattar inte cachelagring som standard. Vi rekommenderar att du lägger till en cache i varje virtuell Linux-dator när du har markerat att det inte redan finns en lokal cache.

Flera olika DNS-dnsmasq, till exempel, är tillgängliga. Här följer stegen för att installera dnsmasq på de vanligaste distributionerna:

**Ubuntu (använder resolvconf)**
  * Installera dnsmasq-paketet ("sudo apt-get install dnsmasq").

**SUSE (använder netconf)**:
1. Installera dnsmasq-paketet ("sudo zypper install dnsmasq").
2. Aktivera dnsmasq-tjänsten ("systemctl Enable dnsmasq. service").
3. Starta dnsmasq-tjänsten ("systemctl start dnsmasq. service").
4. Redigera "/etc/sysconfig/Network/config" och ändra NETCONFIG_DNS_FORWARDER = "" till "dnsmasq".
5. Uppdatera matcha. conf ("netconfig Update") för att ange cachen som den lokala DNS-matcharen.

**CentOS av falsk Wave-programvara (tidigare OpenLogic; använder NetworkManager)**
1. Installera dnsmasq-paketet ("sudo yum install dnsmasq").
2. Aktivera dnsmasq-tjänsten ("systemctl Enable dnsmasq. service").
3. Starta dnsmasq-tjänsten ("systemctl start dnsmasq. service").
4. Lägg till "lägga Domain-Name-servers 127.0.0.1;" till "/etc/dhclient-eth0.conf".
5. Starta om nätverks tjänsten ("Service Network restart") för att ställa in cachen som den lokala DNS-matcharen

> [!NOTE]
> : Dnsmasq-paketet är bara ett av de många DNS-cacheminnen som är tillgängliga för Linux. Innan du använder den kontrollerar du lämpligheten för dina behov och att ingen annan cache har installerats.
>
>

**Försök på klient Sidan**

DNS är i första hand ett UDP-protokoll. Eftersom UDP-protokollet inte garanterar meddelande leverans, hanterar själva DNS-protokollet omprövnings logik. Varje DNS-klient (operativ system) kan använda olika logik för omprövning beroende på skapare:

* Nya försök för Windows-operativsystem efter en sekund och sedan igen efter ytterligare två, fyra och ytterligare fyra sekunder.
* De nya standarderna för Linux-installationen efter fem sekunder.  Du bör ändra det här för att försöka igen fem gånger vid en sekunds intervall.  

För att kontrol lera de aktuella inställningarna på en virtuell Linux-dator, "Cat/etc/resolv.conf", och titta på "alternativ"-raden, till exempel:

```config-conf
options timeout:1 attempts:5
```

Filen matcha. conf genereras automatiskt och bör inte redige ras. De specifika steg som lägger till raden alternativ varierar beroende på distribution:

**Ubuntu** (använder resolvconf)
1. Lägg till alternativ raden till '/etc/resolvconf/resolv.conf.d/Head '.
2. Kör "resolvconf-u" för att uppdatera.

**SUSE** (använder netconf)
1. Lägg till timeout: 1 försök: 5 till parametern NETCONFIG_DNS_RESOLVER_OPTIONS = "" i "/etc/sysconfig/Network/config".
2. Kör netconfig Update för att uppdatera.

**CentOS av falsk Wave-programvara (tidigare OpenLogic)** (använder NetworkManager)
1. Lägg till timeout för RES_OPTIONS =: 1 försök: 5 till/etc/sysconfig/Network.
2. Kör "Network-restart" för att uppdatera.

## <a name="name-resolution-using-your-own-dns-server"></a>Namn matchning med hjälp av en egen DNS-Server
Dina namn matchnings behov kan gå utöver de funktioner som Azure tillhandahåller. Du kan till exempel behöva DNS-matchning mellan virtuella nätverk. Du kan använda dina egna DNS-servrar för att försäkra dig om det här scenariot.  

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till rekursiva matchare av Azure för att matcha värdnamn som finns i samma virtuella nätverk. En DNS-server som kör i Azure kan till exempel svara på DNS-frågor efter sina egna DNS-zonfiler och vidarebefordra alla andra frågor till Azure. Den här funktionen gör det möjligt för virtuella datorer att se både dina poster i dina zonfiler och värdnamn som Azure tillhandahåller (via vidarebefordraren). Åtkomst till de rekursiva matcharna i Azure tillhandahålls via det virtuella IP-168.63.129.16.

DNS-vidarebefordring möjliggör även DNS-matchning mellan virtuella nätverk och gör det möjligt för dina lokala datorer att matcha värdnamn som Azure tillhandahåller. För att lösa en virtuell dators värdnamn måste den virtuella datorn för DNS-servern finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värdnamn-frågor till Azure. Eftersom DNS-suffixet skiljer sig i varje virtuellt nätverk kan du använda regler för villkorlig vidarebefordran för att skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som utför DNS-matchning mellan virtuella nätverk med hjälp av den här metoden:

![DNS-matchning mellan virtuella nätverk](./media/azure-dns/inter-vnet-dns.png)

När du använder namn matchning som Azure tillhandahåller, tillhandahålls det interna DNS-suffixet till varje virtuell dator med hjälp av DHCP. När du använder din egen lösning för namn matchning levereras inte det här suffixet till virtuella datorer eftersom suffixet stör andra DNS-arkitekturer. Om du vill referera till datorer efter FQDN eller konfigurera suffixet på dina virtuella datorer kan du använda PowerShell eller API för att fastställa suffixet:

* För virtuella nätverk som hanteras av Azure Resource Manager är suffixet tillgängligt via [nätverks gränssnitts kortets](/rest/api/virtualnetwork/networkinterfaces) resurs. Du kan också köra `azure network public-ip show <resource group> <pip name>` kommandot för att visa information om din offentliga IP-adress, som innehåller det fullständiga domän namnet för nätverkskortet.

Om du inte uppfyller dina behov när du vidarebefordrar frågor till Azure måste du ange en egen DNS-lösning.  DNS-lösningen måste:

* Ange lämplig matchning för värdnamn, till exempel via [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Om du använder DDNS kan du behöva inaktivera rensning av DNS-poster. DHCP-lån för Azure är mycket långa och rensning kan ta bort DNS-poster för tidigt.
* Ange lämplig rekursiv matchning för att tillåta matchning av externa domän namn.
* Vara tillgänglig (TCP och UDP på port 53) från de klienter som den hanterar och kan komma åt Internet.
* Skyddas mot åtkomst från Internet för att minimera hot från externa agenter.

> [!NOTE]
> Om du vill ha bästa möjliga prestanda inaktiverar Azure DNS du IPv6 och tilldelar en [offentlig IP-adress på instans nivå](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) till varje virtuell dator med DNS-servrar.  
>
>
