---
title: 'Snabb start: interaktiv kart sökning med Azure Maps'
description: 'Snabb start: Lär dig hur du skapar interaktiva, sökbar Maps. Se hur du skapar ett Azure Maps konto, hämtar en primär nyckel och använder webb-SDK för att konfigurera kart program'
author: anastasia-ms
ms.author: v-stharr
ms.date: 7/10/2020
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 24a834c87fe34d90dec5961bb3f8d376c6e5e62d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373223"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Snabb start: skapa en interaktiv Sök karta med Azure Maps

Den här artikeln visar hur du använder Azure Maps för att skapa en karta som ger användarna en interaktiv Sök upplevelse. Vi går igenom de grundläggande stegen:

* Skapa ditt eget Azure Maps-konto.
* Hämta din primär nyckel som ska användas i demonstrations webb programmet.
* Ladda ned och öppna programmet demo karta.

I den här snabb starten används Azure Maps Web SDK, men Azure Maps tjänsterna kan användas med valfri kart kontroll. [Här](open-source-projects.md#third-part-map-control-plugins) följer några populära kart kontroller med öppen källkod som Azure Mapss teamet har skapat plugin-program för.

## <a name="prerequisites"></a>Krav

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Logga in på [Azure-portalen](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Skapa ett Azure Maps-konto

Skapa ett nytt Azure Maps-konto med följande steg:

1. Klicka på **Skapa en resurs** längst upp till vänster i [Azure Portal](https://portal.azure.com).
2. I rutan *Sök på Marketplace skriver du* **Azure Maps**.
3. Välj **Azure Maps** i *resultatet*. Klicka på knappen **Skapa** som visas nedanför kartan.
4. Ange följande värden på sidan **Skapa Maps-konto**:
    * Den *Prenumeration* som ska användas för det här kontot.
    * Namnet på *Resursgrupp* för kontot. Du kan välja att *skapa ny* eller *använda befintlig* resursgrupp.
    * *Namn* för ditt nya konto.
    * *Pris nivån* för det här kontot.
    * Läs *licensen* och *sekretesspolicy* och markera kryssrutan för att godkänna villkoren.
    * Klicka på knappen **Skapa**.

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Skapa Maps-konto i portalen":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Hitta primärnyckeln för ditt konto

När ditt Maps-konto har skapats hämtar du den primära nyckeln som gör att du kan fråga Kartornas API: er.

1. Öppna ditt Maps-konto i portalen.
2. I avsnittet Inställningar väljer du **autentisering**.
3. Kopiera **Primärnyckel** till Urklipp. Spara den lokalt för senare användning i den här självstudien.

>[!NOTE]
> Om du använder prenumerations nyckeln i stället för den primära nyckeln återges kartan inte korrekt. Av säkerhets skäl rekommenderar vi också att du roterar mellan dina primära och sekundära nycklar. Om du vill rotera nycklar uppdaterar du appen så att den använder den sekundära nyckeln, distribuerar och trycker sedan på knappen cykel/uppdatera bredvid den primära nyckeln för att generera en ny primär nyckel. Den gamla primär nyckeln kommer att inaktive ras. Mer information om nyckel rotation finns i [konfigurera Azure Key Vault med nyckel rotation och granskning](../key-vault/secrets/tutorial-rotation-dual.md)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Hämta primär nyckel Azure Maps nyckel i Azure Portal":::

## <a name="download-the-demo-application"></a>Hämta demoprogrammet

1. Gå till [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Kopiera innehållet i filen.
2. Spara innehållet i den här filen lokalt som **AzureMapDemo.html**. Öppna den i en textredigerare.
3. Sök efter strängen `<Your Azure Maps Key>`. Ersätt den med **primärnyckelvärdet** från föregående avsnitt.

## <a name="open-the-demo-application"></a>Öppna demonstrations programmet

1. Öppna filen **AzureMapDemo.html** i en webbläsare.
2. Observera kartan som visas för staden Los Angeles. Zooma in och ut om du vill se hur kartan automatiskt återges med mer eller mindre information beroende på zoomnivån.
3. Ändra kartans standardmittpunkt. I filen **AzureMapDemo.html** söker du efter variabeln med namnet **center**. Ersätt variabelns parvärde för longitud, latitud med de nya värdena **[-74.0060, 40.7128]**. Spara filen och uppdatera webbläsaren.
4. Prova den interaktiva sökupplevelsen. I sökrutan i det övre vänstra hörnet i demowebbappen söker du efter **restauranger**.
5. Flytta musen över listan med adresser och platser som visas under sökrutan. Observera hur motsvarande nål på kartan visar information om den platsen. Observera att namnen och adresserna som visas är fiktiva för att skydda privata företag.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Webb program för interaktiv kart sökning":::


## <a name="clean-up-resources"></a>Rensa resurser

>[!WARNING]
>Självstudierna som anges i avsnittet [Nästa steg](#next-steps) beskriver hur du använder och konfigurerar Azure Maps med ditt konto. Rensa inte resurserna som du skapar i den här snabbstarten om du planerar att fortsätta med självstudierna.

Om du inte planerar att fortsätta med självstudierna gör du följande för att rensa resurserna:

1. Stäng webbläsaren som kör webbprogrammet **AzureMapDemo.html**.
2. Gå till sidan Azure Portal. Välj **alla resurser** på huvud Portal sidan. Du kan också klicka på Meny ikonen i det övre vänstra hörnet. Välj **Alla resurser**.
3. Klicka på ditt Azure Maps-konto. Klicka på **ta bort** längst upp på sidan.

Fler kodexempel och en interaktiv kodningsupplevelse finns i dessa guider:

[Hitta en adress med Azure Maps Search-tjänsten](how-to-search-for-address.md)

[Använd Azure Maps Kartkontroll](how-to-use-map-control.md)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ditt Azure Maps-konto och skapade ett demo program. Ta en titt på följande självstudier om du vill lära dig mer om Azure Maps:

> [!div class="nextstepaction"]
> [Sök i närliggande punkter av intresse med Azure Maps](tutorial-search-location.md)