---
title: Vad är Azure Security Center?| Microsoft Docs
description: Den här sidan beskriver Security Center viktiga fördelar med att identifiera ditt säkerhets tillstånd och förbättra den med täckning av moln resurser och lokala resurser.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: c4861192c7f2bbfb2a51e19b88daee45b501949b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634778"
---
# <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?

Azure Security Center är ett enhetligt system för hantering av infrastruktursäkerhet som stärker dina datacentras säkerhetstillstånd och tillhandahåller avancerat skydd för dina hybridarbetsbelastningar såväl i molnet, oavsett om de finns i Azure eller inte, som lokalt.

Att hålla dina resurser skyddade är en gemensam uppgift för din molnleverantör, Azure och dig som kund. Du måste se till att dina arbetsbelastningar skyddas när du migrerar till molnet, samtidigt som det, när du flyttar till IaaS (infrastruktur som en tjänst), innebär ett större kundansvar jämfört med hur det var i PaaS (plattform som en tjänst) och SaaS (programvara som en tjänst). Azure Security Center ger dig de verktyg du behöver för att stärka ditt nätverk, skydda dina tjänster och se till att du har koll på din säkerhetshållning.

Azure Security Center kan hantera de tre mest brådskande säkerhetsriskerna:

-   **Snabbt ändra arbets belastningar** – det är både en styrka och en utmaning i molnet. Å ena sidan kan slutanvändarna göra mer. Å andra sidan, hur försäkrar du dig om att de ständigt föränderliga tjänster som används och skapas också uppfyller dina säkerhetsstandarder och följer rekommenderade säkerhetsmetoder?

-   **Allt mer sofistikerade attacker** – Oavsett var du kör dina arbetsbelastningar blir attackerna ständigt alltmer avancerade. Du måste skydda dina arbetsbelastningar i det offentliga molnet. Det här är i praktiken arbetsbelastningar som är öppna mot internet och som kan gör dig ännu mer sårbar om du inte följer rekommenderade säkerhetsmetoder.

-   **Kompetens inom säkerhetsområdet är en bristvara**– Antalet säkerhetsaviseringar och aviseringssystem överträffar vida antalet administratörer med den nödvändiga bakgrund och erfarenhet som krävs för att se till att dina miljöer är skyddade. Att hålla sig informerad om de senaste attackerna är en konstant utmaning, och det är omöjligt att alltid vara på rätt plats eftersom kampen mot säkerhetshoten sker längs en ständigt föränderlig stridslinje.

Security Center har verktyg som kan hjälpa dig med de här utmaningarna.

-   **Förstärk säkerhets position**: Security Center utvärderar din miljö och gör det möjligt att förstå statusen för dina resurser och om de är säkra.

-   **Skydda mot hot**: Security Center utvärderar dina arbets belastningar och aktiverar rekommendationer för skydd mot hot och säkerhets aviseringar.

-   **Bli säker snabbare**: Allt sker med molnhastighet i Security Center. Eftersom Security Center är inbyggt sker distributionen snabbt och enkelt, och du får automatisk etablering och skydd för Azure-tjänster.

> [!NOTE]
> Den här tjänsten har stöd för [Azure-Lighthouse](../lighthouse/overview.md), som låter tjänst leverantörer logga in på sin egen klient för att hantera prenumerationer och resurs grupper som kunder har delegerat. För Azure Security Center scenarier måste en prenumeration delegeras i stället för enskilda resurs grupper.

## <a name="architecture"></a>Arkitektur

Eftersom Security Center är inbyggt i Azure, PaaS Services i Azure, inklusive Service Fabric, SQL Database, SQL-hanterad instans och lagrings konton – övervakas och skyddas av Security Center utan att det krävs någon distribution.

Security Center skyddar dessutom icke-Azure-servrar och virtuella datorer i molnet eller lokalt, för både Windows-och Linux-servrar genom att installera Log Analytics-agenten på dem. Virtuella Azure-datorer etableras automatiskt i Security Center.

De händelser som samlas in från agenterna och från Azure korreleras i Security Analytics-motorn för att ge dig anpassade rekommendationer (härdning av aktiviteter), som du bör följa för att se till att dina arbets belastningar är säkra och säkerhets aviseringar. Du bör kontrollera dessa aviseringar så snart som möjligt så att du kan försäkra dig om att dina arbetsbelastningar inte utsätts för potentiellt skadliga attacker.

