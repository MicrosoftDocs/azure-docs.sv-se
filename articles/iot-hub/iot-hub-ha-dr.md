---
title: Återställning av Azure IoT Hub hög tillgänglighet och haveri beredskap | Microsoft Docs
description: Beskriver de Azure-och IoT Hub-funktioner som hjälper dig att skapa Azure IoT-lösningar med hög tillgänglighet med funktioner för haveri beredskap.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: c665e30ed9b284f7c93cf8588b710c9f22457a0a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92151668"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Hög tillgänglighet och haveriberedskap för IoT Hub

Som ett första steg mot att implementera en elastisk IoT-lösning måste arkitekter, utvecklare och företags ägare definiera drift tiden för de lösningar som de skapar. Dessa mål kan definieras främst baserat på specifika affärs mål för varje scenario. I det här sammanhanget beskriver artikeln [Azure-affärskontinuitet teknisk vägledning](/azure/architecture/resiliency/) ett allmänt ramverk som hjälper dig att tänka på verksamhets kontinuitet och haveri beredskap. [Haveri beredskap och hög tillgänglighet för Azure](/azure/architecture/reliability/disaster-recovery) -programpapper ger arkitektur vägledning om strategier för Azure-program för att uppnå hög tillgänglighet (ha) och haveri beredskap (Dr).

Den här artikeln beskriver de HA och DR-funktioner som erbjuds specifikt av IoT Hubs tjänsten. De breda områden som beskrivs i den här artikeln är:

- HA inom region
- DR mellan regioner
- Uppnå flera regioner HA

Beroende på de drift tids mål som du definierar för dina IoT-lösningar bör du bestämma vilka av alternativen som beskrivs nedan bäst som passar dina affärs mål. Att inkludera något av dessa HA/DR-alternativ i IoT-lösningen kräver en noggrann utvärdering av handeln mellan:

- Återhämtnings nivå du behöver 
- Implementering och underhålls komplexitet
- KSV-påverkan

## <a name="intra-region-ha"></a>HA inom region

