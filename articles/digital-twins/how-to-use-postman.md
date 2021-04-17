---
title: Gör förfrågningar med Postman
titleSuffix: Azure Digital Twins
description: Lär dig hur du konfigurerar och använder Postman för att testa Azure Digital Twins API:er.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: a528e224511fda363afb80a7749a018e07b5fa26
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588011"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Så här använder du Postman för att skicka begäranden till Azure Digital Twins API:er

[Postman](https://www.getpostman.com/) är ett REST-testverktyg som tillhandahåller viktiga FUNKTIONER för HTTP-begäranden i ett skrivbords- och plugin-baserat grafiskt användargränssnitt. Du kan använda den för att skapa HTTP-begäranden och skicka dem till Azure Digital Twins [REST-API:er](how-to-use-apis-sdks.md).

I den här artikeln beskrivs hur du konfigurerar [Postman REST-klienten](https://www.getpostman.com/) så att den interagerar med Azure Digital Twins API:er med hjälp av följande steg:

1. Använd Azure CLI för att [**hämta en bearer-token som**](#get-bearer-token) du använder för att göra API-begäranden i Postman.
1. Konfigurera en [**Postman-samling och**](#about-postman-collections) konfigurera Postman REST-klienten så att den använder din bearer-token för autentisering. När du ställer in samlingen kan du välja något av följande alternativ:
    1. [**Importera**](#import-collection-of-azure-digital-twins-apis) en förbyggd samling med Azure Digital Twins API-begäranden.
    1. [**Skapa**](#create-your-own-collection) din egen samling från grunden.
1. [**Lägg till**](#add-an-individual-request) begäranden i din konfigurerade samling och skicka dem till Azure Digital Twins API:er.

Azure Digital Twins har två uppsättningar API:er som du kan arbeta med: **dataplanet** och **kontrollplanet**. Mer information om skillnaden mellan dessa API-uppsättningar finns i [*How-to: Use the Azure Digital Twins API:er och SDKs*](how-to-use-apis-sdks.md). Den här artikeln innehåller information om båda API-uppsättningarna.

## <a name="prerequisites"></a>Förutsättningar

Om du vill fortsätta med att använda Postman för Azure Digital Twins-API:er måste du konfigurera en Azure Digital Twins-instans och ladda ned Postman. Resten av det här avsnittet går igenom de här stegen.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurera en Azure Digital Twins instans

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Ladda ned Postman

Ladda sedan ned skrivbordsversionen av Postman-klienten. Gå till [*www.getpostman.com/apps*](https://www.getpostman.com/apps) och följ anvisningarna för att ladda ned appen.

## <a name="get-bearer-token"></a>Hämta bearer-token

Nu när du har skapat Postman och din Azure Digital Twins-instans måste du hämta en bearer-token som Postman-begäranden kan använda för att auktorisera mot de Azure Digital Twins API:erna.

Det finns flera möjliga sätt att hämta denna token. Den här artikeln använder [Azure CLI för](/cli/azure/install-azure-cli) att logga in på ditt Azure-konto och hämta en token på det sättet.

Om du har ett Azure CLI [installerat lokalt](/cli/azure/install-azure-cli)kan du starta en kommandotolk på datorn för att köra följande kommandon.
Annars kan du öppna [Azure Cloud Shell](https://shell.azure.com) i webbläsaren och köra kommandona där.

1. Kontrollera först att du är inloggad i Azure med rätt autentiseringsuppgifter genom att köra det här kommandot:

    ```azurecli-interactive
    az login
    ```

2. Använd sedan kommandot [az account get-access-token](/cli/azure/account#az_account_get_access_token) för att hämta en bearer-token med åtkomst till Azure Digital Twins tjänsten. I det här kommandot skickar du resurs-ID:t för Azure Digital Twins-tjänstslutpunkten för att få en åtkomsttoken som kan komma åt Azure Digital Twins resurser. 

    Vilken kontext som krävs för token beror på vilken uppsättning API:er du använder, så använd flikarna nedan för att välja mellan API:er för [dataplanet](how-to-use-apis-sdks.md#overview-data-plane-apis) och [kontrollplanet.](how-to-use-apis-sdks.md#overview-control-plane-apis)

    # <a name="data-plane"></a>[Dataplanet](#tab/data-plane)
    
    Om du vill hämta en token som ska användas **med API:erna** för dataplanet använder du följande statiska värde för tokenkontexten: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Det här är resurs-ID:t för Azure Digital Twins tjänstslutpunkt.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)
    
    Om du vill hämta en token som ska användas **med kontrollplanets** API:er använder du följande värde för tokenkontexten: `https://management.azure.com/` .
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Om du behöver komma åt din Azure Digital Twins-instans med hjälp av ett tjänsthuvudnamn eller användarkonto som tillhör en annan Azure Active Directory-klientorganisation från instansen, måste du begära en **token** från Azure Digital Twins-instansens "hemklient". Mer information om den här processen finns i [*How-to: Write app authentication code*](how-to-authenticate-client.md#authenticate-across-tenants).

3. Kopiera värdet för `accessToken` i resultatet och spara det för användning i nästa avsnitt. Det här är **ditt tokenvärde** som du anger för Postman för att auktorisera dina begäranden.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Skärmbild av konsolen som visar resultatet av kommandot az account get-access-token. Fältet accessToken och dess exempelvärde är markerat.":::

>[!TIP]
>Denna token är giltig i minst fem minuter och högst 60 minuter. Om du får slut på tid som tilldelats för den aktuella token kan du upprepa stegen i det här avsnittet för att hämta en ny.

Nu ska du konfigurera Postman att använda denna token för att göra API-begäranden till Azure Digital Twins.

## <a name="about-postman-collections"></a>Om Postman-samlingar

Begäranden i Postman sparas i **samlingar** (grupper av begäranden). När du skapar en samling för att gruppera dina begäranden kan du använda vanliga inställningar för många begäranden samtidigt. Detta kan förenkla auktoriseringen om du planerar att skapa fler än en begäran mot Azure Digital Twins-API:erna, eftersom du bara behöver konfigurera den här informationen en gång för hela samlingen.

När du arbetar Azure Digital Twins kan du komma igång genom att importera en förbyggd [samling med alla Azure Digital Twins begäranden](#import-collection-of-azure-digital-twins-apis). Du kanske vill göra detta om du utforskar API:erna och snabbt vill konfigurera ett projekt med exempel på förfrågningar.

Du kan också välja att börja från [](#create-your-own-collection) början genom att skapa en egen tom samling och fylla den med enskilda begäranden som endast anropar de API:er som du behöver. 

I följande avsnitt beskrivs båda dessa processer. Resten av artikeln äger rum i ditt lokala Postman-program, så gå vidare och öppna Postman-programmet på datorn nu.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importera en samling Azure Digital Twins API:er

Ett snabbt sätt att komma igång med Azure Digital Twins i Postman är att importera en förbyggd samling begäranden för Azure Digital Twins API:er.

### <a name="download-the-collection-file"></a>Ladda ned samlingsfilen

Det första steget när du importerar API-uppsättningen är att ladda ned en samling. Välj fliken nedan för ditt val av dataplan eller kontrollplan för att se de förbyggda samlingsalternativen.

# <a name="data-plane"></a>[Dataplanet](#tab/data-plane)

Det finns för närvarande Azure Digital Twins tillgängliga dataplanssamlingar att välja mellan:
* [**Azure Digital Twins Postman-samling:**](https://github.com/microsoft/azure-digital-twins-postman-samples)Den här samlingen ger en enkel komma igång-upplevelse för Azure Digital Twins i Postman. Begärandena innehåller exempeldata så att du kan köra dem med minimala ändringar som krävs. Välj den här samlingen om du vill ha en sammanfattande uppsättning API-nyckelbegäranden som innehåller exempelinformation.
    - Du hittar samlingen genom att gå till länken till lagringsplatsen och öppna filen *med namnetpostman_collection.jspå*.
* **[Azure Digital Twins dataplanet Swagger:](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)** Den här lagringsplatsen innehåller den fullständiga Swagger-filen för Azure Digital Twins API-uppsättningen, som kan laddas ned och importeras till Postman som en samling. Detta ger en omfattande uppsättning av varje API-begäran, men med tomma datakroppar i stället för exempeldata. Välj den här samlingen om du vill ha åtkomst till varje API-anrop och fylla i alla data själv.
    - Du hittar samlingen genom att gå till lagringsplatsens länk och välja mappen för den senaste specifikationsversionen. Härifrån öppnar du filen med namnet *digitaltwins.jspå*.

# <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)

Samlingen som för närvarande är tillgänglig för kontrollplanet [**är Azure Digital Twins kontrollplanet Swagger**](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Den här lagringsplatsen innehåller den fullständiga Swagger-filen Azure Digital Twins API-uppsättningen, som kan laddas ned och importeras till Postman som en samling. Detta ger en omfattande uppsättning av varje API-begäran.

Du hittar samlingen genom att gå till lagringsplatsens länk och välja mappen för den senaste specifikationsversionen. Härifrån öppnar du filen med namnet *digitaltwins.jspå*.

---

Så här laddar du ned den valda samlingen till din dator så att du kan importera den till Postman.
1. Använd länkarna ovan för att öppna samlingsfilen i GitHub i webbläsaren.
1. Välj **raw-knappen** för att öppna filens råtext.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Skärmbild av dataplanet som digitaltwins.jspå filen i GitHub. Det finns en markering runt raw-knappen." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Kopiera texten från fönstret och klistra in den i en ny fil på datorn.
1. Spara filen med filnamnstillägget *.json* (filnamnet kan vara vad du vill, så länge du kommer ihåg det för att hitta filen senare).

### <a name="import-the-collection"></a>Importera samlingen

Importera sedan samlingen till Postman.

1. Välj knappen Importera i **Postman-huvudfönstret.**
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Skärmbild av ett nyligen öppnat Postman-fönster. Knappen Importera är markerad." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. I fönstret Importera som följer väljer du **Ladda upp filer** och navigerar till samlingsfilen på den dator som du skapade tidigare. Välj Öppna.
1. Välj knappen **Importera** för att bekräfta.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Skärmbild av Postman-fönstret &quot;Importera&quot; med filen som ska importeras som en samling och knappen Importera.":::

Den nyligen importerade samlingen kan nu visas från din huvudsakliga Postman-vy på fliken Samlingar.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Skärmbild av Postman-huvudfönstret. Den nyligen importerade samlingen är markerad på fliken Samlingar." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Fortsätt sedan till nästa avsnitt för att lägga till en bearer-token i samlingen för auktorisering och ansluta den till din Azure Digital Twins-instans.

### <a name="configure-authorization"></a>Konfigurera auktorisering

Redigera sedan den samling som du har skapat för att konfigurera viss åtkomstinformation. Markera samlingen som du har skapat och välj ikonen **Visa fler åtgärder för** att hämta en meny. Välj **Redigera**.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Skärmbild av Postman. Ikonen &quot;Visa fler åtgärder&quot; för den importerade samlingen är markerad och &quot;Redigera&quot; är markerat i alternativen." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Följ de här stegen för att lägga till en bearer-token i samlingen för auktorisering. Här använder du det **tokenvärde** som du samlade in i avsnittet Hämta [bearer-token](#get-bearer-token) för att kunna använda det för alla API-begäranden i din samling.

1. Kontrollera att du är på fliken Auktorisering i dialogrutan Redigera **för din** samling. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Skärmbild av den importerade samlingens redigeringsdialogruta i Postman med fliken Auktorisering." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Ange **OAuth 2.0 som** Typ, klistra in din åtkomsttoken i rutan Åtkomsttoken och välj **Spara**.

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Skärmbild av dialogrutan Postman-redigering för den importerade samlingen på fliken Auktorisering. Typen är OAuth 2.0 och rutan Åtkomsttoken är markerad." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Ytterligare konfiguration

# <a name="data-plane"></a>[Dataplanet](#tab/data-plane)

Om du gör en [datainsamling](how-to-use-apis-sdks.md#overview-data-plane-apis) kan du hjälpa samlingen att enkelt ansluta till  dina Azure Digital Twins genom att ange några variabler som medföljer samlingarna. När många begäranden i en samling kräver samma värde (till exempel värdnamnet för din Azure Digital Twins-instans) kan du lagra värdet i en variabel som gäller för varje begäran i samlingen. Båda de nedladdningsbara samlingarna för Azure Digital Twins har i förväg skapade variabler som du kan ange på samlingsnivå.

1. Gå till fliken Variabler i **redigeringsdialogrutan för** din samling.

1. Använd din instanss **värdnamn från** [*avsnittet Förutsättningar för*](#prerequisites) att ange fältet CURRENT VALUE för relevant variabel. Välj **Spara**.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Skärmbild av den importerade samlingens redigeringsdialogruta i Postman med fliken Variabler. Fältet CURRENT VALUE (AKTUELLT VÄRDE) är markerat." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Om din samling har ytterligare variabler fyller du i och sparar även dessa värden.

När du är klar med stegen ovan är du klar med att konfigurera samlingen. Du kan stänga redigeringsfliken för samlingen om du vill.

# <a name="control-plane"></a>[Kontrollplan](#tab/control-plane)

Om du gör en [kontrollplanssamling](how-to-use-apis-sdks.md#overview-control-plane-apis) har du gjort allt du behöver för att konfigurera samlingen. Du kan stänga redigeringsfliken för samlingen om du vill och gå vidare till nästa avsnitt.

--- 

### <a name="explore-requests"></a>Utforska begäranden

Utforska sedan begärandena i API Azure Digital Twins samlingen. Du kan expandera samlingen för att visa de förskapade begärandena (sorterade efter åtgärdskategori). 

Olika begäranden kräver olika information om din instans och dess data. Om du vill se all information som krävs för att skapa en viss begäran letar du upp begärandeinformationen [i Azure Digital Twins REST API referensdokumentationen](/rest/api/azure-digitaltwins/).

Du kan redigera informationen om en begäran i Postman-samlingen med hjälp av följande steg:

1. Välj den i listan för att hämta den redigerbara informationen. 

1. Fyll i värden för variablerna som anges på **fliken Params** under **Sökvägsvariabler**.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Skärmbild av Postman. Samlingen expanderas för att visa en begäran. Avsnittet &quot;Sökvägsvariabler&quot; är markerat i begärandeinformationen." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Ange nödvändig information **om rubriker** **eller** brödtext på respektive flik.

När all nödvändig information har angetts kan du köra begäran med **knappen** Skicka.

Du kan också lägga till egna begäranden i samlingen med hjälp av processen som beskrivs i avsnittet [*Lägg till en enskild*](#add-an-individual-request) begäran nedan.

## <a name="create-your-own-collection"></a>Skapa en egen samling

I stället för att importera den befintliga samlingen av Azure Digital Twins API:er kan du också skapa en egen samling från grunden. Du kan sedan fylla i den med enskilda begäranden med hjälp [Azure Digital Twins REST API referensdokumentationen](/rest/api/azure-digitaltwins/).

### <a name="create-a-postman-collection"></a>Skapa en Postman-samling

1. Om du vill skapa en samling väljer **du knappen** Nytt i postman-huvudfönstret.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Skärmbild av Postman-huvudfönstret. Knappen &quot;Nytt&quot; är markerad." lightbox="media/how-to-use-postman/postman-new.png":::

    Välj en typ av **Samling.**

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Skärmbild av dialogrutan &quot;Skapa ny&quot; i Postman. Alternativet &quot;Samling&quot; är markerat.":::

1. Då öppnas en flik där du kan fylla i information om den nya samlingen. Välj ikonen Redigera bredvid samlingens standardnamn (**Ny samling**) för att ersätta den med ditt eget namnval. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Skärmbild av den nya samlingens redigeringsdialogruta i Postman. Ikonen Redigera bredvid namnet &quot;Ny samling&quot; är markerad." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Fortsätt sedan till nästa avsnitt för att lägga till en bearer-token i samlingen för auktorisering.

### <a name="configure-authorization"></a>Konfigurera auktorisering

Följ dessa steg om du vill lägga till en token för en bearer i samlingen för auktorisering. Det är här du använder **det tokenvärde** som du samlade in i avsnittet Hämta [bearer-token](#get-bearer-token) för att kunna använda det för alla API-begäranden i din samling.

1. Gå till fliken Auktorisering i redigeringsdialogrutan för den **nya** samlingen.

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Skärmbild av den nya samlingens redigeringsdialogruta i Postman med fliken Auktorisering." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Ange Typ till **OAuth 2.0,** klistra in din åtkomsttoken i rutan Åtkomsttoken och välj **Spara.**

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Skärmbild av dialogrutan Postman-redigering för den nya samlingen på fliken Auktorisering. Typen är OAuth 2.0 och rutan Åtkomsttoken är markerad." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

När du är klar med stegen ovan är du klar med att konfigurera samlingen. Du kan stänga redigeringsfliken för den nya samlingen om du vill.

Den nya samlingen kan ses från din huvudsakliga Postman-vy på fliken Samlingar.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Skärmbild av Postman-huvudfönstret. Den nya anpassade samlingen är markerad på fliken Samlingar." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Lägga till en enskild begäran

Nu när din samling har ställts in kan du lägga till dina egna begäranden till Azure Digital Twin-API:erna.

1. Om du vill skapa en begäran använder du **knappen** Nytt igen.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Skärmbild av Postman-huvudfönstret. Knappen &quot;Nytt&quot; är markerad." lightbox="media/how-to-use-postman/postman-new.png":::

    Välj en typ av **Begäran.**

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Skärmbild av dialogrutan Skapa ny i Postman. Alternativet &quot;Begäran&quot; är markerat.":::

1. Den här åtgärden öppnar fönstret SPARA BEGÄRAN, där du kan ange ett namn för din begäran, ge den en valfri beskrivning och välja den samling som den ingår i. Fyll i informationen och spara begäran i samlingen som du skapade tidigare.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Skärmbild av fönstret &quot;Spara begäran&quot; i Postman som visar de fält som beskrivs. Knappen &quot;Spara Azure Digital Twins en samling&quot; är markerad.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Nu kan du visa din begäran under samlingen och välja den för att hämta den redigerbara informationen.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Skärmbild av Postman. Den Azure Digital Twins-samlingen expanderas för att visa information om begäran." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Ange information om begäran

Om du vill göra en Postman-begäran till Azure Digital Twins API:er behöver du URL:en för API:et och information om vilken information det kräver. Du hittar den här informationen i [Azure Digital Twins REST API referensdokumentationen](/rest/api/azure-digitaltwins/).

För att fortsätta med en exempelfråga använder den här artikeln fråge-API:et (och dess referensdokumentation [)](/rest/api/digital-twins/dataplane/query/querytwins)för att fråga efter alla digitala tvillingar i en instans.

1. Hämta begärande-URL:en och skriv från referensdokumentationen. För fråge-API:et är detta för närvarande *POST `https://digitaltwins-hostname/query?api-version=2020-10-31`*.
1. I Postman anger du typ för begäran och anger begärande-URL:en. Fyll i platshållarna i URL:en efter behov. Här använder du instansens **värdnamn från** [*avsnittet Förutsättningar.*](#prerequisites)
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Skärmbild av information om den nya begäran i Postman. Fråge-URL:en från referensdokumentationen har fyllts i i rutan url för begäran." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Kontrollera att parametrarna som visas för begäran på fliken **Params** matchar de som beskrivs i referensdokumentationen. För den här begäran i Postman `api-version` fylls parametern i automatiskt när begärande-URL:en angavs i föregående steg. För fråge-API:et är detta den enda obligatoriska parametern, så det här steget är klart.
1. På fliken **Authorization (Auktorisering)** anger du Type **(Typ) till Inherit auth from parent (Ärv autentisering från överordnad).** Detta anger att den här begäran använder den auktorisering som du konfigurerade tidigare för hela samlingen.
1. Kontrollera att huvudena som visas för begäran på **fliken Rubriker** matchar de som beskrivs i referensdokumentationen. För den här begäran har flera huvuden fyllts i automatiskt. För fråge-API:et krävs inget av huvudalternativen, så det här steget är klart.
1. Kontrollera att brödtexten som visas för begäran på **fliken Brödtext** matchar de behov som beskrivs i referensdokumentationen. För fråge-API:et krävs en JSON-brödtext för att tillhandahålla frågetexten. Här är ett exempel på en brödtext för den här begäran som frågar efter alla digital twins i instansen:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Skärmbild av information om den nya begäran i Postman på fliken Brödtext. Den innehåller en rå JSON-brödtext med frågan &quot;SELECT * FROM DIGITALTWINS&quot;." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Mer information om att skapa Azure Digital Twins frågor finns i [*How-to: Query the twin graph*](how-to-query-graph.md).

1. I referensdokumentationen finns andra fält som kan krävas för din typ av begäran. För fråge-API:et har alla krav nu uppfyllts i Postman-begäran, så det här steget är klart.
1. Använd knappen **Skicka** för att skicka din slutförda begäran.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Skärmbild av Postman som visar information om den nya begäran. Knappen Skicka är markerad." lightbox="media/how-to-use-postman/postman-request-send.png":::

När du har skickat begäran visas svarsinformationen i Postman-fönstret under begäran. Du kan visa svarets statuskod och brödtext.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Skärmbild av den skickade begäran i Postman. Under begärandeinformationen visas svaret. Statusen är 200 OK och brödtexten visar frågeresultat." lightbox="media/how-to-use-postman/postman-request-response.png":::

Du kan också jämföra svaret med de förväntade svarsdata som anges i referensdokumentationen för att verifiera resultatet eller lära dig mer om eventuella fel som uppstår.

## <a name="next-steps"></a>Nästa steg

Mer information om Digital Twins-API:er finns i [*How-to: Use the Azure Digital Twins API:er och SDKs*](how-to-use-apis-sdks.md), eller visa referensdokumentationen för [REST-API:erna](/rest/api/azure-digitaltwins/).