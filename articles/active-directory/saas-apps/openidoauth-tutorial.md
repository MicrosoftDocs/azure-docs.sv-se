---
title: Konfigurera ett OpenID/OAuth-program från Azure AD-appgalleri | Microsoft Docs
description: Steg för att konfigurera ett OpenID/OAuth-program från Azure AD-appgalleri.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.custom: has-adal-ref
ms.openlocfilehash: 1934b6256ecf4f35c54bbc2ac497c331b2c5ee89
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543932"
---
# <a name="configure-an-openidoauth-application-from-the-azure-ad-app-gallery"></a>Konfigurera ett OpenID/OAuth-program från Azure AD-appgalleri

## <a name="process-of-adding-an-openid-application-from-the-gallery"></a>Processen att lägga till ett OpenID-program från galleriet

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png))

2. Gå till **företags program**  >  **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Välj **Nytt program** överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du namnet på programmet. Välj ett alternativ från resultatpanelen och registrera dig för programmet.

    ![Openid i resultatlistan](common/search-new-app.png)

    > [!NOTE]
    > För OpenID Connect och OAuth-appar är knappen **Lägg till** inaktiverad som standard. Här bör administratören för klientorganisationen välja registreringsknappen och ge medgivande till programmet. Programmet läggs sedan till i kundklienten, där du kan göra konfigurationerna. Du behöver inte lägga till programmet uttryckligen.

    ![Knappen Lägg till](./media/openidoauth-tutorial/addbutton.png)

5. När du väljer registreringslänken omdirigeras du till sidan Azure Active Directory (Azure AD) för autentiseringsuppgifter.

6. Efter lyckad autentisering godkänner du medgivande från sidan för medgivande. Sedan visas programmets startsida.

    > [!NOTE]
    > Du kan endast lägga till en instans av programmet. Om du redan har lagt till en och försöker ge medgivande igen läggs den därmed inte till i klienten på nytt. Därför kan du endast logiskt använda en appinstans i klienten.

## <a name="authentication-flow-using-openid-connect"></a>Autentiseringsflöde med OpenID Connect

Det mest grundläggande inloggningsflödet innehåller följande steg:

![Autentiseringsflöde med OpenID Connect](./media/openidoauth-tutorial/authenticationflow.png)

### <a name="multitenant-application"></a>Program för flera klientorganisationer
Ett program för flera klientorganisationer är avsett att användas i många organisationer, inte bara en enda. Det kallas vanligen för SaaS-webbprogram (Software as a Service) och skrivs av en oberoende programvaruleverantör (ISV).

Program för flera klientorganisationer måste tillhandahållas i varje katalog där de kommer att användas. De kräver att användaren eller administratören ger sitt medgivande till att registrera dem. Medgivandeprocessen startar när ett program har registrerats i katalogen och ges åtkomst till Graph API eller kanske någon annan webb-API. När en användare eller administratör från en annan organisation registrerar sig för att använda programmet visar en dialogruta de behörigheter som programmet behöver.

Användaren eller administratören kan sedan ge medgivande till programmet. Medgivandet ger programmet åtkomst till angivna data och slutligen registreras programmet i katalogen.

> [!NOTE]
> Om du gör ditt program tillgängligt för användare i flera kataloger, behöver du en mekanism för att avgöra vilken klient de befinner sig i. Ett program för en enda klientorganisation behöver bara söka i en egen katalog för en användare. Ett program för flera klientorganisationer behöver identifiera en viss användare från alla kataloger i Azure AD.
>
> Om du vill utföra den här uppgiften tillhandahåller Azure AD en gemensam autentiseringsslutpunkt där alla program för flera klientorganisationer kan dirigera inloggningsförfrågningar, i stället för en klientspecifik slutpunkt. Den här slutpunkten är `https://login.microsoftonline.com/common` för alla kataloger i Azure AD. En klientspecifik slutpunkt kan vara `https://login.microsoftonline.com/contoso.onmicrosoft.com`.
>
> Den gemensamma slutpunkten är viktig att överväga på när du utvecklar ditt program. Du behöver den nödvändiga logiken för att hantera flera klienter vid inloggning, utloggning och verifiering av token.

Som standard främjas program för flera klientorganisationer i Azure AD. De är lätta att komma åt mellan olika organisationer och de är lätta att använda när du har godkänt medgivande.

## <a name="consent-framework"></a>Ramverk för medgivande

Du kan använda Azure AD-ramverket för medgivande för att utveckla webbprogram och interna klientprogram för flera klientorgansiationer. Dessa program tillåter inloggning genom användarkonton från en Azure AD-klient, som skiljer sig från det där programmet har registrerats. De kan också behöva komma åt webb-API:er som:
- Microsoft Graph API för att få åtkomst till Azure AD, Intune och tjänster i Office 365.
- Övriga för Microsoft-tjänsters API:er.
- Dina egna webb-API:er.

Ramverket är baserat på att en användare eller administratör ger sitt medgivande till ett program som ber att få registreras i deras katalog. Registreringen kan handla om åtkomst till katalogdata. När tillstånd har getts kan klientprogrammet anropa Microsoft Graph API för användarens räkning och använda informationen vid behov.

