---
title: 'ML Studio (klassisk): data utvärdering – Azure'
description: Fyra villkor som dina data måste uppfylla för att vara klara för data vetenskap. Den här videon innehåller konkreta exempel som hjälper dig med grundläggande data utvärdering.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: sdgilley
ms.author: sgilley
ms.custom: seodec18
ms.date: 03/22/2019
ms.openlocfilehash: 07db1f1c8021759fbd5702ad9353f74fcd1524c9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516982"
---
# <a name="is-your-data-ready-for-data-science"></a>Är dina data klara för datavetenskap?
## <a name="video-2-data-science-for-beginners-series"></a>Video 2: data vetenskap för nybörjare-serien
Lär dig hur du utvärderar dina data för att se till att de uppfyller grundläggande villkor för att vara klara för data vetenskap.

För att få ut det mesta av serien, se alla. [Gå till listan med videor](#other-videos-in-this-series)
<br>

> [!VIDEO https://channel9.msdn.com/Shows/SupervisionNotRequired/9/player]
>
>

## <a name="other-videos-in-this-series"></a>Andra videor i den här serien
*Data vetenskap för nybörjare* är en snabb introduktion till data vetenskap i fem korta videor.

* Video 1: [5 frågor data vetenskaps svar](data-science-for-beginners-the-5-questions-data-science-answers.md) *(5 min 14 SEK)*
* Video 2: är dina data klara för data vetenskap?
* Video 3: [Ställ en fråga som du kan besvara med data](data-science-for-beginners-ask-a-question-you-can-answer-with-data.md) *(4 min 17 SEK)*
* Video 4: [förutsäga ett svar med en enkel modell](data-science-for-beginners-predict-an-answer-with-a-simple-model.md) *(7 min 42 SEK)*
* Video 5: [Kopiera andras arbete för att göra data vetenskap](data-science-for-beginners-copy-other-peoples-work-to-do-data-science.md) *(3 min 18 SEK)*

## <a name="transcript-is-your-data-ready-for-data-science"></a>Avskrift: är dina data klara för data vetenskap?
Välkommen till "är dina data klara för data vetenskap?" den andra videon i seriens *data vetenskap för nybörjare*.  

Innan data vetenskap kan ge dig de svar du önskar, måste du ge det en viss högkvalitativ råmaterial att arbeta med. Precis som att göra en pizza, desto bättre de ingredienser som du börjar med, desto bättre slut produkt. 

## <a name="criteria-for-data"></a>Villkor för data
I data vetenskap finns det vissa ingredienser som måste hämtas tillsammans, inklusive:

* Relevanta
* Ansluten
* Vis
* Tillräckligt för att fungera med

## <a name="is-your-data-relevant"></a>Är dina data relevanta?
Den första ingrediensen – du behöver data som är relevanta.

![Relevanta data kontra irrelevanta data – utvärdera data](./media/data-science-for-beginners-is-your-data-ready-for-data-science/relevant-and-irrelevant-data.png)

Till vänster visar tabellen blod alkoholen för sju personer som testats utanför en Boston-stapel, det röda Sox batting-genomsnittet i det senaste spelet och priset på mjölk i närmaste lager.

Detta är alla perfekt legitima data. Det är bara fel att det inte är relevant. Det finns ingen uppenbar relation mellan dessa tal. Om någon gav dig det aktuella priset för mjölk och det röda Sox batting-genomsnittet, finns det inget sätt att gissa sitt blod alkohols innehåll.

Titta nu på tabellen till höger. Den här gången mäts varje persons kropps vikt och antalet drycker de hade haft.  Siffrorna i varje rad är nu relevanta för varandra. Om jag har fått min kropps vikt och antalet Margaritas jag har haft kan du göra en gissning på innehållet i ditt blod alkohol.

## <a name="do-you-have-connected-data"></a>Har du anslutna data?
Nästa ingrediens är anslutna till data.

![Anslutna data eller data som är frånkopplade, data är klara](./media/data-science-for-beginners-is-your-data-ready-for-data-science/connected-vs-disconnected-data.png)

Här följer några relevanta uppgifter om profilernas kvalitet: galler-temperatur, Patty vikt och betyg i det lokala livsmedels magasinet. Observera dock mellanrummen i tabellen till vänster.

De flesta data mängder saknar vissa värden. Det är vanligt att ha hål som detta och det finns olika sätt att jobba runt dem. Men om det inte finns något som saknas börjar dina data se ut som schweiziska ost.

Om du tittar på tabellen till vänster finns det mycket data som saknas, det är svårt att komma igång med alla typer av relationer mellan galler och Patty vikt. I det här exemplet visas frånkopplade data.

Tabellen till höger, men är fullständig och fullständig – ett exempel på anslutna data.

## <a name="is-your-data-accurate"></a>Är dina data korrekta?
Nästa ingrediens är korrekt. Det finns fyra mål att trycka på.

![Korrekta data och felaktiga data-data kriterier](./media/data-science-for-beginners-is-your-data-ready-for-data-science/inaccurate-vs-accurate-data.png)

Titta på målet i det övre högra hörnet. Det finns en tät gruppering direkt runt tjurarna. Det stämmer naturligtvis. Oddly, på språket för data vetenskap, anses prestanda på mål högerkanten under den också vara korrekt.

Om du har kopplat bort mitten av pilarna ser du att det är mycket nära Tjurfäktnings ögat. Pilarna sprids ut runt målet, så de anses vara inexakta, men de centreras runt tjurens ögon, så att de betraktas som korrekta.

Titta nu på det övre vänstra målet. Här är pilarna nära varandra, en tät gruppering. De är exakta, men de är inte korrekta eftersom centret är på väg från tjurarna ögat. Pilarna i det nedre vänstra målet är både felaktiga och inexakta. Den här Archer behöver mer praxis.

## <a name="do-you-have-enough-data-to-work-with"></a>Har du tillräckligt med data för att arbeta med?
Slutligen är komponent #4 tillräckligt med data.

![Har du tillräckligt med data för analys? Data utvärdering](./media/data-science-for-beginners-is-your-data-ready-for-data-science/barely-enough-data.png)

Tänk på varje data punkt i tabellen som en pensel linje i en målning. Om du bara har några av dem kan målningen vara suddig – det är svårt att berätta vad det är.

Om du lägger till några fler penseldrag börjar din målning att få lite skarpare.

När du har knappt tillräckligt många linjer ser du bara tillräckligt många för att fatta några breda beslut. Är det någon som jag kanske vill besöka? Det ser ljust ut, som ser ut som rent vatten – Ja, det är där jag går på semester.

När du lägger till mer data blir bilden tydligare och du kan fatta mer detaljerade beslut. Nu kan du titta på de tre hotellen på den vänstra banken. Du kan observera arkitektur funktionerna i den i förgrunden. Du kan även välja att stanna kvar på den tredje våningen på grund av vyn.

Med data som är relevanta, anslutna, korrekta och tillräckligt många, har du alla ingredienser som behövs för att göra vissa data vetenskaps kvalitet av hög kvalitet.

Se till att ta en titt på de andra fyra videor i *data vetenskap för nybörjare* från Microsoft Azure Machine Learning Studio (klassisk).

## <a name="next-steps"></a>Nästa steg
* [Testa ett första data vetenskaps experiment med Machine Learning Studio (klassisk)](create-experiment.md)
* [Få en introduktion till Machine Learning på Microsoft Azure](../overview-what-is-azure-ml.md)