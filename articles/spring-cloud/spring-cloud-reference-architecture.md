---
ms.date: 02/16/2021
ms.topic: reference-architecture
author: kriation
title: Referens arkitektur för Azure våren Cloud
ms.author: akaleshian
ms.service: spring-cloud
description: Den här referens arkitekturen är en grund som använder en typisk Enterprise Hub-och eker-design för användning av Azure våren Cloud.
ms.openlocfilehash: 74183ca2decf8487e5c41cf36d5784538021077f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604176"
---
# <a name="azure-spring-cloud-reference-architecture"></a>Referens arkitektur för Azure våren Cloud

Den här referens arkitekturen är en grund som använder en typisk Enterprise Hub-och eker-design för användning av Azure våren Cloud. I designen distribueras Azure våren-molnet i en enskild eker som är beroende av delade tjänster som finns i hubben. Arkitekturen bygger på komponenter för att uppnå Tenets i [Microsoft Azure Well-Architected Framework][16].

En implementering av den här arkitekturen finns i [Azure våren Cloud referens Architecture][10] -lagringsplatsen på GitHub.

Distributions alternativ för den här arkitekturen är Azure Resource Manager (ARM), terraform och Azure CLI. Artefakterna i den här lagrings platsen innehåller en grund som du kan anpassa efter din miljö. Du kan gruppera resurser som Azure-brandvägg eller Application Gateway i olika resurs grupper eller prenumerationer. Den här grupperingen hjälper till att hålla olika funktioner separata, till exempel IT-infrastruktur, säkerhet, affärs programs team och så vidare.

## <a name="planning-the-address-space"></a>Planera adress utrymmet

Azure våren Cloud kräver två dedikerade undernät:

* Service runtime
* Start program för våren

Vart och ett av dessa undernät kräver ett dedikerat kluster. Flera kluster kan inte dela samma undernät. Minimi storleken på varje undernät är/28. Antalet program instanser som Azure våren Cloud kan stödja varierar beroende på under nätets storlek. Du hittar de detaljerade Virtual Network (VNET) krav i avsnittet [krav för virtuella nätverk][11] i [Distribuera Azure våren Cloud i ett virtuellt nätverk][17].

> [!WARNING]
> Den valda under näts storleken får inte överlappa det befintliga VNET-adressutrymmet och får inte överlappa några peer-eller lokal under näts adress intervall.

## <a name="use-cases"></a>Användningsfall

Vanliga användningsområden för den här arkitekturen inkluderar:

* Interna program som distribueras i hybrid moln miljöer
* Externt riktade program

Dessa användnings fall är liknande, förutom säkerhets-och nätverks trafik reglerna. Den här arkitekturen har utformats för att stödja olika delarna.

## <a name="private-applications"></a>Privata program

I följande lista beskrivs infrastruktur kraven för privata program. Dessa krav är typiska i miljöer med hög behörighet.

* Ingen direkt utgång på det offentliga Internet förutom kontroll Plans trafik.
* Utgående trafik ska färdas via en central nätverks virtuell installation (NVA) (till exempel Azure-brandvägg).
* Vilande data ska krypteras.
* Data som överförs bör krypteras.
* DevOps distributions pipelines kan användas (till exempel Azure DevOps) och kräver nätverks anslutning till Azure våren Cloud.
* Microsofts säkerhets metod för Zero Trust kräver att hemligheter, certifikat och autentiseringsuppgifter lagras i ett säkert valv. Den rekommenderade tjänsten är Azure Key Vault.
* DNS-poster (Application Host Domain Name Service) lagras i Azure Privat DNS.
* Namn matchning för värdar lokalt och i molnet ska vara dubbelriktad.
* Du måste tillämpa minst en säkerhets mätning.
* Azure-tjänstens beroenden bör kommunicera via tjänstens slut punkter eller privata länkar.
* Resurs grupper som hanteras av Azure våren Cloud-distributionen får inte ändras.
* Undernät som hanteras av Azure våren Cloud-distributionen får inte ändras.
* Ett undernät får bara ha en instans av Azure våren Cloud.
* Om [Azure våren Cloud config server][8] används för att läsa in konfigurations egenskaper från en lagrings plats, måste lagrings platsen vara privat.

