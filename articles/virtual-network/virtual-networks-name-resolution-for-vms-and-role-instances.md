---
title: Namnmatchning för resurser i virtuella nätverk i Azure
titlesuffix: Azure Virtual Network
description: Namnmatchningsscenarier för Azure IaaS, hybridlösningar, mellan olika molntjänster, Active Directory och användning av din egen DNS-server.
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
ms.openlocfilehash: 80a0b4634a2e84181271b515d2f6f63271cce7f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784973"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Namnmatchning för resurser i virtuella nätverk i Azure

Beroende på hur du använder Azure som värd för IaaS-, PaaS- och hybridlösningar kan du behöva tillåta att de virtuella datorerna (VM) och andra resurser som distribueras i ett virtuellt nätverk kommunicerar med varandra. Även om du kan aktivera kommunikation med hjälp av IP-adresser är det mycket enklare att använda namn som enkelt kan sparas och inte ändras. 

När resurser som distribueras i virtuella nätverk behöver matcha domännamn till interna IP-adresser kan de använda någon av tre metoder:

* [Azure DNS privata zoner](../dns/private-dns-overview.md)
* [Namnmatchning som tillhandahålls av Azure](#azure-provided-name-resolution)
* [Namnmatchning som använder din egen DNS-server](#name-resolution-that-uses-your-own-dns-server) (som kan vidarebefordra frågor till Azure-tillhandahållna DNS-servrar)

Vilken typ av namnmatchning du använder beror på hur dina resurser behöver kommunicera med varandra. I följande tabell visas scenarier och motsvarande namnmatchningslösningar:

> [!NOTE]
> Azure DNS privata zoner är den föredragna lösningen och ger dig flexibilitet att hantera dina DNS-zoner och -poster. Mer information finns på sidan om att [använda Azure DNS för privata domäner](../dns/private-dns-overview.md).

> [!NOTE]
> Om du använder Azure Provided DNS tillämpas lämpligt DNS-suffix automatiskt på dina virtuella datorer. För alla andra alternativ måste du antingen använda fullständigt kvalificerade domännamn (FQDN) eller tillämpa lämpligt DNS-suffix manuellt på dina virtuella datorer.

| **Scenario** | **Lösning** | **DNS-suffix** |
| --- | --- | --- |
| Namnmatchning mellan virtuella datorer som finns i samma virtuella nätverk eller Azure Cloud Services rollinstanser i samma molntjänst. | [Azure DNS privata zoner](../dns/private-dns-overview.md) eller [namnmatchning som tillhandahålls av Azure](#azure-provided-name-resolution) |Värdnamn eller FQDN |
| Namnmatchning mellan virtuella datorer i olika virtuella nätverk eller rollinstanser i olika molntjänster. |[Azure DNS privata zoner](../dns/private-dns-overview.md) eller kund-hanterade DNS-servrar som vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Namnmatchning från en Azure App Service (webbapp, funktion eller robot) med hjälp av virtuell nätverksintegrering till rollinstanser eller virtuella datorer i samma virtuella nätverk. |Kund-hanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Namnmatchning från App Service Web Apps till virtuella datorer i samma virtuella nätverk. |Kund-hanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Namnmatchning från App Service Web Apps i ett virtuellt nätverk till virtuella datorer i ett annat virtuellt nätverk. |Kund-hanterade DNS-servrar vidarebefordrar frågor mellan virtuella nätverk för lösning av Azure (DNS-proxy). Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Lösning av lokala dator- och tjänstnamn från virtuella datorer eller rollinstanser i Azure. |Kund-hanterade DNS-servrar (till exempel lokal domänkontrollant, lokal skrivskyddad domänkontrollant eller en sekundär DNS-synkronisering med zonöverföringar). Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Lösning av Azure-värdnamn från lokala datorer. |Vidarebefordra frågor till en kund hanterad DNS-proxyserver i motsvarande virtuella nätverk vidarebefordrar proxyservern frågor till Azure för lösning. Se [Namnmatchning med din egen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Endast FQDN |
| Omvänd DNS för interna IP-adresser. |[Azure DNS privata zoner eller](../dns/private-dns-overview.md) [namnmatchning eller namnmatchning som tillhandahålls](#azure-provided-name-resolution) av Azure med din egen [DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Inte tillämpligt |
| Namnmatchning mellan virtuella datorer eller rollinstanser som finns i olika molntjänster, inte i ett virtuellt nätverk. |Inte tillämpligt. Anslutning mellan virtuella datorer och rollinstanser i olika molntjänster stöds inte utanför ett virtuellt nätverk. |Inte tillämpligt|

## <a name="azure-provided-name-resolution"></a>Namnmatchning från Azure

Namnmatchning från Azure ger endast grundläggande auktoritativa DNS-funktioner. Om du använder det här alternativet hanteras DNS-zonnamn och -poster automatiskt av Azure och du kommer inte att kunna styra DNS-zonnamnen eller livscykeln för DNS-poster. Om du behöver en komplett DNS-lösning för dina virtuella nätverk måste du använda [Azure DNS](../dns/private-dns-overview.md) zoner eller [Kund-hanterade DNS-servrar.](#name-resolution-that-uses-your-own-dns-server)

Tillsammans med en lösning av offentliga DNS-namn tillhandahåller Azure intern namnmatchning för virtuella datorer och rollinstanser som finns i samma virtuella nätverk eller molntjänst. Virtuella datorer och instanser i en molntjänst delar samma DNS-suffix, så enbart värdnamnet räcker. Men i virtuella nätverk som distribueras med den klassiska distributionsmodellen har olika molntjänster olika DNS-suffix. I så fall behöver du FQDN för att matcha namn mellan olika molntjänster. I virtuella nätverk som distribueras med Azure Resource Manager-distributionsmodellen är DNS-suffixet konsekvent över alla virtuella datorer i ett virtuellt nätverk, så FQDN behövs inte. DNS-namn kan tilldelas till både virtuella datorer och nätverksgränssnitt. Även om azure-tillhandahållen namnmatchning inte kräver någon konfiguration, är det inte det rätta valet för alla distributionsscenarier, enligt beskrivningen i föregående tabell.

> [!NOTE]
> När du använder webb- och arbetsroller för molntjänster kan du också komma åt de interna IP-adresserna för rollinstanser med hjälp av Azure Service Management REST API. Mer information finns i Service [Management REST API Reference](/previous-versions/azure/ee460799(v=azure.100)). Adressen baseras på rollnamnet och instansnumret. 
>

### <a name="features"></a>Funktioner

Namnmatchning som tillhandahålls av Azure innehåller följande funktioner:
* Användarvänlighet. Ingen konfiguration krävs.
* Hög tillgänglighet. Du behöver inte skapa och hantera kluster av dina egna DNS-servrar.
* Du kan använda tjänsten tillsammans med dina egna DNS-servrar för att matcha både lokala värdnamn och Azure-värdnamn.
* Du kan använda namnmatchning mellan virtuella datorer och rollinstanser i samma molntjänst utan att behöva ett FQDN.
* Du kan använda namnmatchning mellan virtuella datorer i virtuella nätverk som använder Azure Resource Manager-distributionsmodellen, utan att du behöver ett FQDN. Virtuella nätverk i den klassiska distributionsmodellen kräver ett FQDN när du löser namn i olika molntjänster. 
* Du kan använda värdnamn som bäst beskriver dina distributioner i stället för att arbeta med automatiskt genererade namn.

### <a name="considerations"></a>Överväganden

Punkter att tänka på när du använder namnmatchning från Azure:
* Det går inte att ändra DNS-suffixet som skapats av Azure.
* DNS-sökning är begränsad till ett virtuellt nätverk. DNS-namn som skapats för ett virtuellt nätverk kan inte matchas från andra virtuella nätverk.
* Du kan inte registrera dina egna poster manuellt.
* WINS och NetBIOS stöds inte. Du kan inte se dina virtuella datorer i Utforskaren.
* Värdnamn måste vara DNS-kompatibla. Namn får bara använda 0–9, a–z och "-" och får inte börja eller sluta med "-".
* DNS-frågetrafik begränsas för varje virtuell dator. Begränsning bör inte påverka de flesta program. Om begränsning av begäran observeras ser du till att cachelagring på klientsidan är aktiverat. Mer information finns i [DNS-klientkonfiguration.](#dns-client-configuration)
* Endast virtuella datorer i de första 180 molntjänsterna registreras för varje virtuellt nätverk i en klassisk distributionsmodell. Den här gränsen gäller inte för virtuella nätverk i Azure Resource Manager.
* IP Azure DNS adressen är 168.63.129.16. Det här är en statisk IP-adress som inte ändras.

### <a name="reverse-dns-considerations"></a>Överväganden för omvänd DNS
Omvänd DNS stöds i alla ARM-baserade virtuella nätverk. Du kan utfärda omvända DNS-frågor (PTR-frågor) för att mappa IP-adresser för virtuella datorer till FQDN för virtuella datorer.
* Alla PTR-frågor för IP-adresser för virtuella datorer returnerar FQDN med formen \[ vmname \] .internal.cloudapp.net
* Framåt sökning på FQDN:er av formen \[ vmname \] .internal.cloudapp.net matchas med IP-adressen som tilldelats den virtuella datorn.
* Om det virtuella nätverket är länkat [till Azure DNS](../dns/private-dns-overview.md) privata zoner som ett virtuellt registreringsnätverk returnerar omvända DNS-frågor två poster. En post kommer att ha formen \[ vmname \] .[ privatednszonename] och den andra kommer att ha formen \[ vmname \] .internal.cloudapp.net
* Omvänd DNS-sökning är begränsad till ett visst virtuellt nätverk även om det är peerkopplat till andra virtuella nätverk. Omvända DNS-frågor (PTR-frågor) för IP-adresser för virtuella datorer som finns i peer-baserade virtuella nätverk returnerar NXDOMAIN.
* Om du vill inaktivera omvänd DNS-funktion i ett virtuellt nätverk kan du göra det genom att skapa en zon för omvänd sökning med [hjälp av Azure DNS](../dns/private-dns-overview.md) privata zoner och länka den här zonen till ditt virtuella nätverk. Om IP-adressutrymmet för det virtuella nätverket till exempel är 10.20.0.0/16 kan du skapa en tom privat DNS-zon 20.10.in-addr.arpa och länka den till det virtuella nätverket. När du länkar zonen till ditt virtuella nätverk bör du inaktivera automatisk registrering på länken. Den här zonen åsidosätter standardzonerna för omvänd sökning för det virtuella nätverket och eftersom den här zonen är tom får du NXDOMAIN för dina omvända DNS-frågor. Se vår [snabbstartsguide](../dns/private-dns-getstarted-portal.md) för information om hur du skapar en privat DNS-zon och länkar den till ett virtuellt nätverk.

> [!NOTE]
> Om du vill att omvänd DNS-sökning ska omfatta flera virtuella nätverk kan du skapa en zon för omvänd sökning (in-addr.arpa) [Azure DNS](../dns/private-dns-overview.md) privata zoner och länka den till flera virtuella nätverk. Du måste dock manuellt hantera omvända DNS-poster för de virtuella datorerna.
>


## <a name="dns-client-configuration"></a>DNS-klientkonfiguration

Det här avsnittet beskriver cachelagring på klientsidan och återförsök på klientsidan.

### <a name="client-side-caching"></a>Cachelagring på klientsidan

Alla DNS-frågor behöver inte skickas i nätverket. Cachelagring på klientsidan hjälper till att minska svarstiden och förbättra återhämtningsförmågan för nätverkslyckan genom att matcha återkommande DNS-frågor från en lokal cache. DNS-poster innehåller en TTL-mekanism (Time-to-Live) som gör att cachen kan lagra posten så länge som möjligt utan att påverka postens äldstahet. Cachelagring på klientsidan passar därför för de flesta situationer.

Windows DNS-standardklienten har inbyggd DNS-cache. Vissa Linux-distributioner inkluderar inte cachelagring som standard. Om du upptäcker att det inte redan finns en lokal cache lägger du till en DNS-cache för varje virtuell Linux-dator.

Det finns ett antal olika DNS-cachelagringspaket (till exempel dnsmasq). Så här installerar du dnsmasq på de vanligaste distributionerna:

* **Ubuntu (använder resolvconf):**
  * Installera dnsmasq-paketet med `sudo apt-get install dnsmasq` .
* **SUSE (använder netconf):**
  * Installera dnsmasq-paketet med `sudo zypper install dnsmasq` .
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service` . 
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service` . 
  * Redigera **/etc/sysconfig/network/config** och ändra *NETCONFIG_DNS_FORWARDER=""* till *dnsmasq*.
  * Uppdatera resolve.conf med `netconfig update` för att ange cachen som den lokala DNS-matcharen.
* **CentOS (använder NetworkManager):**
  * Installera dnsmasq-paketet med `sudo yum install dnsmasq` .
  * Aktivera tjänsten dnsmasq med `systemctl enable dnsmasq.service` .
  * Starta tjänsten dnsmasq med `systemctl start dnsmasq.service` .
  * Lägg *till prepend domain-name-servers 127.0.0.1;* **till /etc/dhclient-eth0.conf**.
  * Starta om nätverkstjänsten med `service network restart` för att ange cachen som den lokala DNS-matcharen.

> [!NOTE]
> Dnsmasq-paketet är bara ett av många DNS-cacheminnen som är tillgängliga för Linux. Innan du använder den kontrollerar du om den är lämplig för dina specifika behov och kontrollerar att ingen annan cache har installerats.

    
### <a name="client-side-retries"></a>Återförsök på klientsidan

DNS är främst ett UDP-protokoll. Eftersom UDP-protokollet inte garanterar meddelandeleverans hanteras omprövningslogiken i själva DNS-protokollet. Varje DNS-klient (operativsystem) kan ha olika logik för omförsök, beroende på skaparens önskemål:

* Windows-operativsystem försöker igen efter en sekund och sedan igen efter ytterligare två sekunder, fyra sekunder och ytterligare fyra sekunder. 
* Linux-standardinstallationen försöker igen efter fem sekunder. Vi rekommenderar att du ändrar specifikationerna för återförsök till fem gånger, med en sekunds intervall.

Kontrollera de aktuella inställningarna på en virtuell Linux-dator med `cat /etc/resolv.conf` . Titta på *alternativraden,* till exempel:

```bash
options timeout:1 attempts:5
```

Filen resolv.conf genereras vanligtvis automatiskt och bör inte redigeras. De specifika stegen för att lägga *till alternativlinjen* varierar beroende på distribution:

* **Ubuntu** (använder resolvconf):
  1. Lägg till *alternativraden* i **/etc/resolvconf/resolv.conf.d/tail**.
  2. Kör `resolvconf -u` för att uppdatera.
* **SUSE** (använder netconf):
  1. Lägg *till timeout:1-försök:5* **till parametern NETCONFIG_DNS_RESOLVER_OPTIONS=""** i **/etc/sysconfig/network/config**.
  2. Kör `netconfig update` för att uppdatera.
* **CentOS** (använder NetworkManager):
  1. Lägg *till ekot "options timeout:1 attempts:5"* till **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Uppdatera med `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Namnmatchning som använder din egen DNS-server

Det här avsnittet beskriver virtuella datorer, rollinstanser och webbappar.

### <a name="vms-and-role-instances"></a>Virtuella datorer och rollinstanser

Dina namnmatchningsbehov kan gå längre än de funktioner som tillhandahålls av Azure. Du kan till exempel behöva använda Microsoft Windows Server Active Directory domäner, matcha DNS-namn mellan virtuella nätverk. För att täcka dessa scenarier ger Azure dig möjlighet att använda dina egna DNS-servrar.

DNS-servrar i ett virtuellt nätverk kan vidarebefordra DNS-frågor till de rekursiva matcharna i Azure. På så sätt kan du matcha värdnamn i det virtuella nätverket. En domänkontrollant (DC) som körs i Azure kan till exempel svara på DNS-frågor för sina domäner och vidarebefordra alla andra frågor till Azure. Med vidarebefordran av frågor kan virtuella datorer se både dina lokala resurser (via domänkontrollanten) och azure-tillhandahållna värdnamn (via vidarebefordraren). Åtkomst till de rekursiva matcharna i Azure tillhandahålls via den virtuella IP-adressen 168.63.129.16.

DNS-vidarebefordran möjliggör även DNS-upplösning mellan virtuella nätverk och gör att dina lokala datorer kan matcha värdnamn som tillhandahålls av Azure. För att kunna matcha en virtuell dators värdnamn måste den virtuella DNS-servern finnas i samma virtuella nätverk och konfigureras för att vidarebefordra värdnamnsfrågor till Azure. Eftersom DNS-suffixet är olika i varje virtuellt nätverk kan du använda regler för villkorlig vidarebefordran för att skicka DNS-frågor till rätt virtuellt nätverk för lösning. Följande bild visar två virtuella nätverk och ett lokalt nätverk som utför DNS-upplösning mellan virtuella nätverk med hjälp av den här metoden. Ett exempel på en DNS-vidarebefordrare finns i [galleriet Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) och [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder)

> [!NOTE]
> En rollinstans kan utföra namnmatchning av virtuella datorer i samma virtuella nätverk. Detta sker med hjälp av det FQDN som består av den virtuella datorns värdnamn **och internal.cloudapp.net** DNS-suffix. I det här fallet lyckas dock bara namnmatchning om rollinstansen har det vm-namn som definierats i [rollschemat (.cscfg-filen).](/previous-versions/azure/reference/jj156212(v=azure.100))
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Rollinstanser som behöver utföra namnmatchning av virtuella datorer i ett annat virtuellt nätverk (FQDN med **hjälp** av internal.cloudapp.net-suffixet) måste göra det med hjälp av metoden som beskrivs i det här avsnittet (anpassade DNS-servrar som vidarebefordrar mellan de två virtuella nätverken).
>

![Diagram över DNS mellan virtuella nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

När du använder namnmatchning från Azure tillhandahåller Azure Dynamic Host Configuration Protocol (DHCP) ett internt DNS-suffix (**.internal.cloudapp.net**) till varje virtuell dator. Det här suffixet aktiverar värdnamnmatchning eftersom värdnamnsposterna finns **i internal.cloudapp.net** zonen. När du använder en egen namnmatchningslösning tillhandahålls inte det här suffixet till virtuella datorer eftersom det stör andra DNS-arkitekturer (till exempel domän-ansluten scenarier). I stället tillhandahåller Azure en platshållare som inte fungerar (*reddog.microsoft.com*).

Om det behövs kan du fastställa det interna DNS-suffixet med hjälp av PowerShell eller API:et:

* För virtuella nätverk i Azure Resource Manager-distributionsmodeller är suffixet tillgängligt via nätverksgränssnittet [REST API,](/rest/api/virtualnetwork/networkinterfaces) [PowerShell-cmdleten Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) och [kommandot az network nic show](/cli/azure/network/nic#az_network_nic_show) Azure CLI.
* I klassiska distributionsmodeller är suffixet tillgängligt via api-anropet [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) eller cmdleten [Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure.service/get-azurevm)

Om vidarebefordran av frågor till Azure inte passar dina behov bör du tillhandahålla en egen DNS-lösning. Din DNS-lösning måste:

* Ange lämplig värdnamnsmatchning, till exempel via [DDNS.](virtual-networks-name-resolution-ddns.md) Om du använder DDNS kan du behöva inaktivera DNS-postrallring. Azure DHCP-lån är långa och rensning kan ta bort DNS-poster i förtid. 
* Ange lämplig rekursiv lösning för att tillåta lösning av externa domännamn.
* Vara tillgänglig (TCP och UDP på port 53) från de klienter som den fungerar och ha åtkomst till Internet.
* Skyddas mot åtkomst från Internet för att minimera hot från externa agenter.

> [!NOTE]
> För bästa prestanda bör IPv6 inaktiveras när du använder virtuella Azure-datorer som DNS-servrar.

### <a name="web-apps"></a>Webbappar
Anta att du behöver utföra namnmatchning från din webbapp som skapats med hjälp av App Service, länkad till ett virtuellt nätverk, till virtuella datorer i samma virtuella nätverk. Utöver att konfigurera en anpassad DNS-server som har en DNS-vidarebefordrare som vidarebefordrar frågor till Azure (virtuell IP-adress 168.63.129.16) utför du följande steg:
1. Aktivera integrering av virtuella nätverk för din webbapp, om det inte redan är gjort, enligt beskrivningen i [Integrera din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. I Azure Portal väljer du För App Service plan som är värd för webbappen Synkronisera nätverk **under** **Nätverk**, **Virtual Network Integration**.

    ![Skärmbild av namnmatchning för virtuellt nätverk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Om du behöver utföra namnmatchning från din webbapp som skapats med hjälp av App Service, länkad till ett virtuellt nätverk, till virtuella datorer i ett annat virtuellt nätverk, måste du använda anpassade DNS-servrar på båda virtuella nätverken på följande sätt:

* Konfigurera en DNS-server i det virtuella målnätverket på en virtuell dator som också kan vidarebefordra frågor till den rekursiva matcharen i Azure (virtuell IP-adress 168.63.129.16). Ett exempel på en DNS-vidarebefordrare finns i [galleriet Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) och [GitHub.](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder) 
* Konfigurera en DNS-vidarebefordrare i det virtuella källnätverket på en virtuell dator. Konfigurera den här DNS-vidarebefordraren för att vidarebefordra frågor till DNS-servern i det virtuella målnätverket.
* Konfigurera DNS-källservern i inställningarna för det virtuella källnätverket.
* Aktivera integrering av virtuellt nätverk för din webbapp för att länka till det virtuella källnätverket genom att följa anvisningarna i Integrera [din app med ett virtuellt nätverk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* I Azure Portal väljer du För App Service plan som är värd för webbappen Synkronisera nätverk **under** **Nätverk** **, Virtual Network Integration**.

## <a name="specify-dns-servers"></a>Ange DNS-servrar
När du använder dina egna DNS-servrar ger Azure möjlighet att ange flera DNS-servrar per virtuellt nätverk. Du kan också ange flera DNS-servrar per nätverksgränssnitt (för Azure Resource Manager) eller per molntjänst (för den klassiska distributionsmodellen). DNS-servrar som anges för ett nätverksgränssnitt eller en molntjänst får företräde framför DNS-servrar som anges för det virtuella nätverket.

> [!NOTE]
> Egenskaper för nätverksanslutning, till exempel DNS-server-IP-adresser, bör inte redigeras direkt på virtuella datorer. Det beror på att de kan raderas när tjänsten helas när den virtuella nätverksadaptern ersätts. Detta gäller för både virtuella Windows- och Linux-datorer.

När du använder distributionsmodellen Azure Resource Manager du ange DNS-servrar för ett virtuellt nätverk och ett nätverksgränssnitt. Mer information finns i [Hantera ett virtuellt nätverk](manage-virtual-network.md) och Hantera ett [nätverksgränssnitt.](virtual-network-network-interface.md)

> [!NOTE]
> Om du väljer en anpassad DNS-server för ditt virtuella nätverk måste du ange minst en IP-adress för DNS-servern. Annars ignorerar det virtuella nätverket konfigurationen och använder AZURE-tillhandahållen DNS i stället.

När du använder den klassiska distributionsmodellen kan du ange DNS-servrar för det virtuella nätverket i Azure Portal eller [nätverkskonfigurationsfilen](/previous-versions/azure/reference/jj157100(v=azure.100)). För molntjänster kan du ange DNS-servrar via [tjänstkonfigurationsfilen](/previous-versions/azure/reference/ee758710(v=azure.100)) eller med hjälp av PowerShell, [med New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Om du ändrar DNS-inställningarna för ett virtuellt nätverk eller en virtuell dator som redan har distribuerats måste du, för att de nya DNS-inställningarna ska gälla, utföra en förnyelse av DHCP-lån på alla berörda virtuella datorer i det virtuella nätverket. För virtuella datorer som kör Windows-operativsystemet kan du göra detta genom att skriva `ipconfig /renew` direkt i den virtuella datorn. Stegen varierar beroende på operativsystemet. Se relevant dokumentation för din os-typ.

## <a name="next-steps"></a>Nästa steg

Azure Resource Manager distributionsmodell:

* [Hantera ett virtuellt nätverk](manage-virtual-network.md)
* [Hantera ett nätverksgränssnitt](virtual-network-network-interface.md)

Klassisk distributionsmodell:

* [Konfigurationsschema för Azure-tjänsten](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network konfigurationsschema](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Konfigurera en Virtual Network med hjälp av en nätverkskonfigurationsfil](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)