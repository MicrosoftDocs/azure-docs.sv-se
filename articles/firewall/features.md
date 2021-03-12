---
title: Azure Firewall-funktioner
description: Lär dig mer om Azure Firewall-funktioner
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: victorh
ms.openlocfilehash: 21bb1856409b7fbea1eeffb8b3769dd63119da50
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612855"
---
# <a name="azure-firewall-features"></a>Azure Firewall-funktioner

[Azure-brandväggen](overview.md) är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser.

![Översikt över brandväggar](media/overview/firewall-threat.png)

Azure-brandväggen innehåller följande funktioner:

- Inbyggd hög tillgänglighet
- Tillgänglighetszoner
- Obegränsad molnskalbarhet
- Programmets FQDN-filtreringsregler
- Regler för filtrering av nätverkstrafik
- FQDN-taggar
- Tjänsttaggar
- Hotinformation
- Stöd för utgående SNAT
- Stöd för inkommande DNAT
- Flera offentliga IP-adresser
- Azure Monitor-loggning
- Tvingad tunneltrafik
- Webb kategorier (förhands granskning)
- Certifieringar

## <a name="built-in-high-availability"></a>Inbyggd hög tillgänglighet

Hög tillgänglighet är inbyggt, så inga extra belastningsutjämnare krävs och det finns inget behov av att konfigurera.

## <a name="availability-zones"></a>Tillgänglighetszoner

Azure-brandväggen kan konfigureras under distributionen för att omfatta flera Tillgänglighetszoner för ökad tillgänglighet. Med Tillgänglighetszoner ökar din tillgänglighet till 99,99% drift tid. Mer information finns i Azure Firewall [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). SLA för 99,99% drift tid erbjuds när två eller fler Tillgänglighetszoner har valts.

Du kan också associera Azure-brandväggen till en speciell zon, precis för närhets skäl, med service standard service avtalet för 99,95%.

Det kostar inget extra att distribuera en brand vägg i en tillgänglighets zon. Det finns dock ytterligare kostnader för inkommande och utgående data överföringar som är associerade med Tillgänglighetszoner. Mer information finns i [pris information om bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/).

Azure Firewall-Tillgänglighetszoner är tillgängliga i regioner som stöder Tillgänglighetszoner. Mer information finns i [regioner som stöder Tillgänglighetszoner i Azure](../availability-zones/az-region.md)

> [!NOTE]
> Tillgänglighetszoner kan bara konfigureras under distributionen. Du kan inte konfigurera en befintlig brand vägg att inkludera Tillgänglighetszoner.

