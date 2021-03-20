---
title: Extrahera information i Excel med Textanalys och energi automatisering
titleSuffix: Azure Cognitive Services
description: Lär dig hur du extraherar Excel-text utan att behöva skriva kod, med Textanalys och energi automatisering.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/27/2019
ms.author: aahi
ms.openlocfilehash: 197d28b2ac3d94b6639a6611b2919bdeb2b182e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359909"
---
# <a name="extract-information-in-excel-using-text-analytics-and-power-automate"></a>Extrahera information i Excel med Textanalys och energi automatisering 

I den här självstudien skapar du ett energi flöde för att extrahera text i ett Excel-kalkylblad utan att behöva skriva kod. 

Det här flödet tar ett kalkyl blad med problem som har rapporter ATS om en lägenhet komplex och klassificera dem i två kategorier: rör dragning och annat. Det kommer också att extrahera namn och telefonnummer till de innehavare som skickade dem. Slutligen kommer flödet att lägga till den här informationen i Excel-bladet. 

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> * Använd automatisk energi för att skapa ett flöde
> * Ladda upp Excel-data från OneDrive för företag
> * Extrahera text från Excel och skicka den till API för textanalys 
> * Använd informationen från API: et för att uppdatera ett Excel-blad.

## <a name="prerequisites"></a>Förutsättningar

