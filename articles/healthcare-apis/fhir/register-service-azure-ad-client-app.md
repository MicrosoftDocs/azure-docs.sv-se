---
title: Registrera en service app i Azure AD – Azure API för FHIR
description: Lär dig hur du registrerar ett tjänst klient program i Azure Active Directory.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 6f7bf122b292ca144eac406957f19a13c7ba6662
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020061"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrera ett tjänst klient program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett tjänst klient program i Azure Active Directory. Klient program registreringar är Azure Active Directory representationer av program som kan användas för att autentisera och hämta token. En tjänst klient är avsedd att användas av ett program för att få en åtkomsttoken utan interaktiv autentisering av en användare. Den har vissa program behörigheter och använder en program hemlighet (lösen ord) för att hämta åtkomsttoken.

Följ dessa steg om du vill skapa en ny tjänst klient.

## <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. I [Azure Portal](https://portal.azure.com)navigerar du till **Azure Active Directory**.

2. Välj **Appregistreringar**.

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Välj **ny registrering**.

4. Ge tjänst klienten ett visnings namn. Tjänst klient program använder vanligt vis inte en svars-URL.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure Portal. Ny registrering av tjänst klient program.":::

5. Välj **Register** (Registrera).

## <a name="api-permissions"></a>API-behörigheter

Nu när du har registrerat ditt program måste du välja vilka API-behörigheter som programmet ska kunna begära för användarens räkning:

1. Välj **API-behörigheter**.
1. Välj **Lägg till en behörighet**.

    Om du använder Azure-API: et för FHIR lägger du till en behörighet i Azure sjukvårds-API: erna genom att söka efter **Azure sjukvårds-API** : er under **API: er som används i organisationen**. 

    Om du refererar till ett annat resurs program väljer du den [FHIR-API resurs program registrering](register-resource-azure-ad-client-app.md) som du skapade tidigare under **Mina API: er**.

    :::image type="content" source="media/service-client-app/service-client-org-api.png" alt-text="Konfidentiell klient. Mina org API: er" lightbox="media/service-client-app/service-client-org-api-expanded.png":::

1. Välj omfång (behörigheter) som det konfidentiella programmet ska kunna begära för en användares räkning:

    :::image type="content" source="media/service-client-app/service-client-add-permission.png" alt-text="Tjänst klient. Delegerade behörigheter":::

1. Bevilja medgivande till programmet. Om du inte har de behörigheter som krävs kan du kontakta Azure Active Directory-administratören:

    :::image type="content" source="media/service-client-app/service-client-grant-permission.png" alt-text="Tjänst klient. Bevilja medgivande":::

## <a name="application-secret"></a>Apphemlighet

Tjänst klienten måste ha en hemlighet (Password) för att få en token.

1. Välj **certifikat & hemligheter**.
2. Välj **Ny klienthemlighet**.

    ![Azure Portal. Tjänstens klient hemlighet](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Ange en beskrivning och varaktighet för hemligheten (antingen 1 år, 2 år eller aldrig).

4. När hemligheten har genererats visas den bara en gång i portalen. Anteckna och lagra på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett tjänst klient program i Azure Active Directory. Testa sedan åtkomst till din FHIR-server med Postman.
 
>[!div class="nextstepaction"]
>[Få åtkomst till Azure API för FHIR med Postman](access-fhir-postman-tutorial.md)