Mer information om Tillgänglighetszoner finns [i regioner och Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Obegränsad molnskalbarhet

Azure Firewall kan skala upp så mycket du behöver för att hantera föränderliga nätverkstrafikflöden, så du behöver inte budgetera för hög trafikbelastning.

## <a name="application-fqdn-filtering-rules"></a>Programmets FQDN-filtreringsregler

Du kan begränsa utgående HTTP/S-trafik eller Azure SQL-trafik till en angiven lista med fullständigt kvalificerade domän namn (FQDN), inklusive jokertecken. Den här funktionen kräver ingen TLS-avslutning.

## <a name="network-traffic-filtering-rules"></a>Regler för filtrering av nätverkstrafik

Du kan centralt skapa *Tillåt* eller *neka* regler för nätverks filtrering efter källa och mål-IP-adress, port och protokoll. Azure Firewall är helt tillståndskänslig så att den kan identifiera legitima paket för olika typer av anslutningar. Regler tillämpas och loggas i flera prenumerationer och virtuella nätverk.

## <a name="fqdn-tags"></a>FQDN-taggar

[FQDN-Taggar](fqdn-tags.md) gör det enkelt för dig att tillåta en välkänd Azure-tjänstenätverks trafik genom brand väggen. Anta exempelvis att du vill tillåta Windows Update-nätverkstrafik via brandväggen. Du skapar en programregel och inkluderar Windows Update-taggen. Nätverkstrafik från Windows Update kan nu flöda genom brandväggen.

## <a name="service-tags"></a>Tjänsttaggar

En [service tag](service-tags.md) -uppsättning representerar en grupp med IP-adressprefix för att minimera komplexiteten för att skapa säkerhets regler. Du kan inte skapa en egen service tag eller ange vilka IP-adresser som ska ingå i en tagg. Microsoft hanterar adressprefix som omfattas av tjänsttaggen och uppdaterar automatiskt tjänsttaggen när adresserna ändras.

## <a name="threat-intelligence"></a>Hotinformation

[Threat Intelligence](threat-intel.md)-baserad filtrering kan aktive ras för brand väggen för att varna och neka trafik från/till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet.

## <a name="outbound-snat-support"></a>Stöd för utgående SNAT

Alla IP-adresser för utgående trafik över virtuellt nätverk översätts till den offentliga Azure Firewall-IP-adressen (Source Network Address Translation). Du kan identifiera och tillåta trafik som kommer från ditt virtuella nätverk till fjärranslutna Internetmål. Azure-brandväggen är inte SNAT när målets IP-adress är ett privat IP-intervall per [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). 

Om din organisation använder ett offentligt IP-adressintervall för privata nätverk, kommer Azure-brandväggen att besvara trafiken till någon av de privata IP-adresserna för brand väggen i AzureFirewallSubnet. Du kan konfigurera Azure-brandväggen så att den **inte är** SNAT för ditt offentliga IP-adressintervall. Mer information finns i [Azure FIREWALL SNAT privata IP-adressintervall](snat-private-range.md).

## <a name="inbound-dnat-support"></a>Stöd för inkommande DNAT

Inkommande Internet nätverks trafik till din offentliga brand Väggs IP-adress är översatt (målets nätverks adress översättning) och filtreras till de privata IP-adresserna i dina virtuella nätverk.

## <a name="multiple-public-ip-addresses"></a>Flera offentliga IP-adresser

Du kan associera [flera offentliga IP-adresser](deploy-multi-public-ip-powershell.md) (upp till 250) med brand väggen.

Detta möjliggör följande scenarier:

- **DNAt** – du kan översätta flera standard port instanser till backend-servrarna. Om du till exempel har två offentliga IP-adresser kan du översätta TCP-port 3389 (RDP) för båda IP-adresserna.
- **SNAT** -fler portar är tillgängliga för utgående SNAT-anslutningar, vilket minskar risken för SNAT-port överbelastning. För tillfället väljer Azure Firewall slumpmässigt den offentliga IP-adress som ska användas för en anslutning. Om du har filtrering nedströms i nätverket måste du tillåta alla offentliga IP-adresser som är associerade med brandväggen. Överväg att använda ett [offentligt IP](../virtual-network/public-ip-address-prefix.md) -adressprefix för att förenkla den här konfigurationen.

## <a name="azure-monitor-logging"></a>Azure Monitor-loggning

Alla händelser är integrerade med Azure Monitor, vilket gör att du kan arkivera loggar till ett lagringskonto, strömma händelser till din händelsehubb eller skicka dem till Azure Monitor-loggar. Azure Monitor log-exempel finns i [Azure Monitor loggar för Azure-brandväggen](./firewall-workbook.md).

Mer information finns i [Självstudier: övervaka Azure Firewall-loggar och-mått](./firewall-diagnostics.md). 

Azure Firewall-arbetsboken innehåller en flexibel arbets yta för analys av Azure Firewall-data. Du kan använda den för att skapa omfattande visuella rapporter i Azure Portal. Mer information finns i [övervaka loggar med hjälp av Azure Firewall-arbetsböcker](firewall-workbook.md).

## <a name="forced-tunneling"></a>Tvingad tunneltrafik

Du kan konfigurera Azure-brandväggen för att dirigera all Internet-baserad trafik till en utsedd nästa hopp i stället för att gå direkt till Internet. Du kan till exempel ha en lokal gräns brand vägg eller en annan virtuell nätverks installation (NVA) för att bearbeta nätverks trafik innan den skickas till Internet. Mer information finns i [Tvingad tunnel trafik i Azure Firewall](forced-tunneling.md).

## <a name="web-categories-preview"></a>Webb kategorier (förhands granskning)

Med webb kategorier kan administratörer tillåta eller neka användar åtkomst till webbplats kategorier som spel webbplatser, webbplatser för sociala medier och andra. Webb kategorier ingår i Azure Firewall standard, men det är mer finjusterat i Azure Firewall Premium Preview. Till skillnad från webb kategori funktionen i standard-SKU: n som matchar kategorin baserat på ett FQDN, matchar Premium-SKU: n kategorin enligt hela URL: en för både HTTP-och HTTPS-trafik. Mer information om för hands versionen av Azure Firewall Premium finns i för [hands versions funktioner för Azure Firewall Premium](premium-features.md).

Om till exempel Azure-brandväggen fångar upp en HTTPS-begäran för `www.google.com/news` förväntas följande kategorisering: 

- Brand Väggs standard – endast FQDN-delen kommer att undersökas, så `www.google.com` kommer att kategoriseras som *sökmotor*. 

- Brand Väggs Premium – hela URL: en kommer att undersökas, så `www.google.com/news` kommer att kategoriseras som *Nyheter*.

Kategorierna organiseras baserat på allvarlighets grad under **ansvar**, **hög bandbredd**, **företags användning**, **produktivitets förlust**, **allmän surfning** och **Okategoriserade**.

### <a name="categorization-change"></a>Ändring av kategorisering

Du kan begära en kategoriserings ändring om du:

 - Tänk på att en FQDN eller URL ska vara under en annan kategori 
 
eller 

- ha en föreslagen kategori för en Okategoriserade-FQDN eller URL

Du är välkommen att skicka en begäran till [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .

### <a name="category-exceptions"></a>Kategori undantag

Du kan skapa undantag för dina webb kategori regler. Skapa en separat regel samling för Tillåt eller neka med en högre prioritet inom regel samlings gruppen. Du kan till exempel konfigurera en regel samling som tillåter `www.linkedin.com` med prioritet 100 med en regel samling som nekar **sociala nätverk** med prioritet 200. Detta skapar undantaget för den fördefinierade webb kategorin för **sociala nätverk** .



## <a name="certifications"></a>Certifieringar

Azure Firewall är ett PCI-kort (Payment Card Industry), Service Organization Controls (SOC), Internationella standardiseringsorganisationen (ISO) och ICSA Labs-kompatibel. Mer information finns i [certifieringar för Azure Firewall-kompatibilitet](compliance-certifications.md).

## <a name="next-steps"></a>Nästa steg

- [För hands versions funktioner för Azure Firewall Premium](premium-features.md)