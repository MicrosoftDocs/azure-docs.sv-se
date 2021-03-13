---
title: Registrera en offentlig klient app i Azure AD – Azure API för FHIR
description: Den här artikeln förklarar hur du registrerar ett offentligt klient program i Azure Active Directory, som förberedelse för distribution av FHIR-API i Azure.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 3624812cf9119c270c30cf5a7cbebb57030a5113
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020103"
---
# <a name="register-a-public-client-application-in-azure-active-directory"></a>Registrera ett offentligt klient program i Azure Active Directory

I den här artikeln får du lära dig hur du registrerar ett offentligt program i Azure Active Directory.  

Klient program registreringar är Azure Active Directory representationer av program som kan autentisera och fråga efter API-behörigheter för en användares räkning. Offentliga klienter är program som mobil program och en enda sida JavaScript-program som inte kan spara hemligheter konfidentiellt. Proceduren påminner om att [Registrera en konfidentiell klient](register-confidential-azure-ad-client-app.md), men eftersom offentliga klienter inte är betrodda för att innehålla en program hemlighet behöver du inte lägga till en.

Snabb starten tillhandahåller allmän information om hur du [registrerar ett program med Microsoft Identity Platform](../../active-directory/develop/quickstart-register-app.md).

## <a name="app-registrations-in-azure-portal"></a>Appregistreringar i Azure Portal

1. Klicka på **Azure Active Directory** på den vänstra navigeringspanelen på [Azure Portal](https://portal.azure.com).

2. Klicka på **Appregistreringar** på bladet **Azure Active Directory** :

    ![Azure Portal. Ny app-registrering.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klicka på den **nya registreringen**.

## <a name="application-registration-overview"></a>Översikt över program registrering

1. Ge programmet ett visnings namn.

2. Ange en svars-URL. Svars-URL: en är den plats där autentiseringsnivåer kommer att returneras till klient programmet. Du kan lägga till fler svars-URL: er och redigera befintliga senare.

    ![Azure Portal. Ny registrering av offentliga appar.](media/how-to-aad/portal-aad-register-new-app-registration-PUB-CLIENT-NAME.png)


Så här konfigurerar du [Skriv bordet](../../active-directory/develop/scenario-desktop-app-registration.md), [mobilt](../../active-directory/develop/scenario-mobile-app-registration.md) eller [en Enkels Ides](../../active-directory/develop/scenario-spa-app-registration.md) applikation som ett offentligt program:

1. I [Azure Portal](https://portal.azure.com)väljer du din App i **Appregistreringar** och väljer sedan **autentisering**.

2. Välj **Avancerade inställningar**  >  **standard klient typ**. För **behandla program som en offentlig klient** väljer du **Ja**.

3. För ett program med en enda sida väljer du **åtkomsttoken** och **ID-token** för att aktivera implicit flöde.

   - Om ditt program loggar in användare väljer du **ID-token**.
   - Om ditt program också behöver anropa ett skyddat webb-API väljer du **åtkomsttoken**.

## <a name="api-permissions"></a>API-behörigheter

På samma sätt som i det [konfidentiella klient programmet](register-confidential-azure-ad-client-app.md)måste du välja vilka API-behörigheter som programmet ska kunna begära för användarens räkning:

1. Öppna **API-behörigheterna**.

    Om du använder Azure-API: et för FHIR lägger du till en behörighet i Azure sjukvårds-API: erna genom att söka efter Azure sjukvårds-API: er under **API: er som används i organisationen**. Du kommer bara att kunna hitta detta om du redan har [distribuerat Azure API för FHIR](fhir-paas-powershell-quickstart.md).

    
    Om du refererar till ett annat resurs program väljer du den [FHIR-API resurs program registrering](register-resource-azure-ad-client-app.md) som du skapade tidigare under **Mina API: er**:

    ![Azure Portal. Nya offentliga API-behörigheter – Azure API för FHIR standard](media/public-client-app/api-permissions.png)


2. Välj de behörigheter som du vill att programmet ska kunna begära: ![ Azure Portal. Appbehörigheter](media/public-client-app/app-permissions.png)

## <a name="validate-fhir-server-authority"></a>Verifiera FHIR-Server auktoritet
Om programmet som du har registrerat i den här artikeln och din FHIR-Server finns i samma Azure AD-klient, är du redo att gå vidare till nästa steg.

Om du konfigurerar klient programmet i en annan Azure AD-klient från din FHIR-Server måste du uppdatera **utfärdaren**. I Azure API för FHIR, ställer du in behörigheten under Inställningar--> autentisering. Ange utfärdare till **https://login.microsoftonline.com/\<TENANT-ID>** .

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du registrerar ett offentligt klient program i Azure Active Directory. Testa sedan åtkomst till din FHIR-server med Postman.
 
>[!div class="nextstepaction"]
>[Få åtkomst till Azure API för FHIR med Postman](access-fhir-postman-tutorial.md)