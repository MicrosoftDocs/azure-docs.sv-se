---
title: Vanliga frågor och svar om tal till text
titleSuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om tal till text-tjänsten.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: 21924ad5da8833ca5cf8373270ed1bfd3facfdc9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388625"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Vanliga frågor och svar om tal till text

Om du inte kan hitta svar på dina frågor i dessa vanliga frågor och svar kan du titta närmare på [andra support alternativ](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Allmänt

**F: Vad är skillnaden mellan en bas linje modell och en anpassad tal till text modellen?**

**A**: en bas linje modell har tränats med hjälp av Microsoft-ägda data och har redan distribuerats i molnet. Du kan använda en anpassad modell för att anpassa en modell så att den bättre passar en specifik miljö som har ett speciellt omgivnings brus eller språk. I fabriks golv, bilar eller störningar i gator krävs en anpassad akustisk modell. Ämnen som biologi, fysik, Radiology, produkt namn och anpassade akronymer kräver en anpassad språk modell. Om du tränar en anpassad modell bör du börja med relaterad text för att förbättra igenkänningen av särskilda termer och fraser.

**F: Hur börjar jag om jag vill använda en bas linje modell?**

**A**: börja med att hämta en [prenumerations nyckel](overview.md#try-the-speech-service-for-free). Om du vill göra REST-anrop till de fördistribuerade bas linje modellerna kan du läsa mer i [REST-API: erna](./overview.md#reference-docs). Om du vill använda WebSockets laddar du [ned SDK: n](speech-sdk.md).

**F: behöver jag alltid skapa en anpassad tal modell?**

**A**: Nej. Om programmet använder generisk, dagligt språk behöver du inte anpassa en modell. Om programmet används i en miljö där det inte finns något eller något bakgrunds brus behöver du inte anpassa en modell.

Du kan distribuera bas linje och anpassade modeller i portalen och sedan köra test av precisions testning. Du kan använda den här funktionen för att mäta precisionen för en bas linje modell jämfört med en anpassad modell.

**F: Hur vet jag när bearbetningen av data uppsättningen eller modellen är slutförd?**

**A**: för närvarande är status för modellen eller data uppsättningen i tabellen det enda sättet att känna till. När bearbetningen är **klar har statusen slutförts.**

**F: kan jag skapa fler än en modell?**

S **: det** finns ingen gräns för antalet modeller som du kan ha i din samling.

**F: Jag har gjort ett misstag. Hur gör jag för att vill du avbryta data import eller skapande av modell som pågår?**

**A**: för närvarande kan du inte återställa en akustisk eller språk anpassnings process. Du kan ta bort importerade data och modeller när de är i ett Terminal-tillstånd.

**F: Jag får flera resultat för varje fras med det detaljerade utdataformatet. Vilken av dem ska jag använda?**

**A**: Ta alltid det första resultatet, även om ett annat resultat ("N-Best") kan ha ett högre konfidens värde. Tal tjänsten betraktar det första resultatet som bäst. Det kan också vara en tom sträng om inget tal identifierats.

De andra resultaten är förmodligen sämre och har kanske inte full användning av versaler och skiljetecken. De här resultaten är mest användbara i särskilda scenarier, till exempel att ge användarna möjlighet att välja korrigeringar från en lista eller hantera felaktigt identifierade kommandon.

**F: Varför finns det olika bas modeller?**

**A**: du kan välja bland fler än en bas modell i tal tjänsten. Varje modell namn innehåller det datum då det lades till. När du börjar träna en anpassad modell använder du den senaste modellen för att få bästa möjliga noggrannhet. Äldre bas modeller är fortfarande tillgängliga under en tid när en ny modell görs tillgänglig. Du kan fortsätta att använda modellen som du har arbetat med tills den dras tillbaka (se [modellens livs cykel](custom-speech-overview.md#model-lifecycle)). Vi rekommenderar fortfarande att du växlar till den senaste bas modellen för bättre noggrannhet.

**F: kan jag uppdatera min befintliga modell (modell stackning)?**

**A**: du kan inte uppdatera en befintlig modell. Som en lösning kombinerar du den gamla data uppsättningen med den nya data uppsättningen och omanpassning.

Den gamla data uppsättningen och den nya data uppsättningen måste kombineras i en enda. zip-fil (för akustiska data) eller i en txt-fil (för språk data). När anpassningen är färdig måste den nya, uppdaterade modellen omdistribueras för att få en ny slut punkt

**F: när en ny version av en bas modell är tillgänglig uppdateras min distribution automatiskt?**

**A**: distributioner kommer inte att uppdateras automatiskt.

Om du har anpassat och distribuerat en modell förblir den distributionen. Du kan inaktivera den distribuerade modellen, omanpassa med den nya versionen av bas modellen och omdistribuera för att få bättre noggrannhet.

Både bas modeller och anpassade modeller kommer att dras tillbaka efter en stund (se [modellens livs cykel](custom-speech-overview.md#model-lifecycle)).

**F: kan jag hämta min modell och köra den lokalt?**

**A**: du kan köra en anpassad modell lokalt i en [Docker-behållare](speech-container-howto.md?tabs=cstt).

**F: kan jag kopiera eller flytta mina data uppsättningar, modeller och distributioner till en annan region eller prenumeration?**

**A**: du kan använda [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) för att kopiera en anpassad modell till en annan region eller prenumeration. Det går inte att kopiera data uppsättningar eller distributioner. Du kan importera en data uppsättning igen i en annan prenumeration och skapa slut punkter där modell kopiorna används.

**F: loggas mina förfrågningar?**

**A**: som standard loggas inte (varken ljud eller avskrifter). Om det behövs kan du välja *logg innehåll från det här slut punkts* alternativet när du [skapar en anpassad slut punkt](how-to-custom-speech-train-model.md#deploy-a-custom-model). Du kan också aktivera ljud loggning i [talet SDK](how-to-use-logging.md) för varje begäran utan att skapa en anpassad slut punkt. I båda fallen lagras ljud-och igenkännings resultat från begär anden i säkert lagrings utrymme. För prenumerationer som använder Microsoft-ägda lagrings enheter är de tillgängliga i 30 dagar.

Du kan exportera de loggade filerna på sidan distribution i tal Studio om du använder en anpassad slut punkt med *logg innehåll från den här slut punkten* aktive rad. Om ljud loggning har Aktiver ATS via SDK anropar du [API: et](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) för att komma åt filerna.

**F: är mina förfrågningar begränsade?**

**A**: se [kvoter och begränsningar för tal tjänster](speech-services-quotas-and-limits.md).

**F: Hur debiteras jag för ljud med dubbla kanaler?**

**A**: om du skickar varje kanal separat (varje kanal i en egen fil) debiteras du för varje fils varaktighet. Om du skickar en enskild fil med varje kanal som är multiplexad tillsammans debiteras du för den enskilda filens varaktighet. Mer information om priser finns på sidan med [priser för Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Om du har ytterligare sekretess problem som hindrar dig från att använda tjänsten för anpassad röst kontaktar du en av support kanalerna.

## <a name="increasing-concurrency"></a>Ökande samtidighet
Se [kvoter och begränsningar för tal tjänster](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importera data

**F: Vad är gränsen för en data uppsättnings storlek och varför är den begränsad?**

**A**: gränsen beror på begränsningen av storleken på en fil för http-uppladdning. Se [kvoter för tal tjänster och begränsningar](speech-services-quotas-and-limits.md) för den faktiska gränsen. Du kan dela upp data i flera data uppsättningar och välja alla för att träna modellen.

**F: kan jag zippa upp mina textfiler så att jag kan ladda upp en större textfil?**

**A**: Nej. För närvarande tillåts endast okomprimerade textfiler.

**F: data rapporten säger att det inte gick att yttranden. Vad är problemet?**

**A**: det går inte att ladda upp 100 procent av yttranden i en fil. Om den stora delen av yttranden i en akustisk eller språk data uppsättning (till exempel mer än 95 procent) har importer ATS kan data uppsättningen användas. Vi rekommenderar dock att du försöker förstå varför yttranden misslyckades och korrigerar problemen. De vanligaste problemen, till exempel formateringsfel, är enkla att åtgärda.

## <a name="creating-an-acoustic-model"></a>Skapa en akustisk modell

**F: hur mycket akustiska data behöver jag?**

**A**: Vi rekommenderar att du börjar med mellan 30 minuter och en timmes akustiska data.

**F: vilka data ska jag samla in?**

S **: samla** in data som är så nära program scenariot och användnings fallet som möjligt. Data insamlingen bör matcha mål programmet och användare vad gäller enheter, enheter, miljöer och typer av högtalare. I allmänhet bör du samla in data från så många olika högtalare som möjligt.

**F: Hur ska jag samla in akustiska data?**

**A**: du kan skapa ett fristående data insamlings program eller använda ljud inspelnings program från andra program. Du kan också skapa en version av programmet som loggar ljuddata och sedan använder dessa data.

**F: behöver jag skriva av anpassnings data själv?**

**A**: Ja. Du kan skriva av den själv eller använda en professionell avskrifts tjänst. Vissa användare föredrar professionella scheman och andra använder gemensamt skapade eller utför avskrifterna själva.

**F: hur lång tid tar det att träna en anpassad modell med ljud data?**

**A**: träna en modell med ljuddata kan vara en långvarig process. Beroende på mängden data kan det ta flera dagar att skapa en anpassad modell. Om den inte kan slutföras inom en vecka kan tjänsten avbryta inlärnings åtgärden och rapportera modellen som misslyckad.

Använd en av [regionerna](custom-speech-overview.md#set-up-your-azure-account) där dedikerad maskin vara är tillgänglig för utbildning för snabbare resultat. I allmänhet bearbetar tjänsten cirka 10 timmar ljud data per dag i regioner med sådan maskin vara. Det kan bara bearbeta ungefär 1 timme ljud data per dag i andra regioner. Du kan kopiera den helt utbildade modellen till en annan region med hjälp av [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Utbildning med enbart text är mycket snabbare och slutförs vanligt vis inom några minuter.

Vissa bas modeller kan inte anpassas med ljud data. För dem använder tjänsten bara texten i avskriften för utbildning och ignorerar ljuddata. Träningen kommer att bli mycket snabbare och resultatet är detsamma som utbildning med enbart text. Se [språk stöd](language-support.md#speech-to-text) för en lista över bas modeller som stöder utbildning med ljud data.

## <a name="accuracy-testing"></a>Precisions testning

**F: Vad är en fel frekvens i Word (WER) och hur beräknas det?**

**A**: WER är utvärderings måttet för tal igenkänning. WER räknas som det totala antalet fel, inklusive infogningar, borttagningar och ersättningar, dividerat med det totala antalet ord i referens avskriften. Mer information finns i [utvärdera Custom Speech noggrannhet](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**F: Hur gör jag för att avgöra om resultatet av ett precisions test är lämpligt?**

**A**: resultatet visar en jämförelse mellan bas linje modellen och den modell som du har anpassat. Du bör sträva efter att ändra bas linje modellen för att göra anpassningar.

**F: Hur gör jag för att ta reda på WER för en bas modell så att jag kan se om det har förbättrats?**

**A**: de offline-test resultaten visar bas linje noggrannheten för den anpassade modellen och förbättringen över bas linjen.

## <a name="creating-a-language-model"></a>Skapa en språk modell

**F: hur mycket text data behöver jag ladda upp?**

**A**: det beror på hur olika vokabulär och fraser som används i ditt program kommer från start språks modellerna. För alla nya ord är det praktiskt att ange så många exempel som möjligt för användningen av dessa ord. För vanliga fraser som används i ditt program, inklusive fraser i språk data, är det också användbart eftersom det instruerar systemet att även lyssna efter de här villkoren. Det är vanligt att ha minst 100 och ofta flera hundra eller fler yttranden i språk data uppsättningen. Även om vissa typer av frågor förväntas vara vanligare än andra kan du infoga flera kopior av de vanliga frågorna i data uppsättningen.

**F: kan jag bara ladda upp en lista med ord?**

**A**: när en lista med ord laddas upp läggs orden till i ord listan, men det visar inte hur orden används vanligt vis. Genom att tillhandahålla fullständig eller partiell yttranden (meningar eller fraser av saker som användare sannolikt kommer att säga) kan språk modellen lära sig de nya orden och hur de används. Den anpassade språk modellen är inte bara till för att lägga till nya ord i systemet, utan även för att justera sannolikheten för kända ord för ditt program. Att tillhandahålla fullständig yttranden hjälper systemet att lära sig bättre.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Klient organisations modell (Custom Speech med Microsoft 365 data)

**F: vilken information ingår i klient modellen och hur skapas den?**

**A:** En klient modell skapas med e-postmeddelanden och dokument i [offentliga grupper](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) som kan ses av alla i din organisation.

**F: vilka tal upplevelser förbättras av klient organisations modellen?**

**A:** När klient organisations modellen är aktive rad, skapas och publiceras, används den för att förbättra igenkänningen för alla företags program som skapats med tal tjänsten. Det innebär också att du skickar en Azure AD-token som anger medlemskap i företaget.

De tal upplevelser som är inbyggda i Microsoft 365, till exempel Diktering och PowerPoint-textning, ändras inte när du skapar en klient modell för dina tal tjänst program.

## <a name="next-steps"></a>Nästa steg

- [Felsökning](troubleshooting.md)
- [Viktig information](releasenotes.md)