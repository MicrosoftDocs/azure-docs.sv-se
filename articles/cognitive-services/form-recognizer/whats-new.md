---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i Formigenkänning API.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: e952d481daf53b1806dc3cfbb658c8c0c21f6984
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516305"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD036 -->
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

Tjänsten Formigenkänning uppdateras kontinuerligt. Använd den här artikeln för att hålla dig uppdaterad med funktionsförbättringar, korrigeringar och dokumentationsuppdateringar.

## <a name="april-2021"></a>April 2021
<!-- markdownlint-disable MD029 -->

### <a name="sdk-updates-api--version-21-preview3"></a>SDK-uppdateringar (API-version 2.1-preview.3)

### <a name="c-version-310-beta4"></a>**C# version 3.1.0-beta.4**

* **Nya metoder för att analysera data från identitetsdokument:**

   **[StartRecognizeIdDocumentsFromUriAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   **[StartRecognizeIdDocumentsAsync](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizeiddocumentsasync?view=azure-dotnet-preview&preserve-view=true)**

   En lista över fältvärden finns i _Fält som_ [extraherats](concept-identification-cards.md#fields-extracted) i vår Formigenkänning dokumentation.

* Utökade uppsättningen dokumentspråk som kan anges för metoden **[StartRecognizeContent.](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecontent?view=azure-dotnet-preview&preserve-view=true)**

* **Ny egenskap `Pages` som stöds av följande klasser:**

   **[RecognizeBusinessCardsOptions](/dotnet/api/azure.ai.formrecognizer.recognizebusinesscardsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormsOptions](/dotnet/api/azure.ai.formrecognizer.recognizecustomformsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/dotnet/api/azure.ai.formrecognizer.recognizeinvoicesoptions?view=azure-dotnet-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/dotnet/api/azure.ai.formrecognizer.recognizereceiptsoptions?view=azure-dotnet-preview&preserve-view=true)**</br>

   Med `Pages` egenskapen kan du välja enskilda sidor eller ett sidintervall för PDF- och TIFF-dokument med flera sidor. För enskilda sidor anger du sidnumret, till exempel `3` . För ett sidintervall (till exempel sida 2 och sidor 5–7) anger du p-åldersnumren och intervallen avgränsade med kommatecken: `2, 5-7` .    

* **Ny egenskap `ReadingOrder` som stöds för följande klass:**

   **[RecognizeContentOptions](/dotnet/api/azure.ai.formrecognizer.recognizecontentoptions?view=azure-dotnet-preview&preserve-view=true)**

  Egenskapen är en valfri parameter som gör att du kan ange vilken läsordningsalgoritm– eller – som ska användas för `ReadingOrder` `basic` att ordna `natural` extrahering av textelement. Om inget värde anges är standardvärdet `basic` .

#### <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

* Klienten använder som standard den senaste versionen av tjänsten som stöds, som för närvarande är **2.1-preview.3.**

* **[StartRecognizeCustomForms-metoden](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient.startrecognizecustomforms?view=azure-dotnet-preview&preserve-view=true#Azure_AI_FormRecognizer_FormRecognizerClient_StartRecognizeCustomForms_System_String_System_IO_Stream_Azure_AI_FormRecognizer_RecognizeCustomFormsOptions_System_Threading_CancellationToken_)** kastar nu en `RequestFailedException()` när en ogiltig fil skickas.

### <a name="java-version-310-beta3"></a>**Java version 3.1.0-beta.3**

* **Nya metoder för att analysera data från identitetsdokument:**

  **[beginRecognizeIdDocumentsFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocumentsfromurl?view=azure-java-preview&preserve-view=true)**

  **[beginRecognizeIdDocuments](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizeiddocuments?view=azure-java-preview&preserve-view=true)**

   En lista över fältvärden finns i _Fält som_ [extraherats](concept-identification-cards.md#fields-extracted) i vår Formigenkänning dokumentation.

* **Stöd för bitmappsavbildningsfil (.bmp) för anpassade formulär och träningsmetoder `FormContentType` i uppräkning**:

* `image/bmp`

* **Ny egenskap `Pages` som stöds av följande klasser:**

   **[RecognizeBusinessCardsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizebusinesscardsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeCustomFormOptions](/java/api/com.azure.ai.formrecognizer.models.recognizecustomformsoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeInvoicesOptions](/java/api/com.azure.ai.formrecognizer.models.recognizeinvoicesoptions?view=azure-java-preview&preserve-view=true)**</br>
   **[RecognizeReceiptsOptions](/java/api/com.azure.ai.formrecognizer.models.recognizereceiptsoptions?view=azure-java-preview&preserve-view=true)**</br>

  Med `Pages` egenskapen kan du välja enskilda sidor eller ett sidintervall för PDF- och TIFF-dokument med flera sidor. För enskilda sidor anger du sidnumret, till exempel `3` . För ett sidintervall (till exempel sida 2 och sidorna 5–7) anger du sidnumren och intervallen avgränsade med kommatecken: `2, 5-7` .

* **Stöd för bitmappavbildningsfil (.bmp) för anpassade formulär och träningsmetoder i [fälten FormContentType](/java/api/com.azure.ai.formrecognizer.models.formcontenttype?view=azure-java-preview&preserve-view=true#fields)**:

  `image/bmp`

* **Nytt `ReadingOrder` nyckelordsargument som stöds för följande metoder:**

* **[beginRecognizeContent](https://docs.microsoft.com/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontent?view=azure-java-preview&preserve-view=true)**</br>
**[beginRecognizeContentFromUrl](/java/api/com.azure.ai.formrecognizer.formrecognizerclient.beginrecognizecontentfromurl?view=azure-java-preview&preserve-view=true)**</br>

   Nyckelordsargumentet är en valfri parameter som gör att du kan ange vilken läsordningsalgoritm– eller – som ska användas för att ordna `ReadingOrder` `basic` `natural` extrahering av textelement. Om inget värde anges är standardvärdet `basic` .

* Klienten använder som standard den senaste versionen av tjänsten som stöds, som för närvarande är **2.1-preview.3.**

### <a name="javascript-version-310-beta3"></a>**JavaScript version 3.1.0-beta.3**

* **Nya metoder för att analysera data från identitetsdokument:**

    **[beginRecognizeIdDocumentsFromUrl](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocumentsFromUrl_string__BeginRecognizeIdDocumentsOptions_)**

    **[beginRecognizeIdDocuments](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-preview&preserve-view=true&branch=main#beginRecognizeIdDocuments_FormRecognizerRequestBody__BeginRecognizeIdDocumentsOptions_)**

    En lista över fältvärden finns i _Fält som_ [extraherats](concept-identification-cards.md#fields-extracted) i vår Formigenkänning dokumentation.

* **Nya fältvärden som lagts till i FieldValue-gränssnittet**:

    `gender`– möjliga värden är `M` `F` eller `X` .</br>
   `country`– möjliga värden följer [landskodssträngen ISO alpha-3](https://www.iso.org/obp/ui/#search) med tre bokstäver.

* **Nytt alternativ `pages` som stöds av alla metoder för formulärigenkänning (anpassade formulär och alla fördefinierade modeller). Med argumentet kan du välja enskilda sidor eller ett sidintervall för PDF- och TIFF-dokument med flera sidor. För enskilda sidor anger du sidnumret, till exempel `3` . För ett sidintervall (till exempel sida 2 och sidorna 5–7) anger du sidnumren och intervallen avgränsade med kommatecken: `2, 5-7` .

* Stöd har lagts till för **[en ReadingOrder-typ](/javascript/api/@azure/ai-form-recognizer/readingorder?view=azure-node-preview&preserve-view=true)** för innehållsigenkänningsmetoderna. Med det här alternativet kan du styra vilken algoritm som tjänsten använder för att avgöra hur identifierade rader med text ska sorteras. Du kan ange vilken algoritm för `basic` läsordning, eller , som ska användas för att ordna `natural` extrahering av textelement. Om inget värde anges är standardvärdet `basic` .

* Dela **[FormField-typen](/javascript/api/@azure/ai-form-recognizer/formfield?view=azure-node-preview&preserve-view=true)** i flera olika gränssnitt. Detta bör inte orsaka API-kompatibilitetsproblem förutom i vissa gränsfall (odefinierad valueType).

* Har migrerats till **2.1-preview.3** Formigenkänning för alla REST API anrop.

### <a name="python-version--310b4"></a>**Python version 3.1.0b4**

* **Nya metoder för att analysera data från identitetsdokument:**

  **[begin_recognize_id_documents_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  **[begin_recognize_id_documents](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true)**

  En lista över fältvärden finns i _Fält_ [som extraherats](concept-identification-cards.md#fields-extracted) i vår Formigenkänning dokumentation.

* **Nya fältvärden som lagts till [i FieldValueType-uppräkning](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.fieldvaluetype?view=azure-python-preview&preserve-view=true)**:

   gender – möjliga värden `M` `F` är eller `X` .

  country – möjliga värden följer [ISO alfa-3 landskoder](https://www.iso.org/obp/ui/#search).

* **Stöd för Image File (.bmp) för anpassade formulär och träningsmetoder i [FormContentType-uppräkning](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formcontenttype?view=azure-python-preview&preserve-view=true)**:

    bild/bmp

* **Nytt `pages` nyckelordsargument som stöds av följande metoder:**

    **[begin_recognize_receipts](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true&branch=main#begin-recognize-receipts-receipt----kwargs-)**

    **[begin_recognize_receipts_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-receipts-from-url-receipt-url----kwargs-)**

   **[begin_recognize_business_cards](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-business-card----kwargs-)**

   **[begin_recognize_business_cards_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-business-cards-from-url-business-card-url----kwargs-)**

   **[begin_recognize_invoices](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-invoice----kwargs-)**

   **[begin_recognize_invoices_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-invoices-from-url-invoice-url----kwargs-)**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

  **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Med `pages` nyckelordsargumentet kan du välja enskilda sidor eller ett sidintervall för PDF- och TIFF-dokument med flera sidor. För enskilda sidor anger du sidnumret, till exempel `3` . För ett sidintervall (till exempel sida 2 och sidorna 5–7) anger du sidnumren och intervallen avgränsade med kommatecken: `2, 5-7` .

* **Nytt `readingOrder` nyckelordsargument som stöds för följande metoder:**

   **[begin_recognize_content](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-form----kwargs-)**

   **[begin_recognize_content_from_url](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python-preview&preserve-view=true#begin-recognize-content-from-url-form-url----kwargs-)**

   Nyckelordsargumentet är en valfri parameter som gör att du kan ange vilken läsordningsalgoritm– eller – som ska användas för att ordna `readingOrder` `basic` `natural` extrahering av textelement. Om inget värde anges är standardvärdet `basic` .

## <a name="march-2021"></a>Mars 2021

**Formigenkänning v2.1 offentlig förhandsversion 3 är nu tillgänglig.** v2.1-preview.3 har släppts, inklusive följande funktioner:

* **Ny förbyggd ID-modell** Med den nya fördefinierade ID-modellen kan kunderna ta ID:n och returnera strukturerade data för att automatisera bearbetningen. Den kombinerar våra kraftfulla OCR-funktioner (optisk teckenläsning) med ID Understanding-modeller för att extrahera viktig information från pass och amerikanska drivrutinslicenser, till exempel namn, födelsedatum, utfärdandedatum, förfallodatum med mera.

  [Läs mer om den fördefinierade ID-modellen](concept-identification-cards.md)

   :::image type="content" source="./media/id-canada-passport-example.png" alt-text="passport-exempel" lightbox="./media/id-canada-passport-example.png":::

* **Extrahering av radobjekt för den fördefinierade fakturamodellen** – Den fördefinierade fakturamodellen stöder nu extrahering av radobjekt. Den extraherar nu fullständiga objekt och deras delar – beskrivning, mängd, kvantitet, produkt-ID, datum med mera. Med ett enkelt API/SDK-anrop kan du extrahera användbara data från dina fakturor – text, tabell, nyckel/värde-par och radobjekt.

   [Läs mer om den fördefinierade fakturamodellen](concept-invoices.md)

* **Övervakad** tabelletiketter och träning, tomvärdesetiketter – Förutom [Formigenkänning:s](https://techcommunity.microsoft.com/t5/azure-ai/enhanced-table-extraction-from-documents-with-form-recognizer/ba-p/2058011)senaste funktioner för automatisk tabellhämtning för djupinlärning kan kunder nu märka och träna på tabeller. Den här nya versionen innehåller möjligheten att märka och träna radobjekt/tabeller (dynamiska och fasta) och träna en anpassad modell för att extrahera nyckel/värde-par och radobjekt. När en modell har tränats extraherar modellen radobjekt som en del av JSON-utdata i avsnittet documentResults.

    :::image type="content" source="./media/table-labeling.png" alt-text="Tabelletiketter" lightbox="./media/table-labeling.png":::

    Förutom att etikettera tabeller kan du nu märka tomma värden och regioner. Om vissa dokument i din träningsuppsättning inte har värden för vissa fält kan du märka dem så att din modell kan extrahera värden korrekt från analyserade dokument.

* **Stöd för 66 nya språk –** Formigenkänning layout-API och anpassade modeller stöder nu 73 språk.

  [Läs mer Formigenkänning om språkstöd](language-support.md)

* **Naturlig läsordning,** handskriftsklassificering och sidval – Med den här uppdateringen kan du välja att hämta textradsutdata i naturlig läsordning i stället för standardordningen från vänster till höger och uppifrån och ned. Använd den nya frågeparametern readingOrder och ställ in den på "naturligt" värde för mer användarvänliga läsordningsutdata. För latinska språk klassificerar Formigenkänning textrader som handskriven stil eller inte och ger en förtroendepoäng.

* **Kvalitetsförbättringar för fördefinierade kvittomodeller** Den här uppdateringen innehåller många kvalitetsförbättringar för den fördefinierade kvittomodellen, särskilt kring extrahering av radobjekt.

## <a name="november-2020"></a>November 2020

### <a name="new-features"></a>Nya funktioner

**Formigenkänning v2.1 allmänt tillgänglig förhandsversion 2.** v2.1-preview.2 har släppts, inklusive följande funktioner:

- **Ny förbyggd fakturamodell** – Med den nya fördefinierade fakturamodellen kan kunderna ta fakturor i olika format och returnera strukturerade data för att automatisera fakturabearbetningen. Den kombinerar våra kraftfulla OCR-funktioner (optisk teckenläsning) med fakturaförståelse av djupinlärningsmodeller för att extrahera viktig information från fakturor på engelska. Den extraherar nyckeltext, tabeller och information som kund, leverantör, faktura-ID, fakturans förfallodatum, totalsumma, förfallobelopp, skattebelopp, frakt till och fakturera till.

  > [Läs mer om den fördefinierade fakturamodellen](concept-invoices.md)

  :::image type="content" source="./media/invoice-example.jpg" alt-text="fakturaexempel" lightbox="./media/invoice-example.jpg":::

- **Förbättrad tabelluttrahering** – Formigenkänning nu förbättrad tabellrahering, som kombinerar våra kraftfulla OCR-funktioner (optisk teckenläsning) med en modell för extrahering av djupinlärningstabeller. Formigenkänning kan extrahera data från tabeller, inklusive komplexa tabeller med sammanfogade kolumner, rader, inga kantlinjer med mera.

  :::image type="content" source="./media/tables-example.jpg" alt-text="tabellexempel" lightbox="./media/tables-example.jpg":::


  > [Läs mer om extrahering av layout](concept-layout.md)

- **Uppdatering av klientbibliotek** – [](quickstarts/client-library.md) De senaste versionerna av klientbiblioteken för .NET, Python, Java och JavaScript stöder Formigenkänning 2.1 API.
- **Nytt språk som stöds:** Japanska – Följande nya språk stöds nu: `AnalyzeLayout` för och : Japanska ( `AnalyzeCustomForm` `ja` ). [Stöd för språk](language-support.md)
- **Stilindikator för textrad (handskriven/annan) (endast** latinska språk) – Formigenkänning matar nu ut ett objekt som klassificerar om varje textrad är handskriven eller inte, tillsammans med en `appearance` förtroendepoäng. Den här funktionen stöds endast för latinska språk.
- **Kvalitetsförbättringar –** Extraheringsförbättringar inklusive förbättringar av ensiffrig extrahering.
- **Ny try-it-out-funktion** i Formigenkänning Sample and Labeling Tool – Möjlighet att prova fördefinierade modeller för faktura, kvitto och visitkort och layout-API:et med hjälp av Formigenkänning-exempeletikettverktyget. Se hur dina data extraheras utan att du behöver skriva någon kod.

  > [Prova Formigenkänning exempelverktyget](https://fott-preview.azurewebsites.net/)

  ![FOTT-exempel](./media/ui-preview.jpg)

- **Feedbackloop** – När du analyserar filer via exempeletikettverktyget kan du nu även lägga till den i träningsuppsättningen och justera etiketterna om det behövs och träna för att förbättra modellen.
- **Automatisk etikett för** dokument – Etiketterar automatiskt ytterligare dokument baserat på tidigare märkta dokument i projektet.

## <a name="august-2020"></a>Augusti 2020

### <a name="new-features"></a>Nya funktioner

**Formigenkänning den offentliga förhandsversionen av v2.1 är nu tillgänglig.** V2.1-preview.1 har släppts, inklusive följande funktioner:


- **REST API finns tillgänglig –** Visa [referensen v2.1-preview.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeBusinessCardAsync)
- **Nya språk som** stöds Utöver [](language-support.md) engelska stöds nu följande språk: för och : Engelska ( ), kinesiska `Layout` `Train Custom Model` `en` (förenklad) ( `zh-Hans` ), nederländska ( ), franska ( `nl` ), tyska ( ), italienska ( ), portugisiska ( ) och spanska ( `fr` `de` `it` `pt` `es` ).
- **Kryssruta/markeringsmarkering** – Formigenkänning stöder identifiering och extrahering av markeringsmarkeringar, till exempel kryssrutor och alternativknappar. Markeringar extraheras i och du kan nu även märka och träna i Träna med etiketter för `Layout` `Train Custom Model`  -   att extrahera nyckelvärdepar för markeringsmarkeringar.
- **Model Compose** – gör att flera modeller kan bestå och anropas med ett enda modell-ID. När du skickar ett dokument som ska analyseras med ett sammansatt modell-ID utförs först ett klassificeringssteg för att dirigera det till rätt anpassad modell. Model Compose är tillgängligt för `Train Custom Model`  -  _Train with labels (Träna med etiketter)._
- **Modellnamn** – lägg till ett eget namn i dina anpassade modeller för enklare hantering och spårning.
- **[Ny förbyggd modell för visitkort för extrahering](concept-business-cards.md)** av vanliga fält på engelska, språk visitkort.
- **[Nya språk för färdiga](concept-receipts.md)** kvitton utöver EN-US, stöd är nu tillgängligt för EN-AU, EN-CA, EN-GB, EN-IN
- **Kvalitetsförbättringar för** `Layout` , Träna utan etiketter `Train Custom Model`  -  _och_ _Träna med etiketter_.

**v2.0** innehåller följande uppdatering:

- [Klientbiblioteken](quickstarts/client-library.md) för NET, Python, Java och JavaScript har blivit allmänt tillgängliga.

**Nya exempel** finns på GitHub.

- Spelboken [Knowledge Extraction Recipes – Forms](https://github.com/microsoft/knowledge-extraction-recipes-forms) samlar in metodtips från verkliga Formigenkänning-kundengagemang och tillhandahåller användbara kodexempel, checklistor och exempelpipelines som används för att utveckla dessa projekt.
- [Exempeletikettverktyget har uppdaterats](https://github.com/microsoft/OCR-Form-Tools) för att stödja de nya v2.1-funktionerna. Se den [här snabbstarten](quickstarts/label-tool.md) för att komma igång med verktyget.
- Exemplet [intelligent kiosk](https://github.com/microsoft/Cognitive-Samples-IntelligentKiosk/blob/master/Documentation/FormRecognizer.md) Formigenkänning visar hur du integrerar och tränar utan `Analyze Receipt` `Train Custom Model`  -  _etiketter._

## <a name="july-2020"></a>Juli 2020

### <a name="new-features"></a>Nya funktioner
<!-- markdownlint-disable MD004 -->
* **v2.0-referens** tillgänglig – Visa [v2.0](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) API-referensen och de uppdaterade API:erna för [.NET,](/dotnet/api/overview/azure/ai.formrecognizer-readme) [Python,](/python/api/overview/azure/) [Java](/java/api/overview/azure/ai-formrecognizer-readme)och [JavaScript.](/javascript/api/overview/azure/)
* **Tabellförbättringar och extraheringsförbättringar** – innehåller noggrannhetsförbättringar och förbättringar av tabellutökningar, särskilt möjligheten att lära sig tabellrubriker och strukturer i anpassad träna utan _etiketter_.

* **Valutastöd –** Identifiering och extrahering av globala valutasymboler.
* **Azure Gov** – Formigenkänning är nu också tillgängligt i Azure Gov.
* **Förbättrade säkerhetsfunktioner:**
  * **Bring Your Own Key** – Formigenkänning krypterar automatiskt dina data när de sparas i molnet för att skydda dem och för att hjälpa dig att uppfylla organisationens säkerhets- och efterlevnadsåtaganden. Som standard använder din prenumeration krypteringsnycklar som hanteras av Microsoft. Nu kan du också hantera din prenumeration med dina egna krypteringsnycklar. [Kund hanterade nycklar, även kallade BYOK (Bring Your Own Key),](./encrypt-data-at-rest.md)ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.
  * **Privata slutpunkter** – Gör att du i ett virtuellt nätverk på [ett säkert sätt kan komma åt data via en Private Link.](../../private-link/private-link-overview.md)

## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Nya funktioner

* **CopyModel-API har lagts till i klient-API:er** – Nu kan du använda klient-API:erna för att kopiera modeller från en prenumeration till en annan. Allmän information [om den här funktionen finns](./disaster-recovery.md) i Back up and recover models ( Back up and recover models) om du vill ha allmän information om den här funktionen.
* **Azure Active Directory integrering –** Nu kan du använda dina autentiseringsuppgifter för Azure AD för att autentisera Formigenkänning-klientobjekten i SDK:erna.
* **SDK-specifika ändringar** – Den här ändringen omfattar både mindre funktionsändringar och icke-mindre ändringar. Mer information finns i SDK-ändringsloggar.
  * [Ändringslogg för C# SDK Förhandsversion 3](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Ändringslogg för Python SDK Preview 3](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Ändringslogg för Java SDK Preview 3](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Ändringslogg för JavaScript SDK Förhandsversion 3](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nya funktioner

* **SDK-stöd för Formigenkänning API v2.0** – Den här månaden har vi utökat vårt tjänststöd för att inkludera en förhandsversion av SDK för Formigenkänning v2.0 (förhandsversion). Använd länkarna nedan för att komma igång med val av språk:
  * [.NET SDK](/dotnet/api/overview/azure/ai.formrecognizer-readme)
  * [Java SDK](/java/api/overview/azure/ai-formrecognizer-readme)
  * [Python SDK](/python/api/overview/azure/ai-formrecognizer-readme)
  * [JavaScript SDK](/javascript/api/overview/azure/ai-form-recognizer-readme)

  Den nya SDK:n stöder alla funktioner i v2.0-REST API för Formigenkänning. Du kan till exempel träna en modell med eller utan etiketter och extrahera text, nyckelvärdepar och tabeller från dina formulär, extrahera data från kvitton med den färdiga kvittotjänsten och extrahera text och tabeller med layouttjänsten från dina dokument. Du kan dela din feedback om SDK:erna via [sdk-feedbackformuläret](https://aka.ms/FR_SDK_v1_feedback).

* **Kopiera anpassad modell** Nu kan du kopiera modeller mellan regioner och prenumerationer med hjälp av den nya funktionen Kopiera anpassad modell. Innan du anropar API:et Kopiera anpassad modell måste du först få behörighet att kopiera till målresursen genom att anropa kopieringsauktoriseringsåtgärden mot målresursslutpunkten.

  * [Generera en kopieringsauktorisering](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModelAuthorization) REST API
  * [Kopiera en anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/CopyCustomFormModel) REST API

### <a name="security-improvements"></a>Förbättringar av säkerhet

* Customer-Managed nycklar är nu tillgängliga för FormRecognizer. Mer information finns i [Datakryptering i vila för Formigenkänning](./encrypt-data-at-rest.md).
* Använd hanterade identiteter för åtkomst till Azure-resurser med Azure Active Directory. Mer information finns i [Auktorisera åtkomst till hanterade identiteter.](../authentication.md#authorize-access-to-managed-identities)

## <a name="march-2020"></a>Mars 2020

### <a name="new-features"></a>Nya funktioner

* **Värdetyper för etikettering** Nu kan du ange de typer av värden som du etiketterar med Formigenkänning exempeletikettverktyget. Följande värdetyper och variationer stöds för närvarande:
  * `string`
    * standard, `no-whitespaces` , `alphanumeric`
  * `number`
    * Standard `currency`
  * `date`
    * standard, `dmy` , `mdy` , `ymd`
  * `time`
  * `integer`

  Se guiden [exempeletikettverktyg för att](./quickstarts/label-tool.md#specify-tag-value-types) lära dig hur du använder den här funktionen.


* **Tabellvisualisering** Exempeletikettverktyget visar nu tabeller som identifierades i dokumentet. Med den här funktionen kan du visa de tabeller som har identifierats och extraherats från dokumentet före etikettering och analys. Den här funktionen kan vara på/av med hjälp av alternativet skikt.

  Följande bild är ett exempel på hur tabeller identifieras och extraheras:

  > [!div class="mx-imgBorder"]
  > ![Tabellvisualisering med exempeletikettverktyget](./media/whats-new/table-viz.png)

    De extraherade tabellerna är tillgängliga i JSON-utdata under `"pageResults"` .

  > [!IMPORTANT]
  > Etiketteringstabeller stöds inte. Om tabeller inte känns igen och extrateras automatiskt kan du bara märka dem som nyckel/värde-par. När du etiketterar tabeller som nyckel/värde-par ska du märka varje cell som ett unikt värde.

### <a name="extraction-enhancements"></a>Extraheringsförbättringar

Den här versionen innehåller extraheringsförbättringar och noggrannhetsförbättringar, särskilt möjligheten att märka och extrahera flera nyckel/värde-par i samma textrad.

### <a name="sample-labeling-tool-is-now-open-source"></a>Exempeletikettverktyget har nu öppen källkod

Det Formigenkänning exempeletikettverktyget är nu tillgängligt som ett projekt med öppen källkod. Du kan integrera den i dina lösningar och göra kundspecifika ändringar som uppfyller dina behov.

Mer information om Formigenkänning exempeletikettverktyget finns i dokumentationen på [GitHub.](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)

### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2

TLS 1.2 tillämpas nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services säkerhet](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

Den här versionen introducerar Formigenkänning 2.0 (förhandsversion). I avsnitten nedan hittar du mer information om nya funktioner, förbättringar och ändringar.

### <a name="new-features"></a>Nya funktioner

* **Anpassad modell**
  * **Träna med etiketter** Nu kan du träna en anpassad modell med manuellt märkta data. Den här metoden resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.
  * **Asynkront API** Du kan använda asynkrona API-anrop för att träna med och analysera stora datamängder och filer.
  * **Stöd för TIFF-filer** Nu kan du träna med och extrahera data från TIFF-dokument.
  * **Noggrannhetsförbättringar för extrahering**

* **Förbyggd kvittomodell**
  * **Tipsbelopp** Nu kan du extrahera tipsbelopp och andra handskrivna värden.
  * **Extrahering av radobjekt** Du kan extrahera radobjektsvärden från kvitton.
  * **Konfidensvärden** Du kan visa modellens konfidens för varje extraherat värde.
  * **Noggrannhetsförbättringar för extrahering**

* **Layout-extrahering** Nu kan du använda layout-API:et för att extrahera textdata och tabelldata från dina formulär.

### <a name="custom-model-api-changes"></a>API-ändringar för anpassad modell

Alla API:er för träning och användning av anpassade modeller har bytt namn och vissa synkrona metoder är nu asynkrona. Följande är större ändringar:

* Processen för att träna en modell är nu asynkron. Du initierar träning via **API-anropet /custom/models.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **custom/models/{modelID}** för att returnera träningsresultatet.
* Extrahering av nyckel/värde initieras nu av **API-anropet /custom/models/{modelID}/analyze.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **custom/models/{modelID}/analyzeResults/{resultID}** för att returnera extraheringens resultat.
* Åtgärds-ID:erna för åtgärden  Träna finns nu i platshuvudet för HTTP-svar, inte **i rubriken Operation-Location.**

### <a name="receipt-api-changes"></a>API-kvittoändringar

API:erna för att läsa försäljningskvitton har bytt namn.

* Extrahering av kvittodata initieras nu av **API-anropet /prebuilt/receipt/analyze.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **/prebuilt/receipt/analyzeResults/{resultID}** för att returnera extraheringens resultat.

### <a name="output-format-changes"></a>Ändringar i utdataformat

JSON-svaren för alla API-anrop har nya format. Vissa nycklar och värden har lagts till, tagits bort eller bytt namn. Se snabbstarter för exempel på aktuella JSON-format.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabbstart för](quickstarts/client-library.md) att komma igång med att skriva en formulärbearbetningsapp med Formigenkänning med valfri utvecklingsspråk.

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](./overview.md)