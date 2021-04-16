---
title: Azure Red Hat OpenShift tilldelningsmatris för ansvar
description: Lär dig mer om ägarskapet för ansvarsområden för driften av ett Azure Red Hat OpenShift kluster
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, support
ms.openlocfilehash: 4bb00cb533d0065a992831f09ed8280c96efcdee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537004"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Översikt över ansvarsområden för Azure Red Hat OpenShift

Det här dokumentet beskriver ansvarsområdena för Microsoft, Red Hat och kunder för Azure Red Hat OpenShift kluster. Mer information om Azure Red Hat OpenShift och dess komponenter finns i Azure Red Hat OpenShift tjänstdefinition.

Även om Microsoft och Red Hat Azure Red Hat OpenShift tjänsten delar kunden ansvaret för klustrets funktioner. Även Azure Red Hat OpenShift värdkluster finns på Azure-resurser i Azure-kundprenumerationer, nås de via fjärranslutningar. Underliggande plattform och datasäkerhet ägs av Microsoft och Red Hat.

## <a name="overview"></a>Översikt
<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Incident- och drifthantering</a></strong>
   </td>
   <td><strong><a href="#change-management">Ändringshantering</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Identitets- och åtkomsthantering</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Säkerhet och regelefterlevnad</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Kunddata</a>
   </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Kundprogram</a>
   </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Utvecklartjänster </a>
   </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
   <td>Kund </td>
  </tr>
  <tr>
   <td>Plattformsövervakning </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Loggning </td>
   <td>Microsoft och Red Hat </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Delad </td>
  </tr>
  <tr>
   <td>Programnätverk </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Klusternätverk </td>
   <td>Microsoft och Red Hat </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Virtuellt nätverk </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Delad </td>
   <td>Delad </td>
  </tr>
  <tr>
   <td>Kontrollplansnoder </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Arbetsnoder </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Klusterversion </td>
   <td>Microsoft och Red Hat </td>
   <td>Delad </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Kapacitetshantering </td>
   <td>Microsoft och Red Hat </td>
   <td>Delad </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Virtuell lagring </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
  <tr>
   <td>Fysisk infrastruktur och säkerhet </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
   <td>Microsoft och Red Hat </td>
  </tr>
</table>


Tabell 1. Ansvarsområden efter resurs


## <a name="tasks-for-shared-responsibilities-by-area"></a>Uppgifter för delat ansvar per område 

### <a name="incident-and-operations-management"></a>Incident- och drifthantering 