- Ett Microsoft Azure-konto. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/cognitive-services/) eller [Logga](https://portal.azure.com/)in.
- En Textanalys resurs. Om du inte har någon kan du [skapa en i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) och använda den kostnads fria nivån för att slutföra den här självstudien.
- Den [nyckel och slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som genererades åt dig under registreringen.
- Ett kalkyl blad som innehåller klient problem. Exempel data tillhandahålls på GitHub
- Microsoft 365, med OneDrive för företag.

## <a name="add-the-excel-file-to-onedrive-for-business"></a>Lägg till Excel-filen till OneDrive för företag

Hämta exempel Excel-filen från [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/TextAnalytics/sample-data/ReportedIssues.xlsx). Den här filen måste lagras i ditt OneDrive för företag-konto.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/example-data.png" alt-text="Exempel från Excel-filen.":::

Problemen rapporteras i rå text. Vi använder API för textanalys organisationens namngivna enhets igenkänning för att extrahera personens namn och telefonnummer. Sedan kommer flödet att leta efter ordet "rör" i beskrivningen för att kategorisera problemen. 

## <a name="create-a-new-power-automate-workflow"></a>Skapa ett nytt arbets flöde för energi automatisering

Gå till [webbplatsen för Energis par automatisering](https://preview.flow.microsoft.com/)och logga in. Klicka sedan på **skapa** och **schemalagt flöde**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-creation.png" alt-text="Skärmen skapa flöde.":::


På sidan **Bygg ett schemalagt flöde** initierar du ditt flöde med följande fält:

|Fält |Värde  |
|---------|---------|
|**Flödesnamn**     | **Schemalagd granskning** eller ett annat namn.         |
|**Startar**     |  Ange aktuellt datum och aktuell tid.       |
|**Upprepa varje**     | **1 timme**        |

## <a name="add-variables-to-the-flow"></a>Lägg till variabler i flödet

> [!NOTE]
> Om du vill se en avbildning av det slutförda flödet kan du ladda ned det från [GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/TextAnalytics/flow-diagrams). 

Skapa variabler som representerar den information som ska läggas till i Excel-filen. Klicka på **nytt steg** och Sök efter **initiera variabel**. Gör detta fyra gånger för att skapa fyra variabler.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/initialize-variables.png" alt-text="Initiera variabler.":::

Lägg till följande information till de variabler som du har skapat. De representerar kolumnerna i Excel-filen. Om några variabler är komprimerade kan du klicka på dem för att expandera dem.

| Action |Namn   | Typ | Värde |
|---------|---------|---|---|
| Initiera variabel | var_person | Sträng | Person |
| Initiera variabel 2 | var_phone | Sträng | Phone_Number |
| Initiera variabel 3 | var_plumbing | Sträng | göra grovjobbet |
| Initiera variabel 4 | var_other | Sträng | other | 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/flow-variables.png" alt-text="information som finns i Flow-variablerna":::

## <a name="read-the-excel-file"></a>Läs Excel-filen

Klicka på **nytt steg** och skriv **Excel** och välj sedan **list rader som finns i en tabell** i listan med åtgärder.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows.png" alt-text="Lägg till Excel-rader.":::

Lägg till Excel-filen i flödet genom att fylla i fälten i den här åtgärden. Den här självstudien kräver att filen har överförts till OneDrive för företag.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/list-excel-rows-options.png" alt-text="Fyll Excel-rader":::

Klicka på **nytt steg** och Lägg till en **Apply för varje** åtgärd.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action.png" alt-text="Lägg till ett Apply-kommando.":::

Klicka på **Välj utdata från föregående steg**. I rutan dynamiskt innehåll som visas väljer du **värde**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/select-output.png" alt-text="Välj utdata från Excel-filen.":::

## <a name="send-a-request-to-the-text-analytics-api"></a>Skicka en begäran till API för textanalys

Om du inte redan har gjort det måste du skapa en [textanalys-resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) i Azure Portal.

### <a name="create-a-text-analytics-connection"></a>Skapa en Textanalys anslutning

I rutan **tillämpa** på klickar du på **Lägg till en åtgärd**. Gå till din Textanalys resurs **nyckel-och slut punkts** sida i Azure Portal och hämta nyckeln och slut punkten för din textanalys resurs.

Ange följande information i ditt flöde för att skapa en ny Textanalys-anslutning.

> [!NOTE]
> Om du redan har skapat en Textanalys anslutning och vill ändra din anslutnings information, klickar du på ellipsen i det övre högra hörnet och klickar på **+ Lägg till ny anslutning**.

| Fält           | Värde                                                                                                             |
|-----------------|-------------------------------------------------------------------------------------------------------------------|
| Anslutningsnamn | Ett namn på anslutningen till din Textanalys-resurs. Till exempel `TAforPowerAutomate`. |
| Kontonyckel     | Nyckeln till din Textanalys-resurs.                                                                                   |
| Webbplats-URL        | Slut punkten för Textanalys resursen.                                                       |

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-credentials.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde.":::

## <a name="extract-the-excel-content"></a>Extrahera Excel-innehållet 

När anslutningen har skapats söker du efter **textanalys** och väljer **entiteter**. Då extraheras information från kolumnen Beskrivning i problemet.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/extract-info.png" alt-text="Lägg till Textanalys entiteter.":::

Klicka **i textfältet och** Välj **Beskrivning** från det dynamiska innehålls fönster som visas. Ange `en` för språk. (Klicka på Visa avancerade alternativ om du inte ser språket)

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/description-from-dynamic-content.png" alt-text="Lägg till Textanalys inställningar.":::


## <a name="extract-the-person-name"></a>Extrahera personens namn

Härnäst kommer vi att hitta entiteten persons typ i Textanalys utdata. I listan **tillämpa** på klickar du på **Lägg till en åtgärd** och skapar en annan **som gäller för varje** åtgärd. Klicka i text rutan och välj **entiteter** i fönstret för dynamiskt innehåll som visas.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-2.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 11.2":::

Klicka på **Lägg till en åtgärd** i den nyligen skapade åtgärden **för varje 2** -åtgärd och Lägg till en **villkors** kontroll.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/create-condition.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 3":::

I villkors fönstret klickar du på den första text rutan. I fönstret dynamiskt innehåll söker du efter **typ av entitet** och väljer den.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-entities-value.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 4":::

Kontrol lera att den andra rutan är inställd på **är lika** med. Välj sedan den tredje rutan och Sök efter `var_person` i fönstret med dynamiskt innehåll. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/choose-variable-value.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 5":::

I villkoret **om ja** skriver du i Excel och väljer sedan **Uppdatera en rad**.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 3-6":::

Ange Excel-informationen och uppdatera **nyckel kolumnen**, **nyckel värde** och **PersonName** fält. Detta lägger till namnet som identifieras av API: t i Excel-bladet. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/yes-column-action-options.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 3,7":::

## <a name="get-the-phone-number"></a>Hämta telefonnumret

Minimera åtgärden **tillämpa på varje 2** genom att klicka på namnet. Lägg sedan till en annan **Apply för varje** åtgärd, som tidigare. Det får namnet **Använd för varje 3**. Välj text rutan och Lägg till **entiteter** som utdata för den här åtgärden. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-3.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 7,8":::

I **tillämpa på varje 3 lägger du** till en **villkors** kontroll. Det får namnet **villkor 2**. I den första text rutan söker du efter och lägger till **entitetstypen** från fönstret med dynamiskt innehåll. Se till att rutan Center är inställt på **är lika** med. Ange sedan i den högra text rutan `var_phone` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-options.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 1.9":::

Lägg till en åtgärd för att **Uppdatera en rad** i villkoret **om ja** . Ange sedan den information som vi gjorde ovan för kolumnen telefonnummer i Excel-bladet. Detta lägger till telefonnumret som identifieras av API: t i Excel-bladet. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-2-yes-column.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 10":::


## <a name="get-the-plumbing-issues"></a>Få anslutnings problem

Minimera **gäller för varje 3** genom att klicka på namnet. Skapa sedan en annan **som gäller för var och** en i den överordnade åtgärden. Välj text rutan och Lägg till **entiteter** som utdata för den här åtgärden från fönstret med dynamiskt innehåll. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/add-apply-action-4.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 11.3":::


Sedan kontrollerar flödet om problemet är beskrivningen från tabell raden i Excel innehåller ordet "rörning". Om ja, läggs "rörning" i kolumnen IssueType. Annars anger vi "Övrigt".

Lägg till en **villkors** kontroll inuti alternativet **tillämpa på varje 4** -åtgärd. Det kommer att heta **villkor 3**. I den första text rutan söker du efter och lägger till **Beskrivning** från Excel-filen med hjälp av det dynamiska innehålls fönstret. Se till att rutan i mitten **innehåller**. Leta sedan upp och välj i den högra text rutan `var_plumbing` . 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/condition-3-options.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 12.5":::


I villkoret **om ja** klickar du på **Lägg till en åtgärd** och väljer **Uppdatera en rad**. Ange sedan informationen som tidigare. I kolumnen IssueType väljer du `var_plumbing` . När du gör det används en "rör"-etikett på raden.

I **om inget** villkor klickar du på **Lägg till en åtgärd** och väljer **Uppdatera en rad**. Ange sedan informationen som tidigare. I kolumnen IssueType väljer du `var_other` . Då används etiketten "Övrigt" på raden.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/plumbing-issue-condition.png" alt-text="Lägg till Textanalys autentiseringsuppgifter till ditt flöde. 13.4":::

## <a name="test-the-workflow"></a>Testa arbetsflödet

I det övre högra hörnet på skärmen klickar du på **Spara** och sedan på **testa**. Välj  **jag utför utlösnings åtgärden**. Klicka på **spara & test**, **Kör Flow** och sedan på **Slutför**.

Excel-filen kommer att uppdateras i ditt OneDrive-konto. Det ser ut ungefär så här.

> [!div class="mx-imgBorder"] 
> :::image type="content" source="../media/tutorials/excel/updated-excel-sheet.png" alt-text="Det uppdaterade Excel-kalkylbladet.":::

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska fler lösningar](../text-analytics-user-scenarios.md)
