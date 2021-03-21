---
title: Namnmatchning för resurser i virtuella nätverk i Azure
titlesuffix: Azure Virtual Network
description: Namn matchnings scenarier för Azure IaaS, hybrid lösningar, mellan olika moln tjänster, Active Directory och att använda din egen DNS-server.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222861"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Namnmatchning för resurser i virtuella nätverk i Azure

Beroende på hur du använder Azure för att hantera IaaS-, PaaS-och hybrid lösningar kan du behöva tillåta att de virtuella datorerna (VM) och andra resurser som distribueras i ett virtuellt nätverk kommunicerar med varandra. Även om du kan aktivera kommunikation med hjälp av IP-adresser, är det mycket enklare att använda namn som enkelt kan sparas och inte ändras. 

När resurser som distribueras i virtuella nätverk måste matcha domän namn med interna IP-adresser, kan de använda en av tre metoder:

* [Azure DNS privata zoner](../dns/private-dns-overview.md)
* [Azure-tillhandahållen namn matchning](#azure-provided-name-resolution)
* [Namn matchning som använder din egen DNS-Server](#name-resolution-that-uses-your-own-dns-server) (som kan vidarebefordra frågor till Azure-TILLHANDAHÅLLna DNS-servrar)

Vilken typ av namnmatchning du använder beror på hur dina resurser behöver kommunicera med varandra. I följande tabell visas scenarier och motsvarande namn matchnings lösningar:

> [!NOTE]
> Azure DNS privata zoner är den bästa lösningen och ger dig flexibilitet när du hanterar DNS-zoner och-poster. Mer information finns på sidan om att [använda Azure DNS för privata domäner](../dns/private-dns-overview.md).

> [!NOTE]
> Om du använder Azure är det lämpligt DNS-suffix som tillämpas automatiskt på dina virtuella datorer. För alla andra alternativ måste du antingen använda fullständigt kvalificerade domän namn (FQDN) eller manuellt tillämpa rätt DNS-suffix på dina virtuella datorer.

| **Scenario** | **Lösning** | **DNS-suffix** |
| --- | --- | --- |
| Namn matchning mellan virtuella datorer som finns i samma virtuella nätverk, eller Azure Cloud Services roll instanser i samma moln tjänst. | [Azure DNS privata zoner](../dns/private-dns-overview.md) eller [namn matchning från Azure](#azure-provided-name-resolution) |Värdnamn eller FQDN |
| Namn matchning mellan virtuella datorer i olika virtuella nätverk eller roll instanser i olika moln tjänster. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller kund hanterade DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namn matchning från en Azure App Service (webbapp, funktion eller robot) med hjälp av integrering av virtuella nätverk till roll instanser eller virtuella datorer i samma virtuella nätverk. |Kundhanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namn matchning från App Service Web Apps till virtuella datorer i samma virtuella nätverk. |Kundhanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Namn matchning från App Service Web Apps i ett virtuellt nätverk till virtuella datorer i ett annat virtuellt nätverk. |Kundhanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Matchning av lokala dator-och tjänst namn från virtuella datorer eller roll instanser i Azure. |Kundhanterade DNS-servrar (den lokala domänkontrollanten, den lokala skrivskyddade domänkontrollanten eller en sekundär DNS-post synkroniserad med zon överföringar, till exempel). Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Matchning av Azure-värdnamn från lokala datorer. |Vidarebefordra frågor till en kundhanterad DNS-proxyserver i det motsvarande virtuella nätverket vidarebefordrar proxyservern frågor till Azure för att lösa problemet. Se [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Endast FQDN |
| Omvänd DNS för interna IP-adresser. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller [Azure-tillhandahållen namn matchning](#azure-provided-name-resolution) eller [namn matchning med hjälp av en egen DNS-Server](#name-resolution-that-uses-your-own-dns-server). |Inte tillämpligt |
| Namn matchning mellan virtuella datorer eller roll instanser som finns i olika moln tjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutning mellan virtuella datorer och roll instanser i olika moln tjänster stöds inte utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Azure-tillhandahållen namn matchning

Den tillhandahållna Azure-namnmatchning innehåller bara grundläggande auktoritära DNS-funktioner. Om du använder det här alternativet hanteras DNS-zonens namn och poster automatiskt av Azure och du kommer inte att kunna kontrol lera DNS-zonens namn eller livs cykeln för DNS-poster. Om du behöver en fullständigt aktuell DNS-lösning för dina virtuella nätverk måste du använda [Azure DNS privata zoner](../dns/private-dns-overview.md) eller [kund hanterade DNS-servrar](#name-resolution-that-uses-your-own-dns-server).

Tillsammans med matchning av offentliga DNS-namn tillhandahåller Azure intern namn matchning för virtuella datorer och roll instanser som finns i samma virtuella nätverk eller moln tjänst. Virtuella datorer och instanser i en moln tjänst delar samma DNS-suffix så att själva värd namnet är tillräckligt. Men i virtuella nätverk som distribueras med den klassiska distributions modellen har olika moln tjänster olika DNS-suffix. I så fall behöver du FQDN för att matcha namn mellan olika moln tjänster. I virtuella nätverk som distribueras med hjälp av Azure Resource Manager distributions modell är DNS-suffixet konsekvent i alla virtuella datorer i ett virtuellt nätverk, så det fullständiga domän namnet behövs inte. DNS-namn kan tilldelas både för virtuella datorer och nätverks gränssnitt. Även om Azure-tillhandahållen namn matchning inte kräver någon konfiguration, är det inte det lämpligaste alternativet för alla distributions scenarier, enligt beskrivningen i föregående tabell.

> [!NOTE]
> När du använder webb-och arbets roller i Cloud Services kan du också komma åt de interna IP-adresserna för roll instanser med hjälp av Azure Service Management-REST API. Mer information finns i [REST API referens för tjänst hantering](/previous-versions/azure/ee460799(v=azure.100)). Adressen baseras på roll namnet och instans numret. 
>

### <a name="features"></a>Funktioner

Azure-angiven namn matchning omfattar följande funktioner:
* Lätt att använda. Ingen konfiguration krävs.
* Hög tillgänglighet. Du behöver inte skapa och hantera kluster av dina egna DNS-servrar.
* Du kan använda tjänsten tillsammans med dina egna DNS-servrar för att matcha både lokala och Azure-värdnamn.
* Du kan använda namn matchning mellan virtuella datorer och roll instanser inom samma moln tjänst, utan att det krävs något fullständigt domän namn.
* Du kan använda namn matchning mellan virtuella datorer i virtuella nätverk som använder Azure Resource Manager distributions modell, utan att det krävs något fullständigt domän namn. Virtuella nätverk i den klassiska distributions modellen kräver ett fullständigt domän namn när du matchar namn i olika moln tjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererade namn.

### <a name="considerations"></a>Överväganden

Saker att tänka på när du använder Azure-angiven namn matchning:
* Det går inte att ändra Azure-skapade DNS-suffix.
* DNS-sökning är begränsad till ett virtuellt nätverk. DNS-namn som skapats för ett virtuellt nätverk kan inte matchas från andra virtuella nätverk.
* Du kan inte registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte. Du kan inte se dina virtuella datorer i Utforskaren.
* Värd namn måste vara DNS-kompatibla. Namn får endast innehålla 0-9, a-z och "-", och får inte börja eller sluta med "-".
* DNS-frågans trafik begränsas för varje virtuell dator. Begränsningen påverkar inte de flesta program. Om begränsningen för begäran observeras kontrollerar du att cachelagring på klient sidan är aktiverat. Mer information finns i [konfiguration av DNS-klient](#dns-client-configuration).
* Endast virtuella datorer i de första 180 moln tjänsterna registreras för varje virtuellt nätverk i en klassisk distributions modell. Den här begränsningen gäller inte för virtuella nätverk i Azure Resource Manager.
* Den Azure DNS IP-adressen är 168.63.129.16. Detta är en statisk IP-adress och kommer inte att ändras.

### <a name="reverse-dns-considerations"></a>Omvända DNS-överväganden
Omvänd DNS stöds i alla ARM-baserade virtuella nätverk. Du kan utfärda omvända DNS-frågor (PTR-frågor) om du vill mappa IP-adresser för virtuella datorer till FQDN: er för virtuella datorer.
* Alla PTR-frågor för IP-adresser för virtuella datorer kommer att returnera FQDN: er för formatet \[ VMName \] . Internal.cloudapp.net
* Vanlig sökning på FQDN: er för formatet \[ VMName \] . Internal.cloudapp.net kommer att matcha till den IP-adress som tilldelats den virtuella datorn.
* Om det virtuella nätverket är länkat till en [Azure DNS privata zoner](../dns/private-dns-overview.md) som ett virtuellt register, kommer de omvända DNS-frågorna returnera två poster. En post kommer att ha formatet \[ VMName \] . [ privatednszonename] och den andra kommer att ha formatet \[ VMName \] . Internal.cloudapp.net
* Omvänd DNS-sökning är begränsad till ett angivet virtuellt nätverk även om det är peer-kopplat till andra virtuella nätverk. Omvända DNS-frågor (PTR-frågor) för IP-adresser för virtuella datorer som finns i peer-kopplade virtuella nätverk returnerar NXDOMAIN.
* Om du vill inaktivera omvänd DNS-funktion i ett virtuellt nätverk kan du göra det genom att skapa en zon för omvänd sökning med [Azure DNS privata zoner](../dns/private-dns-overview.md) och länka zonen till det virtuella nätverket. Exempel: om IP-adressutrymmet för det virtuella nätverket är 10.20.0.0/16 kan du skapa en tom privat DNS-zon 20.10.in-addr. arpa och länka den till det virtuella nätverket. När du länkar zonen till det virtuella nätverket bör du inaktivera automatisk registrering på länken. Den här zonen åsidosätter standard zoner för omvänd sökning för det virtuella nätverket och eftersom den här zonen är tom kommer du att få NXDOMAIN för dina omvända DNS-frågor. I vår [snabb starts guide](../dns/private-dns-getstarted-portal.md) finns mer information om hur du skapar en privat DNS-zon och länkar den till ett virtuellt nätverk.

> [!NOTE]
> Om du vill att omvänd DNS-sökning ska omfatta över virtuella nätverk kan du skapa en zon för omvänd sökning (i-addr. arpa) [Azure DNS privata zoner](../dns/private-dns-overview.md) och länka den till flera virtuella nätverk. Du måste dock manuellt hantera de omvända DNS-posterna för de virtuella datorerna.
>


## <a name="dns-client-configuration"></a>Konfiguration av DNS-klient

I det här avsnittet beskrivs cachelagring på klient sidan och försök på klient sidan.

### <a name="client-side-caching"></a>Cachelagring på klient Sidan

Alla DNS-frågor behöver inte skickas över nätverket. Cachelagring på klient sidan bidrar till att minska svars tiden och förbättra återhämtningen till nätverks signaler genom att lösa återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en metod för TTL (Time-to-Live) som gör det möjligt för cache att lagra posten så länge som möjligt utan att det går att påverka posternas aktualitet. Cachelagring på klient sidan är därför lämpligt i de flesta situationer.

Standard-DNS-klienten för Windows har en inbyggd DNS-cache. Vissa Linux-distributioner omfattar inte cachelagring som standard. Om du inte redan har en lokal cache lägger du till en DNS-cache i varje virtuell Linux-dator.

Det finns ett antal olika tillgängliga DNS-caching-paket (till exempel dnsmasq). Så här installerar du dnsmasq på de vanligaste distributionerna:

* **Ubuntu (använder resolvconf)**:
  * Installera dnsmasq-paketet med `sudo apt-get install dnsmasq` .
* **SUSE (använder netconf)**:
  * Installera dnsmasq-paketet med `sudo zypper install dnsmasq` .
  * Aktivera dnsmasq-tjänsten med `systemctl enable dnsmasq.service` . 
  * Starta dnsmasq-tjänsten med `systemctl start dnsmasq.service` . 
  * Redigera **/etc/sysconfig/Network/config** och ändra *NETCONFIG_DNS_FORWARDER = ""* till *dnsmasq*.
  * Uppdatera matcha. conf med `netconfig update` , för att ställa in cachen som den lokala DNS-matcharen.
* **CentOS (använder NetworkManager)**:
  * Installera dnsmasq-paketet med `sudo yum install dnsmasq` .
  * Aktivera dnsmasq-tjänsten med `systemctl enable dnsmasq.service` .
  * Starta dnsmasq-tjänsten med `systemctl start dnsmasq.service` .
  * Lägg till *lägga Domain-Name-servers 127.0.0.1;* till **/etc/dhclient-eth0.conf**.
  * Starta om nätverks tjänsten med `service network restart` , för att ställa in cachen som den lokala DNS-matcharen.

> [!NOTE]
> Dnsmasq-paketet är bara ett av många DNS-cacheminnen som är tillgängliga för Linux. Innan du använder den kontrollerar du lämpligheten för dina specifika behov och kontrollerar att ingen annan cache är installerad.

    
### <a name="client-side-retries"></a>Försök på klient Sidan

DNS är i första hand ett UDP-protokoll. Eftersom UDP-protokollet inte garanterar meddelande leverans hanteras logiken för återförsök i själva DNS-protokollet. Varje DNS-klient (operativ system) kan använda olika omprövnings logik, beroende på Skaparens preferens:

* Försök att köra Windows-operativsystem igen efter en sekund och sedan igen efter ytterligare två sekunder, fyra sekunder och ytterligare fyra sekunder. 
* De nya standarderna för Linux-installationen efter fem sekunder. Vi rekommenderar att du ändrar specifikationerna för återförsök till fem gånger, med ett sekunders intervall.

Kontrol lera de aktuella inställningarna på en virtuell Linux-dator med `cat /etc/resolv.conf` . Titta på *alternativ* raden, till exempel:

```bash
options timeout:1 attempts:5
```

Filen matcha. conf genereras vanligt vis automatiskt och bör inte redige ras. De specifika stegen för att lägga till *alternativ* raden varierar beroende på distribution:

* **Ubuntu** (använder resolvconf):
  1. Lägg till *alternativ* raden i **/etc/resolvconf/resolv.conf.d/tail**.
  2. Kör `resolvconf -u` för att uppdatera.
* **SUSE** (använder netconf):
  1. Lägg till *timeout: 1 försök: 5* till parametern **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** i **/etc/sysconfig/Network/config**.
  2. Kör `netconfig update` för att uppdatera.
* **CentOS** (använder NetworkManager):
  1. Lägg till *ECHO "alternativ timeout: 1 försök: 5"* till **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Uppdatera med `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namn matchning som använder din egen DNS-Server

I det här avsnittet beskrivs virtuella datorer, roll instanser och webbappar.

### <a name="vms-and-role-instances"></a>Virtuella datorer och roll instanser

Dina namn matchnings behov kan gå utöver de funktioner som tillhandahålls av Azure. Du kan till exempel behöva använda Microsoft Windows Server Active Directory Domains, matcha DNS-namn mellan virtuella nätverk. Azure ger dig möjlighet att använda dina egna DNS-servrar för att få de här scenarierna.

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till de rekursiva matcharna i Azure. På så sätt kan du matcha värdnamn inom det virtuella nätverket. En domänkontrollant (DC) som körs i Azure kan till exempel svara på DNS-frågor för sina domäner och vidarebefordra alla andra frågor till Azure. Genom att vidarebefordra frågor kan virtuella datorer se både dina lokala resurser (via DOMÄNKONTROLLANTen) och de Azure-tillhandahållna värd namnen (via vidarebefordraren). Åtkomst till de rekursiva matcharna i Azure tillhandahålls via den virtuella IP-168.63.129.16.

DNS-vidarebefordring möjliggör även DNS-matchning mellan virtuella nätverk och gör det möjligt för dina lokala datorer att matcha värd namn för Azure. För att kunna matcha en virtuell dators värdnamn måste den virtuella DNS-servern finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värd namn frågor till Azure. Eftersom DNS-suffixet skiljer sig i varje virtuellt nätverk kan du använda regler för villkorlig vidarebefordran för att skicka DNS-frågor till rätt virtuellt nätverk för matchning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som utför DNS-matchning mellan virtuella nätverk med hjälp av den här metoden. Ett exempel på en DNS-vidarebefordrare finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> En roll instans kan utföra namn matchning för virtuella datorer i samma virtuella nätverk. Det gör det genom att använda det fullständiga domän namnet, som består av den virtuella datorns värdnamn och **Internal.cloudapp.net** DNS-suffix. Men i det här fallet lyckas namn matchningen bara om roll instansen har det VM-namn som definierats i [roll schemat (. cscfg-fil)](/previous-versions/azure/reference/jj156212(v=azure.100)).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Roll instanser som behöver utföra namn matchning för virtuella datorer i ett annat virtuellt nätverk (FQDN med hjälp av **Internal.cloudapp.net** -suffixet) måste göra detta med hjälp av metoden som beskrivs i det här avsnittet (anpassade DNS-servrar som vidarebefordrar mellan de två virtuella nätverken).
>

![Diagram över DNS mellan virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder Azure-tillhandahållen namn matchning tillhandahåller Azure Dynamic Host Configuration Protocol (DHCP) ett internt DNS-suffix (**. Internal.cloudapp.net**) för varje virtuell dator. Det här suffixet möjliggör värd namns matchning eftersom värd namns posterna finns i **Internal.cloudapp.net** -zonen. När du använder din egen lösning för namn matchning levereras inte det här suffixet till virtuella datorer eftersom det stör andra DNS-arkitekturer (t. ex. domänanslutna scenarier). I stället tillhandahåller Azure en plats hållare som inte fungerar (*reddog.Microsoft.com*).

Vid behov kan du fastställa det interna DNS-suffixet med hjälp av PowerShell eller API: et:

* För virtuella nätverk i Azure Resource Manager distributions modeller är suffixet tillgängligt via [nätverks gränssnittet REST API](/rest/api/virtualnetwork/networkinterfaces), PowerShell-cmdleten [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) och [AZ Network NIC show](/cli/azure/network/nic#az-network-nic-show) Azure CLI-kommandot.
* I klassiska distributions modeller är suffixet tillgängligt via API-anropet [Get-Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) eller cmdleten [Get-AzureVM-debug](/powershell/module/servicemanagement/azure.service/get-azurevm) .

Om frågor om vidarebefordran till Azure inte passar dina behov, bör du ange en egen DNS-lösning. DNS-lösningen måste:

* Ange lämplig värd namns matchning via [DDNS](virtual-networks-name-resolution-ddns.md), till exempel. Om du använder DDNS kan du behöva inaktivera rensning av DNS-poster. Azure DHCP-lån är långa och rensningen kan ta bort DNS-poster för tidigt. 
* Ange lämplig rekursiv matchning för att tillåta matchning av externa domän namn.
* Vara tillgänglig (TCP och UDP på port 53) från de klienter som den hanterar och kan komma åt Internet.
* Skyddas mot åtkomst från Internet, för att minimera hot från externa agenter.

> [!NOTE]
> För bästa prestanda bör IPv6 vara inaktiverat när du använder virtuella Azure-datorer som DNS-servrar.

### <a name="web-apps"></a>Webbappar
Anta att du behöver utföra namn matchning från din webbapp som skapats med hjälp av App Service, som är länkat till ett virtuellt nätverk, till virtuella datorer i samma virtuella nätverk. Förutom att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare som vidarebefordrar frågor till Azure (virtuell IP-168.63.129.16) utför du följande steg:
1. Aktivera integrering av virtuella nätverk för din webbapp, om du inte redan gjort det, enligt beskrivningen i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I Azure Portal, för App Service plan som är värd för webbappen, väljer du **Synkronisera nätverk** under **nätverk**, **Virtual Network integrering**.

    ![Skärm bild av namn matchning för virtuellt nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Om du behöver utföra namn matchning från din webbapp som skapats med hjälp av App Service, som är länkat till ett virtuellt nätverk, till virtuella datorer i ett annat virtuellt nätverk, måste du använda anpassade DNS-servrar i båda de virtuella nätverken enligt följande:

* Konfigurera en DNS-server i det virtuella mål nätverket, på en virtuell dator som också kan vidarebefordra frågor till den rekursiva matcharen i Azure (virtuell IP-168.63.129.16). Ett exempel på en DNS-vidarebefordrare finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Konfigurera en DNS-vidarebefordrare i det virtuella käll nätverket på en virtuell dator. Konfigurera den här DNS-vidarebefordraren så att den vidarebefordrar frågor till DNS-servern i det virtuella mål nätverket.
* Konfigurera din käll-DNS-server i ditt virtuella käll nätverks inställningar.
* Aktivera integrering av virtuella nätverk för din webbapp för att länka till det virtuella käll nätverket genom att följa anvisningarna i [integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure Portal, för App Service plan som är värd för webbappen, väljer du **Synkronisera nätverk** under **nätverk**, **Virtual Network integrering**.

## <a name="specify-dns-servers"></a>Ange DNS-servrar
När du använder dina egna DNS-servrar ger Azure möjlighet att ange flera DNS-servrar per virtuellt nätverk. Du kan också ange flera DNS-servrar per nätverksgränssnitt (för Azure Resource Manager) eller per molntjänst (för den klassiska distributionsmodellen). DNS-servrar som angetts för ett nätverks gränssnitt eller en moln tjänst får företräde framför DNS-servrar som har angetts för det virtuella nätverket.

> [!NOTE]
> Nätverks anslutningens egenskaper, till exempel DNS-serverns IP-adresser, bör inte redige ras direkt i de virtuella datorerna. Detta beror på att de kan tas bort under tjänstens gång när det virtuella nätverkskortet byts ut. Detta gäller både virtuella Windows-och Linux-datorer.

När du använder Azure Resource Manager distributions modell kan du ange DNS-servrar för ett virtuellt nätverk och ett nätverks gränssnitt. Mer information finns i [hantera ett virtuellt nätverk](manage-virtual-network.md) och [hantera ett nätverks gränssnitt](virtual-network-network-interface.md).

> [!NOTE]
> Om du väljer Anpassad DNS-server för det virtuella nätverket måste du ange minst en IP-adress för DNS-servern. annars kommer det virtuella nätverket att ignorera konfigurationen och använda Azure-DNS i stället.

När du använder den klassiska distributions modellen kan du ange DNS-servrar för det virtuella nätverket i Azure Portal eller i [nätverks konfigurations filen](/previous-versions/azure/reference/jj157100(v=azure.100)). För moln tjänster kan du ange DNS-servrar via [tjänst konfigurations filen](/previous-versions/azure/reference/ee758710(v=azure.100)) eller med hjälp av PowerShell, med [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för ett virtuellt nätverk eller en virtuell dator som redan har distribuerats måste du utföra ett förnyat DHCP-lån på alla berörda virtuella datorer i det virtuella nätverket för att de nya DNS-inställningarna ska börja gälla. För virtuella datorer som kör Windows OS kan du göra detta genom att skriva `ipconfig /renew` direkt i den virtuella datorn. Stegen varierar beroende på operativ system. Se relevant dokumentation för din OS-typ.

## <a name="next-steps"></a>Nästa steg

Azure Resource Manager distributions modell:

* [Hantera ett virtuellt nätverk](manage-virtual-network.md)
* [Hantera ett nätverks gränssnitt](virtual-network-network-interface.md)

Klassisk distributions modell:

* [Konfigurations schema för Azure-tjänsten](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network konfigurations schema](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Konfigurera en Virtual Network med hjälp av en nätverks konfigurations fil](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)