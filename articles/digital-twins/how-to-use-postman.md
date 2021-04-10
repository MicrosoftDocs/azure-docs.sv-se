---
title: Gör förfrågningar med Postman
titleSuffix: Azure Digital Twins
description: 'Lär dig hur du konfigurerar och använder Postman för att testa Azure Digitals dubbla API: er.'
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: d4a6e25578cd26b10b34f74a9f859d4957cc553b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783820"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Så här använder du Postman för att skicka förfrågningar till Azure Digitals dubbla API: er

[Postman](https://www.getpostman.com/) är ett rest-testverktyg som tillhandahåller nyckel funktioner för http-begäran i ett Skriv bords-och plugin-baserade GUI. Du kan använda den för att hantverka HTTP-begäranden och skicka dem till [REST-API: erna för Azure Digitals](how-to-use-apis-sdks.md).

Den här artikeln beskriver hur du konfigurerar [Postman rest-klienten](https://www.getpostman.com/) så att den interagerar med Azure Digitals dubbla API: er med hjälp av följande steg:

1. Använd Azure CLI för att [**Hämta en Bearer-token**](#get-bearer-token) som du ska använda för att skapa API-begäranden i Postman.
1. Konfigurera en [**Postman-samling**](#about-postman-collections) och konfigurera POSTman rest-klienten så att den använder din Bearer-token för att autentisera. När du konfigurerar samlingen kan du välja något av följande alternativ:
    1. [**Importera**](#import-collection-of-azure-digital-twins-apis) en fördefinierad samling av Azure Digitals dubbla API-begäranden.
    1. [**Skapa**](#create-your-own-collection) din egen samling från grunden.
1. [**Lägg till begär Anden**](#add-an-individual-request) till den konfigurerade samlingen och skicka dem till Azures digitala dubbla API: er.

Azure Digitals dubbla finns två uppsättningar med API: er som du kan arbeta med: **data plan** och **kontroll plan**. Mer information om skillnaden mellan dessa API-uppsättningar finns i [*How-to: använda Azure Digitals dubbla API: er och SDK: er*](how-to-use-apis-sdks.md). Den här artikeln innehåller information om båda API-uppsättningarna.

## <a name="prerequisites"></a>Förutsättningar

Om du vill fortsätta med att använda Postman för att komma åt Azures digitala dubbla API: er måste du konfigurera en digital Azure-instans och hämta Postman. Resten av det här avsnittet vägleder dig genom de här stegen.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurera Azure Digitals dubbla instanser

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Hämta Postman

Sedan hämtar du Skriv bords versionen av Postman-klienten. Gå till [*www.getpostman.com/apps*](https://www.getpostman.com/apps) och följ anvisningarna för att ladda ned appen.

## <a name="get-bearer-token"></a>Hämta Bearer-token

Nu när du har konfigurerat Postman och din Azure Digital-instansen måste du skaffa en Bearer-token som Postman-begäranden kan använda för att auktorisera mot Azure Digitals dubbla API: er.

Det finns flera möjliga sätt att hämta denna token. I den här artikeln används [Azure CLI](/cli/azure/install-azure-cli) för att logga in på ditt Azure-konto och få en token på detta sätt.

Om du har ett Azure CLI [installerat lokalt](/cli/azure/install-azure-cli)kan du starta en kommando tolk på datorn för att köra följande kommandon.
Annars kan du öppna ett [Azure Cloud Shell](https://shell.azure.com) -fönster i webbläsaren och köra kommandona där.

1. Kontrol lera först att du är inloggad i Azure med rätt autentiseringsuppgifter genom att köra det här kommandot:

    ```azurecli-interactive
    az login
    ```

2. Sedan använder du kommandot [AZ Account get-Access-token](/cli/azure/account#az_account_get_access_token) för att hämta en Bearer-token med åtkomst till tjänsten Azure Digitals dubbla. I det här kommandot skickar du resurs-ID: t för den Azure Digital-tjänstens slut punkt för att få en åtkomsttoken som kan komma åt Azure Digitals resurser. 

    Vilken kontext som krävs för token beror på vilken uppsättning av API: er du använder, så Använd flikarna nedan för att välja mellan [data plan](how-to-use-apis-sdks.md#overview-data-plane-apis) och [kontroll Plans](how-to-use-apis-sdks.md#overview-control-plane-apis) -API: er.

    # <a name="data-plane"></a>[Dataplanet](#tab/data-plane)
    
    Använd följande statiska värde för token-kontexten för att hämta en token som ska användas med **data Plans** -API: er: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Det här är resurs-ID: t för Azure Digitals-tjänstens slut punkt.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)
    
    Använd följande värde för token-kontexten för att hämta en token som ska användas med **kontroll Plans** -API: er: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---


3. Kopiera värdet för `accessToken` i resultatet och spara det så att det används i nästa avsnitt. Detta är ditt **token-värde** som du kommer att ge till Postman för att auktorisera dina begär Anden.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Skärm bild av konsolen som visar resultatet av kommandot AZ Account get-Access-token. Fältet accessToken och dess exempel värde är markerat.":::

>[!TIP]
>Denna token är giltig i minst fem minuter och högst 60 minuter. Om du tar slut på den tid som tilldelats aktuell token kan du upprepa stegen i det här avsnittet för att få en ny.

Härnäst ska du ställa in Postman att använda denna token för att göra API-begäranden till Azure Digitals-dubbla.

## <a name="about-postman-collections"></a>Om Postman-samlingar

Begär anden i Postman sparas i **samlingar** (grupper av förfrågningar). När du skapar en samling för att gruppera dina begär Anden kan du använda vanliga inställningar för många begär Anden samtidigt. Detta kan avsevärt förenkla auktoriseringen om du planerar att skapa fler än en begäran mot Azures digitala dubbla API: er, eftersom du bara behöver konfigurera dessa uppgifter en gång för hela samlingen.

När du arbetar med digitala Azure-dubbla, kan du komma igång genom att importera en [fördefinierad samling av alla förfrågningar om Azure Digitals dubbla](#import-collection-of-azure-digital-twins-apis). Du kanske vill göra detta om du utforskar API: erna och snabbt vill skapa ett projekt med förfrågan exempel.

Du kan också välja att börja från början genom att [skapa en egen tom samling](#create-your-own-collection) och fylla den med enskilda förfrågningar som bara anropar de API: er som du behöver. 

I följande avsnitt beskrivs båda dessa processer. Resten av artikeln äger rum i ditt lokala Postman-program, så gå vidare och öppna Postman-programmet på datorn nu.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importera insamling av Azure Digitals dubbla API: er

Ett snabbt sätt att komma igång med Azure Digital-meddelanden i Postman är att importera en fördefinierad samling begär Anden för Azure Digitals dubbla API: er.

### <a name="download-the-collection-file"></a>Hämta samlings filen

Det första steget i att importera API-uppsättningen är att ladda ned en samling. Välj fliken nedan för ditt val av data plan eller kontroll plan för att se de fördefinierade samlings alternativen.

# <a name="data-plane"></a>[Dataplanet](#tab/data-plane)

Det finns för närvarande två Azure Digital-dataplans samlingar som du kan välja mellan:
* [**Azure Digitals Postman-samling**](https://github.com/microsoft/azure-digital-twins-postman-samples): den här samlingen ger en enkel komma igång-upplevelse för Azure Digital-meddelanden i Postman. Förfrågan innehåller exempel data, så du kan köra dem med minimala ändringar som krävs. Välj den här samlingen om du vill ha en digestible uppsättning nyckel-API-begäranden som innehåller exempel information.
    - Du hittar samlingen genom att navigera till lagrings platsen-länken och öppna filen som heter *postman_collection.jspå*.
* **[Azure Digitals dataplan-Swagger](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)**: den här lagrings platsen innehåller den fullständiga Swagger-filen för API-uppsättningen för Azure Digitals dubbla, som kan hämtas och importeras till Postman som en samling. Detta ger en omfattande uppsättning av varje API-begäran, men med tomma data instanser i stället för exempel data. Välj den här samlingen om du vill ha åtkomst till alla API-anrop och fylla i alla data själv.
    - Du hittar samlingen genom att navigera till lagrings platsen-länken och välja mappen för den senaste Specifikations versionen. Härifrån öppnar du filen som heter *digitaltwins.jspå*.

# <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)

Den samling som för närvarande är tillgänglig för kontroll planet är [**Azure Digitals Swagger för kontroll plan**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Den här lagrings platsen innehåller den fullständiga Swagger-filen för Azure Digitals dubbla API-uppsättningar, som kan hämtas och importeras till Postman som en samling. Detta ger en omfattande uppsättning av varje API-begäran.

Du hittar samlingen genom att navigera till lagrings platsen-länken och välja mappen för den senaste Specifikations versionen. Härifrån öppnar du filen som heter *digitaltwins.jspå*.

---

Så här hämtar du den valda samlingen till datorn så att du kan importera den till Postman.
1. Använd länkarna ovan för att öppna samlings filen i GitHub i webbläsaren.
1. Välj **RAW** -knappen för att öppna filens RAW-text.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Skärm bild av data planet digitaltwins.jspå filen i GitHub. Det finns en markering runt den råa knappen." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Kopiera texten från fönstret och klistra in den i en ny fil på din dator.
1. Spara filen med fil namns tillägget *. JSON* (fil namnet kan vara vad du vill, så länge du kan komma ihåg att hitta filen senare).

### <a name="import-the-collection"></a>Importera samlingen

Importera sedan samlingen till Postman.

1. I fönstret för huvud fönstret Postman väljer du knappen **Importera** .
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Skärm bild av ett nyligen öppnat Boxman-fönster. Knappen Importera är markerad." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. I fönstret Importera som följer väljer du **överför filer** och navigerar till samlings filen på den dator som du skapade tidigare. Välj Öppna.
1. Klicka på knappen **Importera** för att bekräfta.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Skärm bild av Postman import-fönstret, som visar filen som ska importeras som en samling och knappen Importera.":::

Den nyligen importerade samlingen kan nu visas från din huvudsakliga Postman-vy på fliken samlingar.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Skärm bild av huvud fönstret Postman. Den nyligen importerade samlingen är markerad på fliken samlingar." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Fortsätt sedan till nästa avsnitt för att lägga till en Bearer-token i samlingen för auktorisering och Anslut den till din Azure Digital-instansen.

### <a name="configure-authorization"></a>Konfigurera auktorisering

Redigera sedan den samling som du har skapat för att konfigurera viss åtkomst information. Markera den samling som du har skapat och välj ikonen **Visa fler åtgärder** för att hämta en meny. Välj **Redigera**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Skärm bild av Postman. Ikonen &quot;Visa fler åtgärder&quot; för den importerade samlingen markeras och &quot;redigera&quot; är markerad i alternativen." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Följ dessa steg om du vill lägga till en Bearer-token i samlingen för auktorisering. Här använder du det **token-värde** som du samlade in i avsnittet [Hämta Bearer-token](#get-bearer-token) för att kunna använda det för alla API-begäranden i din samling.

1. Se till att du är på fliken **auktorisering** i dialog rutan Redigera för samlingen. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Skärm bild av den importerade samlingens redigerings dialog ruta i Postman, som visar fliken auktorisering." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Ange typen till **OAuth 2,0**, klistra in åtkomsttoken i rutan åtkomsttoken och välj **Spara**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Skärm bild av dialog rutan Postman Edit för den importerade samlingen på fliken auktorisering. Typen är OAuth 2,0 och rutan åtkomsttoken är markerad." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Ytterligare konfiguration

# <a name="data-plane"></a>[Dataplanet](#tab/data-plane)

Om du skapar en [data Plans](how-to-use-apis-sdks.md#overview-data-plane-apis) samling kan du enkelt ansluta till Azures digitala resurser genom att ställa in några **variabler** som medföljer samlingarna. När många begär anden i en samling kräver samma värde (t. ex. värd namnet för din Azure Digital-instans) kan du lagra värdet i en variabel som gäller för varje begäran i samlingen. Båda de nedladdnings bara samlingarna för Azure Digitals, levereras med fördefinierade variabler som du kan ställa in på samlings nivå.

1. Gå till fliken **variabler** i dialog rutan Redigera för samlingen.

1. Använd din instanss **värdnamn** från avsnittet [*krav*](#prerequisites) för att ange det aktuella värde fältet för den aktuella variabeln. Välj **Spara**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Skärm bild av den importerade samlingens Edit-dialog i Postman, och fliken &quot;variabler&quot; visas. Fältet ' aktuellt värde ' är markerat." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Om din samling har ytterligare variabler, Fyll i och spara även dessa värden.

När du är klar med stegen ovan är du klar med att konfigurera samlingen. Du kan stänga fliken redigering för samlingen om du vill.

# <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)

Om du skapar en [kontroll Plans](how-to-use-apis-sdks.md#overview-control-plane-apis) samling har du gjort allt du behöver för att konfigurera samlingen. Du kan stänga fliken redigering för samlingen om du vill och fortsätta till nästa avsnitt.

--- 

### <a name="explore-requests"></a>Utforska förfrågningar

Sedan kan du utforska förfrågningarna i Azure Digitals API-samling. Du kan expandera samlingen för att visa de i förväg skapade förfrågningarna (sorterade efter kategori för åtgärd). 

Olika begär Anden kräver annan information om din instans och dess data. Om du vill se all information som krävs för att skapa en viss begäran kan du leta upp informationen om begäran i [Azures digitala dubbla REST API referens dokumentation](/rest/api/azure-digitaltwins/).

Du kan redigera information om en begäran i Postman-samlingen med hjälp av följande steg:

1. Välj den i listan för att hämta den redigerbara informationen. 

1. Fyll i värden för variablerna som anges på fliken **parametrar** under **Path-variabler**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Skärm bild av Postman. Samlingen expanderas för att visa en begäran. Avsnittet Path-variabler är markerat i förfrågnings informationen." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Ange nödvändiga **rubriker** eller **brödtext** på respektive flik.

När du har angett all information som krävs kan du köra begäran med knappen **Skicka** .

Du kan också lägga till dina egna förfrågningar till samlingen med hjälp av processen som beskrivs i avsnittet [*Lägg till en enskild begäran*](#add-an-individual-request) nedan.

## <a name="create-your-own-collection"></a>Skapa din egen samling

I stället för att importera den befintliga samlingen av alla Azure Digitals dubbla API: er, kan du också skapa en egen samling från grunden. Du kan sedan fylla den med enskilda förfrågningar med hjälp av [Azure digitals REST API referens dokumentation](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Skapa en Postman-samling

1. Om du vill skapa en samling väljer du knappen **nytt** i fönstret för huvud fönstret Postman.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Skärm bild av huvud fönstret Postman. Knappen &quot;nytt&quot; är markerad." lightbox="media/how-to-use-postman/postman-new.png":::

    Välj en typ av **samling**.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Skärm bild av dialog rutan skapa ny i Postman. Alternativet Collection är markerat.":::

1. Då öppnas en flik där du kan fylla i informationen för den nya samlingen. Välj redigerings ikonen bredvid samlingens standard namn (**ny samling**) för att ersätta den med ditt eget val av namn. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Skärm bild av den nya samlingens Edit-dialog i Postman. Redigerings ikonen bredvid namnet &quot;ny samling&quot; är markerad." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Fortsätt sedan till nästa avsnitt för att lägga till en Bearer-token i samlingen för auktorisering.

### <a name="configure-authorization"></a>Konfigurera auktorisering

Följ dessa steg om du vill lägga till en Bearer-token i samlingen för auktorisering. Här använder du det **token-värde** som du samlade in i avsnittet [Hämta Bearer-token](#get-bearer-token) för att kunna använda det för alla API-begäranden i din samling.

1. Gå till fliken **auktorisering** i dialog rutan Redigera för den nya samlingen.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Skärm bild av dialog rutan Redigera för den nya samlingen i Postman, som visar fliken auktorisering." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Ange typen till **OAuth 2,0**, klistra in åtkomsttoken i rutan åtkomsttoken och välj **Spara**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Skärm bild av dialog rutan Postman Edit för den nya samlingen på fliken auktorisering. Typen är OAuth 2,0 och rutan åtkomsttoken är markerad." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

När du är klar med stegen ovan är du klar med att konfigurera samlingen. Du kan stänga fliken Redigera för den nya samlingen om du vill.

Den nya samlingen kan visas från din huvudsakliga Postman-vy på fliken samlingar.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Skärm bild av huvud fönstret Postman. Den nyligen skapade anpassade samlingen markeras på fliken samlingar." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Lägg till en enskild begäran

Nu när samlingen har kon figurer ATS kan du lägga till dina egna förfrågningar till Azures digitala dubbla API: er.

1. Använd knappen **nytt** om du vill skapa en begäran.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Skärm bild av huvud fönstret Postman. Knappen &quot;nytt&quot; är markerad." lightbox="media/how-to-use-postman/postman-new.png":::

    Välj en typ av **begäran**.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Skärm bild av dialog rutan skapa ny i Postman. Alternativet Request är markerat.":::

1. Den här åtgärden öppnar fönstret Spara förfrågan där du kan ange ett namn för din begäran, ge det en valfri beskrivning och välja den samling som den är en del av. Fyll i informationen och spara begäran till samlingen som du skapade tidigare.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Skärm bild av fönstret &quot;Spara förfrågan&quot; i Postman som visar de fält som beskrivs. Knappen &quot;Spara till Azure Digitals sammanflätad samling&quot; är markerad.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Nu kan du Visa din begäran under samlingen och välja den för att hämta redigerbar information.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Skärm bild av Postman. Azure Digitals-samlingen är expanderad för att visa information om begäran." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Ange information om begäran

Om du vill göra en Postman-begäran till en av Azures digitala dubbla API: er behöver du URL: en till API: et och information om vilka uppgifter som krävs. Du hittar den här informationen i [Azure digitals REST API referens dokumentation](/rest/api/azure-digitaltwins/).

Om du vill fortsätta med en exempel fråga kommer den här artikeln att använda fråge-API: t (och dess [referens dokumentation](/rest/api/digital-twins/dataplane/query/querytwins)) för att fråga efter alla digitala dubbla i en instans.

1. Hämta URL för begäran och skriv från referens dokumentationen. Detta är för närvarande *inlägg `https://digitaltwins-hostname/query?api-version=2020-10-31`* för API: et för frågor.
1. I Postman anger du typen för begäran och anger URL: en för begäran och fyller i plats hållare i URL: en efter behov. Det är här du ska använda din instanss **värdnamn** från avsnittet [*krav*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Skärm bild av den nya begärans information i Postman. Fråge-URL: en från referens dokumentationen har fyllts i i rutan URL för begäran." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Kontrol lera att parametrarna som visas för begäran på fliken **params** matchar de som beskrivs i referens dokumentationen. För den här begäran i Postman `api-version` fylldes parametern automatiskt när URL: en för begäran angavs i föregående steg. Detta är den enda parameter som krävs för fråge-API, så det här steget är färdigt.
1. På fliken **auktorisering** ställer du in typen för att **Ärva autentisering från överordnad**. Detta anger att den här begäran kommer att använda den auktorisering som du konfigurerade tidigare för hela samlingen.
1. Kontrol lera att rubrikerna som visas för begäran på fliken **sidhuvud** matchar de som beskrivs i referens dokumentationen. I den här begäran har flera rubriker fyllts i automatiskt. För fråge-API: t krävs inget av rubrik alternativen, så det här steget är färdigt.
1. Kontrol lera att texten som visas för begäran på fliken **brödtext** stämmer överens med de behov som beskrivs i referens dokumentationen. För fråge-API krävs en JSON-text för att tillhandahålla frågetexten. Här är en exempel text för den här begäran som frågar efter alla digitala dubbla i instansen:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Skärm bild av den nya begärans information i Postman på fliken brödtext. Den innehåller en RAW JSON-text med en fråga av typen SELECT * FROM DIGITALTWINS." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Mer information om hur du kan använda Azures digitala dubbla frågor finns i [*How-to: fråga den dubbla grafen*](how-to-query-graph.md).

1. Kontrol lera referens dokumentationen för alla andra fält som kan krävas för din typ av begäran. För fråge-API: t har alla krav nu uppnåtts i Postman-begäran, så det här steget är klar.
1. Använd knappen **Skicka** för att skicka din slutförda begäran.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Skärm bild av Postman som visar information om den nya begäran. Knappen Skicka är markerad." lightbox="media/how-to-use-postman/postman-request-send.png":::

När du har skickat begäran visas svars informationen i fönstret Postman under begäran. Du kan visa svarets status kod och brödtext.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Skärm bild av den skickade begäran i Postman. Under förfrågnings detaljerna visas svaret. Status är 200 OK och brödtext visar frågeresultat." lightbox="media/how-to-use-postman/postman-request-response.png":::

Du kan också jämföra svaret på förväntade svars data som anges i referens dokumentationen för att kontrol lera resultatet eller lära dig mer om eventuella fel som uppstår.

## <a name="next-steps"></a>Nästa steg

Läs mer om de digitala dubbla API: erna genom att läsa [*så här använder du Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md), eller Visa [referens dokumentationen för REST-API: erna](/rest/api/azure-digitaltwins/).