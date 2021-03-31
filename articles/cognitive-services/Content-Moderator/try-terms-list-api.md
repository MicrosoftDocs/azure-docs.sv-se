---
title: Måttlig text med anpassade term listor – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Använd API för List hantering för att skapa anpassade listor med villkor som ska användas med API: et för text redigering.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800048"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Måttlig med anpassade term listor i API-konsolen

Den standardmässiga globala listan med termer i Azure Content Moderator räcker för de flesta modereringsbehov. Du kan dock behöva kontrollera termer som är specifika för din organisation. Till exempel vill du kanske tagga namn på konkurrenter för vidare granskning. 

Använd [API för List hantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) för att skapa anpassade listor med villkor som ska användas med API: et för text redigering. **Text skärms** åtgärden söker igenom texten efter svordomar och jämför även text mot anpassade och delade-blockeringslistor.

> [!NOTE]
> Det finns en maxgräns på **5 termlistor** där varje lista kan innehålla **högst 10 000 termer**.
>

Du kan använda API: et för List hantering för att utföra följande uppgifter:
- Skapa en lista.
- Lägga till termer i en lista.
- Kontrollera termer mot termer i en lista.
- Ta bort termer från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen

Innan du kan testa API: et i online-konsolen behöver du din prenumerations nyckel. Den här nyckeln finns på fliken **Inställningar** i rutan **OCP-APIM-Subscription-Key** . Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera Sök index

När du har gjort ändringar i en term lista måste du uppdatera dess index för att ändringarna ska ingå i framtida genomsökningar. Det här steget påminner om hur en sökmotor på Skriv bordet (om den är aktive rad) eller en Webbs öknings motor kontinuerligt uppdaterar sitt index för att inkludera nya filer eller sidor.

1. Välj **term listor** på den vänstra menyn i [term List HANTERINGs-API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)och välj sedan **Uppdatera Sök index**. 

   Sidan **giltighets listor – uppdatera Sök index** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Term listor – uppdatera Sök index sidan Val av område](images/test-drive-region.png)

   **Term listor – uppdatera API-konsolen för sökindex** öppnas.

3. I rutan **listId** anger du List-ID. Ange din prenumerations nyckel och välj sedan **Skicka**.

   ![Term visar API – uppdatera Sök index konsol rutan svar innehåll](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Skapa en termlista
1. Gå till [term List Management API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   Sidan **villkors listor – skapa** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Term listor – skapa val av sid region](images/test-drive-region.png)

   **Termen listor – skapa API-** konsolen öppnas.
 
3. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

4. I rutan **begär ande innehåll** anger du värden för **namn** (till exempel min lista) och **Beskrivning**.

   ![Term listor – namn och beskrivning för begäran om att skapa konsolen](images/try-terms-list-create-1.png)

5. Använd plats hållare för nyckel/värde-par för att tilldela mer beskrivande metadata till listan.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Lägg till List-metadata som nyckel/värde-par och inte faktiska villkor.
 
6. Välj **Skicka**. Listan har skapats. Observera det **ID-** värde som är associerat med den nya listan. Du behöver detta ID för andra hanterings funktioner för term listor.

   ![Term listor – innehålls rutan skapa konsol svar visar List-ID](images/try-terms-list-create-2.png)
 
7. Lägg till termer till min-lista. På den vänstra menyn väljer du **Lägg till term** under **term**. 

   Sidan **term – Lägg till term** öppnas. 

8. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Term – Lägg till term för sidans regions val](images/test-drive-region.png)

   **Termen – Lägg till term-** API-konsolen öppnas.
 
9. I rutan **listId** anger du det List-ID som du genererade och väljer ett värde för **språk**. Ange din prenumerations nyckel och välj sedan **Skicka**.

   ![Term – Lägg till terms-konsolens frågeparametrar](images/try-terms-list-create-3.png)
 
10. Om du vill kontrol lera att villkoret har lagts till i listan väljer du **term** i den vänstra menyn och väljer sedan **Hämta alla villkor**. 

    **Termen-hämta alla villkor-** API-konsolen öppnas.

11. I rutan **listId** anger du List-ID och anger sedan din prenumerations nyckel. Välj **Skicka**.

12. I rutan **svars innehåll** kontrollerar du de villkor som du har angett.

    ![Term: rutan hämta alla villkors svars innehåll visar de villkor som du har angett](images/try-terms-list-create-4.png)
 
13. Lägg till några fler villkor. Nu när du har skapat en anpassad lista med villkor kan du prova att [Skanna lite text](try-text-api.md) med hjälp av listan med anpassade villkor. 

## <a name="delete-terms-and-lists"></a>Ta bort termer och listor

Det är enkelt att ta bort en term eller en lista. Du använder API: et för att utföra följande uppgifter:

- Ta bort en term. (**Term-Delete**)
- Ta bort alla termer i en lista utan att ta bort listan. (**Term – ta bort alla villkor**)
- Ta bort en lista och allt dess innehåll. (**Term listor – ta bort**)

I det här exemplet tas en enstaka term bort.

1. I [term List hanterings-API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)väljer du **term** i den vänstra menyn och väljer sedan **ta bort**. 

   **Termen-Delete** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Term – ta bort val av sid område](images/test-drive-region.png)

   **Termen-ta bort** API-konsolen öppnas.
  
3. I rutan **listId** anger du ID: t för den lista som du vill ta bort en term från. Detta ID är det tal (i vårt exempel **122**) som returneras i **term listorna – hämta information** konsol för listan. Ange termen och välj ett språk.
 
   ![Term – ta bort parametrar för konsol frågor](images/try-terms-list-delete-1.png)

4. Ange din prenumerations nyckel och välj sedan **Skicka**.

5. För att kontrol lera att villkoret har tagits bort använder du **term listorna – hämta alla** konsoler.

   ![Term listor-rutan hämta alla konsol svars innehåll visar att termen tas bort](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Ändra List information

Du kan redigera en listas namn och beskrivning och lägga till objekt i metadata.

1. I [term List hanterings-API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)går du till den vänstra menyn och väljer **term listor** och väljer sedan **uppdaterings information**. 

   Sidan **villkors listor – uppdaterings information** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Term listor-val av sid region för uppdaterings information](images/test-drive-region.png)

   **Term listor – API-konsolen för uppdaterings information** öppnas.

3. I rutan **listId** anger du List-ID och anger sedan din prenumerations nyckel.

4. I rutan **begär ande innehåll** , gör dina ändringar och välj sedan **Skicka**.

   ![Term listor – information om hur du redigerar begär ande brödtext i uppdaterings konsolen](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller börja med [villkoret som listar .net snabb start](term-lists-quickstart-dotnet.md) för att integrera med ditt program.
