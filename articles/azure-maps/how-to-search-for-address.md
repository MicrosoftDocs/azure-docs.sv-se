---
title: Sök efter en plats med Azure Maps Sök tjänster
description: 'Läs mer om Azure Maps Search-tjänsten. Se hur du använder den här uppsättningen med API: er för kodning, omvänd hår kodning, fuzzy-sökningar och omvänd Cross-gata-sökningar.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 01/19/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: dddf56edf2037d87a28589a59834db32f8d04a4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98598374"
---
# <a name="search-for-a-location-using-azure-maps-search-services"></a>Sök efter en plats med Azure Maps Sök tjänster

[Azure Maps search service](/rest/api/maps/search) är en uppsättning RESTful-API: er som är utformade för att hjälpa utvecklare att söka i adresser, platser och affärs listor efter namn, kategori och annan geografisk information. Förutom att stödja traditionell hår ande kod kan tjänster också omvända koder för hår koder och korsade gator utifrån latitud och longitud. Värdena för latitud och longitud som returneras av sökningen kan användas som parametrar i andra Azure Maps tjänster, t. ex. [routning](/rest/api/maps/route) och [väder](/rest/api/maps/weather) tjänster.


I den här artikeln får du lära dig att:

* Begär latitud-och longitud-koordinater för en adress (kod för den här adressen) med hjälp av [Sök-API: et]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddress).
* Sök efter en adress eller ett intresse punkt (POI) med hjälp av [API: et för fuzzy search](/rest/api/maps/search/getsearchfuzzy).
* Gör en [Omvänd adresss ökning](/rest/api/maps/search/getsearchaddressreverse) för att översätta koordinat platsen till gatuadressen.
* Översätt koordinatens placering till en mänsklig, kors gata genom att använda [Sök adress omvänt kors gatan API](/rest/api/maps/search/getsearchaddressreversecrossstreet).  Oftast behövs detta för att spåra program som tar emot ett GPS-flöde från en enhet eller till gång och vill veta var koordinaten finns.

## <a name="prerequisites"></a>Förutsättningar

