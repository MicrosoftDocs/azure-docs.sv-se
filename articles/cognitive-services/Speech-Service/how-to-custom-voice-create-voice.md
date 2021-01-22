---
title: Skapa en anpassad röst tal tjänst
titleSuffix: Azure Cognitive Services
description: När du är redo att ladda upp dina data går du till den anpassade röst portalen. Skapa eller Välj ett anpassat röst projekt. Projektet måste dela rätt språk och nationella inställningar och köns egenskaper som de data som du tänker använda för din röst träning.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: b59d9ebf55f7a4c02891a782b7271eec2f521576
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663289"
---
# <a name="create-a-custom-voice"></a>Skapa en anpassad röst

I [förbereda data för anpassad röst](how-to-custom-voice-prepare-data.md)beskrev vi de olika data typer som du kan använda för att träna en anpassad röst och de olika format kraven. När du har för berett dina data kan du börja överföra dem till den [anpassade röst portalen](https://aka.ms/custom-voice-portal)eller via det anpassade API: et för röst träning. Här beskriver vi stegen för att träna en anpassad röst via portalen.

> [!NOTE]
> På den här sidan förutsätter vi att du har läst [Kom igång med anpassad röst](how-to-custom-voice.md) och [förbereder data för anpassad röst](how-to-custom-voice-prepare-data.md)och har skapat ett anpassat röst projekt.

Kontrol lera språk som stöds för anpassad röst: [språk för anpassning](language-support.md#customization).

## <a name="upload-your-datasets"></a>Ladda upp dina data uppsättningar

När du är redo att ladda upp dina data går du till den [anpassade röst portalen](https://aka.ms/custom-voice-portal). Skapa eller Välj ett anpassat röst projekt. Projektet måste dela rätt språk och nationella inställningar och köns egenskaper som de data som du tänker använda för din röst träning. Välj till exempel `en-GB` om ljud inspelningarna du har gjort på engelska med en brittisk accent.

Gå till fliken **data** och klicka på **överför data**. I guiden väljer du rätt datatyp som matchar vad du har för berett.

Varje data uppsättning som du överför måste uppfylla kraven för den datatyp som du väljer. Det är viktigt att du formaterar dina data korrekt innan de överförs. Detta säkerställer att data bearbetas korrekt av den anpassade röst tjänsten. Fortsätt genom att [förbereda data för anpassad röst](how-to-custom-voice-prepare-data.md) och se till att dina data har formaterats korrekt.

> [!NOTE]
> F0-användare (kostnads fri prenumeration) kan ladda upp två data uppsättningar samtidigt. S0-användare (standard prenumeration) kan ladda upp fem data uppsättningar samtidigt. Om du når gränsen väntar du tills minst en av dina data uppsättningar är klar med importen. Försök sedan igen.

> [!NOTE]
> Det maximala antalet data uppsättningar som får importeras per prenumeration är 10. zip-filer för kostnads fria prenumerationer (F0) och 500 för S0-användare (standard prenumeration).

Data uppsättningar verifieras automatiskt när du trycker på knappen Överför. Data verifieringen innehåller en serie kontroller för ljudfilerna för att kontrol lera fil format, storlek och samplings frekvens. Åtgärda felen om det finns några och skicka igen. När begäran om data import har initierats bör du se en post i data tabellen som motsvarar den data uppsättning som du precis har laddat upp.

Följande tabell visar bearbetnings tillstånd för importerade data uppsättningar:

| Stat | Innebörd |
| ----- | ------- |
| Bearbetar | Din data uppsättning har tagits emot och bearbetas. |
| Lyckades | Din data uppsättning har verifierats och kan nu användas för att bygga en röst modell. |
| Misslyckad | Data uppsättningen har misslyckats under bearbetningen på grund av många orsaker, till exempel filfel, data problem eller nätverks problem. |

När verifieringen är klar kan du se det totala antalet matchade yttranden för varje data uppsättning i kolumnen **yttranden** . Om den datatyp som du har valt kräver en tids segmentering, motsvarar den här kolumnen bara den yttranden vi har segmenterat för dig antingen baserat på dina avskrifter eller genom tal avskrifts tjänsten. Du kan hämta data uppsättningen ytterligare om du vill visa detaljerad information om hur yttranden har importer ATS och deras mappnings avskrifter. Tips: lång ljud segmentering kan ta mer än en timme att slutföra data bearbetningen.

För en-US-och zh-CN-datauppsättningar kan du ytterligare hämta en rapport för att kontrol lera uttal-poängen och ljud nivån för var och en av dina inspelningar. Uttal-poängen sträcker sig från 0 till 100. Poängen under 70 indikerar normalt ett tal fel eller felaktig matchning av skript. En tung accent kan minska uttal-poängen och påverka den genererade digitala rösten.

En högre signal-till-brus-förhållande (SNR) indikerar lägre brus i ljudet. Du kan normalt komma åt ett 50 + SNR genom att spela in i Professional Studios. Ljud med ett SNR under 20 kan resultera i uppenbar brus i den genererade rösten.

Överväg att registrera om alla yttranden med låga uttal-resultat eller dåliga signal-till-brus-förhållande. Om du inte kan spela in igen kan du undanta dessa yttranden från din data uppsättning.

## <a name="build-your-custom-voice-model"></a>Bygg din anpassade röst modell

När din data uppsättning har verifierats kan du använda den för att bygga din anpassade röst modell.

1.  Navigera till **text till tal > anpassad röst > [namn på projektet] > träning**.

2.  Klicka på **träna modell**.

3.  Ange sedan ett **namn** och en **Beskrivning** som hjälper dig att identifiera den här modellen.

    Välj ett namn noggrant. Namnet som du anger här är det namn som du använder för att ange rösten i din begäran om tal syntes som en del av SSML-indatamängden. Endast bokstäver, siffror och några interpunktionstecken, till exempel-, och \_ (,) är tillåtna. Använd olika namn för olika röst modeller.

    En vanlig användning av fältet **Beskrivning** är att registrera namnen på de data uppsättningar som användes för att skapa modellen.

4.  På sidan **Välj tränings data** väljer du en eller flera data uppsättningar som du vill använda för utbildning. Kontrol lera antalet yttranden innan du skickar dem. Du kan börja med ett valfritt antal yttranden för en-US-och zh-CN röst modell. För andra språk måste du välja fler än 2 000 yttranden för att kunna träna en röst.

    > [!NOTE]
    > Dubbla ljud namn tas bort från utbildningen. Se till att de data uppsättningar du väljer inte innehåller samma ljud namn i flera. zip-filer.

    > [!TIP]
    > Att använda data uppsättningarna från samma talare krävs för kvalitets resultat. När data uppsättningarna som du har skickat för utbildning innehåller ett totalt antal mindre än 6 000 distinkta yttranden, tränar du din röst modell genom den statistiska parameter syntes tekniken. Om dina utbildnings data överstiger ett totalt antal 6 000 distinkta yttranden, kommer du att starta en tränings process med sammanslagnings tekniken. Normalt kan sammanfognings tekniken resultera i mer naturliga och högre röst resultat. [Kontakta det anpassade röst teamet](https://go.microsoft.com/fwlink/?linkid=2108737) om du vill träna en modell med den senaste neurala TTS-tekniken som kan producera en digital röst motsvarighet till de offentligt tillgängliga [neurala-rösterna](language-support.md#neural-voices).

5.  Klicka på **träna** för att börja skapa din röst modell.

I övnings tabellen visas en ny post som motsvarar den nyligen skapade modellen. Tabellen visar också status: bearbetning, lyckades, misslyckades.

Den status som visas visar processen för att konvertera data uppsättningen till en röst modell, som visas här.

| Stat | Innebörd |
| ----- | ------- |
| Bearbetar | Din röst modell skapas. |
| Lyckades | Din röst modell har skapats och kan distribueras. |
| Misslyckad | Din röst modell har misslyckats i utbildningen på grund av många orsaker, till exempel osett data problem eller nätverks problem. |

Inlärnings tiden varierar beroende på mängden ljud data som bearbetas. Vanliga tider sträcker sig från cirka 30 minuter för hundratals yttranden till 40 timmar för 20 000 yttranden. När modell utbildningen har slutförts kan du börja testa den.

> [!NOTE]
> F0-användare (kostnads fri prenumeration) kan träna ett röst tecken samtidigt. S0-användare (standard prenumeration) kan träna tre röster samtidigt. Om du når gränsen väntar du tills minst en av dina röst teckensnitt har slutfört utbildningen och försöker sedan igen.

> [!NOTE]
> Det maximala antalet röst modeller som får tränas per prenumeration är 10 modeller för F0-användare och 100-användare (standard prenumeration).

Om du använder neurala röst träning kan du välja att träna en modell som är optimerad för strömnings scenarier i real tid, eller en HD neurala-modell som är optimerad för asynkrona [långa ljud syntes](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testa din röst modell

När röst teckensnittet har skapats kan du testa det innan du distribuerar det för användning.

1.  Navigera till **text till tal > anpassad röst > [namn på projektet] > testning**.

2.  Klicka på **Lägg till test**.

3.  Välj en eller flera modeller som du vill testa.

4.  Ange den text som du vill att rösten eller rösten ska tala. Om du har valt att testa flera modeller samtidigt, används samma text för testningen för olika modeller.

    > [!NOTE]
    > Språket för din text måste vara detsamma som språket för röst teckensnittet. Endast framgångs bara tränade modeller kan testas. Endast oformaterad text stöds i det här steget.

5.  Klicka på **Skapa**.

När du har skickat in din testbegäran kommer du tillbaka till test sidan. Tabellen innehåller nu en post som motsvarar din nya begäran och kolumnen Status. Det kan ta några minuter att syntetisera talet. När kolumnen Status är **klar** kan du spela upp ljudet eller ladda ned text inmatningen (en. txt-fil) och ljud utmatningen (en. wav-fil) och ytterligare audition för kvalitet.

Du kan också hitta test resultaten på sidan information i varje modell som du har valt för testning. Gå till fliken **utbildning** och klicka på modell namnet för att ange modell informations sidan.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Skapa och Använd en anpassad röst slut punkt

När du har skapat och testat din röst modell distribuerar du den i en anpassad text till tal-slutpunkt. Du använder sedan den här slut punkten i stället för den vanliga slut punkten när du gör text till tal-begäran via REST API. Din anpassade slut punkt kan bara anropas av den prenumeration som du har använt för att distribuera teckensnittet.

Om du vill skapa en ny anpassad röst slut punkt går du till **text-till-tal > anpassad röst > distribution**. Välj **Lägg till slut punkt** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj sedan den anpassade röst modell som du vill koppla till den här slut punkten.

När du har klickat på knappen **Lägg till** i tabellen slut punkt visas en post för den nya slut punkten. Det kan ta några minuter att instansiera en ny slut punkt. När statusen för distributionen har **slutförts** är slut punkten klar att användas.

> [!NOTE]
> F0-användare kan bara ha en modell distribuerad. S0-användare (standard prenumeration) kan skapa upp till 50 slut punkter, var och en med sin egen anpassade röst.

> [!NOTE]
> Om du vill använda din anpassade röst måste du ange namnet på röst modellen, använda den anpassade URI: n direkt i en HTTP-begäran och använda samma prenumeration för att gå igenom autentiseringen av TTS-tjänsten.

När slut punkten har distribuerats visas slut punktens namn som en länk. Klicka på länken om du vill visa information som är speciell för din slut punkt, till exempel slut punkts nyckel, slut punkts-URL och exempel kod.

Online-testning av slut punkten är också tillgänglig via den anpassade röst portalen. Om du vill testa slut punkten väljer du **kontrol lera slut** punkt på informations sidan för **slut punkten** . Sidan slut punkts testning visas. Ange texten som ska läsas (i antingen oformaterat text-eller [SSML-format](speech-synthesis-markup.md) i text rutan. Om du vill höra texten som talas i ditt anpassade röst teckensnitt väljer du **spela upp**. Den här test funktionen kommer att debiteras mot din anpassade tal syntes användning.

Den anpassade slut punkten är funktionellt identisk med standard slut punkten som används för text till tal-begäranden. Se [REST API](rest-text-to-speech.md) för mer information.

## <a name="next-steps"></a>Nästa steg

* [Guide: spela in dina röst exempel](record-custom-voice-samples.md)
* [Text-till-Speech API-referens](rest-text-to-speech.md)
* [Långt ljud-API](long-audio-api.md)