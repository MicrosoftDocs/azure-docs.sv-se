---
title: Automatisk skalning och zonredundant Application Gateway v2
description: Den här artikeln beskriver Azure Application Standard_v2 och WAF_v2 SKU, som innehåller funktioner för automatisk skalning och zon redundant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: victorh
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: fad6e27c4ee7e8c10237cb3face5cfab9329b2ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98059729"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatisk skalning och zonredundant Application Gateway v2 

Application Gateway är tillgängligt under en Standard_v2 SKU. Brand vägg för webbaserade program (WAF) är tillgänglig under en WAF_v2 SKU. V2-SKU: n erbjuder prestanda förbättringar och ger stöd för viktiga nya funktioner som automatisk skalning, zon redundans och stöd för statiska VIP. Befintliga funktioner under standard-och WAF-SKU: n fortsätter att stödjas i den nya v2-SKU: n, med några undantag som anges i [jämförelse](#differences-from-v1-sku) avsnittet.

Den nya v2-SKU: n innehåller följande förbättringar:

- Automatisk **skalning**: Application Gateway-eller WAF-distributioner under SKU: n för automatisk skalning kan skala ut eller i baserat på Ändra trafik belastnings mönster. Automatisk skalning tar även bort behovet av att välja distributionsstorlek eller instansantal under etablering. Den här SKU: n erbjuder verklig elastiskhet. I Standard_v2 och WAF_v2 SKU: n kan Application Gateway använda både fast kapacitet (autoskalning inaktive rad) och i autoskalning aktiverat läge. Läget för fast kapacitet är användbart för scenarier med konsekventa och förutsägbara arbets belastningar. Autoskalning-läget är fördelaktigt i program som ser varians i program trafik.
- **Zon redundans**: en Application Gateway-eller WAF-distribution kan spänna över flera Tillgänglighetszoner och du tar bort behovet av att etablera separata Application Gateway instanser i varje zon med en Traffic Manager. Du kan välja en enskild zon eller flera zoner där Application Gateway instanser distribueras, vilket gör det mer flexibelt för zon haverit. Backend-poolen för program kan distribueras på samma sätt i tillgänglighets zoner.

  Zon redundans är bara tillgängligt där Azure-zoner är tillgängliga. I andra regioner stöds alla andra funktioner. Mer information finns i [regioner och Tillgänglighetszoner i Azure](../availability-zones/az-overview.md)
- **Statisk VIP**: Application Gateway v2 SKU stöder enbart statisk VIP-typ. Detta säkerställer att den VIP som är associerad med programgatewayen inte ändras för distributionens livs cykel, även efter en omstart.  Det finns ingen statisk VIP i v1, så du måste använda Application Gateway-URL: en i stället för IP-adressen för att routning av domän namn ska kunna App Services via programgatewayen.
- **Omskrivning av rubrik**: Application Gateway låter dig lägga till, ta bort eller uppdatera http-begäran och svarshuvuden med v2-SKU. Mer information finns i [skriva om HTTP-huvuden med Application Gateway](rewrite-http-headers.md)
- **Key Vault-integrering**: Application Gateway v2 stöder integrering med Key Vault för Server certifikat som är anslutna till https-aktiverade lyssnare. Läs mer i [TLS-avslutning med Key Vault-certifikat](key-vault-certs.md).
- Ingångs **kontroll av Azure Kubernetes-tjänsten**: Application Gateway v2 ingångs styrenheten tillåter att Azure Application Gateway används som ingångs punkt för en Azure Kubernetes-tjänst (AKS) som kallas AKS-kluster. Mer information finns i [Vad är Application Gateway ingress-kontrollant?](ingress-controller-overview.md).
- **Prestanda förbättringar**: v2-SKU: n erbjuder upp till fem bättre TLS-avläsnings prestanda jämfört med standard-/WAF SKU: n.
- **Snabbare distribution och uppdaterings tid** V2-SKU: n ger snabbare distribution och uppdaterings tid jämfört med standard-/WAF SKU. Detta inkluderar även WAF konfigurations ändringar.

![Diagram över automatisk skalnings zon.](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regioner som stöds

Standard_v2-och WAF_v2 SKU: n är tillgänglig i följande regioner: Norra centrala USA, södra centrala USA, västra USA, västra USA 2, östra USA, östra USA 2, centrala USA, norra Europa, Västeuropa, Sydostasien, Frankrike Storbritannien, västra, USA, östra, västra Japan, östra Australien, sydöstra Australien, södra Brasilien, centrala Kanada, östra USA Asien, östra, centrala Korea , Storbritannien, södra, centrala Indien, västra Indien, södra Indien.

## <a name="pricing"></a>Priser

Med v2-SKU: n drivs pris sättnings modellen av förbrukning och är inte längre kopplad till instans antal eller storlekar. Priserna för v2-SKU har två komponenter:

- **Fast pris** – det här är ett Tim pris (eller delvis tim) för att etablera en Standard_v2 eller WAF_v2 Gateway. Observera att 0 ytterligare minimi instanser fortfarande säkerställer hög tillgänglighet för tjänsten som alltid ingår i fast pris.
- **Pris för kapacitets enhet** – det här är en förbruknings-baserad kostnad som debiteras utöver den fasta kostnaden. Kapacitets enhets avgiften beräknas också per timme eller delvis per timme. Det finns tre dimensioner för kapacitets enhet – beräknings enhet, beständiga anslutningar och data flöde. Beräkningsenhet är ett mått på förbrukad processorkapacitet. TLS-anslutningar per sekund, beräkningar för omskrivning av webbadresser och bearbetning av WAF-regler är faktorer som påverkar beräkningsenheter. Beständig anslutning är ett mått på upprättade TCP-anslutningar till Application Gateway under ett visst fakturerings intervall. Data flödet är Genomsnittligt antal megabitar/s som bearbetats av systemet under ett angivet fakturerings intervall.  Faktureringen görs på en kapacitets enhets nivå för allt ovanför antalet reserverade instanser.

Varje kapacitets enhet består av högst: 1 beräknings enhet, 2500 beständiga anslutningar och 2,22 – Mbps-genomflöde.

Mer information finns i [förstå priser](understanding-pricing.md).

## <a name="scaling-application-gateway-and-waf-v2"></a>Skala Application Gateway och WAF v2

Application Gateway-och WAF kan konfigureras för skalning i två lägen:

- Automatisk **skalning** – när automatisk skalning är aktiverat skalar Application Gateway-och WAF v2-SKU: erna upp eller ned baserat på program trafik krav. Det här läget ger bättre elastiskhet för ditt program och eliminerar behovet av att gissa storleken på programgatewayen eller antalet instanser. I det här läget kan du också spara kostnader genom att inte kräva att gatewayen körs vid den högsta etablerade kapaciteten för förväntad högsta trafik belastning. Du måste ange ett minsta och alternativt maximalt antal instanser. Lägsta kapacitet garanterar att Application Gateway och WAF v2 inte hamnar under det minsta antal instanser som anges, även om trafik saknas. Varje instans motsvarar ungefär 10 extra reserverade kapacitets enheter. Noll betyder ingen reserverad kapacitet och är helt automatisk skalning av natur. Du kan också ange ett maximalt antal instanser, vilket säkerställer att Application Gateway inte skalas bortom det angivna antalet instanser. Du debiteras bara för den mängd trafik som hanteras av gatewayen. Antalet instanser kan vara mellan 0 och 125. Standardvärdet för maximalt antal instanser är 20 om inget värde anges.
- **Manuellt** – du kan alternativt välja manuellt läge där gatewayen inte kan skalas om. I det här läget, om det finns mer trafik än vad Application Gateway eller WAF kan hantera, kan det leda till förlust av trafik. Med manuellt läge är det obligatoriskt att ange instans antal. Antalet instanser kan variera mellan 1 och 125 instanser.

## <a name="autoscaling-and-high-availability"></a>Automatisk skalning och hög tillgänglighet

Azure Application gatewayer distribueras alltid med hög tillgänglighet. Tjänsten består av flera instanser som skapas som konfigurerade (om autoskalning är inaktive rad) eller krävs av program belastningen (om autoskalning är aktiverat). Observera att från användarens perspektiv behöver du inte nödvändigt vis ha insyn i de enskilda instanserna, utan bara i Application Gateway tjänsten som helhet. Om en viss instans har problem och slutar fungera, kommer Azure Application Gateway att transparent skapa en ny instans.

Observera att även om du konfigurerar autoskalning med noll minsta instanser, kommer tjänsten fortfarande att vara hög tillgänglig, vilket alltid ingår i det fasta priset.

Det kan dock ta tid att skapa en ny instans (cirka sex eller sju minuter). Om du inte vill hantera den här stillestånds tiden kan du därför konfigurera ett minsta instans antal på 2, helst med stöd för tillgänglighets zon. På så sätt måste du ha minst två instanser i Azure Application Gateway under normala omständigheter, så om ett av dem hade problem kan det andra försöket att hantera trafiken under tiden som en ny instans skapas. Observera att en Azure Application Gateway-instans kan stödja runt 10 kapacitets enheter, så beroende på hur mycket trafik du vanligt vis har kanske du vill konfigurera den minsta instans inställningen för automatisk skalning till ett värde som är högre än 2.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Funktions jämförelse mellan v1 SKU och v2 SKU

I följande tabell jämförs de funktioner som är tillgängliga med varje SKU.

| Funktion                                           | v1 SKU   | v2-SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisk skalning                                       |          | &#x2713; |
| Zonredundans                                   |          | &#x2713; |
| Statisk VIP                                        |          | &#x2713; |
| Ingångs kontroll för Azure Kubernetes service (AKS) |          | &#x2713; |
| Azure Key Vault-integrering                       |          | &#x2713; |
| Skriv över HTTP (S)-rubriker                           |          | &#x2713; |
| URL-baserad routning                                 | &#x2713; | &#x2713; |
| Värd för flera platser                             | &#x2713; | &#x2713; |
| Omdirigering av trafik                               | &#x2713; | &#x2713; |
| Brandvägg för webbaserade program (WAF)                    | &#x2713; | &#x2713; |
| Anpassade WAF-regler                                  |          | &#x2713; |
| Transport Layer Security (TLS)/Secure Sockets Layer (SSL)-avslutning            | &#x2713; | &#x2713; |
| TLS-kryptering från slut punkt till slut punkt                         | &#x2713; | &#x2713; |
| Sessionstillhörighet                                  | &#x2713; | &#x2713; |
| Anpassade felsidor                                | &#x2713; | &#x2713; |
| WebSocket-stöd                                 | &#x2713; | &#x2713; |
| Stöd för HTTP/2                                    | &#x2713; | &#x2713; |
| Anslutningstömning                               | &#x2713; | &#x2713; |

> [!NOTE]
> Den automatiska skalning v2-SKU: n stöder nu [standard hälso avsökningar](application-gateway-probe-overview.md#default-health-probe) för att automatiskt övervaka hälsan för alla resurser i sin backend-pool och markera de Server dels medlemmar som betraktas som felaktiga. Standard hälso avsökningen konfigureras automatiskt för Server delar som inte har någon anpassad avsöknings konfiguration. Läs mer i [hälso avsökningar i Application Gateway](application-gateway-probe-overview.md).

## <a name="differences-from-v1-sku"></a>Skillnader från v1 SKU

I det här avsnittet beskrivs funktioner och begränsningar för v2-SKU: n som skiljer sig från v1 SKU.

|Skillnad|Information|
|--|--|
|Certifikat för autentisering|Stöds inte.<br>Mer information finns i [Översikt över TLS to end-TLS med Application Gateway](ssl-overview.md#end-to-end-tls-with-the-v2-sku).|
|Mixa Standard_v2 och standard Application Gateway i samma undernät|Stöds inte|
|User-Defined väg (UDR) på Application Gateway undernät|Stöds (vissa scenarier). I för hands version.<br> Mer information om vilka scenarier som stöds finns i [Application Gateway konfigurations översikt](configuration-infrastructure.md#supported-user-defined-routes).|
|NSG för inkommande port intervall| – 65200 till 65535 för Standard_v2 SKU<br>– 65503 till 65534 för standard-SKU.<br>Mer information finns i [vanliga frågor och svar](application-gateway-faq.yml#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Prestanda loggar i Azure Diagnostics|Stöds inte.<br>Azure-mått ska användas.|
|Fakturering|Faktureringen är schemalagd för att starta den 1 juli 2019.|
|FIPS-läge|Dessa stöds inte för närvarande.|
|Läge för endast ILB|Detta stöds inte för närvarande. Offentliga och ILB läge stöds tillsammans.|
|Net Watcher-integrering|Stöds inte.|
|Azure Security Center-integration|Ännu inte tillgängligt.

## <a name="migrate-from-v1-to-v2"></a>Migrera från v1 till v2

Det finns ett Azure PowerShell-skript i PowerShell-galleriet som hjälper dig att migrera från v1-Application Gateway/WAF till SKU: n för automatisk skalning i v2. Med det här skriptet kan du kopiera konfigurationen från v1-gatewayen. Trafikmigreringen är fortfarande ditt ansvar. Mer information finns i [migrera Azure Application Gateway från v1 till v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Nästa steg

- [Snabbstart: Dirigera webbtrafik med Azure Application Gateway – Azure Portal](quick-create-portal.md)
- [Skapa en automatisk skalning, en zon redundant Programgateway med en reserverad virtuell IP-adress med hjälp av Azure PowerShell](tutorial-autoscale-ps.md)
- Läs mer om [Application Gateway](overview.md).
- Läs mer om [Azure-brandväggen](../firewall/overview.md).
