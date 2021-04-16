---
title: Övervaka tillgänglighet och svarstider för alla webbplatser – Azure Monitor
description: Konfigurera pingtester i Application Insights. Få aviseringar om en webbplats blir otillgänglig eller svarar långsamt.
ms.topic: conceptual
ms.date: 04/15/2021
ms.reviewer: sdash
ms.openlocfilehash: ecfd4ffee3582ff37411e59c75d8be8fca5e945f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516631"
---
# <a name="monitor-the-availability-of-any-website"></a>Övervaka tillgängligheten för en webbplats

Namnet "URL ping test" är lite av en felaktigt. För att vara tydlig använder de här testerna inte ICMP (Internet Control Message Protocol) för att kontrollera webbplatsens tillgänglighet. I stället använder de mer avancerade FUNKTIONER för HTTP-begäranden för att verifiera om en slutpunkt svarar. De mäter också prestandan som är associerad med svaret och lägger till möjligheten att ange anpassade framgångsvillkor tillsammans med mer avancerade funktioner som parsning av beroende begäranden och att tillåta återförsök.

Det finns två typer av URL-pingtest som du kan skapa, grundläggande och standardtest för ping.

> [!NOTE]
> Basic- och Standard-pingtester är för närvarande i offentlig förhandsversion. Dessa förhandsversioner tillhandahålls utan serviceavtal. Vissa funktioner kanske inte stöds eller kan vara begränsade.

Basic jämfört med Standard:

- Basic är begränsat till fem platser per test.
- Standardtester kan ha anpassade huvuden eller begärandetext.
- Standardtester kan använda valfri HTTP-förfrågningsmetod medan Basic bara kan använda `GET` .
- Livslängdskontroll för SSL-certifikat varnar dig om en a viss tidsperiod innan certifikatet upphör att gälla.
- Standardtester är en betalfunktion.

> [!NOTE]
> Det finns för närvarande inga ytterligare avgifter för förhandsgranskningsfunktionen Standard Ping-tester. Priser för funktioner som är i förhandsversion kommer att meddelas i framtiden och ett meddelande visas innan faktureringen påbörjas. Om du väljer att fortsätta använda Standard Ping-tester efter tidsperioden debiteras du enligt gällande pris.

## <a name="create-a-url-ping-test"></a>Skapa ett URL-pingtest

För att kunna skapa ett tillgänglighetstest behöver du använda en befintlig Application Insight-resurs eller [skapa en Application Insights resurs](create-new-resource.md).

Om du vill skapa din första tillgänglighetsbegäran öppnar du fönstret Tillgänglighet och väljer Skapa test & väljer din test-SKU.

:::image type="content" source="./media/monitor-web-app-availability/create-basic-test.png" alt-text="Skärmbild av att skapa ett grundläggande URL-pingtest i Azure-portalen":::

|Inställning | Förklaring |
|--------|-------------|
|**URL** |  URL: en kan vara en webbsida som du vill testa, men den måste vara synlig från Internet. URL: en kan innehålla en frågesträng. Du kan arbeta med din databas om du vill. Om URL-adressen matchar en omdirigering följer vi den upp till tio omdirigeringar.|
|**Parsa beroende begäranden**| Testa begär bilder, skript, formatfiler och andra filer som ingår i webbsidan som testas. Den registrerade svarstiden innefattar den tid det tar att hämta dessa filer. Testet misslyckas om någon av dessa resurser inte kan laddas ned inom tidsgränsen för hela testet. Om alternativet inte är markerat begärs endast filen på den URL som du har angett i testet. Om du aktiverar det här alternativet resulterar det i en striktare kontroll. Testet kan misslyckas i fall, vilket kanske inte är märkbart när du bläddrar manuellt på webbplatsen. |
|**Aktivera återförsök**| När testet misslyckas försöks det igen efter ett kort intervall. Ett fel rapporteras endast om tre på varandra följande försök misslyckas. Efterföljande tester utförs sedan med den vanliga testfrekvensen. Återförsök pausas tillfälligt tills nästa lyckade test. Den här regeln tillämpas separat på varje testplats. **Vi rekommenderar det här alternativet**. I genomsnitt försvinner ca 80 % av felen vid återförsök.|
| **Valideringstest för SSL-certifikat** | Du kan verifiera SSL-certifikatet på din webbplats för att kontrollera att det är korrekt installerat, giltigt, betrott och inte ger några fel till någon av dina användare. |
| **Proaktiv livslängdskontroll** | På så sätt kan du definiera en viss tidsperiod innan SSL-certifikatet upphör att gälla. När det upphör att gälla misslyckas testet. |
|**Testfrekvens**| Anger hur ofta testet ska köras från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Testplatser**| Är de platser där våra servrar skickar webbförfrågningar till din URL. **Vårt minsta antal rekommenderade testplatser är fem** för att säkerställa att du kan skilja mellan problem på din webbplats och nätverksproblem. Du kan välja fler än fem platser med standardtest och upp till 16 platser.|