I följande lista visas de komponenter som utgör designen:

* Lokalt nätverk
  * Domain Name Service (DNS)
  * Gateway
* Hubb prenumeration
  * Azure Firewall-undernät
  * Application Gateway undernät
  * Undernät för delade tjänster
* Ansluten prenumeration
  * Virtual Network peer
  * Azure skydds-undernät

I följande lista beskrivs Azure-tjänsterna i denna referens arkitektur:

* [Azure våren Cloud][1]: en hanterad tjänst som är utformad och optimerad specifikt för Java-baserade våren Boot-program och. NET-baserade [Steeltoe][9] -program.

* [Azure Key Vault][2]: en maskinvarubaserad hanterings tjänst för hantering av autentiseringsuppgifter som har nära integrering med Microsoft Identity Services och beräknings resurser.

* [Azure Monitor][3]: en omfattande serie med övervaknings tjänster för program som distribuerar både i Azure och lokalt.

* [Azure Security Center][4]: ett enhetligt säkerhets hanterings-och skydds system för arbets belastningar i lokala, flera moln och Azure.

* [Azure-pipelines][5]: en helt aktuell tjänst för kontinuerlig integrering/-utveckling (CI/CD) som automatiskt kan distribuera uppdaterade våren Boot-appar till Azure våren Cloud.

Följande diagram visar en väl utformad hubb och eker-design som hanterar ovanstående krav:

![Referens arkitektur diagram för privata program](./media/spring-cloud-reference-architecture/architecture-private.png)

## <a name="public-applications"></a>Offentliga program

I följande lista beskrivs infrastruktur kraven för offentliga program. Dessa krav är typiska i miljöer med hög behörighet.

* Ingångs trafik ska hanteras av minst Application Gateway eller Azures front dörr.
* Azure DDoS Protection standard ska vara aktive rad.
* Ingen direkt utgång på det offentliga Internet förutom kontroll Plans trafik.
* Utgående trafik ska passera en virtuell nätverks installation (NVA) (till exempel Azure-brandvägg).
* Vilande data ska krypteras.
* Data som överförs bör krypteras.
* DevOps distributions pipelines kan användas (till exempel Azure DevOps) och kräver nätverks anslutning till Azure våren Cloud.
* Microsofts säkerhets metod för Zero Trust kräver att hemligheter, certifikat och autentiseringsuppgifter lagras i ett säkert valv. Den rekommenderade tjänsten är Azure Key Vault.
* DNS-poster för program värdar bör lagras i Azure Privat DNS.
* Adresser för dirigerbart Internet ska lagras i offentliga Azure-DNS.
* Namn matchning för värdar lokalt och i molnet ska vara dubbelriktad.
* Du måste tillämpa minst en säkerhets mätning.
* Azure-tjänstens beroenden bör kommunicera via tjänstens slut punkter eller privata länkar.
* Resurs grupper som hanteras av Azure våren Cloud-distributionen får inte ändras.
* Undernät som hanteras av Azure våren Cloud-distributionen får inte ändras.
* Ett undernät får bara ha en instans av Azure våren Cloud.

I följande lista visas de komponenter som utgör designen:

* Lokalt nätverk
  * Domain Name Service (DNS)
  * Gateway
* Hubb prenumeration
  * Azure Firewall-undernät
  * Application Gateway undernät
  * Undernät för delade tjänster
* Ansluten prenumeration
  * Virtual Network peer
  * Azure skydds-undernät

I följande lista beskrivs Azure-tjänsterna i denna referens arkitektur:

* [Azure våren Cloud][1]: en hanterad tjänst som är utformad och optimerad specifikt för Java-baserade våren Boot-program och. NET-baserade [Steeltoe][9] -program.

* [Azure Key Vault][2]: en maskinvarubaserad hanterings tjänst för hantering av autentiseringsuppgifter som har nära integrering med Microsoft Identity Services och beräknings resurser.

* [Azure Monitor][3]: en omfattande serie med övervaknings tjänster för program som distribuerar både i Azure och lokalt.

