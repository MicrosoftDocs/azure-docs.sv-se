---
title: Registrera en konfidentiell klient app i Azure AD – Azure API för FHIR
description: Registrera ett konfidentiellt klient program i Azure Active Directory som autentiseras för en användares räkning och begär åtkomst till resurs program.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284450"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrera ett konfidentiellt klient program i Azure Active Directory

I den här självstudien får du lära dig hur du registrerar ett konfidentiellt klient program i Azure Active Directory (Azure AD).  

En klient program registrering är en Azure AD-representation av ett program som kan användas för att autentisera för en användares räkning och begära åtkomst till [resurs program](register-resource-azure-ad-client-app.md). Ett konfidentiellt klient program är ett program som kan vara betrott att lagra en hemlighet och presentera hemligheten när de begär åtkomsttoken. Exempel på konfidentiella program är program på Server sidan. 

För att registrera ett nytt konfidentiellt klient program, se stegen nedan. 

## <a name="register-a-new-application"></a>Registrera ett nytt program

1. Välj **Azure Active Directory** i [Azure-portalen](https://portal.azure.com).

1. Välj **Appregistreringar**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="Azure Portal. Ny app-registrering.":::

1. Välj **ny registrering**.

1. Ge programmet ett användar gränssnitts visnings namn.

1. För **konto typer som stöds** väljer du vem som kan använda programmet eller åtkomst till API: et.

1. Valfritt Ange en **omdirigerings-URI**. Den här informationen kan ändras senare, men om du känner till svars-URL: en för ditt program anger du det nu.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Ny konfidentiell klient program registrering.":::

1. Välj **Register** (Registrera).

## <a name="api-permissions"></a>API-behörigheter

Nu när du har registrerat ditt program måste du välja vilka API-behörigheter som programmet ska begära för användarens räkning.

1. Välj **API-behörigheter**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Konfidentiell klient. API-behörigheter.":::

1. Välj **Lägg till en behörighet**.

    Om du använder Azure-API: et för FHIR lägger du till en behörighet till Azure-hälsovårds-API: erna genom att söka efter **Azure sjukvårds-API** : **er som min organisation använder**. Sök resultatet för Azure-hälsovårds-API: t returneras bara om du redan har [distribuerat Azure API för FHIR](fhir-paas-powershell-quickstart.md).

    Om du refererar till ett annat resurs program väljer du den [FHIR-API resurs program registrering](register-resource-azure-ad-client-app.md) som du skapade tidigare under **Mina API: er**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Konfidentiell klient. Mina org API: er" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Välj omfång (behörigheter) som det konfidentiella klient programmet ska begära för en användares räkning. Välj **user_impersonation** och välj sedan **Lägg till behörigheter**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Konfidentiell klient. Delegerade behörigheter":::


## <a name="application-secret"></a>Apphemlighet

1. Välj **certifikat & hemligheter** och välj sedan **ny klient hemlighet**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Konfidentiell klient. Program hemlighet.":::

1. Ange en **Beskrivning** av klient hemligheten. Välj den nedrullningsbara menyn **förfaller** för att välja en förfallo tids period och klicka sedan på **Lägg till**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Lägg till en klient hemlighet.":::

1. När klientens hemliga sträng har skapats kopierar du dess **värde** och **ID** och sparar dem på en säker plats.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Klientens hemliga sträng."::: 

> [!NOTE]
>Klientens hemliga sträng visas bara en gång i Azure Portal. När du navigerar bort från webb sidan certifikat & hemligheter och sedan återgår till den, blir värde strängen maskerad. Det är viktigt att göra en kopia av din klient hemliga sträng omedelbart efter att den har skapats. Om du inte har en säkerhets kopia av klient hemligheten måste du upprepa stegen ovan för att återskapa den.
 
## <a name="next-steps"></a>Nästa steg

I den här artikeln har du guidat dig genom stegen för att registrera ett konfidentiellt klient program i Azure AD. Du guidas också igenom stegen för att lägga till API-behörigheter i Azure-hälsovårds-API: et. Slutligen visade du hur du skapar en program hemlighet. Dessutom kan du lära dig hur du får åtkomst till din FHIR-server med Postman.
 
>[!div class="nextstepaction"]
>[Få åtkomst till Azure API för FHIR med Postman](access-fhir-postman-tutorial.md)
