---
title: Utvärdera och förbättra Custom Speech precisions tjänsten för tal igenkänning
titleSuffix: Azure Cognitive Services
description: I det här dokumentet får du lära dig att kvantitativt mäta och förbättra kvaliteten på vår tal-till-text-modell eller din anpassade modell. Ljud + mänskligt avskrifts data krävs för att testa precisionen och 30 minuter till 5 timmar representativt ljud bör tillhandahållas.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: b7e4ea586098ea3eb0dfd684650f798d7988e18b
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634591"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Utvärdera och förbättra noggrannhet i Custom Speech

I den här artikeln får du lära dig hur du kvantitativt mäter och förbättrar noggrannheten hos Microsofts tal till text-modeller eller dina egna anpassade modeller. Ljud + mänskligt avskrifts data krävs för att testa precisionen och 30 minuter till 5 timmar representativt ljud bör tillhandahållas.

## <a name="evaluate-custom-speech-accuracy"></a>Utvärdera noggrannhet i Custom Speech

Bransch standarden för att mäta modell precisionen är en [ord Fels frekvens](https://en.wikipedia.org/wiki/Word_error_rate) (WER). WER räknar antalet felaktiga ord som identifierats under igenkänning och dividerar sedan med det totala antalet ord som anges i den märkta avskriften (visas nedan som N). Slutligen multipliceras talet med 100% för att beräkna WER.

![WER-formel](./media/custom-speech/custom-speech-wer-formula.png)

Felaktigt identifierade ord hamnar i tre kategorier:

* Infogning (I): ord som har lagts till felaktigt i hypotes avskrift
* Borttagning (D): ord som inte kan identifieras i hypotes avskriften
* Ersättning (er): ord som ersatts mellan referens och hypotes

Här är ett exempel:

![Exempel på felaktigt identifierade ord](./media/custom-speech/custom-speech-dis-words.png)

Om du vill replikera WER-mätningar lokalt kan du använda sclite från [SCTK](https://github.com/usnistgov/SCTK).

## <a name="resolve-errors-and-improve-wer"></a>Lös fel och förbättra WER

Du kan använda WER från dator igenkännings resultat för att utvärdera kvaliteten på den modell som du använder med din app, ditt verktyg eller din produkt. En WER med 5%-10% anses vara av bra kvalitet och är redo att användas. En WER med 20% är acceptabelt, men du kanske vill överväga ytterligare utbildning. En WER av 30% eller mer signalerar dålig kvalitet och kräver anpassning och utbildning.

Hur felen distribueras är viktigt. När många borttagnings fel påträffas beror det vanligt vis på svag ljud Signals styrka. För att lösa det här problemet måste du samla in ljud data närmare källan. Infognings fel innebär att ljudet registrerades i en miljö med störningar och att Crosstalk kan finnas, vilket orsakar igenkännings problem. Ersättnings fel uppstår ofta när ett otillräckligt exempel på domänautentiseringsuppgifter har angetts som antingen mänskligt avskrifter eller relaterad text.

Genom att analysera enskilda filer kan du bestämma vilken typ av fel som finns och vilka fel som är unika för en viss fil. Att förstå problem på filnivå hjälper dig att fokusera på förbättringar.

## <a name="create-a-test"></a>Skapa ett test

Om du vill testa kvaliteten på Microsofts bas linje modell för tal till text eller en anpassad modell som du har tränat, kan du jämföra två modeller sida vid sida för att utvärdera noggrannheten. Jämförelsen innehåller WER-och igenkännings resultat. Normalt jämförs en anpassad modell med Microsofts bas linje modell.

Utvärdera modeller sida vid sida:

1. Logga in på [Custom Speech Portal](https://speech.microsoft.com/customspeech).
2. Navigera till **> för tal till text Custom Speech > [namn på projektet] > testning**.
3. Klicka på **Lägg till test**.
4. Välj **utvärdera noggrannhet**. Ge testet ett namn, en beskrivning och välj din ljud-eller data uppsättning med mänskligt märkt skrift.
5. Välj upp till två modeller som du vill testa.
6. Klicka på **Skapa**.

När testet har skapats kan du jämföra resultatet sida vid sida.

### <a name="side-by-side-comparison"></a>Jämförelse sida vid sida

När testet har slutförts, som anges av status ändringen till *lyckades*, hittar du ett WER-nummer för båda modellerna i testet. Klicka på test namnet för att Visa test informations sidan. Den här informations sidan visar en lista över alla yttranden i din data uppsättning, vilket anger tolknings resultatet för de två modellerna jämte avskriften från den skickade data mängden. För att kunna kontrol lera jämförelsen sida vid sida kan du växla mellan olika typer av fel, inklusive infogning, borttagning och ersättning. Genom att lyssna på ljudet och jämföra igenkännings resultaten i varje kolumn, som visar den mänskligt avskriften och resultaten för två tal till text-modeller, kan du bestämma vilken modell som uppfyller dina behov och var ytterligare utbildning och förbättringar krävs.

## <a name="improve-custom-speech-accuracy"></a>Förbättra noggrannhet i Custom Speech

Tal igenkännings scenarier varierar beroende på ljud kvalitet och språk (vokabulär-och tal format). Följande tabell undersöker fyra vanliga scenarier:

| Scenario | Ljud kvalitet | Vokabulär | Tal format |
|----------|---------------|------------|----------------|
| Kundtjänst | Låg, 8 kHz kan vara 2 människor på 1 ljud kanal, kan komprimeras | Begränsa, unikt för domän och produkter | Konversation, löst strukturerat |
| Röst assistent (till exempel Cortana eller ett enhets fönster) | Hög, 16 kHz | Enhet tung (sång titlar, produkter, platser) | Tydligt angivna ord och fraser |
| Diktering (snabb meddelande, anteckningar, sökning) | Hög, 16 kHz | Anpassa | Antecknings bearbetning |
| Textning för video dold | Varierande, inklusive varierande mikrofon användning, tillagd musik | Varierande, från möten, omciterat tal, musik texter | Läsa, beredd eller löst strukturerad |

Olika scenarier ger olika kvalitets resultat. I följande tabell kontrol leras hur innehåll från dessa fyra scenarier i [fel frekvensen i Word (WER)](how-to-custom-speech-evaluate-data.md). Tabellen visar vilka fel typer som är mest vanliga i varje scenario.

| Scenario | Tal igenkännings kvalitet | Infognings fel | Fel vid borttagning | Ersättnings fel |
|----------|----------------------------|------------------|-----------------|---------------------|
| Kundtjänst | Medel (< 30% WER) | Låg, förutom när andra människor pratar i bakgrunden | Kan vara hög. Call Center kan vara störningar och överlappande högtalare kan förvirra modellen | Medium. Produkter och person namn kan orsaka dessa fel |
| Röstassistent | Hög (kan vara < 10% WER) | Låg | Låg | Medel, på grund av sång titlar, produkt namn eller platser |
| Diktering | Hög (kan vara < 10% WER) | Låg | Låg | Högt |
| Textning för video dold | Är beroende av video typ (kan vara < 50% WER) | Låg | Kan vara högt på grund av musik, brus, mikrofon kvalitet | Jargong kan orsaka dessa fel |

Genom att fastställa komponenterna i WER (antal fel vid infogning, borttagning och ersättning) kan du avgöra vilken typ av data som ska läggas till för att förbättra modellen. Använd [Custom Speech Portal](https://speech.microsoft.com/customspeech) för att visa kvaliteten på en bas linje modell. Portalen rapporterar infognings-, ersättnings-och borttagnings frekvenser som kombineras i rapporten för WER-kvalitet.

## <a name="improve-model-recognition"></a>Förbättra modell igenkänning

Du kan minska igenkännings felen genom att lägga till tränings data i [Custom Speech portalen](https://speech.microsoft.com/customspeech). 

Planera för att underhålla din anpassade modell genom att regelbundet lägga till käll material. Din anpassade modell behöver ytterligare utbildning för att hålla koll på ändringar i dina entiteter. Du kan till exempel behöva uppdateringar av produkt namn, låt namn eller nya tjänst platser.

I följande avsnitt beskrivs hur varje typ av ytterligare tränings data kan minska antalet fel.

### <a name="add-related-text-sentences"></a>Lägg till relaterade text meningar

När du tränar en ny anpassad modell börjar du med att lägga till relaterad text för att förbättra erkännandet av domänanslutna ord och fraser. Relaterade text meningar kan främst minska ersättnings fel som är relaterade till fel igenkänning av vanliga ord och företagsspecifika ord genom att visa dem i sammanhang. Domänautentiseringsuppgifter kan vara ovanliga eller uppdelade ord, men deras uttal måste vara enkla att känna igen.

> [!NOTE]
> Undvik relaterade text meningar som innehåller brus, till exempel okända tecken eller ord.

### <a name="add-audio-with-human-labeled-transcripts"></a>Lägg till ljud med medmärkta avskrifter

Ljud med medmärkta avskrifter ger störst precisions förbättringar om ljudet kommer från mål användnings fallet. Exempel måste omfatta det fullständiga tal omfånget. Till exempel skulle ett Call Center för en detalj handels butik få flest samtal om swimwear och sol glasögon under sommaren. Se till att exemplet innehåller det fullständiga omfånget som du vill identifiera.

Överväg följande information:

* Utbildning med ljud ger flest fördelar om ljudet också är svårt att förstå för människor. I de flesta fall bör du börja träna genom att bara använda relaterad text.
* Om du använder ett av de mest använda språken, till exempel amerikansk engelska, så kan det vara en chans att det inte finns något behov av att träna med ljuddata. För sådana språk erbjuder bas modellerna redan bra igenkännings resultat i de flesta fall. Det är förmodligen tillräckligt för att träna med relaterad text.
* Custom Speech kan bara avbilda ord kontext för att minska ersättnings fel, inte infoga eller ta bort fel.
* Undvik exempel som innehåller avskrifts fel, men ta med en mångfald av ljud kvaliteten.
* Undvik meningar som inte är relaterade till problem domänen. Orelaterade meningar kan skada din modell.
* När kvaliteten på avskrifter varierar kan du duplicera undantagna meningar (t. ex. utmärkta avskrifter som innehåller viktiga fraser) för att öka deras vikt.
* Tal tjänsten använder automatiskt avskrifterna för att förbättra erkännandet av domänanslutna ord och fraser som om de har lagts till som relaterad text.
* Det kan ta flera dagar för en utbildnings åtgärd att slutföras. För att förbättra utbildningens hastighet måste du skapa en prenumeration på din röst tjänst i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.

> [!NOTE]
> Det är inte alla bas modeller som stöder utbildning med ljud. Om en bas modell inte stöder den, kommer tal tjänsten endast använda texten från avskrifterna och ignorera ljudet. Se [språk stöd](language-support.md#speech-to-text) för en lista över bas modeller som stöder utbildning med ljud data. Även om en bas modell stöder utbildning med ljuddata, kan tjänsten endast använda en del av ljudet. Fortfarande kommer den att använda alla avskrifter.

> [!NOTE]
> I fall när du ändrar bas modellen som används för utbildning och du har ljud i träning-datauppsättningen, kontrollerar du *alltid* om den nya valda bas modellen [stöder utbildning med ljuddata](language-support.md#speech-to-text). Om den tidigare använda bas modellen inte har stöd för utbildning med ljuddata, och hierarkin data uppsättning innehåller ljud, ökar inlärnings tiden med den nya bas modellen **drastiskt** , och det kan enkelt gå från flera timmar till flera dagar. Detta gäller särskilt om din röst tjänst prenumeration **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.
>
> Om du möter problemet som beskrivs i stycket ovan kan du snabbt minska inlärnings tiden genom att minska mängden ljud i data uppsättningen eller ta bort det helt och hållet texten kvar. Det sistnämnda alternativet är starkt rekommenderat om din prenumeration på röst tjänsten **inte** finns i en [region med den dedikerade maskin varan](custom-speech-overview.md#set-up-your-azure-account) för utbildning.

### <a name="add-new-words-with-pronunciation"></a>Lägg till nya ord med uttal

Ord som har skapats eller är mycket specialiserade kan ha unika uttal. Dessa ord kan identifieras om ordet kan brytas ned i mindre ord för att uttala det. Om du till exempel vill känna igen **Xbox**, uttala som **X Box**. Den här metoden kommer inte att öka den övergripande noggrannheten, men kan öka igenkänningen av dessa nyckelord.

> [!NOTE]
> Den här tekniken är bara tillgänglig för vissa språk för tillfället. Mer information finns i avsnittet anpassning av uttal i [tal-till-text-tabellen](language-support.md) .

## <a name="sources-by-scenario"></a>Källor efter scenario

I följande tabell visas scenarier för röst igenkänning och en lista över käll material som du bör tänka på i de tre kategorierna för utbildnings innehåll som anges ovan.

| Scenario | Tillhör ande text meningar | Ljud + medmärkta avskrifter | Nya ord med uttal |
|----------|------------------------|------------------------------|------------------------------|
| Kundtjänst             | marknadsförings dokument, webbplats, produkt granskningar relaterade till Call Center-aktivitet | Call Center-anrop som har tilldelats av människa | termer som har tvetydiga uttal (se Xbox ovan) |
| Röstassistent         | lista meningar med alla kombinationer av kommandon och entiteter | spela in röster genom att tala om kommandon i enheten och skriva in text | namn (filmer, låtar, produkter) som har unika uttal |
| Diktering               | skrivna ingångar, t. ex. snabb meddelanden eller e-post | liknar ovan | liknar ovan |
| Textning för video dold | TV Visa skript, filmer, marknadsförings innehåll, video sammanfattningar | exakta avskrifter av videor | liknar ovan |

## <a name="next-steps"></a>Nästa steg

* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)