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
ms.openlocfilehash: 0650a173b02e1b8f1f829953be1dd852024e6f65
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524523"
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

I vyn data information kan du ytterligare kontrol lera uttal-poängen och brus nivån för varje data uppsättning. Uttal-poängen sträcker sig från 0 till 100. Poängen under 70 indikerar normalt ett tal fel eller felaktig matchning av skript. En tung accent kan minska uttal-poängen och påverka den genererade digitala rösten.

En högre signal-till-brus-förhållande (SNR) indikerar lägre brus i ljudet. Du kan normalt komma åt ett 50 + SNR genom att spela in i Professional Studios. Ljud med ett SNR under 20 kan resultera i uppenbar brus i den genererade rösten.

Överväg att registrera om alla yttranden med låga uttal-resultat eller dåliga signal-till-brus-förhållande. Om du inte kan spela in igen kan du undanta dessa yttranden från din data uppsättning.

> [!NOTE]
> Om du använder anpassad neurala röst måste du registrera din röst personal på fliken **röst personal** . När du förbereder ditt inspelnings skript ska du se till att ta med nedanstående mening för att få röst personal bekräftelse på att använda sina röst data för att skapa en TTS-röst modell och generera syntetiskt tal. "I [tillstånd ditt för-och efter namn] är du medveten om att inspelningar av min röst kommer att användas av [stat namnet på företaget] för att skapa och använda en syntetisk version av rösten."
Den här meningen används för att kontrol lera om inspelningarna i dina utbildnings data uppsättningar utförs av samma person som godkänner godkännandet. [Läs mer om hur dina data kommer att bearbetas och hur röst personal-verifiering görs här](https://aka.ms/CNV-data-privacy). 

## <a name="build-your-custom-voice-model"></a>Bygg din anpassade röst modell

När din data uppsättning har verifierats kan du använda den för att bygga din anpassade röst modell.

1.  Navigera till **text till tal > anpassad röst > [namn på projektet] > modell**.

2.  Klicka på **träna modell**.

3.  Ange sedan ett **namn** och en **Beskrivning** som hjälper dig att identifiera den här modellen.

    Välj ett namn noggrant. Namnet som du anger här är det namn som du använder för att ange rösten i din begäran om tal syntes som en del av SSML-indatamängden. Endast bokstäver, siffror och några interpunktionstecken, till exempel-, och \_ (,) är tillåtna. Använd olika namn för olika röst modeller.

    En vanlig användning av fältet **Beskrivning** är att registrera namnen på de data uppsättningar som användes för att skapa modellen.

4.  På sidan **Välj tränings data** väljer du en eller flera data uppsättningar som du vill använda för utbildning. Kontrol lera antalet yttranden innan du skickar dem. Du kan börja med ett valfritt antal yttranden för en-US-och zh-CN röst modell med hjälp av inlärnings metoden "anpassningsbar". För andra språk måste du välja fler än 2 000 yttranden för att kunna träna en röst med en standard nivå, inklusive utbildnings metoderna "statistisk parameter" och "sammanfogning" och mer än 300 yttranden för att träna en anpassad neurala-röst. 

    > [!NOTE]
    > Dubbla ljud namn tas bort från utbildningen. Se till att de data uppsättningar du väljer inte innehåller samma ljud namn i flera. zip-filer.

    > [!TIP]
    > Att använda data uppsättningarna från samma talare krävs för kvalitets resultat. Olika utbildnings metoder kräver olika tränings data storlek. För att träna en modell med metoden "statistisk parameter" krävs minst 2 000 distinkta yttranden. För metoden "sammanlänkning" är det 6 000 yttranden, medan för "neurala", så är minimi kravet för data storlek 300 yttranden.

5. Välj **inlärnings metoden** i nästa steg. 

    > [!NOTE]
    > Om du vill träna en neurala röst måste du ange en röst personal-profil med den ljud medgivande fil som du har fått från röst personal som kan bekräfta att hans/hennes tal-data används för att träna en anpassad röst modell. Anpassad neurala-röst är tillgänglig med begränsad åtkomst. Se till att du förstår de [ansvariga AI-kraven](https://aka.ms/gating-overview) och [Använd åtkomsten här](https://aka.ms/customneural). 
    
    På den här sidan kan du också välja att ladda upp skriptet för testning. Test skriptet måste vara en txt-fil som är mindre än 1 MB. Kodnings formatet som stöds omfattar ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE eller UTF-16. Varje stycke i uttryck resulterar i ett separat ljud. Om du vill kombinera alla meningar i ett ljud gör du dem i ett stycke. 

6. Klicka på **träna** för att börja skapa din röst modell.

I övnings tabellen visas en ny post som motsvarar den nyligen skapade modellen. Tabellen visar också status: bearbetning, lyckades, misslyckades.

Den status som visas visar processen för att konvertera data uppsättningen till en röst modell, som visas här.

| Stat | Innebörd |
| ----- | ------- |
| Bearbetar | Din röst modell skapas. |
| Lyckades | Din röst modell har skapats och kan distribueras. |
| Misslyckad | Din röst modell har misslyckats i utbildningen på grund av många orsaker, till exempel osett data problem eller nätverks problem. |

Inlärnings tiden varierar beroende på mängden ljud data som bearbetats och den tränings metod som du har valt. Det kan vara mellan 30 minuter och 40 timmar. När modell utbildningen har slutförts kan du börja testa den. 

> [!NOTE]
> F0-användare (kostnads fri prenumeration) kan träna ett röst tecken samtidigt. S0-användare (standard prenumeration) kan träna tre röster samtidigt. Om du når gränsen väntar du tills minst en av dina röst teckensnitt har slutfört utbildningen och försöker sedan igen.

> [!NOTE]
> Träning av anpassade neurala-röster är inte kostnads fritt. Sök efter [prissättningen](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) här. 

> [!NOTE]
> Det maximala antalet röst modeller som får tränas per prenumeration är 10 modeller för F0-användare och 100-användare (standard prenumeration).

Om du använder neurala röst träning kan du välja att träna en modell som är optimerad för strömnings scenarier i real tid, eller en HD neurala-modell som är optimerad för asynkrona [långa ljud syntes](long-audio-api.md).  

## <a name="test-your-voice-model"></a>Testa din röst modell

Varje övning kommer att generera 100-ljudfiler automatiskt för att hjälpa dig att testa modellen. När din röst modell har skapats kan du testa den innan du distribuerar den för användning.

1.  Navigera till **text till tal > anpassad röst > [namn på projektet] > modell**.

2.  Klicka på namnet på den modell som du vill testa.

3.  På sidan modell information kan du hitta exempelfilerna på fliken **test** . 

Kvaliteten på rösten är beroende av ett antal faktorer, inklusive storleken på tränings data, kvaliteten på inspelningen, precisionen för avskrifts filen, hur väl den inspelade rösten i tränings data matchar den personliga rösten för det avsedda användnings fallet med mera. [Läs mer om funktionerna och gränserna för vår teknik och bästa praxis för att förbättra modell kvaliteten](https://aka.ms/CNV-limits). 

## <a name="create-and-use-a-custom-voice-endpoint"></a>Skapa och Använd en anpassad röst slut punkt

När du har skapat och testat din röst modell distribuerar du den i en anpassad text till tal-slutpunkt. Du använder sedan den här slut punkten i stället för den vanliga slut punkten när du gör text till tal-begäran via REST API. Din anpassade slut punkt kan bara anropas av den prenumeration som du har använt för att distribuera teckensnittet.

Om du vill skapa en ny anpassad röst slut punkt går du till **text-till-tal > anpassad röst >-slutpunkt**. Välj **Lägg till slut punkt** och ange ett **namn** och en **Beskrivning** för din anpassade slut punkt. Välj sedan den anpassade röst modell som du vill koppla till den här slut punkten.

När du har klickat på knappen **Lägg till** i tabellen slut punkt visas en post för den nya slut punkten. Det kan ta några minuter att instansiera en ny slut punkt. När statusen för distributionen har **slutförts** är slut punkten klar att användas.

Du kan **pausa** och **återuppta** slut punkten om du inte använder den hela tiden. När en slut punkt återaktiveras efter en SUS pension, kommer slut punktens URL att vara densamma så att du inte behöver ändra koden i dina appar. 

Du kan också uppdatera slut punkten till en ny modell. Om du vill ändra modellen kontrollerar du att den nya modellen har samma namn som den som du vill uppdatera. 

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
