---
title: Skapa en anpassad klassificerings-och klassificerings regel (förhands granskning)
description: Lär dig hur du skapar anpassade klassificeringar för att definiera data typer i din datafastighet som är unika för din organisation i Azure avdelningens kontroll.
author: animukherjee
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 3/24/2021
ms.openlocfilehash: 7d6baee49250509e50cdeeea8cf8ca6cec5b362d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222180"
---
# <a name="custom-classifications-in-azure-purview"></a>Anpassade klassificeringar i Azure avdelningens kontroll

Den här artikeln beskriver hur du kan skapa anpassade klassificeringar för att definiera data typer i din datafastighet som är unika för din organisation. Det beskriver också hur du skapar anpassade klassificerings regler som gör att du kan hitta angivna data i din datafastighet.

## <a name="default-classifications"></a>Standard klassificeringar

Azure avdelningens kontroll-Data Catalog innehåller en stor uppsättning standard klassificeringar som representerar vanliga personliga data typer som du kan ha i din datafastighet.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/classification.png" alt-text="Välj klassificering" border="true":::

Du kan också skapa anpassade klassificeringar om någon av standard klassificeringarna inte uppfyller dina behov.

## <a name="steps-to-create-a-custom-classification"></a>Steg för att skapa en anpassad klassificering

Följ dessa steg om du vill skapa en anpassad klassificering:

1. Från din katalog väljer du **hanterings Center** på den vänstra menyn.

2. Välj **klassificeringar** under **hantering av metadata**.

3. Välj **+ ny**

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/new-classification.png" alt-text="Ny klassificering" border="true":::

Fönstret **Lägg till ny klassificering** öppnas där du kan ge klassificeringen ett namn och en beskrivning. Det är en bra idé att använda en namn utrymmes konvention, till exempel `your company name.classification name` .

Microsofts system klassificeringar grupperas under det reserverade `MICROSOFT.` namn området. Ett exempel är **Microsoft. Stat. USA. PERSON \_ \_ nummer**.

Namnet på din klassificering måste börja med en bokstav följt av en sekvens med bokstäver, siffror och punkt tecken (.) eller under streck. Inga blank steg är tillåtna. När du skriver genererar UX ett eget namn automatiskt. Det egna namnet är det som användarna ser när de använder det på en till gång i katalogen.

Om du vill att namnet ska vara kort skapar systemet det egna namnet baserat på följande logik:

- Alla de två sista segmenten i namn området trimmas.

- Höljet justeras så att den första bokstaven i varje ord kapitaliseras. Alla andra bokstäver konverteras till gemener.

- Alla under streck ( \_ ) ersätts med blank steg.

Exempel: om du har namngett klassificeringen **contoso.HR. MEDARBETAR \_ -ID**, det egna namnet lagras i systemet som **HR. anställnings-ID**.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/contoso-hr-employee-id.png" alt-text="Contoso.hr.employee_id" border="true":::

Välj **OK** så läggs den nya klassificeringen till i din klassificerings lista.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/custom-classification.png" alt-text="Anpassad klassificering" border="true":::

Om du väljer klassificeringen i listan öppnas klassificerings informations sidan. Här hittar du all information om klassificeringen.

Informationen omfattar hur många instanser det finns, det formella namnet, associerade klassificerings regler (om sådana finns) och ägar namnet.

:::image type="content" source="media/create-a-custom-classification-and-classification-rule/select-classification.png" alt-text="Välj klassificering" border="true":::

## <a name="custom-classification-rules"></a>Anpassade klassificerings regler

Katalog tjänsten innehåller en uppsättning standard klassificerings regler som används av skannern för att automatiskt identifiera vissa data typer. Du kan också lägga till egna anpassade klassificerings regler för att identifiera andra typer av data som du kanske är intresse rad av att hitta i din datafastighet. Den här funktionen kan vara kraftfull när du försöker hitta data i din datafastighet.

