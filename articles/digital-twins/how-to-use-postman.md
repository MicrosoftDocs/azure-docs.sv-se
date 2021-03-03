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
ms.openlocfilehash: d99ec80308152ce9e4870da809acaa25c663d98d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715708"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Så här använder du Postman för att skicka förfrågningar till Azure Digitals dubbla API: er

[Postman](https://www.getpostman.com/) är ett rest-testverktyg som tillhandahåller nyckel funktioner för http-begäran i ett Skriv bords-och plugin-baserade GUI. Du kan använda den för att hantverka HTTP-begäranden och skicka dem till [REST-API: erna för Azure Digitals](how-to-use-apis-sdks.md).

Den här artikeln beskriver hur du konfigurerar [Postman rest-klienten](https://www.getpostman.com/) så att den interagerar med Azure Digitals dubbla API: er med hjälp av följande steg:

1. Använd [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) för att hämta en Bearer-token som du ska använda för att skapa API-begäranden i Postman.
1. Konfigurera en Postman-samling och konfigurera Postman REST-klienten så att den använder din Bearer-token för att autentisera.
1. Använd den konfigurerade Postman för att skapa och skicka en begäran till Azure Digitals dubbla API: er.

## <a name="prerequisites"></a>Förutsättningar

Om du vill fortsätta med att använda Postman för att komma åt Azures digitala dubbla API: er måste du konfigurera en digital Azure-instans och hämta Postman. Resten av det här avsnittet vägleder dig genom de här stegen.

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurera Azure Digitals dubbla instanser

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Hämta Postman

Sedan hämtar du Skriv bords versionen av Postman-klienten. Gå till [*www.getpostman.com/apps*](https://www.getpostman.com/apps) och följ anvisningarna för att ladda ned appen.

## <a name="get-bearer-token"></a>Hämta Bearer-token

Nu när du har konfigurerat Postman och din Azure Digital-instansen måste du skaffa en Bearer-token som Postman-begäranden kan använda för att auktorisera mot Azure Digitals dubbla API: er.

Det finns flera möjliga sätt att hämta denna token. I den här artikeln används [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) för att logga in på ditt Azure-konto och få en token på detta sätt.

Om du har ett Azure CLI [installerat lokalt](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)kan du starta en kommando tolk på datorn för att köra följande kommandon.
Annars kan du öppna ett [Azure Cloud Shell](https://shell.azure.com) -fönster i webbläsaren och köra kommandona där.

1. Kontrol lera först att du är inloggad i Azure med rätt autentiseringsuppgifter genom att köra det här kommandot:

    ```azurecli-interactive
    az login
    ```

1. Sedan använder du kommandot [AZ Account get-Access-token](/cli/azure/account?preserve-view=true&view=azure-cli-latest#az_account_get_access_token) för att hämta en Bearer-token med åtkomst till tjänsten Azure Digitals dubbla.

    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

1. Kopiera värdet för `accessToken` i resultatet och spara det så att det används i nästa avsnitt. Det här är ditt **token-värde** som du kommer att ge till Postman för att autentisera dina begär Anden.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Vy över ett lokalt konsol fönster som visar resultatet av kommandot AZ Account get-Access-token. Ett av fälten i resultatet kallas accessToken och dess exempel värde – från och med ey--är markerat.":::

>[!TIP]
>Denna token är giltig i minst fem minuter och högst 60 minuter. Om du tar slut på den tid som tilldelats aktuell token kan du upprepa stegen i det här avsnittet för att få en ny.

## <a name="set-up-postman-collection-and-authorization"></a>Konfigurera PostMan-samling och auktorisering

Konfigurera sedan Postman för att göra API-begäranden.
Dessa steg sker i ditt lokala Postman-program, så gå vidare och öppna Postman-programmet på datorn.

### <a name="create-a-postman-collection"></a>Skapa en Postman-samling

Begär anden i Postman sparas i **samlingar** (grupper av förfrågningar). När du skapar en samling för att gruppera dina begär Anden kan du använda vanliga inställningar för många begär Anden samtidigt. Detta kan avsevärt förenkla auktoriseringen om du planerar att skapa fler än en begäran mot Azures digitala dubbla API: er, eftersom du bara behöver konfigurera autentisering en gång för hela samlingen.

1. Om du vill skapa en samling trycker du på knappen *+ ny samling* .

    :::image type="content" source="media/how-to-use-postman/postman-new-collection.png" alt-text="Vy över ett nyligen öppnat Boxman-fönster. Knappen Ny samling är markerad":::

1. I fönstret *skapa en ny samling* som följer anger du ett **namn** och en valfri **Beskrivning** för samlingen.

Fortsätt sedan till nästa avsnitt för att lägga till en Bearer-token i samlingen för auktorisering.

### <a name="add-authorization-token-and-finish-collection"></a>Lägg till autentiseringstoken och slutför insamling

1. Gå till fliken *auktorisering* i dialog rutan *skapa en ny samling* . Här placerar du det **token-värde** som du samlade in i avsnittet [Hämta Bearer-token](#get-bearer-token) för att kunna använda det för alla API-begäranden i din samling.

    :::image type="content" source="media/how-to-use-postman/postman-authorization.png" alt-text="Fönstret Skapa en ny SAMLINGs Postman som visar fliken auktorisering.":::

1. Ange *typen* till _**OAuth 2,0**_ och klistra in åtkomsttoken i rutan *åtkomsttoken* .

    :::image type="content" source="media/how-to-use-postman/postman-paste-token.png" alt-text="Fönstret Skapa en ny SAMLINGs Postman som visar fliken auktorisering. En typ av typen OAuth 2,0 är markerad, och rutan åtkomsttoken där åtkomsttoken kan klistras in är markerad.":::

1. När du har klistrat in i din Bearer-token trycker du på *skapa* för att slutföra skapandet av samlingen.

Din nya samling kan nu visas från din huvudsakliga Postman-vy under *samlingar*.

:::image type="content" source="media/how-to-use-postman/postman-post-collection.png" alt-text="Vy över huvud fönstret Postman. Den nyligen skapade samlingen är markerad i fliken samlingar.":::

## <a name="create-a-request"></a>Skapa en begäran

När du har slutfört föregående steg kan du skapa förfrågningar till Azures digitala dubbla API: er.

1. Om du vill skapa en begäran trycker du på knappen *+ ny* .

    :::image type="content" source="media/how-to-use-postman/postman-new-request.png" alt-text="Vy över huvud fönstret Postman. Knappen &quot;nytt&quot; är markerad":::

1. Välj *begäran*.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Vy över de alternativ som du kan välja för att skapa något nytt. Alternativet Request är markerat":::

1. Den här åtgärden öppnar fönstret *Spara förfrågan* där du kan ange ett namn för din begäran, ge det en valfri beskrivning och välja den samling som den är en del av. Fyll i informationen och spara begäran till samlingen som du skapade tidigare.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Vy över fönstret Spara förfrågan där du kan fylla i de fält som beskrivs. Knappen &quot;Spara till Azure Digitals sammanflätad samling&quot; är markerad":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Nu kan du Visa din begäran under samlingen och välja den för att hämta redigerbar information.

:::image type="content" source="media/how-to-use-postman/postman-request-details.png" alt-text="Vy över huvud fönstret Postman. Azure Digitals-samlingen är expanderad och begäran &quot;fråga om dubbla&quot; är markerad. Information om begäran visas i mitten av sidan." lightbox="media/how-to-use-postman/postman-request-details.png":::

### <a name="set-request-details"></a>Ange information om begäran

Om du vill göra en Postman-begäran till en av Azures digitala dubbla API: er behöver du URL: en till API: et och information om vilka uppgifter som krävs. Du hittar den här informationen i [Azure digitals REST API referens dokumentation](/rest/api/azure-digitaltwins/).

Om du vill fortsätta med en exempel fråga kommer den här artikeln att använda fråge-API: t (och dess [referens dokumentation](/rest/api/digital-twins/dataplane/query/querytwins)) för att fråga efter alla digitala dubbla i en instans.

1. Hämta URL för begäran och skriv från referens dokumentationen. Detta är för närvarande *inlägg `https://digitaltwins-hostname/query?api-version=2020-10-31`* för API: et för frågor.
1. I Postman anger du typen för begäran och anger URL: en för begäran och fyller i plats hållare i URL: en efter behov. Det är här du ska använda din instanss **värdnamn** från avsnittet [*krav*](#prerequisites) .
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="I informationen om den nya begäran har fråge-URL: en från referens dokumentationen fyllts i i rutan URL för begäran." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Kontrol lera att parametrarna som visas för begäran på fliken *params* matchar de som beskrivs i referens dokumentationen. För den här begäran i Postman `api-version` fylldes parametern automatiskt när URL: en för begäran angavs i föregående steg. Detta är den enda parameter som krävs för fråge-API, så det här steget är färdigt.
1. På fliken *auktorisering* ställer du in *typen* för att *Ärva autentisering från överordnad*. Detta anger att den här begäran kommer att använda den autentisering som du konfigurerade tidigare för hela samlingen.
1. Kontrol lera att rubrikerna som visas för begäran på fliken *sidhuvud* matchar de som beskrivs i referens dokumentationen. I den här begäran har flera rubriker fyllts i automatiskt. För fråge-API: t krävs inget av rubrik alternativen, så det här steget är färdigt.
1. Kontrol lera att texten som visas för begäran på fliken *brödtext* stämmer överens med de behov som beskrivs i referens dokumentationen. För fråge-API krävs en JSON-text för att tillhandahålla frågetexten. Här är en exempel text för den här begäran som frågar efter alla digitala dubbla i instansen:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="I informationen om den nya begäran visas fliken brödtext. Den innehåller en RAW JSON-text med en fråga av typen SELECT * FROM DIGITALTWINS." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Mer information om hur du kan använda Azures digitala dubbla frågor finns i [*How-to: fråga den dubbla grafen*](how-to-query-graph.md).

1. Kontrol lera referens dokumentationen för alla andra fält som kan krävas för din typ av begäran. För fråge-API: t har alla krav nu uppnåtts i Postman-begäran, så det här steget är klar.
1. Använd knappen *Skicka* för att skicka din slutförda begäran.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Nära informationen om den nya begäran är knappen Skicka markerad." lightbox="media/how-to-use-postman/postman-request-send.png":::

När du har skickat begäran visas svars informationen i fönstret Postman under begäran. Du kan visa svarets status kod och brödtext.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Under informationen om den skickade begäran markeras information om svaret. Det finns en status på 200 OK och brödtext som beskriver digitala dubbla som returnerades av frågan." lightbox="media/how-to-use-postman/postman-request-response.png":::

Du kan också jämföra svaret på förväntade svars data som anges i referens dokumentationen för att kontrol lera resultatet eller lära dig mer om eventuella fel som uppstår.

## <a name="next-steps"></a>Nästa steg

Läs mer om de digitala dubbla API: erna genom att läsa [*så här använder du Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md), eller Visa [referens dokumentationen för REST-API: erna](/rest/api/azure-digitaltwins/).