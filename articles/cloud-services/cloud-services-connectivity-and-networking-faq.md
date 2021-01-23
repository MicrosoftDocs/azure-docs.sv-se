---
title: Problem med anslutningar och nätverk
description: Den här artikeln innehåller vanliga frågor och svar om anslutningar och nätverk för Microsoft Azure Cloud Services.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c7b83c615e4ac19e10b5c4f6cc1a102206b1a39a
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742428"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Anslutnings-och nätverks problem för Azure Cloud Services (klassisk): vanliga frågor och svar

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Den här artikeln innehåller vanliga frågor om anslutnings-och nätverks problem för [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Storleks information finns på [sidan Cloud Services storlek på virtuell dator](cloud-services-sizes-specs.md).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Jag kan inte reservera en IP-adress i en moln tjänst med flera VIP.
Se först till att den virtuella dator instans som du försöker reservera IP-adressen för är påslagen. Sedan kontrollerar du att du använder reserverade IP-adresser för både mellanlagrings-och produktions distributionerna. Ändra *inte* inställningarna när distributionen uppgraderas.

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>Hur gör jag för att använda fjärr skrivbord när jag har en NSG?
Lägg till regler i NSG som tillåter trafik på portarna **3389** och **20000**. Fjärr skrivbord använder port **3389**. Moln tjänst instanser är belastningsutjämnade, så du kan inte styra vilken instans som ska anslutas direkt. *RemoteForwarder* -och *RemoteAccess* -agenterna hanterar Remote Desktop Protocol-trafik (RDP) och gör att klienten kan skicka en RDP-cookie och ange en enskild instans att ansluta till. *RemoteForwarder* -och *RemoteAccess* -agenterna kräver att port **20000** är öppen, vilket kan blockeras om du har en NSG.

## <a name="can-i-ping-a-cloud-service"></a>Kan jag pinga en moln tjänst?

Nej, inte med det normala "ping"/ICMP-protokollet. ICMP-protokollet tillåts inte via Azure Load Balancer.

För att testa anslutningen rekommenderar vi att du gör ett port-ping. Medan Ping.exe använder ICMP kan du använda andra verktyg, till exempel PSPing, nmap och Telnet, för att testa anslutningen till en angiven TCP-port.

Mer information finns i [använda ping i Port i stället för ICMP för att testa Azure VM-anslutning](/archive/blogs/mast/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity).

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>Hur gör jag för att inte ta emot tusentals träffar från okända IP-adresser som kan tyda på en skadlig attack till moln tjänsten?
Azure implementerar en Multilayer nätverks säkerhet för att skydda dess plattforms tjänster mot distribuerade DDoS-attacker (Denial-of-Service). Azure DDoS försvar-systemet är en del av Azures kontinuerliga övervaknings process, som kontinuerligt förbättras genom inträngande tester. Det här DDoSt försvars systemet är utformat för att motstå inte bara angrepp från utsidan utan även från andra Azure-klienter. Mer information finns i [säkerhet för Azure-nätverk](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf).

Du kan också skapa en start aktivitet för att selektivt blockera vissa angivna IP-adresser. Mer information finns i [blockera en speciell IP-adress](cloud-services-startup-tasks-common.md#block-a-specific-ip-address).

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>När jag försöker RDP till min moln tjänst instans visas meddelandet "användar kontot har upphört att gälla".
Du kan få fel meddelandet "det här användar kontot har upphört att gälla" när du hoppar över det förfallo datum som har kon figurer ATS i RDP-inställningarna. Du kan ändra utgångs datumet från portalen genom att följa dessa steg:

1. Logga in på [Azure Portal](https://portal.azure.com), gå till din moln tjänst och välj fliken **fjärr skrivbord** .

2. Välj distributions plats för **produktion** eller **mellanlagring** .

3. Ändra förfallo datumen **för** datumet och spara sedan konfigurationen.

Du bör nu kunna använda RDP till din dator.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Varför balanserar inte Azure Load Balancer ut trafiken jämnt?
Information om hur en intern belastningsutjämnare fungerar finns [Azure Load Balancer nytt distributions läge](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/).

Den distributionsmapp som används är en 5-tupel (käll-IP, källport, mål-IP, målport och protokoll typ) för att mappa trafik till tillgängliga servrar. Den ger endast varaktighet inom en transport-session. Paket i samma TCP-eller UDP-session dirigeras till samma data Center IP (DIP)-instans bakom den belastningsutjämnade slut punkten. När klienten stänger och öppnar anslutningen på nytt eller startar en ny session från samma käll-IP, ändras käll porten och gör att trafiken går till en annan DIP-slutpunkt.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>Hur kan jag omdirigera inkommande trafik till standard-URL: en för min moln tjänst till en anpassad URL?

URL-modulen för att skriva över IIS kan användas för att omdirigera trafik som kommer till standard-URL: en för moln tjänsten (till exempel \* . cloudapp.net) till en viss anpassad namn/URL. Eftersom modulen URL-omskrivning är aktive rad på webb roller som standard och dess regler har kon figurer ATS i programmets web.config, är den alltid tillgänglig på den virtuella datorn oavsett omstarter/avbildningar. Mer information finns i:

- [Skapa omskrivnings regler för modulen för att skriva över URL](/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [Ta bort en standard länk](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>Hur kan jag blockera/inaktivera inkommande trafik till standard-URL: en för min moln tjänst?

Du kan förhindra inkommande trafik till standard-URL: en/namnet på din moln tjänst (till exempel \* . cloudapp.net). Ange värd rubriken till ett anpassat DNS-namn (t. ex. www \. -MyCloudService.com) under plats bindnings konfigurationen i moln tjänst definitions filen (*. csdef), enligt beskrivningen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

Eftersom den här bindningen för värd huvudet tillämpas via csdef-filen är tjänsten endast tillgänglig via det anpassade namnet "www.MyCloudService.com". Alla inkommande begär anden till domänen "*. cloudapp.net" fungerar alltid. Om du använder en anpassad SLB-avsökning eller en intern belastningsutjämnare i tjänsten kan blockering av standard-URL: en eller-namnet för tjänsten störa funktions sättet.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>Hur kan jag se till att den offentliga IP-adressen för en moln tjänst aldrig förändras?

För att se till att den offentliga IP-adressen för din moln tjänst (även kallat VIP) ändras aldrig, så att den kan godkännas av några olika klienter, rekommenderar vi att du har en reserverad IP-adress som är kopplad till den. Annars frigörs den virtuella IP-adress som tillhandahålls av Azure från din prenumeration om du tar bort distributionen. För att utföra VIP-växling måste du ha enskilda reserverade IP: er för både produktions-och mellanlagrings platser. Utan de kan växlings åtgärden Miss lyckas. Om du vill reservera en IP-adress och associera den med din moln tjänst kan du läsa följande artiklar:

- [Reservera IP-adressen för en befintlig moln tjänst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [Koppla en reserverad IP-adress till en moln tjänst med hjälp av en tjänst konfigurations fil](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

Om du har fler än en instans för dina roller bör du inte göra något avbrott om du kopplar RIP till moln tjänsten. Alternativt kan du lägga till IP-intervallet för ditt Azure-datacenter i en lista över tillåtna. Du kan hitta alla Azure IP-intervall på [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

Den här filen innehåller IP-adressintervall (inklusive beräknings-, SQL-och lagrings intervall) som används i Azure-datacenter. En uppdaterad fil publiceras varje vecka som visar de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka. Hämta den nya XML-filen varje vecka och genomför de nödvändiga ändringarna på webbplatsen för att identifiera tjänster som körs i Azure korrekt. Azure ExpressRoute-användare kan vara medveten om att den här filen används för att uppdatera BGP-annonsen för Azure Space under den första veckan i varje månad.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Hur kan jag använda Azure Resource Manager virtuella nätverk med moln tjänster?

Det går inte att placera moln tjänster i Azure Resource Manager virtuella nätverk. Virtuella nätverk i Resource Manager och virtuella nätverk för klassisk distribution kan anslutas via peering. Mer information finns i [peering för virtuella nätverk](../virtual-network/virtual-network-peering-overview.md).


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Hur kan jag hämta en lista över offentliga IP-adresser som används av mina Cloud Services?

Du kan använda följande PS-skript för att hämta en lista över offentliga IP-adresser för Cloud Services under din prenumeration

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```