* [Azure Security Center][4]: ett enhetligt säkerhets hanterings-och skydds system för arbets belastningar i lokala, flera moln och Azure.

* [Azure-pipelines][5]: en helt aktuell tjänst för kontinuerlig integrering/-utveckling (CI/CD) som automatiskt kan distribuera uppdaterade våren Boot-appar till Azure våren Cloud.

* [Azure Application Gateway][6]: en belastningsutjämnare som ansvarar för program trafik med Transport Layer Security (TLS) avlastning på Layer 7.

* [Azure Application brand vägg][7]: en funktion i Azure Application Gateway som tillhandahåller centraliserat skydd av program mot vanliga sårbarheter och sårbarheter.

Följande diagram visar en väl utformad hubb och eker-design som hanterar ovanstående krav:

![Referens arkitektur diagram för offentliga program](./media/spring-cloud-reference-architecture/architecture-public.png)

## <a name="azure-spring-cloud-on-premises-connectivity"></a>Azure våren Cloud lokal anslutning

Program som körs i Azure våren Cloud kan kommunicera med olika Azure, lokala och externa resurser. Genom att använda nav och eker design kan program dirigera trafik externt eller till det lokala nätverket med hjälp av Express Route eller plats-till-plats-VPN (virtuellt privat nätverk).

## <a name="azure-well-architected-framework-considerations"></a>Överväganden för Azure Well-Architected Framework

[Azure Well-Architected Framework][16] är en uppsättning GUID-Tenets som du följer när du etablerar en stark infrastrukturs bas. Ramverket innehåller följande kategorier: kostnads optimering, drift kvalitet, prestanda effektivitet, tillförlitlighet och säkerhet.

### <a name="cost-optimization"></a>Kostnadsoptimering

På grund av typen av distribuerad system design är infrastruktur tillväxten en verklighet. I verkligheten uppstår oväntade och okontrollerade kostnader. Azure våren Cloud bygger på komponenter som skalas så att det kan möta efter frågan och optimera kostnaderna. Kärnan i den här arkitekturen är Azure Kubernetes service (AKS). Tjänsten är utformad för att minska komplexiteten och driften för att hantera Kubernetes, vilket omfattar effektiviteten i klustrets drifts kostnader.

Du kan distribuera olika program och program typer till en enda instans av Azure våren Cloud. Tjänsten stöder automatisk skalning av program som utlöses av mått eller scheman som kan förbättra användningen och kostnads effektiviteten.

Du kan också använda Application Insights och Azure Monitor för att sänka drifts kostnaderna. Med den synlighet som tillhandahålls av den omfattande loggnings lösningen kan du implementera Automation för att skala komponenterna i systemet i real tid. Du kan också analysera loggdata för att avslöja ineffektivitet i program koden som du kan hantera för att förbättra totalkostnaden och prestandan i systemet.

### <a name="operational-excellence"></a>Utmärkt driftseffektivitet

Azure våren-molnet behandlar flera aspekter av drift kvalitet. Du kan kombinera dessa aspekter för att säkerställa att tjänsten fungerar effektivt i produktions miljöer, enligt beskrivningen i följande lista:

* Du kan använda Azure-pipelines för att säkerställa att distributioner är pålitliga och konsekventa samtidigt som du får hjälp att undvika mänskligt fel.
* Du kan använda Azure Monitor och Application Insights för att lagra information om loggar och telemetri.
  Du kan utvärdera insamlade logg-och mått data för att säkerställa programens hälso tillstånd och prestanda. Program prestanda övervakning (APM) är fullständigt integrerat i tjänsten via en Java-agent. Den här agenten ger insyn i alla distribuerade program och beroenden utan extra kod. Mer information finns i blogg inlägget [övervaka program och beroenden i Azure våren Cloud][15].
* Du kan använda Azure Security Center för att säkerställa att program upprätthåller säkerheten genom att tillhandahålla en plattform för att analysera och utvärdera de data som tillhandahålls.
* Tjänsten har stöd för olika distributions mönster. Mer information finns i [Konfigurera en utvecklings miljö i Azure våren Cloud][14].

