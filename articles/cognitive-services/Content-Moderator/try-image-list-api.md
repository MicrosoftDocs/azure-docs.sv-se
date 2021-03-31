---
title: Måttliga bilder med anpassade listor och API-konsolen – Content Moderator
titleSuffix: Azure Content Moderator
description: 'Du kan använda API: et för List hantering i Azure Content Moderator för att skapa anpassade listor med avbildningar.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800156"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Måttlig med anpassade bild listor i API-konsolen

Du kan använda [API: et för List hantering](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) i Azure Content moderator för att skapa anpassade listor med avbildningar. Använd de anpassade listorna med avbildningar med bild redigerings-API: et. Avbildnings redigerings åtgärden utvärderar avbildningen. Om du skapar anpassade listor jämförs den också med bilderna i dina anpassade listor. Du kan använda anpassade listor för att blockera eller tillåta avbildningen.

> [!NOTE]
> Det finns en maxgräns på **5 bildlistor** där varje lista **inte får överstiga 10 000 bilder**.
>

Du kan använda API: et för List hantering för att utföra följande uppgifter:

- Skapa en lista.
- Lägg till avbildningar i en lista.
- Skärm bilder mot bilderna i en lista.
- Ta bort bilder från en lista.
- Ta bort en lista.
- Redigera listinformation.
- Uppdatera indexet så att ändringar i listan inkluderas i en ny genomsökning.

## <a name="use-the-api-console"></a>Använda API-konsolen
Innan du kan testa API: et i online-konsolen behöver du din prenumerations nyckel. Detta finns på fliken **Inställningar** i rutan **OCP-APIM-Subscription-Key** . Mer information finns i [Översikt](overview.md).

## <a name="refresh-search-index"></a>Uppdatera Sök index

När du har gjort ändringar i en avbildnings lista måste du uppdatera dess index för att ändringarna ska ingå i framtida genomsökningar. Det här steget påminner om hur en sökmotor på Skriv bordet (om den är aktive rad) eller en Webbs öknings motor kontinuerligt uppdaterar sitt index för att inkludera nya filer eller sidor.

1. I [list hanterings-API-referensen bild lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)väljer du **bild listor** på den vänstra menyn och väljer sedan **Uppdatera Sök index**.

   Sidan **avbildnings listor – uppdatera Sök index** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 
 
    ![Bild listor – uppdatera Sök index sidan Val av område](images/test-drive-region.png)

    **Avbildnings listorna – uppdatera API-konsolen för Sök index** öppnas.

3. I rutan **listId** anger du List-ID. Ange din prenumerations nyckel och välj sedan **Skicka**.

   ![Bild listor – uppdatera Sök index konsol rutan svar innehålls ruta](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Skapa en bild lista

1. Gå till [Bild List hantering API-referensen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   Sidan **avbildnings listor – skapa** öppnas. 

3. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats.

   ![Bild listor – skapa val av sid region](images/test-drive-region.png)

   **Avbildnings listorna – skapa API-** konsolen öppnas.
 
4. I rutan **OCP-APIM-Subscription-Key** anger du din prenumerations nyckel.

5. I rutan **begär ande innehåll** anger du värden för **namn** (till exempel min lista) och **Beskrivning**.

   ![Avbildnings listor-skapa namn och beskrivning för begär Ande för konsol](images/try-terms-list-create-1.png)

6. Använd plats hållare för nyckel/värde-par för att tilldela mer beskrivande metadata till listan.

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

   Lägg till List-metadata som nyckel/värde-par och inte de faktiska bilderna.
 
7. Välj **Skicka**. Listan har skapats. Observera det **ID-** värde som är associerat med den nya listan. Du behöver det här ID: t för andra funktioner i hantering av bild listor.

   ![Bild listor – innehålls rutan skapa konsol svar visar List-ID](images/try-terms-list-create-2.png)
 
8. Lägg sedan till avbildningar i listan. Välj **bild** på menyn till vänster och välj sedan **Lägg till bild**.

   Sidan **avbildning – Lägg till bild** öppnas. 

9. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats.

   ![Bild – Lägg till val av sid region för bild](images/test-drive-region.png)

   API **-konsolen avbildning – Lägg till bild** öppnas.
 
10. I rutan **listId** anger du det List-ID som du genererade och anger sedan URL: en för den avbildning som du vill lägga till. Ange din prenumerations nyckel och välj sedan **Skicka**.

11. Om du vill kontrol lera att avbildningen har lagts till i listan väljer du **bild** på den vänstra menyn och väljer **Hämta alla bild-ID**.

    API **-konsolen bild-hämta alla bild-ID** : n öppnas.
  
12. I rutan **listId** anger du List-ID och anger sedan din prenumerations nyckel. Välj **Skicka**.

    ![Bild – hämta alla avbildnings-ID: n för konsol svars innehåll visar de bilder som du har angett](images/try-image-list-create-11.png)
 
10. Lägg till några fler bilder. Nu när du har skapat en anpassad lista med avbildningar kan du prova att [utvärdera bilder](try-image-api.md) med hjälp av listan med anpassade avbildningar. 

## <a name="delete-images-and-lists"></a>Ta bort bilder och listor

Det är enkelt att ta bort en bild eller en lista. Du kan använda API: et för att utföra följande uppgifter:

- Ta bort en avbildning. (**Avbildning – ta bort**)
- Ta bort alla bilder i en lista utan att ta bort listan. (**Bild-ta bort alla bilder**)
- Ta bort en lista och allt dess innehåll. (**Bild listor – ta bort**)

Det här exemplet tar bort en enskild avbildning:

1. I [bild listans hanterings-API-referens](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)väljer du **bild** på den vänstra menyn och väljer sedan **ta bort**. 

   Sidan **avbildnings borttagning** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats. 

   ![Bild-ta bort val av sid region](images/test-drive-region.png)
 
   API **-konsolen för avbildnings borttagning** öppnas.
 
3. I rutan **listId** anger du ID för listan att ta bort en avbildning från.  Detta är det antal som returneras i **bilden – hämta alla avbildnings-ID-** konsolen för en lista. Ange sedan **ImageId** för den avbildning som ska tas bort. 

I vårt exempel är List-ID **58953**, värdet för **ContentSource**. Avbildningens ID är **59021**, värdet för **ContentIds**.

1. Ange din prenumerations nyckel och välj sedan **Skicka**.

1. Verifiera att avbildningen har tagits bort med hjälp av konsolen **avbildning – hämta alla avbildnings-ID: n** .
 
## <a name="change-list-information"></a>Ändra List information

Du kan redigera en listas namn och beskrivning och lägga till objekt i metadata.

1. I [list hanterings-API-referensen bild lista](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)väljer du **bild listor** på den vänstra menyn och väljer sedan **uppdaterings information**. 

   Sidan **avbildnings listor – uppdaterings information** öppnas.

2. För **öppna API test-konsolen** väljer du den region som bäst beskriver din plats.  

    ![Bild listor – val av sid region för uppdaterings information](images/test-drive-region.png)

    **Avbildnings listorna – API-konsolen för uppdaterings information** öppnas.
 
3. I rutan **listId** anger du List-ID och anger sedan din prenumerations nyckel.

4. I rutan **begär ande innehåll** , gör dina ändringar och välj sedan knappen **Skicka** på sidan.

   ![Bild listor – uppdaterings information konsol begär ande text redigeringar](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Nästa steg

Använd REST API i koden eller börja med [avbildningen listar .net snabb start](image-lists-quickstart-dotnet.md) för att integrera med ditt program.