Anta till exempel \' att ett företag som heter Contoso har medarbetar-ID: n som är standardiserade i företaget med ordet \" medarbetare \" följt av ett GUID för att skapa anställd {GUID}. En instans av ett medarbetar-ID ser till exempel ut som `EMPLOYEE9c55c474-9996-420c-a285-0d0fc23f1f55` .

Contoso kan konfigurera genomsöknings systemet för att hitta instanser av dessa ID: n genom att skapa en anpassad klassificerings regel. De kan ange ett reguljärt uttryck som matchar data mönstret, i det här fallet `\^Employee\[A-Za-z0-9\]{8}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{4}-\[A-Za-z0-9\]{12}\$` . Om data vanligt vis finns i en kolumn som de känner till, t. ex. anställnings \_ -ID eller Anställningsnr, kan de lägga till ett reguljärt uttryck för kolumn mönster för att göra sökningen ännu mer exakt. Ett exempel på regex är anställnings \_ -ID \| Anställningsnr.

Genomsöknings systemet kan sedan använda den här regeln för att undersöka faktiska data i kolumnen och kolumn namnet för att försöka identifiera varje instans av där det finns ett mönster för medarbetar-ID.

## <a name="steps-to-create-a-custom-classification-rule"></a>Steg för att skapa en anpassad klassificerings regel

Så här skapar du en anpassad klassificerings regel:

1. Skapa en anpassad klassificering genom att följa anvisningarna i föregående avsnitt. Du lägger till den här anpassade klassificeringen i klassificerings regel konfigurationen så att systemet använder det när den hittar en matchning i kolumnen.

2. Välj ikonen för **hanterings Center** .

3. Välj avsnittet **klassificerings regler** .

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/classificationrules.png" alt-text="Panelen klassificerings regler" border="true":::

4. Välj **Nytt**.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/newclassificationrule.png" alt-text="Lägg till ny klassificerings regel" border="true":::

5. Dialog rutan **ny klassificerings regel** öppnas. Fyll i fälten och bestäm om du vill skapa en **regel för reguljära uttryck** eller en **ord lista**.

   |Fält     |Beskrivning  |
   |---------|---------|
   |Name   |    Krävs. Det maximala värdet är 100 tecken.    |
   |Beskrivning      |Valfritt. Det maximala värdet är 256 tecken.    |
   |Klassificerings namn    | Krävs. Välj namnet på klassificeringen i list rutan för att be skannern att tillämpa den om en matchning hittas.        |
   |Tillstånd   |  Krävs. Alternativen är aktiverade eller inaktiverade. Aktive rad är standardvärdet.    |

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-classification-rule.png" alt-text="Skapa ny klassificerings regel" border="true":::

### <a name="creating-a-regular-expression-rule"></a>Skapa en regel för reguljära uttryck

1. Om du skapar en regel för reguljära uttryck visas följande skärm bild. Du kan eventuellt ladda upp en fil som ska användas för att **generera föreslagna regex-mönster** för din regel.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/create-new-regex-rule.png" alt-text="Skapa ny regex-regel" border="true":::

1. Om du vill generera ett föreslaget regex-mönster när du har laddat upp en fil väljer du ett av de föreslagna mönstren och väljer **Lägg till i mönster** för att använda de föreslagna data-och kolumn mönstren. Du kan justera de föreslagna mönstren eller också kan du ange egna mönster utan att ladda upp en fil.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/suggested-regex.png" alt-text="Generera föreslaget regex" border="true":::

   |Fält     |Beskrivning  |
   |---------|---------|
   |Data mönster    |Valfritt. Ett reguljärt uttryck som representerar de data som lagras i data fältet. Gränsen är mycket stor. I det föregående exemplet testar data mönstren för ett anställnings-ID som är ett ord `Employee{GUID}` .  |
   |Kolumn mönster    |Valfritt. Ett reguljärt uttryck som representerar de kolumn namn som du vill matcha. Gränsen är mycket stor. |