### <a name="reliability"></a>Tillförlitlighet

Azure våren Cloud har utformats med AKS som en grundläggande komponent. Även om AKS tillhandahåller en nivå av återhämtning genom klustring, är den här referens arkitekturen införlivande tjänster och arkitektoniska överväganden för att öka programmets tillgänglighet om det finns komponent fel.

Genom att bygga ovanpå en väldefinierad hubb och ekrars design, säkerställer Stiftelsen för den här arkitekturen att du kan distribuera den till flera regioner. För det privata programmets användnings fall använder arkitekturen Azure-Privat DNS för att säkerställa fortsatt tillgänglighet under ett geografiskt haveri. För det offentliga tillämpnings fallet är Azures front dörr och Azure Application Gateway garanterad tillgänglighet.

### <a name="security"></a>Säkerhet

Säkerheten för den här arkitekturen åtgärdas genom att det sker i enlighet med branschcertifierade kontroller och benchmarks. Kontrollerna i den här arkitekturen är från [moln kontroll matrisen][19] (CCM) från [Cloud Security Alliance][18] (CSA) och [Microsoft Azure grunderna][20] (MAFB) med hjälp av [Center för Internet säkerhet][21] (CIS). I de tillämpade kontrollerna ligger fokus på de primära säkerhets design principerna för styrning, nätverk och program säkerhet. Det är ditt ansvar att hantera design principerna för identitets-, åtkomst hantering och lagring när de relaterar till din mål infrastruktur.

#### <a name="governance"></a>Styrning

Den främsta aspekten av styrning som denna arkitektur adress är uppdelning av genom isolering av nätverks resurser. I CCM, rekommenderar DCS-08 inkommande och utgående kontroll för data centret. För att uppfylla kontrollen använder arkitekturen en nav-och ekrars design med hjälp av nätverks säkerhets grupper (NSG: er) för att filtrera öst-väst-trafik mellan resurser. Arkitekturen filtrerar också trafiken mellan centrala tjänster i hubben och resurserna i ekern. Arkitekturen använder en instans av Azure-brandväggen för att hantera trafik mellan Internet och resurserna i arkitekturen.

I följande lista visas den kontroll som hanterar data Center säkerhet i den här referensen:

| CSA CCM-kontroll-ID | CSA CCM-kontroll domän |
| :----------------- | :----------------------|
| DCS – 08 | Post för data Center säkerhet för obehörig person |

#### <a name="network"></a>Nätverk

Nätverks designen som stöder den här arkitekturen härleds från den traditionella nav-och eker modellen. Det här beslutet säkerställer att nätverks isolering är en grundläggande konstruktion. CCM-kontrollen IVS-06 rekommenderar att trafiken mellan nätverk och virtuella datorer begränsas och övervakas mellan betrodda och ej betrodda miljöer. Den här arkitekturen antar kontrollen av implementeringen av NSG: er för öst-väst-trafik och Azure-brandväggen för Nord-syd-trafik. CCM-kontroll IPY-04 rekommenderar att infrastrukturen använder säkra nätverks protokoll för utbyte av data mellan tjänster. Azure-tjänster som stöder den här arkitekturen använder sig av standard säkra protokoll som TLS för HTTP och SQL.

I följande lista visas kaustik kalcinerad magnesium oxid som åtgärdar nätverks säkerheten i den här referensen:

| CSA CCM-kontroll-ID | CSA CCM-kontroll domän |
| :----------------- | :----------------------|
| IPY-04             | Nätverks protokoll      |
| IVS-06             | Nätverkssäkerhet       |

Nätverks implementeringen skyddas ytterligare genom att definiera kontroller från MAFB. Kontrollerna säkerställer att trafik till miljön är begränsad från det offentliga Internet.

I följande lista visas de CIS-kontroller som åtgärdar nätverks säkerheten i den här referensen:

| CIS-kontroll-ID | Beskrivning av CIS-kontroll |
| :------------- | :---------------------- |
| 6,2 | Se till att SSH-åtkomsten är begränsad från Internet. |
| 6.3 | Se till att inga SQL-databaser tillåter ingress 0.0.0.0/0 (alla IP). |
| 6.5 | Se till att Network Watcher är "Enabled". |
| 6.6 | Se till att inkommande trafik med UDP är begränsad från Internet. |

