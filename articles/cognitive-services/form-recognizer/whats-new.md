---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i formulärets tolknings-API.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 81115f5a9ed802f1d07c45ec928dc4b84ea2917b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048756"
---
<!-- markdownlint-disable MD024 -->
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

Formulär igenkännings tjänsten uppdateras regelbundet. Använd den här artikeln för att hålla dig uppdaterad med funktions förbättringar, korrigeringar och dokumentations uppdateringar.

## <a name="march-2021"></a>Mars 2021

**Formulär igenkänning v 2.1 Public Preview 3 är nu tillgängligt.** v 2.1 – för hands version. 3 har släppts, inklusive följande funktioner:

- **Ny fördefinierad ID-modell** Med den nya fördefinierade ID-modellen kan kunder ta med ID och returnera strukturerade data till automatiserad bearbetning. Den kombinerar vår kraftfulla OCR-kapacitet (optisk tecken läsning) med ID förstå modeller för att extrahera viktig information från Passport och amerikanska driv rutins licenser, till exempel namn, födelse datum, utfärdandedatum, förfallo datum och mycket annat.

  [Lär dig mer om den fördefinierade ID-modellen](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="Passport-exempel" lightbox="./media/id-canada-passport-example.png":::

- **Extrahering av rad objekt för fördefinierad faktura modell** – en inbyggd faktura modell stöder nu extrahering av rad objekt. nu extraheras fullständiga objekt och deras delar – beskrivning, belopp, kvantitet, produkt-ID, datum och mer. Med ett enkelt API/SDK-anrop kan du extrahera användbara data från dina fakturor – text, tabell, nyckel/värde-par och rad objekt.

   [Läs mer om den färdiga faktura modellen](concept-invoices.md)

- **Övervakad tabell etikettering och utbildning, Tom-värde-etiketter** – utöver formulär tolkens [tillstånds känsliga djup inlärnings funktioner för automatisk tabell extrahering](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011), kan kunderna märka och träna i tabeller. Den här nya versionen innehåller möjlighet att märka och träna på rad objekt/tabeller (dynamiska och fasta) och träna en anpassad modell för att extrahera nyckel/värde-par och rad objekt. När en modell har tränats extraherar modellen rad objekt som en del av JSON-utdata i avsnittet documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Tabell etikettering" lightbox="./media/table-labeling.png":::

    Förutom att märka tabeller, kan du nu etikettera tomma värden och regioner. om vissa dokument i din tränings uppsättning inte har värden för vissa fält kan du använda detta så att din modell kommer att kunna extrahera värden korrekt från analyserade dokument.

- **Stöd för 66 nya språk** – formulär tolkens layout-API och anpassade modeller stöder nu 73-språk.

  [Läs mer om språk stöd för formulär igenkänning](language-support.md)

- **Naturlig Läs ordning, hand SKRIFTS klassificering och sid val** – med den här uppdateringen kan du välja att hämta text radens utdata i den naturliga Läs ordningen i stället för standard ordningen från vänster till höger och till-till-nederkant. Använd den nya readingOrder för frågor och Ställ in den på "naturlig"-värde för en mer användarvänlig läsning av order utdata. För latinska språk klassificerar formulär tolken text rader som handskrivet format eller inte och ger en säkerhets poäng.

- **Färdiga förbättringar av inbetalnings modell kvalitet** Den här uppdateringen innehåller ett antal kvalitets förbättringar för en fördefinierad kvitto modell, särskilt genom extrahering av rad objekt.

## <a name="november-2020"></a>November 2020

### <a name="new-features"></a>Nya funktioner

**Formulär igenkänning v 2.1 Public Preview 2 är nu tillgängligt.** v 2.1 – för hands version. 2 har släppts, inklusive följande funktioner: 

- **Ny fördefinierad faktura modell** – den nya inbyggda faktura modellen gör det möjligt för kunder att ta fakturor i en rad olika format och returnera strukturerade data för att automatisera faktura bearbetningen. Den kombinerar vår kraftfulla OCR-kapacitet (optisk tecken läsning) med faktura om djup inlärnings modeller för att extrahera viktig information från fakturor på engelska. Den extraherar text, tabeller och information, till exempel kund, leverantör, faktura-ID, förfallo datum för faktura, totalt, förfallet belopp, moms belopp, leverera till, fakturera till och mycket annat.

  > [Läs mer om den färdiga faktura modellen](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="faktura exempel" lightbox="./media/invoice-example.jpg":::

- **Förbättrad** tabell extrahering – formulär igenkänning ger nu förbättrad tabell extrahering, som kombinerar vår kraftfulla OCR-kapacitet (optisk tecken läsning) med en djup inlärnings tabell extraherings modell. Formulär tolken kan extrahera data från tabeller, inklusive komplexa tabeller med sammanfogade kolumner, rader, utan kant linjer och mer. 
 
  :::image type="content" source="./media/tables-example.jpg" alt-text="tabell exempel" lightbox="./media/tables-example.jpg":::

 
  > [Lär dig mer om extrahering av layout](concept-layout.md)

- **Uppdatering av klient bibliotek** – de senaste versionerna av [klient biblioteken](quickstarts/client-library.md) för .net, python, Java och Java Script har stöd för formulär tolken 2,1 API.
- **Nytt språk som stöds: japanska** – följande nya språk stöds nu: for `AnalyzeLayout` och `AnalyzeCustomForm` : Japanese ( `ja` ). [Stöd för språk](language-support.md)
- **Text linje formats indikering (handskriven/övrigt) (endast latinska språk)** – formulär tolken visar nu ett `appearance` objekt som klassificerar om varje textrad är handskriven eller inte, tillsammans med ett säkerhets resultat. Den här funktionen stöds bara för latinska språk.
- **Kvalitets förbättringar** – extraherings förbättringar, inklusive förbättringar av en enda siffer extrahering.
- **Ny funktion för try-inaktivitet i formulär tolkens exempel-och etikett verktyg** – möjlighet att testa färdiga fakturor, inleverans-och visitkorts modeller och layout-API med hjälp av formulär tolkens exempel etikett verktyg. Se hur dina data extraheras utan att du behöver skriva någon kod.

  > [Prova verktyget formulär igenkännings exempel](https://fott-preview.azurewebsites.net/)

  ![FOTT-exempel](./media/ui-preview.jpg)
  
- **Feedback-slinga** – när du analyserar filer via verktyget exempel etikettering kan du nu även lägga till det i inlärnings uppsättningen och justera etiketterna om det behövs och träna för att förbättra modellen.
- **Automatiska etikett dokument** – etiketterar automatiskt ytterligare dokument baserat på tidigare märkta dokument i projektet.

## <a name="august-2020"></a>Augusti 2020

### <a name="new-features"></a>Nya funktioner

**Formulär igenkänning v 2.1 offentlig för hands version är nu tillgänglig.** V 2.1 – för hands version. 1 har släppts, inklusive följande funktioner: 


- **REST API referens är tillgänglig** – Visa [v 2.1 – för hands version. 1 referens](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync) 
- **Nya språk som stöds utöver engelska**, följande [språk](language-support.md) stöds nu: for `Layout` och `Train Custom Model` : engelska ( `en` ), kinesiska (förenklad) ( `zh-Hans` ), nederländska () `nl` , franska (), tyska (), `fr` italienska (), portugisiska ( `de` `it` `pt` ) och spanska ( `es` ).
- **Kryss ruta och identifiering av markerings märke** – formulär tolken stöder identifiering och extrahering av markerings märken som kryss rutor och alternativ knappar. Markerings markeringar extraheras i `Layout` och du kan nu också märka och träna i `Train Custom Model`  -  _träna med etiketter_ för att extrahera nyckel värde par för markerings märken. 
- **Modell sammanställning** – tillåter att flera modeller skapas och anropas med ett enda modell-ID. När ett dokument skickas för att analyseras med ett sammansatt modell-ID utförs ett klassificerings steg först för att dirigera det till rätt anpassad modell. Modellens Skriv sätt är tillgängligt för `Train Custom Model`  -  _träna med etiketter_.
- **Modell namn** – Lägg till ett eget namn i dina anpassade modeller för enklare hantering och spårning.
- Ny fördefinierad **[modell för visitkort](concept-business-cards.md)** som används för att extrahera vanliga fält på engelska, språk visitkort.
- **[Nya språk för förbyggda kvitton](concept-receipts.md)** , förutom en-US, support är nu tillgängligt för en-au, en-ca, en-GB, en-i
- **Kvalitets förbättringar** för `Layout` , `Train Custom Model`  -  _träna utan etiketter_ och _träna med etiketter_.

**v 2.0** innehåller följande uppdatering:

- [Klient biblioteken](quickstarts/client-library.md) för net, python, Java och Java Script har angett allmän tillgänglighet. 

**Nya exempel** finns på GitHub. 

- [Recepten för kunskaps extrahering – formulär Spelbok](https://github.com/microsoft/knowledge-extraction-recipes-forms) samlar in bästa praxis från riktiga formulär som kund engagemang och ger användbara kod exempel, check listor och exempel på pipelines som används för att utveckla dessa projekt. 
- [Verktyget för samplings etiketter](https://github.com/microsoft/OCR-Form-Tools) har uppdaterats för att stödja de nya v 2.1-funktionerna. Se den här [snabb](quickstarts/label-tool.md) starten för att komma igång med verktyget. 
- Formulär tolks exemplet i [intelligent kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) visar hur du integrerar `Analyze Receipt` och `Train Custom Model`  -  _tränar utan etiketter_.

## <a name="july-2020"></a>Juli 2020

### <a name="new-features"></a>Nya funktioner
<!-- markdownlint-disable MD004 -->
* **v 2.0-referensen är tillgänglig** – Visa [v 2.0 API-referensen](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) och uppdaterade SDK: er för [.net](/dotnet/api/overview/azure/ai.formrecognizer-readme), [python](/python/api/overview/azure/), [Java](/java/api/overview/azure/ai-formrecognizer-readme)och [Java Script](/javascript/api/overview/azure/).
* Förbättringar av **tabell förbättringar och extrahering av utdrag** – innehåller precisions förbättringar och förbättringar av tabell extrahering, särskilt möjligheten att lära sig tabell rubriker och strukturer i _anpassade tåg utan etiketter_. 

* **Valuta support** – identifiering och extrahering av globala valuta symboler.
* **Azure gov** -tolken är nu också tillgänglig i Azure gov.
* **Förbättrade säkerhetsfunktioner**: 
  * **Ta med din egen nyckel** igenkännings funktion krypterar dina data automatiskt när de behålls i molnet för att skydda dem och hjälpa dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Du kan nu även hantera din prenumeration med dina egna krypterings nycklar. [Kundhanterade nycklar, som även kallas för att ta med din egen nyckel (BYOK)](./encrypt-data-at-rest.md), erbjuder större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.  
  * **Privata slut punkter** – gör att du på ett virtuellt nätverk (VNet) kan [komma åt data på ett säkert sätt via en privat länk.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Nya funktioner

* **CopyModel-API har lagts till i klient-SDK** : er kan du nu använda klient-SDK: er för att kopiera modeller från en prenumeration till en annan. Se [säkerhetskopiera och återställa modeller](./disaster-recovery.md) för allmän information om den här funktionen.
* **Azure Active Directory integration** – nu kan du använda dina autentiseringsuppgifter för Azure AD för att autentisera dina klient objekt i SDK: er för formulär igenkänning.
* **SDK-/regionsspecifika ändringar** – det här inkluderar både mindre funktions tillägg och avbrytande ändringar. Se SDK-ChangeLogs för mer information.
  * [C# SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java Script SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nya funktioner

* **SDK-stöd för Forms IGENKÄNNING API v 2.0 offentlig för hands version** – den här månaden expanderade vi tjänst supporten för att inkludera en för hands versions-SDK för formulär igenkänning v 2.0 (för hands version). Använd länkarna nedan för att komma igång med ditt val av språk: 
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Den nya SDK: n stöder alla funktioner i v 2.0-REST API för formulär igenkänning. Du kan till exempel träna en modell med eller utan etiketter och extrahera text, nyckel värde par och tabeller från dina formulär, extrahera data från kvitton med den förbyggda tjänsten för inaktuella inleveranser och extrahera text och tabeller med layout tjänsten från dina dokument. Du kan dela din feedback på SDK: erna via [feedback-formuläret för SDK](https://aka.ms/FR_SDK_v1_feedback).

* **Kopiera anpassad modell** Nu kan du kopiera modeller mellan regioner och prenumerationer med hjälp av funktionen ny kopiera anpassad modell. Innan du anropar API: t för kopiera anpassad modell måste du först få behörighet att kopiera till mål resursen genom att anropa åtgärden Kopiera auktorisering mot mål resursens slut punkt.

  * [Skapa en kopierings auktorisering](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Kopiera en anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Förbättringar av säkerhet

* Customer-Managed nycklar är nu tillgängliga för FormRecognizer. Mer information finns i [data kryptering i vila för formulär igenkänning](./encrypt-data-at-rest.md).
* Använd hanterade identiteter för åtkomst till Azure-resurser med Azure Active Directory. Mer information finns i [ge åtkomst till hanterade identiteter](../authentication.md#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Mars 2020

### <a name="new-features"></a>Nya funktioner

* **Värde typer för etiketter** Nu kan du ange vilka typer av värden du märker med formulär tolkens exempel etikett verktyg. Följande värde typer och varianter stöds för närvarande:
  * `string`
    * standard, `no-whitespaces``alphanumeric`
  * `number`
    * objekt `currency`
  * `date` 
    * standard, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  Mer information om hur du använder den här funktionen finns i guiden [exempel etikett verktyg](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Tabell visualisering** Verktyget för att märka exempel visar nu tabeller som har identifierats i dokumentet. Med den här funktionen kan du visa tabeller som har identifierats och extraherats från dokumentet, innan du märker och analyserar. Den här funktionen kan växlas på/av med alternativet lager.

  Följande bild är ett exempel på hur tabeller identifieras och extraheras:

  > [!div class="mx-imgBorder"]
  > ![Tabell visualisering med verktyget för exempel etiketter](./media/whats-new/table-viz.png)

    De extraherade tabellerna är tillgängliga i JSON-utdata under `"pageResults"` .

  > [!IMPORTANT]
  > Etiketterade tabeller stöds inte. Om tabeller inte känns igen och extrated automatiskt kan du bara märka dem som nyckel/värde-par. När du har namngett tabeller som nyckel/värde-par, förser du varje cell med ett unikt värde.

### <a name="extraction-enhancements"></a>Förbättringar av extrahering

I den här versionen ingår extraherings förbättringar och precisions förbättringar, i synnerhet möjlighet att märka och extrahera flera nyckel/värde-par i samma textrad. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Exempel verktyget för etikettering är nu öppen källkod

Formulär tolkens exempel etikett verktyg är nu tillgängligt som ett projekt med öppen källkod. Du kan integrera den i dina lösningar och göra kundspecifika ändringar så att de passar dina behov.

Mer information om verktyget formulär igenkännings exempel etikettering finns i dokumentationen som finns på [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2

TLS 1,2 upprätthålls nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

I den här versionen introduceras formulär tolken 2,0 (för hands version). I avsnitten nedan finns mer information om nya funktioner, förbättringar och ändringar. 

### <a name="new-features"></a>Nya funktioner

* **Anpassad modell**
  * **Träna med etiketter** Nu kan du träna en anpassad modell med manuellt märkta data. Den här metoden resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.
  * **Asynkront API** Du kan använda asynkrona API-anrop för att träna med och analysera stora data uppsättningar och filer.
  * **Stöd för TIFF-fil** Nu kan du träna med och extrahera data från TIFF-dokument.
  * **Förbättringar av extraherings precision**

* **Fördefinierad kvitto modell**
  * **Tip-belopp** Nu kan du extrahera Tip-mängder och andra handskrivna värden.
  * **Extrahering av rad objekt** Du kan extrahera rad objekt värden från inleveranser.
  * **Konfidens intervall** Du kan visa modellens förtroende för varje extraherat värde.
  * **Förbättringar av extraherings precision**

* **Extrahering av layout** Du kan nu använda layout-API: et för att extrahera text data och tabell data från formulären.

### <a name="custom-model-api-changes"></a>Ändringar i anpassade modell-API

Alla API: er för utbildning och användning av anpassade modeller har bytt namn och vissa synkrona metoder är nu asynkrona. Följande är viktiga ändringar:

* Processen för att träna en modell är nu asynkron. Du initierar utbildning genom **/Custom/Models** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **Custom/Models/{modelID}** för att returnera inlärnings resultatet.
* Nyckel/värde-extrahering initieras nu av **/Custom/Models/{modelID}/Analyze** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **Custom/Models/{modelID}/analyzeResults/{resultID}** för att returnera extraherings resultatet.
* Åtgärds-ID för träna-åtgärden finns nu i **plats** rubriken för HTTP-svar, inte **Åtgärds platsens** huvud.

### <a name="receipt-api-changes"></a>Ändringar i kvitto-API

API: erna för läsning av försäljnings kvitton har bytt namn.

* Extrahering av kvitto data initieras nu av **/prebuilt/Receipt/Analyze** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **/prebuilt/Receipt/analyzeResults/{resultID}** för att returnera extraherings resultatet.

### <a name="output-format-changes"></a>Format ändringar

JSON-Svaren för alla API-anrop har nya format. Vissa nycklar och värden har lagts till, tagits bort eller bytt namn. Se snabb starter för exempel på aktuella JSON-format.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabb start](quickstarts/client-library.md) för att komma igång med att skriva en app för bearbetning av formulär med formulär igenkänning på det utvecklings språk du väljer.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)