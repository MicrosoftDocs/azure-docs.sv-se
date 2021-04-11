---
title: Kvoter och begränsningar för tjänsten Speech
titleSuffix: Azure Cognitive Services
description: Snabb referens, detaljerad beskrivning och bästa praxis om kvoter och begränsningar för Azure kognitiva tal tjänster
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: alexeyo
ms.openlocfilehash: f851d7999b063a2b1334564902d81343e3789439
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011181"
---
# <a name="speech-service-quotas-and-limits"></a>Kvoter och begränsningar för tjänsten Speech

Den här artikeln innehåller en snabb referens och en **detaljerad beskrivning** av kvoter och begränsningar för Azure kognitiva tal tjänster för alla [pris nivåer](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/). Den innehåller också några metod tips för att undvika begränsning av förfrågningar. 

## <a name="quotas-and-limits-quick-reference"></a>Snabb referens för kvoter och begränsningar
Hoppa till [text till tal-kvoter och begränsningar](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Tal till text-kvoter och -gränser per Speech-resurs
I tabellerna nedan, utan att raden "justerbar" **inte** är justerbar för alla pris nivåer.

#### <a name="online-transcription"></a>Online-avskriftering
För användning med [tal-SDK](speech-sdk.md) och/eller [tal-till-text REST API för kort ljud](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Kvot | Kostnads fri (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Gräns för samtidig begäran – bas modell** | 1 | 100 (standardvärde) |
| Justerbar | Nr<sup>2</sup> | Ja<sup>2</sup> |
| **Gräns för samtidiga förfrågningar – anpassad modell** | 1 | 20 (standardvärde) |
| Justerbar | Nr<sup>2</sup> | Ja<sup>2</sup> |

#### <a name="batch-transcription"></a>Batch-avskrift
| Kvot | Kostnads fri (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| Gräns [för tal till text REST API v 2.0 och v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) | Batch-avskrift är inte tillgängligt för F0 | 300 förfrågningar per minut |
| Max fil storlek för ljud inspelning | Ej tillämpligt | 1 GB |
| Maximal BLOB-storlek för indata (kan innehålla fler än en fil, till exempel i ett zip-arkiv, se till att observera fil storleks begränsningen ovan) | Ej tillämpligt | 2,5 GB |
| Maximal storlek på BLOB-behållare | Ej tillämpligt | 5 GB |
| Högsta antal blobbar per behållare | Ej tillämpligt | 10000 |
| Maximalt antal filer per avskrifts förfrågan (när flera innehålls-URL: er används som inmatade) | Ej tillämpligt | 1000  |
| Maximalt antal jobb som körs samtidigt | Ej tillämpligt | 2000  |

#### <a name="model-customization"></a>Modell anpassning
| Kvot | Kostnads fri (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| REST API gräns | 300 förfrågningar per minut | 300 förfrågningar per minut |
| Högsta antal tal uppsättningar för tal | 2 | 500 |
| Maximal fil storlek för akustiska data uppsättningar för data import | 2 GB | 2 GB |
| Maximal språk data uppsättnings fil storlek för data import | 200 MB | 1,5 GB |
| Maximal data uppsättnings fil storlek för data import | 1 kB | 1 MB |
| Max text storlek när `text` parameter används i [create Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API-begäran | 200 KB | 500 kB |

<sup>1</sup> **kostnads fri (F0)** pris nivå se även månads traktamenten på [sidan med priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>2</sup> se [ytterligare förklaringar](#detailed-description-quota-adjustment-and-best-practices), [metod tips](#general-best-practices-to-mitigate-throttling-during-autoscaling)och [anpassnings anvisningar](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Text till tal-kvoter och gränser per tal resurs
I tabellen nedan, utan att raden "justerbar", **inte** är justerbar för alla pris nivåer.

| Kvot                                                                          | Kostnads fri (F0)<sup>3</sup>  | Standard (S0)   |
|--------------------------------------------------------------------------------|------------------------|-----------------|
| **Högsta antal transaktioner per sekund (TPS) för standard-och neurala-röster** | 200<sup>4</sup>        | 200<sup>4</sup> |
| **Gräns för samtidig begäran för anpassad röst**                                  |                        |                 |
| Standardvärde                                                                  | 10                     | 10              |
| Justerbar                                                                     | Ingen<sup>5</sup>         | Ja<sup>5</sup> |
| **HTTP-/regionsspecifika kvoter**                                                       |                        |                 |
| Maximal ljud längd som produceras per begäran                                          | 10 min                 | 10 min          |
| Maximalt antal distinkta `<voice>` taggar i SSML                                  | 50                     | 50              |
| **WebSocket-/regionsspecifika kvoter**                                                  |                        |                 |
| Maximal ljud längd som produceras per turn                                             | 10 min                 | 10 min          |
| Max storlek för SSML-meddelande per turn                                                 | 64 kB                  | 64 kB           |
| **REST API gräns**                                                             | 20 förfrågningar per minut | 300 förfrågningar per minut |


<sup>3</sup> **kostnads fri (F0)** pris nivå se även månads traktamenten på [sidan med priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).<br/>
<sup>4</sup> se [ytterligare förklaringar](#detailed-description-quota-adjustment-and-best-practices) och [bästa praxis](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> se [ytterligare förklaringar](#detailed-description-quota-adjustment-and-best-practices), [metod tips](#general-best-practices-to-mitigate-throttling-during-autoscaling)och [anpassnings anvisningar](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Detaljerad beskrivning, kvot justering och bästa praxis
Innan du begär en kvot ökning (om tillämpligt) bör du kontrol lera att det är nödvändigt. Tal tjänsten använder autoskalning-teknikerna för att ta med nödvändiga beräknings resurser i läget "på begäran" och samtidigt för att hålla kundens kostnader låga genom att inte upprätthålla en alltför stor maskin varu kapacitet. Varje gång ditt program får svars koden 429 ("för många begär Anden") medan din arbets belastning ligger inom de angivna gränserna (se [kvoter och begränsningar snabb referens](#quotas-and-limits-quick-reference)), är den mest sannolika förklaringen att tjänsten skalar upp till ditt behov och inte når den nödvändiga skalan än, vilket innebär att det inte omedelbart finns tillräckligt med resurser för att hantera begäran. Det här läget är vanligt vis tillfälligt och bör inte vara längre än det senaste.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Allmänna metod tips för att minimera begränsningen vid automatisk skalning
För att minimera problem som rör begränsning (svarskod 429) rekommenderar vi att du använder följande tekniker:
- Implementera logik för omprövning i ditt program
- Undvik skarpa ändringar i arbets belastningen. Öka arbets belastningen gradvis <br/>
*Exempel.* Ditt program använder text till tal och din aktuella arbets belastning är 5 TPS (transaktioner per sekund). Nästa sekund du ökar belastningen till 20 TPS (det vill säga fyra gånger). Tjänsten börjar omedelbart skala upp för att uppfylla den nya belastningen, men förmodligen kommer den inte att kunna göra det inom en sekund, så en del av begär Anden får svars koden 429.   
- Testa olika belastnings öknings mönster
  - Se [exempel på tal-till-text](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Skapa ytterligare tal resurser i samma eller olika regioner och distribuera arbets belastningen mellan dem med hjälp av "Round Robin"-teknik. Detta är särskilt viktigt för **text till tal-TPS (transaktioner per sekund)** , som anges som 200 per tal resurs och inte kan justeras  

I nästa avsnitt beskrivs vissa fall där du justerar kvoterna.<br/>
Hoppa till [text-till-tal. Ökande gräns för samtidig avskrifts förfrågan för anpassad röst](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Tal till text: öka gränsen för avskrift av samtidiga begär Anden
Som standard är antalet samtidiga förfrågningar begränsade till 100 per tal resurs (bas modell) och till 20 per anpassad slut punkt (anpassad modell). För standard pris nivån kan du öka mängden. Innan du skickar in begäran bör du se till att du är bekant med materialet i [det här avsnittet](#detailed-description-quota-adjustment-and-best-practices) och känner till dessa [metod tips](#general-best-practices-to-mitigate-throttling-during-autoscaling).

>[!NOTE]
> Om du använder anpassade modeller bör du vara medveten om att en tal resurs kan associeras med många anpassade slut punkter som är värdar för många anpassade modell distributioner. Varje anpassad slut punkt har standardvärdet för gränsen för samtidiga förfrågningar (20) genom att skapa. Om du behöver justera det måste du göra en justering av varje anpassad slut punkt **separat**. Observera också att värdet för antalet gränser för samtidiga förfrågningar för bas modellen i en tal resurs **inte har någon** inverkan på de anpassade slut punkter som är associerade med resursen.


Att öka gränsen för samtidiga förfrågningar påverkar **inte** dina kostnader direkt. Tal tjänsten använder "betala endast för det du använder"-modellen. Gränsen definierar hur hög tjänsten kan skalas innan den börjar begränsa dina begär Anden.

Gränser för samtidiga förfrågningar för **bas** -och **anpassade** modeller måste justeras **separat**.

Det befintliga värdet för begränsnings parametern för samtidiga förfrågningar är **inte** synligt via Azure Portal, Command-Line-verktyg eller API-begäranden. Du kan kontrol lera det befintliga värdet genom att skapa en support förfrågan för Azure.

>[!NOTE]
>[Tal behållare](speech-container-howto.md) kräver ingen ökning av gränsen för samtidiga förfrågningar, eftersom behållare endast begränsas av CPU: er för den maskin vara som de är värd för. Tal behållare har dock sina egna kapacitets begränsningar som bör tas med i beräkningen. Se frågan *"kan hjälpa dig med kapacitets planering och kostnads uppskattning för lokal-behållare från tal till text?"* från [vanliga frågor och svar om tal behållare](./speech-container-howto.md).

#### <a name="have-the-required-information-ready"></a>Ha nödvändig information som är klar:
- För **bas modell**:
  - Tal resurs-ID
  - Region
- För **anpassad modell**: 
  - Region
  - ID för anpassad slut punkt

- **Så här hämtar du information (bas modell)**:  
  - Gå till [Azure Portal](https://portal.azure.com/)
  - Välj den tal resurs för vilken du vill öka gränsen för samtidiga begär Anden
  - Välj *Egenskaper* (*resurs hanterings* grupp) 
  - Kopiera och spara värdena för följande fält:
    - **Resurs-ID**
    - **Plats** (din slut punkts region)

- **Så här hämtar du information (anpassad modell)**:
  - Gå till [tal Studio](https://speech.microsoft.com/) Portal
  - Logga in vid behov
  - Gå till Custom Speech
  - Välj ditt projekt
  - Gå till *distribution*
  - Välj den nödvändiga slut punkten
  - Kopiera och spara värdena för följande fält:
    - **Tjänste region** (din slut punkts region)
    - **Slut punkts-ID**
  
#### <a name="create-and-submit-support-request"></a>Skapa och skicka support förfrågan
Påbörja ökningen av gränsen för samtidiga förfrågningar för din resurs eller kontrol lera dagens gräns genom att skicka support förfrågan:

- Se till att du har [nödvändig information](#have-the-required-information-ready)
- Gå till [Azure Portal](https://portal.azure.com/)
- Välj den tal resurs för vilken du vill öka (eller kontrol lera) gränsen för samtidiga begär Anden
- Välj *ny supportbegäran* (*support + fel söknings* grupp) 
- Ett nytt fönster visas med automatiskt ifylld information om din Azure-prenumeration och Azure-resurs
- Ange *Sammanfattning* (till exempel "öka STT för samtidighet")
- Välj "kvot-eller prenumerations problem" i *problem typ*
- Under *typ av problem uppstod* väljer du:
  - "Kvot eller samtidiga förfrågningar ökar" – för en begäran om ökad begäran
  - "Kvot-eller användnings validering" för att kontrol lera den befintliga gränsen
- Klicka på *Nästa: lösningar*
- Fortsätt vidare med att skapa begäran
- På fliken *information* anger du i fältet *Beskrivning* :
  - en anteckning om att begäran gäller **tal-till-text-** kvot
  - **Bas** eller **anpassad** modell
  - Azure-resursinformation som du [samlat in tidigare](#have-the-required-information-ready) 
  - Slutför att ange den information som krävs och klicka på knappen *skapa* på fliken *Granska + skapa*
  - Observera support ärende numret i Azure Portal-meddelanden. Du kommer att kontaktas inom kort för vidare bearbetning

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Tal till text: exempel på bästa praxis för arbets belastnings mönster
Det här exemplet visar den metod vi rekommenderar följande för att minimera möjliga begär ande begränsning på grund av att [autoskalning pågår](#detailed-description-quota-adjustment-and-best-practices). Det är inte ett "exakt recept", men bara en mall som vi bjuder in att följa och anpassa vid behov.

Låt oss anta att en tal resurs har en gräns för samtidiga förfrågningar som är inställd på 300. Starta arbets belastningen från 20 samtidiga anslutningar och öka belastningen med 20 samtidiga anslutningar varje 1,5 – 2 minuter. Kontrol lera tjänst svaren och implementera den logik som går tillbaka (minskar belastningen) om du får för många svars koder 429. Försök igen om 1-2-4-4 minut mönster. (Det är ett nytt försök att öka belastningen om 1 minut, om det fortfarande inte fungerar, sedan 2 min och så vidare)

I allmänhet rekommenderar vi starkt att du testar arbets belastningen och arbets belastnings mönstren innan du går vidare till produktion.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Text till tal: öka avskrifts gränsen för samtidiga begär Anden för anpassad röst
Som standard är antalet samtidiga förfrågningar för en anpassad röst slut punkt begränsad till 10. För standard pris nivån kan du öka mängden. Innan du skickar in begäran bör du se till att du är bekant med materialet i [det här avsnittet](#detailed-description-quota-adjustment-and-best-practices) och känner till dessa [metod tips](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Att öka gränsen för samtidiga förfrågningar påverkar **inte** dina kostnader direkt. Tal tjänsten använder "betala endast för det du använder"-modellen. Gränsen definierar hur hög tjänsten kan skalas innan den börjar begränsa dina begär Anden.

Det befintliga värdet för begränsnings parametern för samtidiga förfrågningar är **inte** synligt via Azure Portal, Command-Line-verktyg eller API-begäranden. Du kan kontrol lera det befintliga värdet genom att skapa en support förfrågan för Azure.

>[!NOTE]
>[Tal behållare](speech-container-howto.md) kräver ingen ökning av gränsen för samtidiga förfrågningar, eftersom behållare endast begränsas av CPU: er för den maskin vara som de är värd för.

#### <a name="prepare-the-required-information"></a>Förbered den information som krävs:
Om du vill skapa en begäran om ökning måste du ange din distributions region och det anpassade slut punkts-ID: t. Utför följande åtgärder för att hämta den: 

- Gå till [tal Studio](https://speech.microsoft.com/) Portal
- Logga in vid behov
- Gå till *anpassad röst*
- Välj ditt projekt
- Gå till *distribution*
- Välj den nödvändiga slut punkten
- Kopiera och spara värdena för följande fält:
    - **Tjänste region** (din slut punkts region)
    - **Slut punkts-ID**
  
#### <a name="create-and-submit-support-request"></a>Skapa och skicka support förfrågan
Påbörja ökningen av gränsen för samtidiga förfrågningar för din resurs eller kontrol lera dagens gräns genom att skicka support förfrågan:

- Se till att du har [nödvändig information](#prepare-the-required-information)
- Gå till [Azure Portal](https://portal.azure.com/)
- Välj den tal resurs för vilken du vill öka (eller kontrol lera) gränsen för samtidiga begär Anden
- Välj *ny supportbegäran* (*support + fel söknings* grupp) 
- Ett nytt fönster visas med automatiskt ifylld information om din Azure-prenumeration och Azure-resurs
- Ange *Sammanfattning* (till exempel "öka storleks gränsen för den anpassade slut punkten för tal)
- Välj "kvot-eller prenumerations problem" i *problem typ*
- Under *typ av problem uppstod* väljer du:
  - "Kvot eller samtidiga förfrågningar ökar" – för en begäran om ökad begäran
  - "Kvot-eller användnings validering" för att kontrol lera den befintliga gränsen
- Klicka på *Nästa: lösningar*
- Fortsätt vidare med att skapa begäran
- På fliken *information* anger du i fältet *Beskrivning* :
  - en anteckning om att begäran gäller **text till tal-** kvot
  - Azure-resursinformation som du [samlat in tidigare](#prepare-the-required-information) 
  - Slutför att ange den information som krävs och klicka på knappen *skapa* på fliken *Granska + skapa*
  - Observera support ärende numret i Azure Portal-meddelanden. Du kommer att kontaktas inom kort för vidare bearbetning