Azure våren Cloud kräver att hanterings trafik utsätts från Azure när den distribueras i en säker miljö. För att åstadkomma detta måste du tillåta att nätverks-och program reglerna visas i [kund ansvar för att köra Azure våren Cloud i VNet](./spring-cloud-vnet-customer-responsibilities.md).

#### <a name="application-security"></a>Programsäkerhet

Den här design huvud delen täcker de grundläggande komponenterna i identitet, data skydd, hantering av nycklar och program konfiguration. Enligt design körs ett program som distribueras i Azure våren-molnet med minst privilegium som krävs för att fungera. Uppsättningen med verifierings kontroller är direkt relaterad till data skydd när tjänsten används. Nyckel hantering stärker detta skiktade program säkerhets metoder.

I följande lista visas CCM-kontroller som hanterar nyckel hantering i den här referensen:

| CSA CCM-kontroll-ID | CSA CCM-kontroll domän |
| :----------------- | :--------------------- |
| EKM – 01 | Kryptering och nyckel hanterings rättighet |
| EKM-02 | Generering av kryptering och nyckel hanterings nyckel |
| EKM-03 | Kryptering och nyckel hantering känsliga data skydd |
| EKM-04 | Lagring och åtkomst till kryptering och nyckel hantering |

Från CCM-, EKM-02-och EKM-03 rekommenderar principer och procedurer för att hantera nycklar och använda krypterings protokoll för att skydda känsliga data. EKM-01 rekommenderar att alla kryptografiska nycklar har identifierbara ägare så att de kan hanteras. EKM-04 rekommenderar att standardalgoritmer används.

I följande lista visas de CIS-kontroller som hanterar nyckel hantering i den här referensen:

| CIS-kontroll-ID | Beskrivning av CIS-kontroll |
| :------------- | :---------------------- |
| 8.1 | Se till att förfallo datumet anges för alla nycklar. |
| 8,2 | Se till att förfallo datumet har angetts för alla hemligheter. |
| 8,4 | Se till att nyckel valvet är återställnings Bart. |

CIS-kontrollerna 8,1 och 8,2 rekommenderar att förfallo datum anges för autentiseringsuppgifter för att säkerställa att rotationen tillämpas. CIS-kontroll 8,4 garanterar att innehållet i nyckel valvet kan återställas för att bibehålla affärs kontinuiteten.

Aspekter av program säkerhet är en grund för att använda den här referens arkitekturen för att ge stöd för en våren-arbetsbelastning i Azure.

## <a name="next-steps"></a>Nästa steg

Utforska den här referens arkitekturen genom de ARM-, terraform-och Azure CLI-distributioner som finns i [Azure-molnet referens arkitekturs][10] lagrings plats.

<!-- Reference links in article -->
[1]: ./index.yml
[2]: ../key-vault/index.yml
[3]: ../azure-monitor/index.yml
[4]: ../security-center/index.yml
[5]: /azure/devops/pipelines/
[6]: ../application-gateway/index.yml
[7]: ../web-application-firewall/index.yml
[8]: ./spring-cloud-tutorial-config-server.md
[9]: https://steeltoe.io/
[10]: https://github.com/Azure/azure-spring-cloud-reference-architecture
[11]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[12]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-network-requirements
[13]: ./spring-cloud-vnet-customer-responsibilities.md#azure-spring-cloud-fqdn-requirements--application-rules
[14]: ./spring-cloud-howto-staging-environment.md
[15]: https://devblogs.microsoft.com/java/monitor-applications-and-dependencies-in-azure-spring-cloud/
[16]: /azure/architecture/framework/
[17]: ./spring-cloud-tutorial-deploy-in-azure-virtual-network.md#virtual-network-requirements
[18]: https://cloudsecurityalliance.org/
[19]: https://cloudsecurityalliance.org/research/working-groups/cloud-controls-matrix
[20]: /azure/security/benchmarks/v2-cis-benchmark
[21]: https://www.cisecurity.org/