---
title: Övervaka tillgänglighet och svarstider på valfri webbplats | Microsoft Docs
description: Konfigurera webbtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
ms.date: 02/14/2021
ms.reviewer: sdash
ms.openlocfilehash: 0ea44aad43a3dd6f11656a45dc7d0e619187fc22
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729036"
---
# <a name="monitor-the-availability-of-any-website"></a>Övervaka tillgängligheten för en webbplats

När du har distribuerat din webbapp/webbplats kan du ställa in återkommande tester för att övervaka tillgänglighet och svars tider. [Azure Application Insights](./app-insights-overview.md) skickar begäranden till ditt program med jämna mellanrum från platser över hela världen. Det kan varna dig om programmet inte svarar eller om det svarar för långsamt.

Du kan konfigurera tillgänglighetstester för valfri HTTP- eller HTTPS-slutpunkt som kan nås från det offentliga Internet. Du behöver inte göra några ändringar på den webbplats som du testar. I själva verket behöver det inte ens vara en webbplats som du äger. Du kan testa tillgängligheten för en REST API som tjänsten är beroende av.

### <a name="types-of-availability-tests"></a>Typer av tillgänglighets test:

Det finns tre typer av tillgänglighets test:

* [URL-pingtest](#create-a-url-ping-test): Ett enkelt test som du kan skapa på Azure-portalen.
* [Webb test för flera steg](availability-multistep.md): en inspelning av en sekvens med webb förfrågningar, som kan spelas upp för att testa mer komplexa scenarier. Webbtester med flera steg skapas i Visual Studio Enterprise och överförs till portalen för körning.
* [Tillgänglighets test för anpassad spårning](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability): om du väljer att skapa ett anpassat program för att köra tillgänglighets test, `TrackAvailability()` kan metoden användas för att skicka resultaten till Application Insights.

**Du kan skapa upp till 100 tillgänglighets test per Application Insights resurs.**

> [!IMPORTANT]
> Både [URL-ping-test](#create-a-url-ping-test) och [webbtest för flera steg](availability-multistep.md) förlitar sig på den offentliga Internet-DNS-infrastrukturen för att lösa domän namnen för de testade slut punkterna. Det innebär att om du använder Privat DNS måste du antingen se till att varje domän namn för ditt test också kan matchas av de offentliga domän namn servrarna eller, om det inte är möjligt, du kan använda [anpassade tester för spårnings tillgänglighet](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) i stället.

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

För att kunna skapa ett tillgänglighets test måste du först skapa en Application Insights-resurs. Om du redan har skapat en resurs fortsätter du till nästa avsnitt för att [skapa ett URL-testtest](#create-a-url-ping-test).

Från Azure Portal väljer du **skapa en resurs**  >  **utvecklarverktyg**  >  **Application Insights** och [skapar en Application Insights resurs](create-new-resource.md).

## <a name="create-a-url-ping-test"></a>Skapa ett URL-pingtest

Namnet "URL-ping-test" är en bit av en misnomer. För att vara klar kommer det här testet inte att göra någon användning av ICMP (Internet Control Message Protocol) för att kontrol lera din webbplats tillgänglighet. I stället används mer avancerade HTTP-begäranden för att kontrol lera om en slut punkt svarar. Den mäter också de prestanda som är kopplade till svaret och ger möjlighet att ange anpassade framgångs kriterier tillsammans med mer avancerade funktioner som att parsa beroende begär Anden och att tillåta återförsök.

Om du vill skapa din första tillgänglighets förfrågan öppnar du fönstret tillgänglighet och väljer **skapa test**.

![Fyll åtminstone i URL:en för din webbplats](./media/monitor-web-app-availability/availability-create-test-001.png)

### <a name="create-a-test"></a>Skapa ett test

|Inställning| Förklaring
|----|----|----|
|**URL** |  URL: en kan vara en webbsida som du vill testa, men den måste vara synlig från Internet. URL: en kan innehålla en frågesträng. Du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.|
|**Parsa beroende begär Anden**| Testbegäran-bilder, skript, formatfiler och andra filer som ingår i webb sidan under testet. Den registrerade svarstiden innefattar den tid det tar att hämta dessa filer. Testet Miss lyckas om någon av dessa resurser inte kan laddas ned inom tids gränsen för hela testet. Om alternativet inte är markerat begärs endast filen på den URL som du har angett i testet. Att aktivera det här alternativet resulterar i en striktare kontroll. Det kan hända att testet Miss lyckas för fall, vilket inte kan märkas när du bläddrar på platsen manuellt.
|**Aktivera återförsök**|När testet Miss lyckas görs ett nytt försök efter ett kort intervall. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. **Vi rekommenderar det här alternativet**. I genomsnitt försvinner ca 80 % av felen vid återförsök.|
|**Test frekvens**| Anger hur ofta testet körs från varje test plats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Test platser**| Är platser där våra servrar skickar webb förfrågningar till din URL. Det **minsta antalet rekommenderade test platser är fem** för att försäkra dig om att du kan särskilja problem på din webbplats från nätverks problem. Du kan välja upp till 16 platser.

**Om din URL inte är synlig från det offentliga Internet kan du välja att selektivt öppna brand väggen för att endast tillåta test transaktionerna genom**. Mer information om brand Väggs undantag för våra tillgänglighets test agenter finns i [IP-adress guiden](./ip-addresses.md#availability-tests).

> [!NOTE]
> Vi rekommenderar starkt att du testar från flera platser med **minst fem platser**. Detta är för att förhindra falska larm som kan resultera i tillfälliga problem med en angiven plats. Vi har också funnit att den optimala konfigurationen är att **antalet test platser ska vara lika med tröskelvärdet för aviserings plats + 2**.

### <a name="success-criteria"></a>Lyckade kriterier

|Inställning| Förklaring
|----|----|----|
| **Timeout för test** |Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade status koden som räknas som lyckad. 200 är koden som anger att en normal webbsida har returnerats.|
| **Innehålls matchning** | En sträng, till exempel "Välkommen!" Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehålls matchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära real tid (för hands version)** | Vi rekommenderar att du använder aviseringar i nästan real tid. Konfigurationen av den här typen av avisering görs efter att ditt tillgänglighets test har skapats.  |
|**Tröskelvärde för aviserings plats**|Vi rekommenderar minst 3/5 platser. Den optimala relationen mellan aviserings platsens tröskelvärde och antalet test platser är **tröskelvärdet för aviserings platsens tröskel**  =  **antal test platser – 2, med minst fem test platser.**|

### <a name="location-population-tags"></a>Plats populations Taggar

Följande befolknings taggar kan användas för attributet Geo-Location när du distribuerar en tillgänglighets-URL ping-test med Azure Resource Manager.

#### <a name="azure-gov"></a>Azure-gov

| Visningsnamn   | Namn på population     |
|----------------|---------------------|
| USGov Virginia | usgov-va-AZR        |
| USGov Arizona  | usgov-PHX-AZR       |
| USGov Texas    | usgov-TX-AZR        |
| USDoD, öst     | usgov-ddeast-AZR    |
| USDoD Central  | usgov-ddcentral-AZR |

#### <a name="azure"></a>Azure

| Visningsnamn                           | Namn på population   |
|----------------------------------------|-------------------|
| Östra Australien                         | EMEA – au – syd-Edge  |
| Brasilien, södra                           | Latam-br-Gru-Edge |
| Central US                             | US-fl-Mia-Edge    |
| Asien, östra                              | APAC-hk-HKN-AZR   |
| East US                                | US-va-Ash-AZR     |
| Frankrike, södra (tidigare Frankrike centrala) | EMEA-CH-ZRH-Edge  |
| Frankrike, centrala                         | EMEA – fr-Pra-Edge  |
| Japan, östra                             | APAC – JP-KAW-Edge  |
| Europa, norra                           | EMEA-GB-DB3 – AZR   |
| USA, norra centrala                       | US-Il-CH1-AZR     |
| USA, södra centrala                       | US-TX-SN1-AZR     |
| Sydostasien                         | APAC – SG-sin-AZR   |
| Storbritannien, västra                                | EMEA – se – sluta-Edge  |
| Europa, västra                            | EMEA – nl-AMS – AZR   |
| USA, västra                                | US-ca-SJC – AZR     |
| Storbritannien, södra                               | EMEA – ru-MSA-Edge  |

## <a name="see-your-availability-test-results"></a>Visa tillgänglighetstestresultat

Tillgänglighets test resultat kan visualiseras med vyer för både linje och punkt diagram.

Efter några minuter klickar du på **Uppdatera** för att se test resultaten.

![Skärm bild som visar tillgänglighets sidan med knappen Uppdatera markerad.](./media/monitor-web-app-availability/availability-refresh-002.png)

I scatterplot-vyn visas exempel på de test resultat som har diagnostiska test – steg information i dem. Testmotorn lagrar diagnosinformation för tester som innehåller fel. För lyckade tester lagras diagnosinformation för en delmängd av körningarna. Hovra över någon av de gröna/röda punkterna för att se testet, test namnet och platsen.

![Rad visning](./media/monitor-web-app-availability/availability-scatter-plot-003.png)

Välj ett visst test, en viss plats eller minska tidsperioden för att visa fler resultat för en viss tidsperiod som du är intresserad av. Använd Sökutforskaren för att visa resultat från alla körningar, eller använd analysfrågor om du vill köra anpassade rapporter för dessa data.

## <a name="inspect-and-edit-tests"></a>Granska och redigera tester

Om du vill redigera, inaktivera eller ta bort ett test klickar du på ellipsen bredvid ett testnamn. Det kan ta upp till 20 minuter för konfigurations ändringar att spridas till alla test agenter när en ändring har gjorts.

![Visa test information. Redigera och inaktivera ett webb test](./media/monitor-web-app-availability/edit.png)

Du kanske vill inaktivera tillgänglighetstester eller varningsreglerna som är associerade med dem medan du utför underhåll på tjänsten.

## <a name="if-you-see-failures"></a>Om du ser fel

Klicka på en röd punkt.

![Klicka på en röd punkt](./media/monitor-web-app-availability/open-instance-3.png)

Du kan se transaktions detaljerna för alla komponenter från ett tillgänglighets test resultat. Här kan du:

* Kontrollera de svar som mottas från servern.
* Diagnostisera fel med korrelerad telemetri från Server sidan som samlats in när det misslyckade tillgänglighets testet bearbetades.
* Logga ett problem eller ett arbets objekt i git eller Azure-kort för att spåra problemet. Buggen innehåller en länk till den här händelsen.
* Öppna resultatet av webbtestet i Visual Studio.

Läs mer om hur du slutför körningen av transaktions diagnostik [här](./transaction-diagnostics.md).

Klicka på raden undantag om du vill se information om det undantag på Server sidan som orsakade att testet för syntetisk tillgänglighet inte kunde köras. Du kan också hämta en [fel söknings ögonblicks bild](./snapshot-debugger.md) för bättre diagnostik på kod nivå.

![Diagnostik på Server Sidan](./media/monitor-web-app-availability/open-instance-4.png)

Förutom de råa resultaten kan du också visa två nyckel tillgänglighets mått i [Metrics Explorer](../essentials/metrics-getting-started.md):

1. Tillgänglighet: antal procent av testerna som lyckades av alla testkörningar.
2. Testets varaktighet: genomsnittlig tid för alla testkörningar.

## <a name="automation"></a>Automation

* [Konfigurera ett tillgänglighetstest automatiskt med hjälp av PowerShell-skript](./powershell.md#add-an-availability-test).
* Konfigurera en [webhook](../alerts/alerts-webhooks.md) som anropas när en avisering genereras.

## <a name="troubleshooting"></a>Felsökning

Dedikerad [fel söknings artikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighets varningar](availability-alerts.md)
* [Webbtester med flera steg](availability-multistep.md)