1. [Skapa ett Azure Maps konto](quick-demo-map-app.md#create-an-azure-maps-account)
2. [Hämta en primär prenumerations nyckel](quick-demo-map-app.md#get-the-primary-key-for-your-account), även kallat primär nyckel eller prenumerations nyckel.

I den här självstudien används [Postman](https://www.postman.com/) -programmet, men du kan välja en annan API utvecklings miljö.

## <a name="request-latitude-and-longitude-for-an-address-geocoding"></a>Begär latitud och longitud för en adress (kod)

I det här exemplet använder vi Azure Maps [Hämta Sök adress-API](/rest/api/maps/search/getsearchaddress) för att konvertera en adress till latitud-och longitud-koordinater. Den här processen kallas även för en *omkodning*. Förutom att returnera koordinaterna returnerar svaret även detaljerade adress egenskaper, till exempel gata, post nummer, kommun och information om land/region.

>[!TIP]
>Om du har en uppsättning adresser till landskod kan du använda [batch-API: et post search](/rest/api/maps/search/postsearchaddressbatch) för att skicka en batch med frågor i ett enda API-anrop.

1. Öppna Postman-appen. Längst upp i Postman-appen väljer du **nytt**. I fönstret **Skapa nytt** väljer du **samling**.  Namnge samlingen och välj knappen **skapa** . Du kommer att använda den här samlingen för resten av exemplen i det här dokumentet.

2. Välj **nytt** om du vill skapa en begäran. I fönstret **Skapa nytt** väljer du **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående steg och välj sedan **Spara**.

3. Välj metoden **Hämta** http på fliken Builder och ange följande URL. I den här förfrågan söker vi efter en speciell adress: `400 Braod St, Seattle, WA 98109` . För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
    https://atlas.microsoft.com/search/address/json?&subscription-key={Azure-Maps-Primary-Subscription-key}&api-version=1.0&language=en-US&query=400 Broad St, Seattle, WA 98109
    ```

4. Klicka på knappen blå **sändning** . Svars texten kommer att innehålla data för en enda plats.

5. Nu ska vi söka efter en adress som har fler än en möjlig plats. I avsnittet **params** ändrar du `query` nyckeln till `400 Broad, Seattle` . Klicka på knappen blå **sändning** .

    :::image type="content" source="./media/how-to-search-for-address/search-address.png" alt-text="Sök efter adress":::

6. Försök sedan `query` att ange nyckeln till `400 Broa` .

7. Klicka på knappen **Skicka** . Nu kan du se att svaret innehåller svar från flera länder. Om du vill göra ett neutralt resultat till det relevanta område för dina användare ska du alltid lägga till så många plats detaljer som möjligt för begäran.

## <a name="using-fuzzy-search-api"></a>Använda API för fuzzy-sökning

[API: et för Azure Maps oskarp sökning](/rest/api/maps/search/getsearchfuzzy) stöder Standards ökning med en rad och kostnads fri form. Vi rekommenderar att du använder Azure Maps Sök i fuzzy API när du inte känner till indatatypen för en Sök förfrågan.  Frågans indata kan vara en fullständig eller delvis adress. Det kan också vara en Point of Interest-token (POI), som ett namn på POI, POI-kategori eller namn på varumärket. För att förbättra relevansen för dina Sök resultat kan frågeresultaten dessutom begränsas av en koordinat plats och radie, eller genom att definiera en begränsnings ruta.

>[!TIP]
>De flesta Sök frågor är standard för maxFuzzyLevel = 1 för att få prestanda och minska ovanliga resultat. Du kan justera oskärpa-nivåer med `maxFuzzyLevel` parametrarna eller `minFuzzyLevel` . Mer information om `maxFuzzyLevel` och en fullständig lista över alla valfria parametrar finns i [fuzzy search URI Parameters](/rest/api/maps/search/getsearchfuzzy#uri-parameters)

### <a name="search-for-an-address-using-fuzzy-search"></a>Sök efter en adress med hjälp av en Fuzzy-sökning

I det här exemplet ska vi använda FUZZY search för att söka hela världen efter `pizza` .  Sedan visar vi dig hur du söker över omfattningen för ett speciellt land. Slutligen visar vi dig hur du använder en koordinat plats och radie för att begränsa sökningen över ett särskilt område och begränsa antalet returnerade resultat.

>[!IMPORTANT]
>Om du vill göra ett neutralt resultat till det relevanta utrymmet för dina användare ska du alltid lägga till så många plats detaljer som möjligt. Mer information finns i [metod tips för sökning](how-to-use-best-practices-for-search.md#geobiased-search-results).

1. Öppna Postman-appen, klicka på **ny** och välj **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i föregående avsnitt eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln.

    ```http
   https://atlas.microsoft.com/search/fuzzy/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=pizza
    ```

    >[!NOTE]
    >_JSON_ -ATTRIBUTET i URL-sökvägen bestämmer svars formatet. I den här artikeln används JSON för enkel användning och läsbarhet. Om du vill hitta andra svars format som stöds, se `format` parameter definitionen i [referens dokumentationen för URI-parametern](/rest/api/maps/search/getsearchfuzzy#uri-parameters).

3. Klicka på **Skicka** och granska svars texten.

    Den tvetydiga frågesträngen för "pizza" returnerade 10 [punkter av intresse resultat](/rest/api/maps/search/getsearchpoi#searchpoiresponse) (POI) i båda kategorierna "pizza" och "restaurang". Varje resultat innehåller information som gatuadress, latitud-och longitud-värden, Visa port och start punkter för platsen. Resultaten varierar nu för den här frågan och är inte kopplade till någon referens plats.
  
    I nästa steg ska vi använda- `countrySet` parametern för att ange endast de länder/regioner som programmet behöver täckning för. En fullständig lista över länder/regioner som stöds finns i [search-täckning](./geocoding-coverage.md).

4. Standard beteendet är att söka hela världen och eventuellt returnera onödiga resultat. Nu ska vi bara söka efter pizza USA. Lägg till `countrySet` nyckeln i avsnittet **params** och ange värdet till `US` . Om du anger `countrySet` nyckeln till `US` kommer resultatet att bindas till USA.

    :::image type="content" source="./media/how-to-search-for-address/search-fuzzy-country.png" alt-text="Sök efter pizza i USA":::

    Resultaten begränsas nu av lands koden och frågan returnerar pizza restauranger i USA.

5. För att få en ännu mer riktad sökning kan du söka över omfånget för en lat./lon. koordinat par. I det här exemplet använder vi lat./lon. i barr området Seattle. Eftersom vi bara vill returnera resultat inom en radie på 400 meter lägger vi till `radius` parametern. Dessutom lägger vi till `limit` parametern för att begränsa resultatet till de fem närmast pizza platserna.

    I avsnittet **params** lägger du till följande nyckel/värde-par:

     | Tangent | Värde |
    |-----|------------|
    | koder | 47,620525 |
    | lon | – 122,349274 |
    | RADIUS | 400 |
    | gräns | 5|

6. Klicka på **Skicka**. Svaret innehåller resultat för pizza-restauranger nära Barr området Seattle.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Sök efter en gatuadress med omvänd adresss ökning

Azure Maps [Get search Address reversed API]( https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse) översätter koordinater till människo läsliga gatuadresser. Detta API används ofta för program som använder GPS-feeds och vill identifiera adresser vid vissa koordinater.

>[!IMPORTANT]
>Om du vill göra ett neutralt resultat till det relevanta utrymmet för dina användare ska du alltid lägga till så många plats detaljer som möjligt. Mer information finns i [metod tips för sökning](how-to-use-best-practices-for-search.md#geobiased-search-results).

>[!TIP]
>Om du har en uppsättning koordinerande platser för omvänd landskod, kan du använda [posts öknings adress omvänt batch-API](/rest/api/maps/search/postsearchaddressreversebatch) för att skicka en batch med frågor i ett enda API-anrop.

I det här exemplet ska vi göra omvända sökningar med några av de valfria parametrarna som är tillgängliga. En fullständig lista över valfria parametrar finns i [parametrarna för omvänd sökning](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

1. I Postman-appen klickar du på **ny** och väljer **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i det första avsnittet eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln. Begäran bör se ut som följande URL:

    ```http
    https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700&number=1
    ```

3. Klicka på **Skicka** och granska svars texten. Du bör se resultatet av en fråga. Svaret innehåller viktig adress information om Safeco-fältet.
  
4. Nu ska vi lägga till följande nyckel/värde-par i **params** -avsnittet:

    | Tangent | Värde | Returer
    |-----|------------|------|
    | antal | 1 |Svaret kan innehålla sidan av gatan (vänster/höger) och även en förskjutnings position för talet.|
    | returnSpeedLimit | true | Returnerar hastighets gränsen på adressen.|
    | returnRoadUse | true | Returnerar användnings typer för vägen på adressen. För alla möjliga typer av användning av vägar, se [användnings typer för vägar](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).|
    | returnMatchType | true| Returnerar matchnings typen. Information om alla möjliga värden finns i [Omvänd adress Sök Resultat](/rest/api/maps/search/getsearchaddressreverse#searchaddressreverseresult)

   :::image type="content" source="./media/how-to-search-for-address/search-reverse.png" alt-text="Sök omvänd.":::

5. Klicka på **Skicka** och granska svars texten.

6. Härnäst ska vi lägga till `entityType` nyckeln och ange dess värde till `Municipality` . `entityType`Nyckeln kommer att åsidosätta `returnMatchType` nyckeln i föregående steg. Vi måste också ta bort `returnSpeedLimit` och `returnRoadUse` eftersom vi ber om information om kommunen.  För alla möjliga entitetstyper, se [enhets typer](/rest/api/maps/search/getsearchaddressreverse#entitytype).

    :::image type="content" source="./media/how-to-search-for-address/search-reverse-entity-type.png" alt-text="Sök omvänd entityType.":::

7. Klicka på **Skicka**. Jämför resultaten med resultaten som returnerades i steg 5.  Eftersom den begärda entitetstypen är nu `municipality` , innehåller inte svaret adress information. Den returnerade filen `geometryId` kan också användas för att begära avgränsnings polygon genom Azure Maps Hämta [API för söknings-polygon](/rest/api/maps/search/getsearchpolygon).

>[!TIP]
>Om du vill ha mer information om dessa parametrar, och om du vill veta mer om andra, kan du läsa [avsnittet omvända Sök parametrar](/rest/api/maps/search/getsearchaddressreverse#uri-parameters).

## <a name="search-for-cross-street-using-reverse-address-cross-street-search"></a>Sök efter kors gatan med omvänd adress över gata-sökning

I det här exemplet ska vi söka efter en kors gatan baserat på en adresss koordinater.

1. I Postman-appen klickar du på **ny** och väljer **begäran**. Ange ett **namn** för begäran. Välj den samling som du skapade i det första avsnittet eller skapa en ny och välj sedan **Spara**.

2. Välj metoden **Hämta** http på fliken Builder och ange följande URL. För den här begäran och andra begär Anden som nämns i den här artikeln ersätter `{Azure-Maps-Primary-Subscription-key}` du med den primära prenumerations nyckeln. Begäran bör se ut som följande URL:
  
    ```http
   https://atlas.microsoft.com/search/address/reverse/crossstreet/json?&api-version=1.0&subscription-key={Azure-Maps-Primary-Subscription-key}&language=en-US&query=47.591180,-122.332700
    ```

    :::image type="content" source="./media/how-to-search-for-address/search-address-cross.png" alt-text="Sök tvär gatan.":::
  
3. Klicka på **Skicka** och granska svars texten. Du märker att svaret innehåller `crossStreet` värdet `South Atlantic Street` .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Maps Search Service REST API](/rest/api/maps/search)

> [!div class="nextstepaction"]
> [Metod tips för Azure Maps Search Service](how-to-use-best-practices-for-search.md)