När du aktiverar Security Center återspeglas säkerhets principen som är inbyggd för Security Center i Azure Policy som ett inbyggt initiativ under kategorin Security Center. Det inbyggda initiativet tilldelas automatiskt till alla Security Center registrerade prenumerationer (oavsett om de har Azure Defender aktiverat) eller inte. Det inbyggda initiativet innehåller endast granskningsprinciper. Läs mer om Security Center-principer i Azure Policy i artikeln om att [arbeta med säkerhetsprinciper](tutorial-security-policy.md).

## <a name="strengthen-security-posture"></a>Förbättra säkerheten

Med Azure Security Center kan du förbättra din säkerhet. Du får hjälp med att identifiera och utföra de härdningsuppgifter som anges som rekommenderade säkerhetsmetoder och implementera dem i dina datorer, datatjänster och appar. Detta inkluderar hantering och tillämpning av säkerhetsprinciper och säkerställande av att dina virtuella Azure-datorer, icke-Azure-servrar och Azure PaaS-tjänster är kompatibla. Security Center ger dig de verktyg du behöver ha få en översikt över dina arbetsbelastningar med fokuserad synlighet för din nätverkssäkerhetsegendom. 

### <a name="manage-organization-security-policy-and-compliance"></a>Hantera principer för organisationssäkerhet och efterlevnad

Det tillhör grunderna i säkerhetsarbetet att känna till och skydda sina arbetsbelastningar, och utgångspunkten är att ha skräddarsydda säkerhetsprinciper på plats. Eftersom alla principer i Security Center bygger på Azure Policy kontroller får du full räckvidd och flexibilitet i en **princip lösning i världs klass**. Du kan konfigurera dina principer i Security Center så att de körs för hanteringsgrupper, för prenumerationer och även för en hel klientorganisation.

:::image type="content" source="./media/security-center-intro/sc-dashboard.png" alt-text="Sidan princip hantering":::

Security Center hjälper dig att **identifiera SKUGG IT-prenumerationer**. Genom att titta på prenumerationer märkta **omfattas inte** på instrumentpanelen kan du se direkt när det har skapats nya prenumerationer och se till att de omfattas av dina principer och skyddas av Azure Security Center.

:::image type="content" source="./media/security-center-intro/sc-policy-dashboard.png" alt-text="Principinstrumentpanelen i Security Center":::

### <a name="continuous-assessments"></a>Kontinuerliga utvärderingar

Security Center identifierar nya resurser som distribueras till dina arbetsbelastningar och utvärderar om de har konfigurerats enligt rekommenderade säkerhetsmetoder. Om så inte är fallet flaggas de och du får en prioriterad lista med rekommendationer om vad du måste åtgärda för att skydda dina datorer. Den här listan över rekommendationer är aktive rad och stöds av [Azures säkerhets benchmark](../security/benchmarks/introduction.md), Microsoft-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet.

För att hjälpa dig att förstå hur viktiga varje rekommendation är för din övergripande säkerhets position, kan Security Center gruppera rekommendationerna i säkerhets kontroller och lägga till ett **säkert Poäng** värde för varje kontroll. Detta är viktigt för att du ska kunna **prioritera ditt säkerhets arbete**.

:::image type="content" source="./media/security-center-intro/sc-secure-score.png" alt-text="Security Center-säkerhetspoäng":::

### <a name="network-map"></a>Nätverks karta

Ett av de mest kraftfulla verktygen Security Center ger en kontinuerlig övervakning av nätverkets säkerhets status som **nätverks kartan**. Med hjälp av kartan kan du se dina arbetsbelastningars topologi, och därmed även se om varje enskild nod har konfigurerats korrekt. Du kan se hur dina noder är anslutna, vilket hjälper dig att blockera oönskade anslutningar som potentiellt kan göra det enklare för en angripare att ta sig in i ditt nätverk.

:::image type="content" source="./media/security-center-intro/sc-net-map.png" alt-text="Security Center-nätverkskarta":::


### <a name="optimize-and-improve-security-by-configuring-recommended-controls"></a>Optimera och förbättra säkerheten genom att konfigurera rekommenderas kontroller

Den avgörande faktorn för värdet av Azure Security Center ligger i dess rekommendationer. Rekommendationerna skräddarsys för varje enskilt säkerhetsproblem som finns i dina arbetsbelastningar och Security Center gör det administrativa säkerhetsarbetet för dig, inte bara genom att söka efter dina säkerhetsrisker, utan även genom att ge dig specifika anvisningar för hur du tar bort dem.

På så sätt gör Security Center det möjligt för dig att inte bara ställa in säkerhetsprinciper, utan även att tillämpa säkra konfigurationsstandarder för alla dina resurser.