1. Under **data mönster** kan du ange två tröskelvärden:

   - **Distinkt matchnings tröskel**: det totala antalet distinkta data värden som måste hittas i en kolumn innan skannern kör data mönstret på den. Det föreslagna värdet är 8. Det här värdet kan justeras manuellt i ett intervall från 2 till 32. Systemet måste ha det här värdet för att se till att kolumnen innehåller tillräckligt med data för skannern för att kunna klassificera den korrekt. En kolumn som innehåller flera rader som innehåller värdet 1 klassificeras till exempel inte. Kolumner som innehåller en rad med ett värde och resten av raderna har null-värden får inte heller klassificeras. Om du anger flera mönster gäller det här värdet för var och en av dem.

   - **Tröskelvärde för minsta matchning**: du kan använda den här inställningen för att ange den minsta procent andelen av distinkta data värdes matchningar i en kolumn som måste hittas av skannern för att klassificeringen ska tillämpas. Det föreslagna värdet är 60%. Du måste vara försiktig med den här inställningen. Om du minskar nivån under 60% kan du införa falska positiva klassificeringar i katalogen. Om du anger flera data mönster inaktive ras den här inställningen och värdet fastställs till 60%.

1. Nu kan du verifiera din regel och **skapa** den.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/verify-rule.png" alt-text="Verifiera regel innan du skapar" border="true":::

1. Testa klassificerings regeln innan du slutför skapande processen för att kontrol lera att den kommer att använda taggar för dina till gångar. Klassificeringarna i regeln tillämpas på de exempel data som laddas upp precis som i en genomsökning. Det innebär att alla system klassificeringar och din anpassade klassificering matchas mot data i filen.

   Indatafiler kan innehålla avgränsade filer (CSV, PSV, SSV, TSV), JSON eller XML-innehåll. Innehållet kommer att parsas baserat på fil namns tillägget för indatafilen. Avgränsade data kan ha ett fil namns tillägg som matchar någon av de nämnda typerna. Till exempel kan TSV-data finnas i en fil med namnet MySampleData.csv. Avgränsat innehåll måste också ha minst 3 kolumner.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-screen.png" alt-text="Testa regel innan du skapar" border="true":::

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/test-rule-uploaded-file-result-screen.png" alt-text="Visa tillämpade klassificeringar efter överföring av en test fil" border="true":::

### <a name="creating-a-dictionary-rule"></a>Skapa en ord lista regel

1. Om du skapar en ord lista regel visas följande skärm bild. Ladda upp en fil som innehåller alla möjliga värden för den klassificering som du skapar i en enda kolumn.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-rule.png" alt-text="Skapa ord lista regel" border="true":::

1. När ord listan har genererats kan du justera de distinkta Matchnings-och minimi matchnings trösklarna och skicka regeln.

- **Distinkt matchnings tröskel**: det totala antalet distinkta data värden som måste hittas i en kolumn innan skannern kör data mönstret på den. Tröskelvärdet för distinkt matchning har inget att göra med mönster matchning, men det är ett krav för mönster matchning. Det föreslagna värdet är 8. Det här värdet kan justeras manuellt i ett intervall från 2 till 32. Systemet måste ha det här värdet för att se till att kolumnen innehåller tillräckligt med data för skannern för att kunna klassificera den korrekt. En kolumn som innehåller flera rader som innehåller värdet 1 klassificeras till exempel inte. Kolumner som innehåller en rad med ett värde och resten av raderna har null-värden får inte heller klassificeras. Om du anger flera mönster gäller det här värdet för var och en av dem.

   :::image type="content" source="media/create-a-custom-classification-and-classification-rule/dictionary-generated.png" alt-text="Skapa ord lista regel med Dictionary-Generated markering." border="true":::

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat din klassificerings regel är den redo att läggas till i en skannings regel uppsättning så att genomsökningen använder regeln vid genomsökning. Mer information finns i [skapa en skannings regel uppsättning](create-a-scan-rule-set.md).
