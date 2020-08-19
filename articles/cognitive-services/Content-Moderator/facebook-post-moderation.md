---
title: 'Självstudie: måttlig Facebook-innehåll – Content Moderator'
titleSuffix: Azure Cognitive Services
description: I självstudien lär du dig att använda maskininlärningsbaserade Content Moderator till att moderera Facebook-inlägg och kommentarer.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 9d8801037be55a262268afcd6e8f5751d158c76e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548522"
---
# <a name="tutorial-moderate-facebook-posts-and-commands-with-azure-content-moderator"></a>Självstudie: måttlig Facebook-inlägg och-kommandon med Azure Content Moderator

I den här självstudien får du lära dig hur du använder Azure-Content Moderator för att få hjälp med att måttliga inlägg och kommentarer på en Facebook-sida. Facebook skickar det innehåll som publiceras av besökarna till tjänsten Content Moderator. Sedan kommer dina Content Moderator-arbetsflöden att publicera innehållet eller skapa granskningar i gransknings verktyget, beroende på innehållet i poängen och tröskelvärdena. Se [Build 2017 demo-videon](https://channel9.msdn.com/Events/Build/2017/T6033) för ett arbets exempel för det här scenariot.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa ett Content Moderator-team.
> * Skapa Azure Functions som lyssnar efter HTTP-händelser från Content Moderator och Facebook.
> * Länka en Facebook-sida till Content Moderator med ett Facebook-program.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar.

Det här diagrammet illustrerar varje komponent i det här scenariot:

![Diagram över Content Moderator ta emot information från Facebook genom "FBListener" och skicka information via "CMListener"](images/tutorial-facebook-moderation.png)

> [!IMPORTANT]
> I 2018 implementerade Facebook en striktare först konsumentsajter-princip för Facebook-appar. Du kommer inte att kunna slutföra stegen i den här själv studie kursen om din app inte har granskats och godkänts av Facebooks gransknings teamet.

## <a name="prerequisites"></a>Förutsättningar

- En prenumerationsnyckeln för Content Moderator. Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på Content Moderator-tjänsten och få din nyckel.
- Ett [Facebook-konto](https://www.facebook.com/).

## <a name="create-a-review-team"></a>Skapa ett granskningsteam

Mer information om hur du registrerar dig för [Content moderator gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) och hur du skapar en gransknings grupp finns i [försök Content moderator på webb](quick-start.md) snabb starten. Anteckna värdet för **team-ID:t** på sidan **Autentiseringsuppgifter**.

## <a name="configure-image-moderation-workflow"></a>Konfigurera arbets flöde för avbildnings redigering

Använd arbets flödes guiden för att [definiera, testa och använda](review-tool-user-guide/workflows.md) för att skapa ett anpassat bild arbets flöde. Content Moderator kommer att använda det här arbets flödet för att automatiskt kontrol lera bilder på Facebook och skicka några till gransknings verktyget. Anteckna **namnet**på arbets flödet.

## <a name="configure-text-moderation-workflow"></a>Konfigurera arbets flöde för text redigering

Mer information finns i Guide för att [definiera, testa och använda arbets flöden](review-tool-user-guide/workflows.md) ; nu skapar du ett anpassat text arbets flöde. Content Moderator kommer att använda det här arbets flödet för att kontrol lera text innehåll automatiskt. Anteckna **namnet**på arbets flödet.

![Konfigurera textarbetsflöden](images/text-workflow-configure.PNG)

Testa arbets flödet med hjälp av knappen **Kör arbets flöde** .

![Testa textarbetsflödet](images/text-workflow-test.PNG)

## <a name="create-azure-functions"></a>Skapa Azure Functions

Logga in på [Azure Portal](https://portal.azure.com/) och följ de här stegen:

1. Skapa en Azure-funktionsapp enligt beskrivningen på sidan [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal).
1. Gå till den nyligen skapade Funktionsapp.
1. I appen går du till fliken **plattforms funktioner** och väljer **konfiguration**. I avsnittet **program inställningar** på nästa sida väljer du **ny program inställning** för att lägga till följande nyckel/värde-par:
    
    | Namn på App-inställning | värde   | 
    | -------------------- |-------------|
    | `cm:TeamId`   | Ditt team-ID för Content Moderator  | 
    | `cm:SubscriptionKey` | Prenumerationsnyckeln för Content Moderator – Se [Autentiseringsuppgifter](review-tool-user-guide/credentials.md) |
    | `cm:Region` | Regionnamnet i Content Moderator utan blanksteg. Du kan hitta det här namnet i fältet **plats** på fliken **Översikt** i Azure-resursen.|
    | `cm:ImageWorkflow` | Namnet på arbetsflödet som ska köras på bilderna |
    | `cm:TextWorkflow` | Namnet på arbetsflödet som ska köras på texten |
    | `cm:CallbackEndpoint` | URL för CMListener-Funktionsapp som du kommer att skapa senare i den här hand boken |
    | `fb:VerificationToken` | En hemlig token som du skapar, används för att prenumerera på Facebook-flöde-händelser |
    | `fb:PageAccessToken` | Din åtkomsttoken för Facebooks Graph API upphör inte att gälla och kan dölja/ta bort inlägg åt dig. Du får den här token i ett senare steg. |

    Klicka på knappen **Spara** överst på sidan.

1. Gå tillbaka till fliken **plattforms funktioner** . Använd **+** knappen i det vänstra fönstret för att öppna fönstret **ny funktion** . Funktionen som du håller på att skapa kommer att ta emot händelser från Facebook.

    ![Azure Functions fönstret med knappen Lägg till funktion markerat.](images/new-function.png)

    1. Klicka på panelen med texten **http-utlösare**.
    1. Ange namnet **FBListener**. Fältet **Auktorisationsnivå** ska vara inställt på **Funktion**.
    1. Klicka på **Skapa**.
    1. Ersätt innehållet i filen **Run. CSX** med innehållet från **FbListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/FbListener/run.csx?range=1-154)]

1. Skapa en ny **http-utlösare** med namnet **CMListener**. Den här funktionen tar emot händelser från Content Moderator. Ersätt innehållet i filen **Run. CSX** med innehållet från **CMListener/Run. CSX**

    [!code-csharp[FBListener: csx file](~/samples-fbPageModeration/CmListener/run.csx?range=1-110)]

---

## <a name="configure-the-facebook-page-and-app"></a>Konfigurera Facebook-sidan och appen

1. Skapa en Facebook-app.

    ![Sidan Facebook-utvecklare](images/facebook-developer-app.png)

    1. Gå till [Facebooks webbplats för utvecklare](https://developers.facebook.com/)
    1. Klicka på **Mina appar**.
    1. Lägg till en ny app.
    1. ge den namnet något
    1. Välj **Webhooks – > konfigureras**
    1. Välj **sida** i list menyn och välj **Prenumerera på det här objektet**
    1. Ange **FBListener Url** som motringnings-URL och den **verifieringstoken** som du konfigurerade i **Funktionsappinställningar**
    1. När prenumerationen är klar bläddrar du i flödet och väljer **prenumerera**.
    1. Klicka på knappen **testa** på **feed** -raden för att skicka ett test meddelande till din FBListener Azure-funktion och tryck sedan på knappen **Skicka till min server** . Du bör se begäran som tas emot på din FBListener.

1. Skapa en Facebook-sida.

    > [!IMPORTANT]
    > I 2018 implementerade Facebook en mer strikt först konsumentsajter av Facebook-appar. Du kommer inte att kunna köra avsnitt 2, 3 och 4 om din app inte har granskats och godkänts av Facebooks gransknings teamet.

    1. Gå till [Facebook](https://www.facebook.com/bookmarks/pages) och skapa en **ny Facebook-sida**.
    1. Ge Facebook-appen åtkomst till sidan genom att följa dessa steg:
        1. Gå till [Graph API Explorer](https://developers.facebook.com/tools/explorer/) (Graph API-utforskaren).
        1. Välj **program**.
        1. Välj **Page Access Token** (Åtkomsttoken för sida), skicka en **Get**-begäran.
        1. Klicka på **Sid-ID** i svaret.
        1. Lägg nu till **/subscribed_apps** i URL:en och skicka en **Get**-begäran (tomt svar).
        1. Skicka en **Post**-begäran. Du får svaret **success: true**.

3. Skapa en åtkomsttoken för Graph API som inte upphör att gälla.

    1. Gå till [Graph API Explorer](https://developers.facebook.com/tools/explorer/) (Graph API-utforskaren).
    2. Välj alternativet**Program**.
    3. Välj alternativet **Get User Access Token** (Hämta åtkomsttoken för användare).
    4. Under **Välj behörigheter** väljer du **manage_pages** och **publish_pages**.
    5. Vi använder **åtkomsttoken** (kortlivad token) i nästa steg.

4. Vi använder Postman för kommande steg.

    1. Öppna **Postman** (eller hämta den [här](https://www.getpostman.com/)).
    2. Importera följande två filer:
        1. [Postman Collection](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/Facebook%20Permanant%20Page%20Access%20Token.postman_collection.json)
        2. [Postman Environment](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FB%20Page%20Access%20Token%20Environment.postman_environment.json)       
    3. Uppdatera följande miljövariabler:
    
        | Tangent | Värde   | 
        | -------------------- |-------------|
        | appId   | Infoga din Facebook-appidentifierare här  | 
        | appSecret | Infoga din Facebook-apphemlighet här | 
        | short_lived_token | Infoga den kortlivade åtkomsttoken som du skapade i föregående steg |
    4. Kör nu de 3 API:er som visas i samlingen: 
        1. Välj **Generate Long-Lived Access Token** (Generera långlivad åtkomsttoken) och klicka på **Skicka**.
        2. Välj **Hämta användar-ID** och klicka på **Skicka**.
        3. Välj **Get Permanent Page Access Token** (Hämta permanent åtkomsttoken för sida) och klicka på **Skicka**.
    5. Kopiera värdet **access_token** från svaret och tilldela det till appinställningen **fb:PageAccessToken**.

Lösningen skickar alla bilder och texter som publiceras på din Facebook-sida till Content Moderator. Sedan anropas de arbets flöden som du konfigurerade tidigare. Innehållet som inte uppfyller dina kriterier som definierats i arbets flödena skickas till granskningar i gransknings verktyget. Resten av innehållet publiceras automatiskt.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett program för att analysera produktbilder i syfte att tagga dem via produkttyp och låta ett granskningsteam fatta välgrundade beslut om innehållsmoderering. Härnäst får du mer information om bildmoderering.

> [!div class="nextstepaction"]
> [Bildmoderering](./image-moderation-api.md)
