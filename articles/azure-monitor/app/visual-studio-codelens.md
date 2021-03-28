---
title: Application Insights Telemetry i Visual Studio CodeLens | Microsoft Docs
description: Kom snabbt åt din Application Insights-begäran och undantagstelemetri med CodeLens i Visual Studio.
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: d412441a7a80a14a0bc7a70de8020692f8f573d6
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642729"
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Application Insights Telemetry i Visual Studio CodeLens
Metoderna i din webbapps kod kan kommenteras med telemetri om körtidsundantag och förfrågningssvarstider. Om du installerar [Azure Application Insights](./app-insights-overview.md) i ditt program visas telemetrin i Visual Studio [CodeLens](/visualstudio/ide/find-code-changes-and-other-history-with-codelens) – anteckningarna längst upp för varje funktion där du är van att se användbar information som antalet platser som refererar till funktionen, eller den person som senast redigerade den.

![CodeLens](./media/visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> Application Insights i CodeLens är tillgängligt i Visual Studio 2015 Update 3 och senare, eller med den senaste versionen av tillägget [Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a). CodeLens är tillgänglig i Visual Studio Enterprise- och Professional-versionerna.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Var du hittar Application Insights-data
Leta efter Application Insights Telemetry i CodeLens-indikatorer för offentliga metodbegäranden av din webbapp. CodeLens-indikatorer visas ovanför metod och andra deklarationer i C#- och Visual Basic-kod. Om Application Insights-data finns tillgängliga för en metod, kommer du att se indikatorer för begäranden och undantag, till exempel "100 begäranden, 1 % misslyckades" eller "10 undantag." Klicka på en CodeLens-indikator för mer information. 

> [!TIP]
> Application Insights-begäran och undantags-indikatorer kan ta några extra sekunder att läsa in när andra CodeLens-indikatorer visas.
> 
> 

## <a name="exceptions-in-codelens"></a>Undantag i CodeLens
![Skärm bild som visar 47-undantag som visas i CodeLens.](./media/visual-studio-codelens/codelens-exceptions.png)

Indikatorn CodeLens-undantag visar antalet undantag som har inträffat under de senaste 24 timmarna från de 15 oftast inträffade undantagen i din app under denna period, vid bearbetning av den begäran som hanteras av metoden.

Om du vill se mer information klickar du på indikatorn CodeLens-undantag:

* Den procentuella förändringen i antalet undantag från de senaste 24 timmarna i förhållande till föregående 24 timmar
* Välj **Gå till kod** för att navigera till källkoden för funktionen som utlöste undantaget
* Välj **Sök** för att fråga efter alla instanser av detta undantag som har inträffat under de senaste 24 timmarna
* Välj **Trend** för att se en trendvisualisering för förekomster av detta undantag under de senaste 24 timmarna
* Välj **Visa alla undantag i denna app** för att fråga efter alla undantag som har inträffat under de senaste 24 timmarna
* Välj **Utforska undantagstrender** för att se en visualiseringstrend för alla undantag som har inträffat under de senaste 24 timmarna. 

> [!TIP]
> Om du ser "0 undantag" i CodeLens men vet att det ska finnas undantag, kontrollera att rätt Application Insights-resurs är markerad i CodeLens. Om du vill välja en annan resurs högerklickar du på ditt projekt i Solution Explorer och väljer **Application Insights > Välj telemetrikälla**. CodeLens visas bara för de 15 mest förekommande undantagen i din app under de senaste 24 timmarna, så om ett undantag är det 16:e oftast förekommande eller mindre, ser du "0 undantag." Undantag från ASP.NET-vyer kanske inte visas på de kontrollantmetoder som genererade dessa vyer.
> 
> [!TIP]
> Om du ser "? undantag" i CodeLens måste du koppla ditt Azure-konto med Visual Studio, eller så har dina autentiseringsuppgifter för Azure-kontot kanske gått ut. I bägge fallen klickar du på "? undantag" och väljer **Lägg till ett konto...** för att ange dina autentiseringsuppgifter.
> 
> 

## <a name="requests-in-codelens"></a>Begäranden i CodeLens
![Skärm bild som visar information om 684-begäranden, inklusive 7% misslyckade.](./media/visual-studio-codelens/codelens-requests.png)

Indikatorn CodeLens-begäran visar antalet HTTP-begäranden som har hanterats av en metod under de senaste 24 timmarna, plus den procent av dessa begäranden som misslyckats.

Om du vill se mer information klickar du på indikatorn CodeLens-begäran:

* Absoluta och procentuella förändringar i antalet begäranden, misslyckade begäranden och de genomsnittliga svarstiderna under de senaste 24 timmarna jämfört med föregående 24 timmar
* Metodens tillförlitlighet, beräknad som procentandelen av de förfrågningar som inte misslyckades under de senaste 24 timmarna
* Välj **Sök** för begäranden eller misslyckade begäranden för att fråga efter alla (misslyckade) begäranden som uppstod under de senaste 24 timmarna
* Välj **Trend** för att se en trendvisualisering för begäranden, misslyckade begäranden eller genomsnittliga svarstider för de senaste 24 timmarna.
* Välj namnet på Application Insights-resursen i det övre vänstra hörnet i CodeLens-informationsvyn för att ändra den resurs som är källa för CodeLens-datan.

## <a name="next-steps"></a><a name="next"></a>Nästa steg
* **[Arbeta med Application Insights i Visual Studio](./visual-studio.md)**. Sök i telemetri, visa data i CodeLens och konfigurera Application Insights. Allt i Visual Studio. 
* **[Arbeta med Application Insights portalen](./overview-dashboard.md)**. Instrumentpaneler, kraftfulla verktyg för diagnostik och analys, aviseringar, live-mappning över beroenden för din app och telemetriexport. 

