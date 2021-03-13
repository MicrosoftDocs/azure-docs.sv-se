---
title: Registrera en konfidentiell klient app i Azure AD – Azure API för FHIR
description: Registrera ett konfidentiellt klient program i Azure Active Directory som autentiseras för en användares räkning och begär åtkomst till resurs program.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 8021fb3fa9f11ef895569f48a2ae21b3f7adcd36
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020131"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrera ett konfidentiellt klient program i Azure Active Directory

I den här självstudien får du lära dig hur du registrerar ett konfidentiellt klient program i Azure Active Directory. 

En klient program registrering är en Azure Active Directory representation av ett program som kan användas för att autentisera för en användares räkning och begära åtkomst till [resurs program](register-resource-azure-ad-client-app.md). Ett konfidentiellt klient program är ett program som kan vara betrott att lagra en hemlighet och presentera hemligheten när de begär åtkomsttoken. Exempel på konfidentiella program är program på Server sidan.

Följ dessa steg om du vill registrera ett nytt konfidentiellt program i portalen.

## <a name="register-a-new-application"></a>Registrera ett nytt program

1. I [Azure Portal](https://portal.azure.com)navigerar du till **Azure Active Directory**.

1. Välj **Appregistreringar**.

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

1. Välj **ny registrering**.

1. Ge programmet ett visnings namn.

1. Ange en svars-URL. Den här informationen kan ändras senare, men om du känner till svars-URL: en för ditt program anger du det nu.

    ![Ny konfidentiell klient program registrering.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)
1. Välj **Register** (Registrera).

## <a name="api-permissions"></a>API-behörigheter

Nu när du har registrerat ditt program måste du välja vilka API-behörigheter som programmet ska kunna begära för användarens räkning:

1. Välj **API-behörigheter**.

    ![Konfidentiell klient. API-behörigheter](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

1. Välj **Lägg till en behörighet**.

    Om du använder Azure-API: et för FHIR lägger du till en behörighet i Azure sjukvårds-API: erna genom att söka efter **Azure sjukvårds-API** : er under **API: er som används i organisationen**. Du kommer bara att kunna hitta detta om du redan har [distribuerat Azure API för FHIR](fhir-paas-powershell-quickstart.md).

    Om du refererar till ett annat resurs program väljer du den [FHIR-API resurs program registrering](register-resource-azure-ad-client-app.md) som du skapade tidigare under **Mina API: er**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Konfidentiell klient. Mina org API: er" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

3. Välj omfång (behörigheter) som det konfidentiella programmet ska kunna begära för en användares räkning:

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Konfidentiell klient. Delegerade behörigheter":::

## <a name="application-secret"></a>Apphemlighet

1. Välj **certifikat & hemligheter**.
1. Välj **Ny klienthemlighet**. 

    ![Konfidentiell klient. Program hemlighet](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Ange en beskrivning och varaktighet för hemligheten (antingen 1 år, 2 år eller aldrig).

3. När den har skapats visas den bara en gång i portalen. Anteckna det och lagra det på ett säkert sätt.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett konfidentiellt klient program i Azure Active Directory. Härnäst kan du komma åt din FHIR-server med Postman
 
>[!div class="nextstepaction"]
>[Få åtkomst till Azure API för FHIR med Postman](access-fhir-postman-tutorial.md)