Kunden och Microsoft och Red Hat delar ansvaret för övervakning och underhåll av ett Azure Red Hat OpenShift kluster. Kunden ansvarar för incident- och drifthantering av [kundprogramdata](#customer-data-and-applications) och eventuella anpassade nätverk som kunden kan ha konfigurerat.

<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong>Microsoft- och Red Hat-ansvarsområden</strong>
   </td>
   <td><strong>Kundens ansvarsområden</strong>
   </td>
  </tr>
  <tr>
   <td>Programnätverk </td>
   <td>
<ul>

<li>Övervaka molnlastbalanserare och inbyggd OpenShift-routertjänst och svara på aviseringar.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Övervaka hälsotillståndet för tjänstlastbalanseringsslutpunkter.

<li>Övervaka hälsotillståndet för programvägar och slutpunkterna bakom dem.

<li>Rapportera avbrott till Microsoft och Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuellt nätverk
   </td>
   <td>
<ul>

<li>Övervaka lastbalanserare i molnet, undernät och Azure-molnkomponenter som krävs för standardplattformsnätverk och svara på aviseringar.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Övervaka nätverkstrafik som eventuellt har konfigurerats via VNet-till-VNet-anslutning, VPN-anslutning eller Private Link-anslutning för potentiella problem eller säkerhetshot.
</li>
</ul>
   </td>
  </tr>
</table>


Tabell 2. Delat ansvar för incident- och drifthantering


### <a name="change-management"></a>Ändringshantering

Microsoft och Red Hat ansvarar för att aktivera ändringar i klusterinfrastrukturen och -tjänsterna som kunden ska kontrollera, samt att underhålla versioner som är tillgängliga för huvudnoderna, infrastrukturtjänsterna och arbetsnoderna. Kunden ansvarar för att initiera infrastrukturändringar och installera och underhålla valfria tjänster och nätverkskonfigurationer i klustret, samt alla ändringar i kunddata och kundprogram.


<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong>Microsoft- och Red Hat-ansvarsområden</strong>
   </td>
   <td><strong>Kundens ansvarsområden</strong>
   </td>
  </tr>
  <tr>
   <td>Loggning </td>
   <td>
<ul>

<li>Aggregera och övervaka plattformsgranskningsloggar centralt.

<li>Tillhandahålla dokumentation för kunden för att aktivera programloggning med Log Analytics via Azure Monitor för containrar.

<li>Ange granskningsloggar vid kundbegäran.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Installera den valfria standardoperatorn för programloggning i klustret.

<li>Installera, konfigurera och underhålla valfria lösningar för apploggning, till exempel loggning av sidovagnscontainrar eller loggningsprogram från tredje part.

<li>Justera storlek och frekvens för programloggar som skapas av kundprogram om de påverkar klustrets stabilitet.

<li>Begära plattformsgranskningsloggar via ett supportfall för att undersöka specifika incidenter.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Programnätverk
   </td>
   <td>
<ul>

<li>Konfigurera lastbalanserare för offentliga moln

<li>Konfigurera intern OpenShift-routertjänst. Ge möjlighet att ställa in routern som privat och lägga till ytterligare en router-shard.

<li>Installera, konfigurera och underhålla OpenShift SDN-komponenter för intern standardtrafik för poddar.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurera icke-standardbehörigheter för poddnätverk för projekt- och poddnätverk, poddingress och utgående poddar med hjälp av NetworkPolicy-objekt.

<li>Begära och konfigurera ytterligare tjänstlastbalanserare för specifika tjänster.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Klusternätverk
   </td>
   <td>
<ul>

<li>Konfigurera klusterhanteringskomponenter, till exempel offentliga eller privata tjänstslutpunkter och nödvändig integrering med virtuella nätverkskomponenter.

<li>Konfigurera interna nätverkskomponenter som krävs för intern klusterkommunikation mellan arbetsnoder och huvudnoder.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Ange valfria IP-adressintervall som inte är standard för dator-CIDR, tjänst-CIDR och podd-CIDR om det behövs via OpenShift Cluster Manager när klustret etableras.

<li>Begär att API-tjänstslutpunkten görs offentlig eller privat när klustret skapas eller när klustret har skapats via Azure CLI.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuellt nätverk
   </td>
   <td>
<ul>

<li>Konfigurera de virtuella nätverkskomponenter som krävs för att etablera klustret, inklusive virtuella privata moln, undernät, lastbalanserare, Internetgatewayer, NAT-gatewayer osv.

<li>Ge kunden möjlighet att hantera VPN-anslutning med lokala resurser, VNet-till-VNet-anslutning och Private Link anslutning efter behov via OpenShift Cluster Manager.

<li>Gör det möjligt för kunder att skapa och distribuera lastbalanserare för offentliga moln för användning med tjänstlastbalanserare.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurera och underhålla valfria nätverkskomponenter för offentliga moln, till exempel VNet-till-VNet-anslutning, VPN-anslutning eller Private Link anslutning.

<li>Begära och konfigurera ytterligare lastbalanserare för specifika tjänster.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Klusterversion
   </td>
   <td>
<ul>

<li>Kommunicera schema och status för uppgraderingar för mindre versioner och underhållsversioner

<li>Publicera ändringsloggar och information om mindre uppgraderingar och underhållsuppgraderingar
</li>
</ul>
   </td>
   <td>
<ul>

<li>Starta uppgradering av kluster

<li>Testa kundprogram på mindre versioner och underhållsversioner för att säkerställa kompatibilitet
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Kapacitetshantering
   </td>
   <td>
<ul>

<li>Övervaka användningen av kontrollplanet (huvudnoder)

<li>Skala och/eller ändra storlek på kontrollplansnoder för att upprätthålla tjänstkvaliteten

<li>Övervaka användningen av kundresurser, inklusive nätverk, lagring och beräkningskapacitet. Om autoskalningsfunktioner inte är aktiverade varnar kunden om eventuella ändringar som krävs för klusterresurser (t.ex. nya beräkningsnoder för skalning, ytterligare lagring osv.)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Använd de tillhandahållna OpenShift Cluster Manager-kontrollerna för att lägga till eller ta bort ytterligare arbetsnoder efter behov.

<li>Svara på Microsoft- och Red Hat-meddelanden om klusterresurskrav.
</li>
</ul>
   </td>
  </tr>
</table>


Tabell 3. Delat ansvar för ändringshantering


### <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering 

Identitets- och åtkomsthanteringen omfattar alla ansvarsområden för att säkerställa att endast rätt personer har åtkomst till kluster-, program- och infrastrukturresurser. Detta omfattar uppgifter som att tillhandahålla åtkomstkontrollmekanismer, autentisering, auktorisering och hantera åtkomst till resurser.


<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong>Microsoft- och Red Hat-ansvarsområden</strong>
   </td>
   <td><strong>Kundens ansvar</strong>
   </td>
  </tr>
  <tr>
   <td>Loggning </td>
   <td>
<ul>

<li>Följ en branschstandardbaserad process för nivåindelad intern åtkomst för plattformsgranskningsloggar.

<li>Tillhandahåll inbyggda RBAC-funktioner för OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurera OpenShift RBAC för att styra åtkomsten till projekt och i tillägg ett projekts programloggar.

<li>Kunden ansvarar för åtkomsthantering för loggningslösningar från tredje part eller anpassade program.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Programnätverk
   </td>
   <td>
<ul>

<li>Tillhandahåll inbyggda RBAC-funktioner för OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Konfigurera OpenShift RBAC för att styra åtkomsten till vägkonfigurationen efter behov.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Klusternätverk
   </td>
   <td>
<ul>

<li>Tillhandahåll inbyggda RBAC-funktioner för OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Hantera Red Hat-organisationsmedlemskap för Red Hat-konton.

<li>Hantera organisationsadministratörer för Red Hat-organisationen för att bevilja åtkomst till OpenShift Cluster Manager.

<li>Konfigurera OpenShift RBAC för att styra åtkomsten till vägkonfigurationen efter behov.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuellt nätverk
   </td>
   <td>
<ul>

<li>Tillhandahåll kundåtkomstkontroller via OpenShift Cluster Manager.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Hantera valfri användaråtkomst till komponenter i offentliga moln via OpenShift Cluster Manager.
</li>
</ul>
   </td>
  </tr>
</table>


Tabell 4. Delat ansvar för identitets- och åtkomsthantering


### <a name="security-and-regulation-compliance"></a>Säkerhet och regelefterlevnad 

Säkerhet och efterlevnad omfattar alla ansvarsområden och kontroller som säkerställer efterlevnad av relevanta lagar, principer och föreskrifter.


<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong>Microsoft- och Red Hat-ansvarsområden</strong>
   </td>
   <td><strong>Kundens ansvar</strong>
   </td>
  </tr>
  <tr>
   <td>Loggning </td>
   <td>
<ul>

<li>Skicka klustergranskningsloggar till microsoft- och Red Hat SIEM för att analysera säkerhetshändelser. Behåll granskningsloggar under en angiven tidsperiod för att stödja kriminalteknisk analys.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analysera programloggar för säkerhetshändelser. Skicka programloggar till en extern slutpunkt via loggning av sidovagnscontainrar eller loggningsprogram från tredje part om längre kvarhållning krävs än vad som erbjuds av standardloggningsstacken.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Virtuellt nätverk
   </td>
   <td>
<ul>

<li>Övervaka virtuella nätverkskomponenter för potentiella problem och säkerhetshot.

<li>Använd ytterligare offentliga Microsoft- och Red Hat Azure-verktyg för ytterligare övervakning och skydd.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Övervaka eventuellt konfigurerade virtuella nätverkskomponenter för potentiella problem och säkerhetshot.

<li>Konfigurera eventuella nödvändiga brandväggsregler eller datacenterskydd efter behov.
</li>
</ul>
   </td>
  </tr>
</table>


Tabell 5. Delat ansvar för säkerhet och regelefterlevnad


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Kundens ansvar vid användning av Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Kunddata och program

Kunden ansvarar för de program, arbetsbelastningar och data som de distribuerar till Azure Red Hat OpenShift. Microsoft och Red Hat tillhandahåller dock olika verktyg som hjälper kunden att hantera data och program på plattformen.


<table>
  <tr>
   <td><strong>Resurs</strong>
   </td>
   <td><strong>Så hjälper Microsoft och Red Hat</strong>
   </td>
   <td><strong>Kundens ansvar</strong>
   </td>
  </tr>
  <tr>
   <td>Kundinformation </td>
   <td>
<ul>

<li>Upprätthålla standarder på plattformsnivå för datakryptering enligt definitionen i branschens säkerhets- och efterlevnadsstandarder. 

<li>Tillhandahålla OpenShift-komponenter som hjälper dig att hantera programdata, till exempel hemligheter.

<li>Aktivera integrering med datatjänster från tredje part (till exempel Azure SQL) för att lagra och hantera data utanför klustret och/eller Microsoft och Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Behåll ansvaret för alla kunddata som lagras på plattformen och hur kundprogram använder och exponerar dessa data.

<li>Etcd-kryptering
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Kundprogram
   </td>
   <td>
<ul>

<li>Etablera kluster med OpenShift-komponenter installerade så att kunderna kan komma åt OPENShift- och Kubernetes-API:erna för att distribuera och hantera program i containrar.

<li>Ge åtkomst till OpenShift-API:er som en kund kan använda för att konfigurera operatörer för att lägga till community-, tredjeparts-, Microsoft- och Red Hat- och Red Hat-tjänster i klustret. 

<li>Tillhandahålla lagringsklasser och plugin-program för att stödja beständiga volymer för användning med kundprogram.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Ha kvar ansvaret för kund- och tredjepartsprogram, data och deras fullständiga livscykel.

<li>Om en kund lägger till Red Hat, community, tredje part, sina egna eller andra tjänster i klustret med hjälp av operatörer eller externa avbildningar, ansvarar kunden för dessa tjänster och för att arbeta med lämplig leverantör (inklusive Red Hat) för att felsöka eventuella problem.

<li>Använd de tillhandahållna verktygen och funktionerna för <a href="https://docs.openshift.com/aro/4/architecture/understanding-development.html#application-types">att konfigurera och distribuera</a>. <a href="https://docs.openshift.com/aro/4/applications/deployments/deployment-strategies.html">hålla dig uppdaterad</a>; <a href="https://docs.openshift.com/aro/4/applications/working-with-quotas.html">konfigurera resursbegäranden och gränser</a>; <a href="https://docs.openshift.com/aro/4/getting_started/scaling-your-cluster.html">ändra storlek på klustret så att det finns tillräckligt med resurser för att köra appar</a>; <a href="https://docs.openshift.com/aro/4/administering_a_cluster/">konfigurera behörigheter</a>; integrera med andra tjänster; <a href="https://docs.openshift.com/aro/4/openshift_images/images-understand.html">hantera avbildningsströmmar eller mallar som kunden distribuerar;</a> <a href="https://docs.openshift.com/aro/4/cloud_infrastructure_access">externt betjäna</a>; spara, återställning och återställning av data; och på annat sätt hantera sina hög tillgängliga och motståndskraftiga arbetsbelastningar.

<li>Ha kvar ansvaret för att övervaka de program som körs på Azure Red Hat OpenShift; inklusive installation och drift av programvara för att samla in mått och skapa aviseringar.
</li>
</ul>
   </td>
  </tr>
</table>


Tabell 7. Kundansvar för kunddata, kundprogram och tjänster