IoT Hubs tjänsten ger till gång till flera regioner genom att implementera redundans i nästan alla lager i tjänsten. Service [avtalet som publiceras av IoT Hubs tjänsten](https://azure.microsoft.com/support/legal/sla/iot-hub) uppnås genom att använda dessa redundanser. Inga ytterligare arbete krävs av utvecklare av en IoT-lösning för att dra nytta av dessa HA-funktioner. Även om IoT Hub erbjuder en rimlig hög drifts garanti, kan tillfälliga problem fortfarande förväntas som med en distribuerad dator plattform. Om du precis har kommit igång med att migrera dina lösningar till molnet från en lokal lösning, måste du växla från att optimera "genomsnittlig tid mellan fel" till "genomsnittlig tid för att återställa". Med andra ord anses tillfälliga haverier vara normala när du arbetar med molnet i blandningen. Lämpliga [principer för återförsök](iot-hub-reliability-features-in-sdks.md) måste vara inbyggda i komponenterna som samverkar med ett moln program för att hantera tillfälliga fel.

> [!NOTE]
> Vissa Azure-tjänster ger också ytterligare tillgänglighets nivåer inom en region genom integrering med [Tillgänglighetszoner (AZS)](../availability-zones/az-overview.md). AZs stöds för närvarande inte av tjänsten IoT Hub.

## <a name="cross-region-dr"></a>DR mellan regioner

Det kan finnas sällsynta situationer när ett Data Center upplever utökade avbrott på grund av strömavbrott eller andra problem som involverar fysiska till gångar. Sådana händelser är sällsynta under vilka den region som har beviljats inom regioner som beskrivs ovan inte alltid bidrar till det. IoT Hub tillhandahåller flera lösningar för att få en återställning från sådana utökade avbrott. 

De återställnings alternativ som är tillgängliga för kunder i en sådan situation är att [Microsoft-initierar redundans](#microsoft-initiated-failover) och [manuell redundans](#manual-failover). Den grundläggande skillnaden mellan de två är att Microsoft initierar den tidigare och användaren initierar den senare. Manuell redundans ger också ett mindre återställnings tids mål (RTO) jämfört med alternativet Microsoft-initierad redundans. De angivna återställnings tider som erbjuds med varje alternativ beskrivs i avsnitten nedan. Om något av dessa alternativ för att utföra redundans av en IoT-hubb från dess primära region används, blir hubben helt funktionell i motsvarande [Azure geo-kopplade region](../best-practices-availability-paired-regions.md).

Båda dessa alternativ för redundans erbjuder följande mål för återställnings punkter (återställnings punkter):

| Datatyp | Återställnings punkt mål (återställnings punkt mål) |
| --- | --- |
| Identitets register |0-5 minuter data förlust |
| Enhets dubbla data |0-5 minuter data förlust |
| Meddelanden från moln till enhet<sup>1</sup> |0-5 minuter data förlust |
| Överordnade<sup>1</sup> -och enhets jobb |0-5 minuter data förlust |
| Meddelanden från enheten till molnet |Alla olästa meddelanden går förlorade |
| Meddelanden om åtgärds övervakning |Alla olästa meddelanden går förlorade |
| Meddelanden från moln till enhet-feedback |Alla olästa meddelanden går förlorade |

<sup>1</sup> Meddelanden från moln till enhet och överordnade jobb återställs inte som en del av manuell redundans.

När redundansväxlingen för IoT Hub har slutförts förväntas alla åtgärder från enhets-och backend-programmen fortsätta att fungera utan att det krävs någon manuell åtgärd. Det innebär att meddelanden från enhet till molnet bör fortsätta att fungera och hela enhets registret är intakt. Händelser som skickas via Event Grid kan förbrukas via samma prenumeration (er) som kon figurer ATS tidigare så länge som de Event Grid prenumerationerna fortsätter att vara tillgängliga. Ingen ytterligare hantering krävs för anpassade slut punkter.

> [!CAUTION]
> - Det Event Hub-kompatibla namnet och slut punkten för den IoT Hub inbyggda händelse slut punkten ändras efter redundansväxlingen. När du tar emot telemetri-meddelanden från den inbyggda slut punkten med hjälp av Event Hub-klienten eller händelse processor värden bör du [använda IoT Hub-anslutningssträngen](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) för att upprätta anslutningen. Detta säkerställer att dina backend-program fortsätter att fungera utan att behöva utföra manuella åtgärder efter redundansväxlingen. Om du använder det Event Hub-kompatibla namnet och slut punkten i ditt program direkt måste du [Hämta den nya Event Hub-kompatibla slut punkten](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) efter redundansväxlingen för att fortsätta åtgärder. 
>
> - Om du använder Azure Functions eller Azure Stream Analytics för att ansluta den inbyggda slut punkten för händelser kan du behöva utföra en **omstart**. Detta beror på att under redundansväxlingen tidigare förskjutningar inte längre är giltiga.
>
> - Vid routning till lagring rekommenderar vi att du listar blobbar eller filer och sedan går över dem, så att alla blobbar eller filer läses utan att du behöver göra några antaganden om partitionen. Partitions intervallet kan eventuellt ändras under en Microsoft-initierad redundans eller manuell redundans. Du kan använda [list-BLOB-API: et](/rest/api/storageservices/list-blobs) för att räkna upp listan över blobbar eller [lista ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/list) för listan med filer. Mer information finns i [Azure Storage som en Dirigerings slut punkt](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

## <a name="microsoft-initiated-failover"></a>Microsoft-initierad redundans

Microsoft-initierad redundans utnyttjas av Microsoft i sällsynta fall för att redundansväxla alla IoT-hubbar från en berörd region till motsvarande geo-kopplade region. Den här processen är ett standard alternativ (inget sätt för användare att avanmäla) och kräver ingen åtgärd från användaren. Microsoft förbehåller sig rätten att fastställa när det här alternativet kommer att användas. Den här mekanismen omfattar inte användarens medgivande innan användarens hubb har redundansväxlats. Microsoft-initierad redundans har ett återställnings tids mål (RTO) på 2-26 timmar. 

Den stora RTO är att Microsoft måste utföra redundansväxlingen på uppdrag av alla berörda kunder i den regionen. Om du kör en mindre viktig IoT-lösning som kan ha en stillestånds tid på ungefär en dag, är det OK att du kan ta ett beroende på det här alternativet för att uppfylla de totala haveri återställnings målen för din IoT-lösning. Den totala tiden för körnings åtgärder som ska utföras fullständigt när den här processen har utlösts beskrivs i avsnittet "tid att återställa".

## <a name="manual-failover"></a>Manuell redundans

Om dina affärs drifts mål inte uppfylls av RTO som Microsoft har initierat redundans, bör du överväga att använda manuell redundans för att utlösa redundansväxlingen själv. RTO med det här alternativet kan vara var som helst mellan 10 minuter och ett par timmar. RTO är för närvarande en funktion i antalet enheter som registrerats mot IoT Hub-instansen som har redundansväxlats. Du kan vänta på att RTO för ett nav som är värd för cirka 100 000 enheter är i ungefärligt på 15 minuter. Den totala tiden för körnings åtgärder som ska utföras fullständigt när den här processen har utlösts beskrivs i avsnittet "tid att återställa".

Alternativet Manuell redundans är alltid tillgängligt för användning oavsett om den primära regionen drabbas av nertid eller inte. Det innebär att det här alternativet kan användas för att utföra planerade redundansväxlingen. Ett exempel på användningen av planerade redundanser är att utföra regelbunden granskning av redundans. Ett ord med försiktighets åtgärder är att en planerad redundansväxling resulterar i en stillestånds tid för hubben för den period som definieras av RTO för det här alternativet, och resulterar också i en data förlust som definieras i tabellen tabellen ovan. Du kan överväga att konfigurera en instans av en test-IoT Hub så att du kan använda alternativet för planerad redundans med jämna mellanrum för att få en bättre tillförlitlighet i din förmåga att komma igång med dina heltäckande lösningar när en riktig katastrof inträffar.

Manuell redundans är tillgängligt utan extra kostnad för IoT Hub som skapats efter 18 maj 2017

Stegvisa instruktioner finns i [Självstudier: utföra manuell redundans för en IoT-hubb](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Köra test övningar

Test övningar bör inte utföras på IoT-hubbar som används i produktions miljöerna.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Använd inte manuell redundans för att migrera IoT Hub till en annan region

Manuell redundans ska *inte* användas som en mekanism för att permanent migrera hubben mellan Azure geo-kopplade regioner. Om du gör det ökar svars tiden för de åtgärder som utförs mot IoT-hubben från enheter som är hemma i den gamla primära regionen.

## <a name="failback"></a>Återställning efter fel

Det går inte att återställa till den gamla primära regionen genom att utlösa Redundansåtgärden en annan gången. Om den ursprungliga redundansväxlingen utfördes för att återställa från ett utökat avbrott i den ursprungliga primära regionen, rekommenderar vi att hubben inte kan återställas till den ursprungliga platsen när platsen har återställts från avbrott-situationen.

> [!IMPORTANT]
> - Användare får endast utföra 2 lyckade redundansväxling och 2 lyckade återställnings åtgärder per dag.
>
> - Tillbaka till redundansväxling/failback-åtgärder är inte tillåtna. Du måste vänta i 1 timme mellan dessa åtgärder.

## <a name="time-to-recover"></a>Tid för återställning

Även om det fullständiga domän namnet (och därmed anslutnings strängen) för IoT Hub-instansen fortfarande är samma efter redundansväxlingen, ändras den underliggande IP-adressen. Den totala tiden för körnings åtgärder som utförs mot din IoT Hub-instans för att bli fullt fungerande När redundansväxlingen utlöses kan uttryckas med hjälp av följande funktion.

Tid för återställning = RTO [10 min-2 timmar för manuell redundansväxling | 2-26 timmar för Microsoft-initierad redundans] + DNS-spridning fördröjning + den tid det tar för klient programmet att uppdatera alla cachelagrade IoT Hub IP-adresser.

> [!IMPORTANT]
> IoT SDK: er cachelagrar inte IP-adressen för IoT Hub. Vi rekommenderar att användar koden interaktivt med SDK: er inte ska cachelagra IP-adressen för IoT Hub.

## <a name="achieve-cross-region-ha"></a>Få mellan region HA

Om dina affärs drifts mål inte uppfylls av RTO som antingen har Microsoft-initierat redundans eller manuella alternativ för redundans, bör du överväga att implementera en automatisk växling mellan flera enheter.
En fullständig behandling av distributions topologier i IoT-lösningar ligger utanför omfånget för den här artikeln. I artikeln beskrivs den *regionala* distributions modellen för redundansväxling för hög tillgänglighet och haveri beredskap.

I en regional redundansväxling körs lösningens Server del främst på en data Center plats. En sekundär IoT-hubb och Server del distribueras på en annan data Center plats. Om IoT-hubben i den primära regionen drabbas av ett avbrott eller om nätverks anslutningen från enheten till den primära regionen avbryts, använder enheterna en sekundär tjänst slut punkt. Du kan förbättra tillgängligheten för lösningen genom att implementera en växlings modell över flera regioner i stället för att hålla dig inom en enda region. 

För att implementera en regional växlings modell med IoT Hub måste du utföra följande steg på hög nivå:

* **En sekundär IoT-hubb och enhets cirkulations logik**: om tjänsten i din primära region avbryts måste enheterna börja ansluta till den sekundära regionen. Med tanke på den viktigaste arten av de flesta tjänster är det vanligt att administratörerna kan utlösa redundansväxlingen mellan regioner. Det bästa sättet att kommunicera den nya slut punkten till enheter, och samtidigt behålla kontrollen över processen, är att regelbundet kontrol lera en *Concierge* -tjänst för den aktuella aktiva slut punkten. Tjänsten concierge kan vara ett webb program som replikeras och hålls tillgängligt med metoder för DNS-omdirigering (till exempel med hjälp av [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > Tjänsten IoT Hub är en slut punkts typ som inte stöds i Azure Traffic Manager. Rekommendationen är att integrera den föreslagna tjänsten Concierge med Azure Traffic Manager genom att implementera slut punktens hälso avsöknings-API.

* **Identitets registrets replikering**: för att kunna användas måste den sekundära IoT-hubben innehålla alla enhets identiteter som kan ansluta till lösningen. Lösningen bör behålla geo-replikerade säkerhets kopior av enhets identiteter och överföra dem till den sekundära IoT-hubben innan du växlar den aktiva slut punkten för enheterna. Funktionen för export av enhets identiteter i IoT Hub är användbar i den här kontexten. Mer information finns i [IoT Hub Developer Guide-Identity Registry](iot-hub-devguide-identity-registry.md).

* **Sammanfoga logik**: när den primära regionen blir tillgänglig igen måste alla tillstånd och data som har skapats på den sekundära platsen migreras tillbaka till den primära regionen. Det här läget och data relaterar mest till enhets identiteter och programmetadata, som måste slås samman med den primära IoT-hubben och andra programspecifika butiker i den primära regionen. 

För att förenkla det här steget bör du använda idempotenta-åtgärder. Idempotenta-åtgärder minimerar sido effekterna från den eventuella konsekventa distributionen av händelser och från dubbletter eller direkt leverans av händelser. Dessutom bör program logiken utformas för att tolerera potentiella inkonsekvenser eller något inaktuell. Den här situationen kan inträffa på grund av den ytterligare tid det tar för systemet att laga kraft baserat på återställnings punkt mål.

## <a name="choose-the-right-hadr-option"></a>Välj rätt HA/DR-alternativ

Här är en sammanfattning av de HA/DR-alternativ som visas i den här artikeln som kan användas som referens ram för att välja rätt alternativ som passar din lösning.

| HA/DR-alternativ | RTO | RPO | Krävs manuell åtgärd? | Implementerings komplexitet | Ytterligare kostnads påverkan|
| --- | --- | --- | --- | --- | --- |
| Microsoft-initierad redundans |2-26 timmar|Referera till tabellen återställnings punkt ovan|Inga|Inga|Inga|
| Manuell redundans |10 min – 2 timmar|Referera till tabellen återställnings punkt ovan|Ja|Mycket låg. Du behöver bara utlösa den här åtgärden från portalen.|Inget|
| Flera regioner HA |< 1 min|Beror på replikeringsfrekvens för din anpassade HA-lösning|Inga|Högt|> 1x kostnaden för 1 IoT Hub|

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure IoT Hub?](about-iot-hub.md)
* [Kom igång med IoT Hub (snabb start)](quickstart-send-telemetry-dotnet.md)
* [Självstudie: utföra manuell redundans för en IoT-hubb](tutorial-manual-failover.md)