[Microsoft Graph API](https://developer.microsoft.com/graph/) ger åtkomst till data i Office 365, som:

- Kalendrar och meddelanden från Exchange.
- Platser och listor från SharePoint.
- Dokument från OneDrive.
- Bärbara datorer från OneNote.
- Uppgifter från Planner.
- Arbetsböcker från Excel.

Graph API tillhandahåller även åtkomst till användare och grupper från Azure AD och andra dataobjekt från flera Microsoft-molntjänster.

Följande steg visar hur medgivande fungerar för programutvecklare och användaren:

1. Anta att du har ett webbklientprogram som behöver begära särskild behörighet för att komma åt en resurs eller ett API. Azure-portalen används för att deklarera behörighetsbegäranden vid konfigurationen. Precis som andra konfigurations inställningar blir de en del av programmets Azure AD-registreringar. Du behöver följa stegen nedan för sökvägen till behörighets begär Anden:

    a. Klicka på **Appregistreringar** på menyns vänstra sida och öppna programmet genom att skriva in program namnet i sökrutan.

    ![Graph API](./media/openidoauth-tutorial/application.png)

    b. Klicka på **Visa API-behörigheter**.

    ![Graph API](./media/openidoauth-tutorial/api-permission.png)

    c. Klicka på **Lägg till en behörighet**.

    ![Graph API](./media/openidoauth-tutorial/add-permission.png)

    d. Klicka på **Microsoft Graph**.

    ![Graph API](./media/openidoauth-tutorial/microsoft-graph.png)

    e. Välj nödvändiga alternativ från **delegerade behörigheter** och **program behörigheter**.

    ![Graph API](./media/openidoauth-tutorial/graphapi.png)

2. Tänk på att ditt programs behörigheter har uppdaterats. Programmet körs och en användare är på väg att använda det för första gången. Programmet måste först hämta en auktoriseringskod från Azure AD/tillåta slutpunkt. Auktoriseringskoden kan sedan användas för att få en ny åtkomst och uppdatera token.

3. Om användaren inte redan autentiserats uppmanar Azure AD/auktorisera slutpunkt till inloggning.

    ![Autentisering](./media/openidoauth-tutorial/authentication.png)

4. När användaren har loggat in avgör Azure AD om användaren måste visas en sida för medgivande. Detta val baseras på om användaren (eller deras organisations administratör) redan har beviljat ett program medgivande.

   Om medgivande inte har beviljats ber Azure AD användare om medgivande och visar de nödvändiga behörigheterna som krävs. De behörigheter som visas i dialogrutan för medgivande överensstämmer med de som valts i de delegerade behörigheterna i Azure-portalen.

    ![Sida för medgivande](./media/openidoauth-tutorial/consentpage.png)

En vanlig användare kan godkänna vissa behörigheter. Andra behörigheter kräver en klient administratörs medgivande.

## <a name="difference-between-admin-consent-and-user-consent"></a>Skillnaden mellan administratörens och användarens medgivande

Som administratör kan du även samtycka till ett programs delegerade behörigheter för samtliga användare i klienten. Administratörens medgivande förhindrar att dialogrutan för medgivande visas för varje användare i klienten. Användare som har administratörsrollen kan ge sitt medgivande i Azure-portalen. På sidan **Inställningar** för ditt program väljer du **nödvändiga behörigheter**  >  **bevilja administratörs medgivande**.

![Knappen Bevilja behörigheter](./media/openidoauth-tutorial/grantpermission.png)

> [!NOTE]
> Att bevilja uttryckligt medgivande genom att använda knappen **bevilja administratörs medgivande** krävs nu för program med en enda sida (SPAs) som använder ADAL.js. Annars misslyckas programmet när åtkomsttoken begärs.

Endast app-behörigheter kräver alltid en klient administratörs medgivande. Om ditt program begär en appspecifik behörighet och en användare försöker logga in på programmet visas ett felmeddelande. Meddelandet säger att användaren inte kan godkänna.

Om programmet använder behörigheter som kräver administratörens medgivande måste det finnas en gest som administratören kan starta åtgärden med, till exempel via en knapp eller länk. Den begäran som programmet skickar för den här åtgärden är den vanliga auktoriseringsbegäran för OAuth2/OpenID Connect. Denna begäran innehåller frågesträngparametern *prompt=admin_consent*.

När administratören har samtyckt och tjänstens huvud namn har skapats i kundens klient behöver inloggnings begär Anden senare inte *prompten = admin_consent* parameter. Eftersom administratören har bestämt att behörigheterna som krävs är godtagbara kan tillfrågas inga andra användare i klienten om medgivande från den tidpunkten och framåt.

En klientadministratör kan inaktivera möjligheten för vanliga användare att samtycka till program. Om den här funktionen har inaktiverats krävs alltid administratörens godkännande för program som ska användas i klienten. Om du vill testa programmet med slutanvändarens medgivande inaktiverat kan du byta konfiguration i [Azure-portalen](https://portal.azure.com/). Den finns i avsnittet [Användarinställningar](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) under **Företagsprogram**.

Parametern *prompt=admin_consent* kan även användas av program som begär behörighet som inte kräver administratörens medgivande. Ett exempel är ett program som kräver en upplevelse där klient administratören "registrerar sig" en gång, och inga andra användare tillfrågas om medgivande från den tidpunkten.

Anta att ett program kräver administratörens medgivande och en administratör loggar in utan att parametern *prompt=admin_consent* skickas. När administratören godkänner programmet gäller det endast för hans/hennes användarkonto. Vanliga användare kan fortfarande inte logga in eller godkänna programmet. Den här funktionen är användbart om du vill ge klientadministratören möjlighet att utforska ditt program innan åtkomst ges andra användare.