Rekommendationerna hjälper dig att minska risken för angrepp för alla dina resurser. Detta omfattar Azure-datorer, icke-Azure-servrar och Azure PaaS-tjänster, som SQL- och Storage-konton och mycket mer, där varje resurstyp bedöms på olika sätt och har sin egen standarder.

:::image type="content" source="./media/security-center-intro/sc-recommendation-example.png" alt-text="Security Center-rekommendationsexempel":::

## <a name="protect-against-threats"></a>Skydda dig mot risker

Med Security Centers hotskydd kan du upptäcka och förebygga hot mot infrastrukturen som en tjänst (IaaS), icke-Azure-servrar och plattformar som en tjänst (PaaS) i Azure.

Security Centers hotskydd innehåller fusionsattackkedjeanalys, som automatiskt korrelerar aviseringar i din miljö, baserat på cyberattackkedjeanalysen, så att du bättre ska förstå en attackkampanjs i dess helhet, var den startade och vilken typ av effekt den haft på dina resurser.

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center säkerhets aviserings listan":::

### <a name="integration-with-microsoft-defender-for-endpoint"></a>Integrering med Microsoft Defender för slut punkt

Azure Defender för-servrar innehåller automatisk, inbyggd integrering med Microsoft Defender för slut punkt. Läs mer och [skydda dina slut punkter med Security Center Integrated EDR-lösning: Microsoft Defender för slut punkt](security-center-wdatp.md)


### <a name="protect-paas"></a>Skydda PaaS

Security Center hjälpa dig att upptäcka hot mot dina Azure PaaS-tjänster. Du kan identifiera hot mot dina Azure-tjänster, inklusive Azure App Service, Azure SQL, Azure Storage Account, och andra datatjänster. Du kan också dra nytta av den interna integreringen med Microsoft Cloud App Securitys analys av användar- och entitetsbeteende (UEBA), och därigenom genomföra avvikelseidentifiering på dina Azure-aktivitetsloggar.

### <a name="block-brute-force-attacks"></a>Blockera råstyrkeattackerare

Security Center hjälper dig att begränsa exponeringen för råstyrkeattacker. Genom att minska åtkomsten till VM-portar med hjälp av just-in-time-VM-åtkomst kan du skydda ditt nätverk genom att förhindra onödig åtkomst. Du kan ange principer för säker åtkomst på valda portar enbart för behöriga användare, tillåtna käll-IP-adressintervall eller IP-adresser under en begränsad tidsperiod.

### <a name="protect-data-services"></a>Skydda datatjänster

Security Center innehåller funktioner som hjälper dig att utföra automatisk klassificering av dina data i Azure SQL. Du kan också få utvärderingar avseende potentiella säkerhetsrisker för Azure SQL- och lagringstjänster och rekommendationer för hur du löser dem.

## <a name="get-secure-faster"></a>Bli skyddad snabbare

Intern Azure-integration (inklusive Azure Policy och Azure Monitor loggar) kombinerat med sömlös integrering med andra Microsoft-säkerhetslösningar, till exempel Microsoft Cloud App Security och Microsoft Defender för slut punkts hjälp se till att din säkerhetslösning är omfattande och enkel att publicera och distribuera.

Dessutom kan du utöka den fullständiga lösningen utöver Azure till arbetsbelastningar som körs i andra moln och i lokala datacentra.

### <a name="automatically-discover-and-onboard-azure-resources"></a>Identifiera och publicera Azure-resurser automatiskt

Security Center ger sömlös inbyggd integrering med Azure och Azure-resurser. Det innebär att du kan samla ihop en fullständig säkerhetshistoria som rör Azure Policy och inbyggda principer i Security Center för alla dina Azure-resurser och se till att alltihop tillämpas automatiskt på nyidentifierade resurser när du skapar dem i Azure.

Omfattande logginsamling som innebär att loggar från både Windows och Linux balanseras i säkerhetsanalysmotorn och används för att skapa rekommendationer och aviseringar.

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Security Center måste du ha en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [gratis provversion](https://azure.microsoft.com/free/).

- Security Center kostnads fri pris nivå är aktive rad för alla dina aktuella Azure-prenumerationer när du besöker Azure Security Center instrument panelen i Azure Portal för första gången, eller om den har Aktiver ATS via programmering via API. Om du vill dra nytta av funktionerna för avancerad säkerhets hantering och hot identifiering måste du aktivera Azure Defender. Azure Defender kan provas kostnads fritt i 30 dagar. På [prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/) finns det mer information.

- Om du är redo att aktivera Azure Defender nu kan du göra följande [: konfigurera Azure Security Center](security-center-get-started.md) vägleder dig genom stegen.