**Om url:en inte visas från** det offentliga Internet kan du välja att selektivt öppna brandväggen så att endast testtransaktioner tillåts genom . Mer information om brandväggundantag för våra tillgänglighetstestagenter finns i [IP-adressguiden.](./ip-addresses.md#availability-tests)

> [!NOTE]
> Vi rekommenderar starkt att du testar från flera **platser med minst fem platser.** Detta för att förhindra falska larm som kan uppstå på grund av tillfälliga problem med en specifik plats. Dessutom har vi upptäckt att den optimala konfigurationen är att antalet testplatser ska vara lika med tröskelvärdet för **aviseringsplatsen + 2**.

## <a name="standard-test"></a>Standardtest

:::image type="content" source="./media/monitor-web-app-availability/standard-test-post.png" alt-text="Skärmbild av fliken standardtestinformation." border="false":::

|Inställning | Förklaring |
|--------|-------------|
| **Anpassade rubriker** | Nyckelvärdepar som definierar driftsparametrarna. |
| **HTTP-begärandeverb** | Ange vilken åtgärd du vill vidta med din begäran. Om det valda verbet inte är tillgängligt i användargränssnittet kan du distribuera ett standardtest med Azure resursövervakare med önskat val. |
| **Begärandetext** | Anpassade data som är associerade med din HTTP-begäran. Du kan ladda upp typ av egen filtyp i ditt innehåll eller inaktivera den här funktionen. För innehåll med rådatainnehåll stöder vi TEXT, JSON, HTML, XML och JavaScript. |

## <a name="success-criteria"></a>Framgångskriterier

|Inställning| Förklaring
|----|----|----|
| **Tidsgräns för test** |Minska det här värdet för att få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade statuskoden som räknas som lyckad. 200 är koden som anger att en normal webbsida har returnerats.|
| **Innehållsmatchning** | En sträng som "Välkommen!" Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehållsmatchning** |

## <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära realtid (förhandsversion)** | Vi rekommenderar att du använder aviseringar i nära realtid. Den här typen av avisering konfigureras när tillgänglighetstestet har skapats.  |
|**Tröskelvärde för aviseringsplats**|Vi rekommenderar minst 3/5 platser. Den optimala relationen mellan tröskelvärdet för aviseringsplats och antalet testplatser är tröskelvärdet för aviseringsplats för   =  **testplatser – 2,** med minst fem testplatser.|

## <a name="location-population-tags"></a>Taggar för platspopulation

Följande populationstaggar kan användas för attributet geo-plats när du distribuerar ett test av tillgänglighets-URL-ping med Azure Resource Manager.

#### <a name="azure-gov"></a>Azure Gov

| Visningsnamn   | Befolkningsnamn     |
|----------------|---------------------|
| USGov Virginia | usgov-va-azr        |
| USGov Arizona  | usgov-phx-azr       |
| USGov Texas    | usgov-tx-azr        |
| USDoD, östra     | usgov-ddeast-azr    |
| USDoD, centrala  | usgov-ddcentral-azr |

#### <a name="azure"></a>Azure

| Visningsnamn                           | Befolkningsnamn   |
|----------------------------------------|-------------------|
| Australien, östra                         | emea-au-syd-edge  |
| Brasilien, södra                           | latam-br-gru-edge |
| Central US                             | us-fl-mia-edge    |
| Asien, östra                              | apac-hk-hkn-azr   |
| East US                                | us-va-azr     |
| Frankrike, södra (tidigare Frankrike, centrala) | emea-ch-z chaz-edge  |
| Frankrike, centrala                         | emea-fr-pra-edge  |
| Japan, östra                             | apac-jp-kaw-edge  |
| Europa, norra                           | emea-gb-db3-azr   |
| USA, norra centrala                       | us-il-ch1-azr     |
| USA, södra centrala                       | us-tx-sn1-azr     |
| Sydostasien                         | apac-sg-sin-azr   |
| Storbritannien, västra                                | emea-se-sto-edge  |
| Europa, västra                            | emea-nl-ams-azr   |
| USA, västra                                | us-ca-färdc-azr     |
| Storbritannien, södra                               | emea-ru-msa-edge  |

## <a name="see-your-availability-test-results"></a>Visa tillgänglighetstestresultat

Tillgänglighetstestresultat kan visualiseras med vyer för både linjediagram och punktdiagram.

Efter några minuter klickar du på **Uppdatera för** att se dina testresultat.

:::image type="content" source="./media/monitor-web-app-availability/availability-refresh-002.png" alt-text="Skärmbild som visar sidan Tillgänglighet med knappen Uppdatera markerad.":::

Punktdiagramsvyn visar exempel på testresultaten som innehåller information om diagnostiska teststeg. Testmotorn lagrar diagnosinformation för tester som innehåller fel. För lyckade tester lagras diagnosinformation för en delmängd av körningarna. Hovra över någon av de gröna/röda punkterna för att se testet, testnamnet och platsen.

:::image type="content" source="./media/monitor-web-app-availability/availability-scatter-plot-003.png" alt-text="Linjevy." border="false":::

Välj ett visst test, en viss plats eller minska tidsperioden för att visa fler resultat för en viss tidsperiod som du är intresserad av. Använd Sökutforskaren för att visa resultat från alla körningar, eller använd analysfrågor om du vill köra anpassade rapporter för dessa data.

## <a name="inspect-and-edit-tests"></a>Granska och redigera tester

Om du vill redigera, inaktivera tillfälligt eller ta bort ett test klickar du på ellipserna bredvid ett testnamn. Det kan ta upp till 20 minuter innan konfigurationsändringarna sprids till alla testagenter när en ändring har gjorts.

:::image type="content" source="./media/monitor-web-app-availability/edit.png" alt-text="Visa testinformation. Redigera och inaktivera ett webbtest." border="false":::

Du kanske vill inaktivera tillgänglighetstester eller varningsreglerna som är associerade med dem medan du utför underhåll på tjänsten.

## <a name="if-you-see-failures"></a>Om du ser fel

Välj en röd punkt.

:::image type="content" source="./media/monitor-web-app-availability/end-to-end.png" alt-text="Skärmbild av fliken transaktionsinformation från start till slut." border="false":::

Från ett tillgänglighetstestresultat kan du se transaktionsinformationen för alla komponenter. Här kan du:

* Granska felsökningsrapporten för att avgöra vad som kan ha orsakat att testet misslyckades men programmet fortfarande är tillgängligt.
* Kontrollera de svar som mottas från servern.
* Diagnostisera fel med korrelerad telemetri på serversidan som samlats in under bearbetningen av det misslyckade tillgänglighetstestet.
* Logga ett problem eller arbetsobjekt i Git eller Azure Boards för att spåra problemet. Buggen innehåller en länk till den här händelsen.
* Öppna resultatet av webbtestet i Visual Studio.

Mer information om transaktionsdiagnostik från slutet till slut finns i dokumentationen [för transaktionsdiagnostik.](./transaction-diagnostics.md)

Klicka på undantagsraden för att se information om undantaget på serversidan som gjorde att det syntetiska tillgänglighetstestet misslyckades. Du kan också hämta [ögonblicksbilden av felsökningen](./snapshot-debugger.md) för mer omfattande diagnostik på kodnivå.

:::image type="content" source="./media/monitor-web-app-availability/open-instance-4.png" alt-text="Diagnostik på serversidan.":::

Förutom rådataresultat kan du även visa två viktiga tillgänglighetsmått i [Metrics Explorer](../essentials/metrics-getting-started.md):

1. Tillgänglighet: antal procent av testerna som lyckades av alla testkörningar.
2. Testets varaktighet: genomsnittlig tid för alla testkörningar.

## <a name="automation"></a>Automation

* [Konfigurera ett tillgänglighetstest automatiskt med hjälp av PowerShell-skript](./powershell.md#add-an-availability-test).
* Konfigurera en [webhook](../alerts/alerts-webhooks.md) som anropas när en avisering genereras.


## <a name="next-steps"></a>Nästa steg

* [Tillgänglighetsaviseringar](availability-alerts.md)
* [Webbtester i flera steg](availability-multistep.md)
* [Felsökning](troubleshoot-availability.md)
