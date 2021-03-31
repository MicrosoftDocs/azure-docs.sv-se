---
title: 'Självstudie: fråga från Power Apps'
titleSuffix: Azure Cognitive Search
description: Steg för steg-anvisningar om hur du skapar en Power app som ansluter till ett Azure Kognitiv sökning-index, skickar frågor och återger resultat.
author: luiscabrer
manager: eladz
ms.author: luisca
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: e8c16f02cf6b77fa54d2a19abac48e9914aa99bd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008573"
---
# <a name="tutorial-query-a-cognitive-search-index-from-power-apps"></a>Självstudie: fråga ett Kognitiv sökning-index från Power Apps

Använd den snabba program utvecklings miljön i Power Apps för att skapa en anpassad app för det sökbara innehållet i Azure Kognitiv sökning.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ansluta till Azure Kognitiv sökning
> * Konfigurera en förfrågan
> * Visualisera resultat i en app för arbets ytor

Om du inte har någon Azure-prenumeration kan du öppna ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* [Power Apps-konto](https://make.powerapps.com)

* [Hotell-exempel index](search-get-started-portal.md) som finns på din Sök tjänst

* [API-nyckel för fråga](search-security-api-keys.md#find-existing-keys)

## <a name="1---create-a-custom-connector"></a>1 – Skapa en anpassad anslutning

En anslutning i Power Apps är en anslutning till data källan. I det här steget ska du skapa en anpassad anslutning för att ansluta till ett Sök index i molnet.

1. [Logga in](https://make.powerapps.com) på Power Apps.

1. Till vänster expanderar du **data**  >  **anpassade anslutningar**.
 
    :::image type="content" source="./media/search-howto-powerapps/1-2-custom-connector.png" alt-text="Menyn anpassad anslutning" border="true":::

1. Välj  **+ ny anpassad anslutning** och välj sedan **skapa från Tom**.

    :::image type="content" source="./media/search-howto-powerapps/1-3-create-blank.png" alt-text="Skapa från en tom meny" border="true":::

1. Ge ditt anpassade anslutnings program ett namn (till exempel *AzureSearchQuery*) och klicka sedan på **Fortsätt**.

1. Ange information på sidan Allmänt:

   * Ikon bakgrunds färg (till exempel #007ee5)
   * Beskrivning (till exempel "en anslutning till Azure Kognitiv sökning")
   * På värden måste du ange din Sök tjänst-URL (t. ex. `<yourservicename>.search.windows.net` )
   * För bas-URL anger du bara "/"

    :::image type="content" source="./media/search-howto-powerapps/1-5-general-info.png" alt-text="Dialog rutan allmän information" border="true":::

1. På sidan säkerhet anger du *API-nyckel* som **Autentiseringstyp**, anger både parameter etiketten och parameter namnet till *API-nyckeln*. För **parameter plats** väljer du *sidhuvud* som visas nedan.

    :::image type="content" source="./media/search-howto-powerapps/1-6-authentication-type.png" alt-text="Typ av autentiseringstyp" border="true":::

1. På sidan definitioner väljer du **+ ny åtgärd** för att skapa en åtgärd som kommer att fråga indexet. Ange värdet "fråga" för sammanfattningen och namnet på åtgärds-ID: t. Ange en beskrivning som *"frågar Sök indexet"*.

    :::image type="content" source="./media/search-howto-powerapps/1-7-new-action.png" alt-text="Nya åtgärds alternativ" border="true":::

1. Rulla nedåt. I förfrågningar väljer du **+ Importera från exempel** för att konfigurera en förfrågan till din Sök tjänst:

   * Välj verbet `GET`

   * För URL: en anger du en exempel fråga för Sök indexet ( `search=*` returnerar alla dokument, `$select=` låter dig välja fält). API-versionen måste anges. Fullständigt angivet kan en URL se ut så här: `https://mydemo.search.windows.net/indexes/hotels-sample-index/docs?search=*&$select=HotelName,Description,Address/City&api-version=2020-06-30`

   * För sidhuvuden skriver du `Content-Type` . Du ställer in värdet på `application/json` i ett senare steg.

     **Power Apps** använder syntaxen i URL: en för att extrahera parametrar från frågan: Sök-, Select-och API-version-parametrarna blir konfigurerbara när du går igenom guiden.

       :::image type="content" source="./media/search-howto-powerapps/1-8-1-import-from-sample.png" alt-text="Importera från exempel" border="true":::

1. Klicka på **Importera** för att fylla begäran automatiskt. Slutför inställningen av parameter-metadata genom att klicka på **...** -symbolen bredvid var och en av parametrarna. Klicka på **tillbaka** för att återgå till sidan för begäran efter varje parameter uppdatering.

   :::image type="content" source="./media/search-howto-powerapps/1-8-2-import-from-sample.png" alt-text="Importera från exempel dialog" border="true":::

1. För *sökning*: ange `*` som **Standardvärde**, ange **obligatoriskt** som *falskt* och ange **synlighet** till *ingen*. 

    :::image type="content" source="./media/search-howto-powerapps/1-10-1-parameter-metadata-search.png" alt-text="Metadata för Sök parameter" border="true":::

1. För *Välj*: ange `HotelName,Description,Address/City` som **standardvärdet**, ange **obligatoriskt** till *falskt*, och Ställ in **visibility** på *none*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-4-parameter-metadata-select.png" alt-text="Välj parameter-metadata" border="true":::

1. För *API-version*: ange `2020-06-30` som **Standardvärde**, ange **obligatorisk** till *Sant* och ange **synlighet** som *intern*.  

    :::image type="content" source="./media/search-howto-powerapps/1-10-2-parameter-metadata-version.png" alt-text="Metadata för versions parameter" border="true":::

1. För *innehålls typ*: inställt på `application/json` .

1. När du har gjort ändringarna växlar du till vyn **Swagger-redigerare** . I avsnittet parametrar bör du se följande konfiguration:

    ```JSON
    parameters:
      - {name: search, in: query, required: false, type: string, default: '*'}
      - {name: $select, in: query, required: false, type: string, default: 'HotelName,Description,Address/City'}
      - {name: api-version, in: query, required: true, type: string, default: '2020-06-30',
        x-ms-visibility: internal}
      - {name: Content-Type, in: header, required: false, type: string}
    ```

1. Växla tillbaka till guiden och gå tillbaka till **3. Begär ande** steg. Rulla ned till avsnittet svar. Klicka på **Lägg till standard svar**. Detta är viktigt eftersom det hjälper Power apps att förstå schemat för svaret. 

1. Klistra in ett exempel svar. Ett enkelt sätt att avbilda ett exempel svar är via Sök Utforskaren i Azure Portal. I Sök Utforskaren ska du ange samma fråga som du gjorde för begäran, men Lägg till **$Top = 2** om du vill begränsa resultatet till bara två dokument:: `search=*&$select=HotelName,Description,Address/City&$top=2` . 

   Power Apps behöver bara några få resultat för att identifiera schemat. Du kan kopiera följande svar till guiden nu, förutsatt att du använder hotell-Sample-indexet.

    ```JSON
    {
        "@odata.context": "https://mydemo.search.windows.net/indexes('hotels-sample-index')/$metadata#docs(*)",
        "value": [
            {
                "@search.score": 1,
                "HotelName": "Arcadia Resort & Restaurant",
                "Description": "The largest year-round resort in the area offering more of everything for your vacation – at the best value!  What can you enjoy while at the resort, aside from the mile-long sandy beaches of the lake? Check out our activities sure to excite both young and young-at-heart guests. We have it all, including being named “Property of the Year” and a “Top Ten Resort” by top publications.",
                "Address": {
                    "City": "Seattle"
                }
            },
            {
                "@search.score": 1,
                "HotelName": "Travel Resort",
                "Description": "The Best Gaming Resort in the area.  With elegant rooms & suites, pool, cabanas, spa, brewery & world-class gaming.  This is the best place to play, stay & dine.",
                "Address": {
                    "City": "Albuquerque"
                }
            }
        ]
    }
    ```

    > [!TIP] 
    > Det finns en tecken gräns för det JSON-svar du kan ange, så du kanske vill förenkla JSON innan du klistrar in det. Svarets schema och format är viktigare än själva värdena. Fältet Beskrivning kan till exempel vara förenklat till bara den första meningen.

1. Klicka på **Importera** för att lägga till standard svaret.

1. Klicka på **Skapa koppling** längst upp till höger för att spara definitionen.

1. Stäng kopplingen genom att klicka på **Stäng** .

## <a name="2---test-the-connection"></a>2 – testa anslutningen

När kopplingen först skapas måste du öppna den igen från listan anpassade anslutningar för att kunna testa den. Om du senare gör ytterligare uppdateringar kan du testa i guiden.

Du behöver en [frågas-API-nyckel](search-security-api-keys.md#find-existing-keys) för den här aktiviteten. Varje gången en anslutning skapas, oavsett om ett test körs eller tas med i en app, behöver anslutningen den fråga-API-nyckel som används för att ansluta till Azure Kognitiv sökning.

1. Längst till vänster klickar du på **anpassade anslutningar**.

1. Hitta din koppling i listan (i den här självstudien är "AzureSearchQuery").

1. Välj kopplingen, expandera åtgärds listan och välj **Visa egenskaper**.

    :::image type="content" source="./media/search-howto-powerapps/1-11-1-test-connector.png" alt-text="Visa egenskaper" border="true":::

1. Välj **Redigera** längst upp till höger.

1. Välj **4. Test** för att öppna test sidan.

1. I test åtgärd klickar du på **+ ny anslutning**.

1. Ange en API-nyckel för fråga. Det här är en Azure Kognitiv sökning-fråga för skrivskyddad åtkomst till ett index. Du kan [hitta nyckeln](search-security-api-keys.md#find-existing-keys) i Azure Portal. 

1. I åtgärder klickar du på knappen **testa åtgärd** . Om du lyckas bör du se en 200-status och i bröd texten i svaret bör du se JSON som beskriver Sök resultaten.

    :::image type="content" source="./media/search-howto-powerapps/1-11-2-test-connector.png" alt-text="JSON-svar" border="true":::

## <a name="3---visualize-results"></a>3 – visualisera resultat

I det här steget skapar du en Power app med en sökruta, en Sök knapp och ett visnings område för resultatet. Power App ansluter till det nyligen skapade anpassade anslutnings programmet för att hämta data från Azure Search.

1. Till vänster expanderar du **appar**  >  **+ ny app**-  >  **arbetsyta**.

    :::image type="content" source="./media/search-howto-powerapps/2-1-create-canvas.png" alt-text="Skapa app för arbets yta" border="true":::

1. Välj typ av program. I den här självstudien skapar du en **Tom app** med **layouten Phone**. **Power Apps Studio** visas.

1. När du är i Studio väljer du fliken **data källor** och klickar på den nya anslutning som du nyss skapade. I vårt fall kallas det *AzureSearchQuery*. Klicka på **Lägg till en anslutning**.

   Ange API-nyckeln för frågan.

    :::image type="content" source="./media/search-howto-powerapps/2-3-connect-connector.png" alt-text="Anslut koppling" border="true":::

    Nu är *AzureSearchQuery* en data källa som är tillgänglig för användning från ditt program.

1. Lägg till några kontroller på arbets ytan på **fliken Infoga**.

    :::image type="content" source="./media/search-howto-powerapps/2-4-add-controls.png" alt-text="Infoga kontroller" border="true":::

1. Infoga följande element:

   * En text etikett med värdet "fråga:"
   * Ett text inmatat element (anropa IT- *txtQuery*, standardvärde: "*")
   * En knapp med texten "search" 
   * Ett lodrätt galleri med namnet (anropa IT- *galleryResults*)

    Arbets ytan bör se ut ungefär så här:

    :::image type="content" source="./media/search-howto-powerapps/2-5-controls-layout.png" alt-text="Layout för kontroller" border="true":::

1. Om du vill att **Sök knappen** ska utfärda en fråga klistrar du in följande åtgärd i **OnSelect**:

    ```
    If(!IsBlank(txtQuery.Text),
        ClearCollect(azResult, AzureSearchQuery.Query({search: txtQuery.Text}).value))
    ```

   Följande skärm bild visar formel fältet för **OnSelect** -åtgärden.

    :::image type="content" source="./media/search-howto-powerapps/2-6-search-button-event.png" alt-text="Knapp OnSelect" border="true":::

   Den här åtgärden gör att knappen uppdaterar en ny samling som heter *azResult* med resultatet från Sök frågan med texten i text rutan *txtQuery* som frågeterm.

   > [!NOTE]
   > Prova detta om du får ett formel-syntaxfel "funktionen" ClearCollect "har ogiltiga funktioner:
   > 
   > * Kontrol lera först att kopplings referensen är korrekt. Ta bort kopplings namnet och börja skriva namnet på din koppling. IntelliSense bör föreslå rätt koppling och verb.
   > 
   > * Om felet kvarstår tar du bort och återskapar anslutningen. Om det finns flera instanser av en koppling kanske appen använder fel en.
   > 

1. Länka den vertikala Galleri kontrollen till den *azResult* -samling som skapades när du avslutade föregående steg. 

   Välj Galleri kontrollen och utför följande åtgärder i rutan Egenskaper.

   * Ange **DataSource** till *azResult*.
   * Välj en **layout** som fungerar för dig baserat på typen av data i ditt index. I det här fallet använde vi *rubrik, under rubrik och brödtext* .
   * **Redigera fält** och välj de fält som du vill visualisera.

    Eftersom vi tillhandahöll ett exempel resultat när vi definierade kopplingen, är appen medveten om de fält som är tillgängliga i ditt index.
    
    :::image type="content" source="./media/search-howto-powerapps/2-7-gallery-select-fields.png" alt-text="Gallerifält" border="true":::   
 
1. Tryck på **F5** för att förhandsgranska appen.  

    :::image type="content" source="./media/search-howto-powerapps/2-8-3-final.png" alt-text="Appen är klar" border="true":::    

<!--     Remember that the fields can be set to calculated values.

    For the example, setting using the *"Image, Title and Subtitle"* layout and specifying the *Image* function as the concatenation of the root path for the data and the file name (for instance, `"https://mystore.blob.core.windows.net/multilang/" & ThisItem.metadata_storage_name`) will produce the result below.

    :::image type="content" source="./media/search-howto-powerapps/2-8-2-final.png" alt-text="Final app" border="true":::         -->

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

Med Power Apps kan du snabbt utveckla anpassade appar. Nu när du vet hur du ansluter till ett sökindex kan du läsa mer om hur du skapar en omfattande visualiserings upplevelse i en anpassad Power app.

> [!div class="nextstepaction"]
> [Utbildnings katalog för Power Apps](/powerapps/learning-catalog/get-started)