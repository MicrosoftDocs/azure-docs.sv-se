---
title: Självstudie om webbapp – klient programs konfiguration
description: Den här självstudien vägleder dig genom stegen för att registrera ett offentligt program för att förbereda för att distribuera ett webb program
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019977"
---
# <a name="client-application-registration"></a>Registrering av klient program
I den föregående själv studie kursen har du distribuerat och konfigurerat Azure API för FHIR. Nu när du har konfigurerat Azure API för FHIR-installationen kommer vi att registrera ett offentligt klient program. Du kan läsa igenom guiden fullständig [registrering av en offentlig klient](register-public-azure-ad-client-app.md) med instruktioner för mer information eller fel sökning, men vi har informat de viktigaste stegen för den här kursen nedan.

1. Navigera till Azure Active Directory
1. Välj **app Registration**  -->  **New Registration**
1. Namnge ditt program
1. Välj **offentlig klient/ursprunglig (mobil & Station ära datorer)** och ange omdirigerings-URI till `https://www.getpostman.com/oauth2/callback` .

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Skärm bild av fönstret registrera ett program och ett exempel på program namn och omdirigerings-URL.":::

## <a name="client-application-settings"></a>Inställningar för klient program

När klient programmet har registrerats kopierar du program-ID: t och klient-ID: t från översikts sidan. Du kommer att behöva dessa två värden senare vid åtkomst till klienten.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Skärm bild av fönstret inställningar för klient program där program-och katalog-ID: n är markerade.":::

### <a name="connect-with-web-app"></a>Anslut med webbapp

Om du har [skrivit din](tutorial-web-app-write-web-app.md) webbapp för att ansluta till Azure-API: t för FHIR, måste du också ange rätt autentiseringsalternativ. 

1. På den vänstra menyn, under **Hantera**, väljer du **autentisering**. 

1. Om du vill lägga till en ny plattforms konfiguration väljer du **webb**.

1. Konfigurera omdirigerings-URI: n som förberedelse för när du skapar ditt webb program i den fjärde delen av den här självstudien. Det gör du genom att lägga till `https://\<WEB-APP-NAME>.azurewebsites.net` i listan omdirigerings-URI. Om du väljer ett annat namn under steget där du [skriver din](tutorial-web-app-write-web-app.md)webbapp måste du komma tillbaka och uppdatera det.

1. Markera kryss rutorna **åtkomsttoken** och **ID-token** .

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Skärm bild av bladet inställningar för app-autentisering med stegen för att lägga till en plattform markerad.":::

## <a name="add-api-permissions"></a>Lägga till API-behörigheter

Nu när du har konfigurerat rätt autentisering anger du API-behörigheterna:

1. Välj **API-behörigheter** och klicka på **Lägg till en behörighet**.
1. Sök efter Azure sjukvårds-API: er som **min organisation använder**.
1. Välj **user_impersonation** och klicka på **Lägg till behörigheter**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Skärm bild av bladet Lägg till API-behörigheter med stegen för att lägga till API-behörigheter markerade.":::

## <a name="next-steps"></a>Nästa steg
Nu har du ett offentligt klient program. I nästa självstudie kommer vi att gå igenom testningen och få åtkomst till det här programmet via Postman.

>[!div class="nextstepaction"]
>[Testa klient program i Postman](tutorial-web-app-test-postman.md)
