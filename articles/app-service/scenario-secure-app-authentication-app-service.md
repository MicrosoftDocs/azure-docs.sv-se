---
title: Självstudie – Lägg till autentisering till en webbapp på Azure App Service | Azure
description: I den här självstudien får du lära dig att aktivera autentisering och auktorisering för en webbapp som körs på Azure App Service. Begränsa åtkomsten till webbappen till användare i din organisation.
services: active-directory, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: a8bd2ef1348692bf57f7e5cb7b6606cfcfd324fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96905578"
---
# <a name="tutorial-add-authentication-to-your-web-app-running-on-azure-app-service"></a>Självstudie: Lägg till autentisering i din webbapp som körs på Azure App Service

Lär dig att aktivera autentisering för din webbapp som körs på Azure App Service och begränsa åtkomsten till användare i din organisation.

:::image type="content" source="./media/scenario-secure-app-authentication-app-service/web-app-sign-in.svg" alt-text="Diagram som visar användar inloggning." border="false":::

App Service tillhandahåller stöd för inbyggd autentisering och auktorisering, så att du kan logga in användare och komma åt data genom att skriva minimal eller ingen kod i din webbapp. Du behöver inte använda modulen App Service autentisering/auktorisering, men hjälper till att förenkla autentisering och auktorisering för din app. Den här artikeln visar hur du skyddar din webbapp med modulen App Service autentisering/auktorisering genom att använda Azure Active Directory (Azure AD) som identitets leverantör.

Modulen autentisering/auktorisering är aktive rad och konfigurerad via Azure Portal-och app-inställningar. Inga SDK: er, specifika språk eller ändringar av program kod krävs. En rad identitets leverantörer stöds, som innehåller Azure AD, Microsoft-konto, Facebook, Google och Twitter. När modulen autentisering/auktorisering är aktive rad passerar varje inkommande HTTP-begäran genom den innan den hanteras av app-koden. Mer information finns [i autentisering och auktorisering i Azure App Service](overview-authentication-authorization.md).

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera autentisering för webbappen.
> * Begränsa åtkomsten till webbappen till användare i din organisation.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-publish-a-web-app-on-app-service"></a>Skapa och publicera en webbapp på App Service

I den här självstudien måste du ha en webbapp distribuerad till App Service. Du kan använda en befintlig webbapp, eller så kan du följa [ASP.net Core snabb start](quickstart-dotnetcore.md) för att skapa och publicera en ny webbapp för att app service.

Oavsett om du använder en befintlig webbapp eller skapar en ny, noterar du namnet på webb programmet och namnet på den resurs grupp som webbappen distribueras till. Du behöver dessa namn i den här självstudien. I den här självstudien innehåller exempel namn i procedurer och skärm dum par *SecureWebApp*.

## <a name="configure-authentication-and-authorization"></a>Konfigurera autentisering och auktorisering

Nu har du en webbapp som körs på App Service. Sedan aktiverar du autentisering och auktorisering för webbappen. Du använder Azure AD som identitets leverantör. Mer information finns i [Konfigurera Azure AD-autentisering för ditt App Service-program](configure-authentication-provider-aad.md).

I [Azure Portal](https://portal.azure.com) -menyn väljer du **resurs grupper** eller söker efter och väljer **resurs grupper** från vilken sida som helst.

I **resurs grupper** söker du efter och väljer din resurs grupp. I **Översikt** väljer du appens hanterings sida.

:::image type="content" alt-text="Skärm bild som visar hur du väljer appens hanterings sida." source="./media/scenario-secure-app-authentication-app-service/select-app-service.png":::

På appens vänstra meny väljer du **autentisering/auktorisering** och aktiverar App Service autentisering genom att välja **på**.

För **Åtgärd att vidta när en begäran inte har autentiserats** väljer du **Logga in med Azure Active Directory**.

Under **autentiseringsproviders** väljer du **Azure Active Directory**. Välj **Express** och godkänn sedan standardinställningarna för att skapa en ny Active Directory-app. Välj **OK**.

:::image type="content" alt-text="Skärm bild som visar Express autentisering." source="./media/scenario-secure-app-authentication-app-service/configure-authentication.png":::

På sidan **autentisering/auktorisering** väljer du **Spara**.

Uppdatera portal sidan när du ser meddelandet med meddelandet `Successfully saved the Auth Settings for <app-name> App` .

Nu har du en app som skyddas av App Service autentisering och auktorisering.

## <a name="verify-limited-access-to-the-web-app"></a>Verifiera begränsad åtkomst till webbappen

När du aktiverade modulen App Service autentisering/auktorisering skapades en app-registrering i Azure AD-klienten. Appens registrering har samma visnings namn som din webbapp. Om du vill kontrol lera inställningarna väljer du **Azure Active Directory** på menyn Portal och väljer **Appregistreringar**. Välj den app-registrering som skapades. I översikten kontrollerar du att **konto typer som stöds** endast har angetts till **min organisation**.

:::image type="content" alt-text="Skärm bild som visar verifiering av åtkomst." source="./media/scenario-secure-app-authentication-app-service/verify-access.png":::

För att kontrol lera att åtkomst till appen är begränsad till användare i din organisation, starta en webbläsare i Incognito eller privat läge och gå till `https://<app-name>.azurewebsites.net` . Du måste dirigeras till en skyddad inloggnings sida och verifiera att oautentiserade användare inte har åtkomst till platsen. Logga in som en användare i din organisation för att få åtkomst till webbplatsen. Du kan också starta en ny webbläsare och försöka logga in med ett personligt konto för att kontrol lera att användare utanför organisationen inte har åtkomst.

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den här själv studie kursen och inte längre behöver webbappen eller tillhör ande resurser [rensar du de resurser som du har skapat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Konfigurera autentisering för webbappen.
> * Begränsa åtkomsten till webbappen till användare i din organisation.

> [!div class="nextstepaction"]
> [App Service-åtkomst till lagring](scenario-secure-app-access-storage.md)
