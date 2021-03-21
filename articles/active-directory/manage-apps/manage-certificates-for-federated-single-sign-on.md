---
title: Hantera Federations certifikat i Azure AD | Microsoft Docs
description: Lär dig hur du anpassar förfallo datumet för dina Federations certifikat och hur du förnyar certifikat som snart upphör att gälla.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63054b18589cdcc2d950d70452b4f982c324a6c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604006"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerad enkel inloggning i Azure Active Directory

I den här artikeln tar vi upp vanliga frågor och information om certifikat som Azure Active Directory (Azure AD) skapar för att upprätta federerad enkel inloggning (SSO) till SaaS-program (program vara som en tjänst). Lägg till program från Azure AD App-galleriet eller med hjälp av en mall för program som inte är en Galleri. Konfigurera programmet med hjälp av alternativet federerad SSO.

Den här artikeln är endast relevant för appar som har kon figurer ATS för att använda Azure AD SSO via [Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML) Federation.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererat certifikat för Galleri program och icke-galleriprogram

När du lägger till ett nytt program från galleriet och konfigurerar en SAML-baserad inloggning (genom att välja **enkel inloggning på**  >  **SAML** från program översikts sidan) genererar Azure AD ett certifikat för programmet som är giltigt i tre år. Om du vill ladda ned det aktiva certifikatet som ett säkerhets certifikat (**CER**-fil) går du tillbaka till sidan (**SAML-baserad inloggning**) och väljer en nedladdnings länk i **certifikat rubriken SAML-signering** . Du kan välja mellan RAW-certifikatet (Binary) eller Base64-certifikatet (Base 64-kodad text). För Galleri program kan det här avsnittet också innehålla en länk för att ladda ned certifikatet som federationsmetadata XML (en **XML** -fil), beroende på kraven för programmet.

![Alternativ för hämtning av SAML Active signerings certifikat](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Du kan också hämta ett aktivt eller inaktivt certifikat genom att välja redigerings ikonen för **SAML-signerings certifikatets** **redigerings** ikon (en blyertspenna), som visar sidan **SAML-signerings certifikat** . Välj ellipsen (**...**) bredvid det certifikat som du vill ladda ned och välj sedan det certifikat format som du vill använda. Du har ytterligare möjlighet att hämta certifikatet i PEM-format (Privacy-Enhanced mail). Det här formatet är identiskt med base64 men med fil namns tillägget **. pem** , som inte känns igen i Windows som certifikat format.

![Alternativ för hämtning av SAML-signeringscertifikat (aktiv och inaktiv)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa förfallo datumet för ditt Federations certifikat och överföra det till ett nytt certifikat

Som standard konfigurerar Azure ett certifikat så att det upphör att gälla efter tre år när det skapas automatiskt under konfigurationen av SAML enkel inloggning. Eftersom du inte kan ändra datum för ett certifikat när du har sparat det måste du:

1. Skapa ett nytt certifikat med önskat datum.
1. Spara det nya certifikatet.
1. Hämta det nya certifikatet i rätt format.
1. Överför det nya certifikatet till programmet.
1. Gör det nya certifikatet aktivt i Azure Active Directory-portalen.

Följande två avsnitt innehåller information om hur du utför de här stegen.

### <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

Skapa och spara först ett nytt certifikat med ett annat förfallo datum:

1. Logga in på [Azure Active Directory Portal](https://aad.portal.azure.com/). Sidan **Azure Active Directory administrations Center** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företags program i ditt konto visas.
1. Välj det program som påverkas. En översikts sida för programmet visas.
1. I det vänstra fönstret på sidan program översikt väljer du **enkel inloggning**.
1. Om sidan **Välj en enkel inloggnings metod** visas väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML-Preview** letar du upp certifikat rubriken för **SAML-signering** och väljer **redigerings** ikonen (en blyertspenna). Sidan **SAML-signeringscertifikat** visas, som visar status (**aktiv** eller **inaktiv**), utgångs datum och tumavtryck (en hash-sträng) för varje certifikat.
1. Välj **nytt certifikat**. En ny rad visas under certifikat listan, där utgångs datum har ett standardvärde på exakt tre år efter det aktuella datumet. (Dina ändringar har inte sparats ännu, så du kan fortfarande ändra förfallo datumet.)
1. Hovra över kolumnen utgångs datum i den nya certifikat raden och välj ikonen **Välj datum** (en kalender). En kalender kontroll visas, och visar dagar i månaden för den nya radens aktuella förfallo datum.
1. Använd kalender kontrollen för att ange ett nytt datum. Du kan ange ett datum mellan det aktuella datumet och tre år efter det aktuella datumet.
1. Välj **Spara**. Det nya certifikatet visas nu med statusen **inaktiv**, det utgångs datum som du har valt och ett tumavtryck.
1. Välj **X** för att återgå till sidan **Konfigurera enkla Sign-On med SAML-Preview** .

### <a name="upload-and-activate-a-certificate"></a>Ladda upp och aktivera ett certifikat

Sedan laddar du ned det nya certifikatet i rätt format, laddar upp det till programmet och gör det aktivt i Azure Active Directory:

1. Visa programmets ytterligare instruktioner för konfiguration av SAML-inloggning med antingen:

   - Välj länken **konfigurations guide** för att visa i ett separat webbläsarfönster eller flik
   - Gå till **inställningen konfigurera** rubrik och välj **Visa steg-för-steg-instruktioner** för att visa i en marginal List.

1. I anvisningarna anger du det kodnings format som krävs för att ladda upp certifikatet.
1. Följ anvisningarna i avsnittet [automatiskt genererat certifikat för galleriet och program som inte är Galleri program](#auto-generated-certificate-for-gallery-and-non-gallery-applications) tidigare. Det här steget hämtar certifikatet i kodnings formatet som krävs för att ladda upp programmet.
1. När du vill gå över till det nya certifikatet går du tillbaka till sidan **SAML-signeringscertifikat** och väljer sedan ellipsen (**...**) i den nyligen sparade certifikat raden och väljer **gör certifikat aktivt**. Status för det nya certifikatet ändras till **aktiv** och det tidigare aktiva certifikatet har ändrats till statusen **inaktiv**.
1. Fortsätt följa de konfigurations anvisningar för SAML-inloggning som du visade tidigare, så att du kan överföra SAML-signerings certifikatet i rätt kodnings format.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Lägg till e-postavisering om att certifikatet upphör att gälla

Azure AD skickar ett e-postmeddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till fler än en e-postadress för att ta emot meddelanden. Ange e-postadressen (ES) som du vill att meddelandena ska skickas till:

1. På sidan **SAML-signerings certifikat** går du till rubriken **e-postadresser för meddelanden** . Som standard använder den här rubriken bara e-postadressen för den administratör som lade till programmet.
1. Under den sista e-postadressen anger du den e-postadress som ska ta emot certifikatets förfallo datum och trycker sedan på RETUR.
1. Upprepa föregående steg för varje e-postadress som du vill lägga till.
1. För varje e-postadress som du vill ta bort väljer du ikonen **ta bort** (ett skräp post kan) bredvid e-postadressen.
1. Välj **Spara**.

Du kan lägga till upp till 5 e-postadresser i meddelande listan (inklusive e-postadressen till den administratör som lade till programmet). Om du vill att fler personer ska meddelas använder du e-postmeddelandena för distributions listan.

Du får e-postmeddelandet via aadnotification@microsoft.com . Lägg till det här e-postmeddelandet till dina kontakter för att undvika att e-postmeddelandet till din skräp post

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som snart upphör att gälla

Om ett certifikat håller på att gå ut kan du förnya det med en procedur som resulterar i inga betydande stillestånds tid för dina användare. Förnya ett certifikat som upphör att gälla:

1. Följ instruktionerna i avsnittet [skapa ett nytt certifikat](#create-a-new-certificate) tidigare, med ett datum som överlappar det befintliga certifikatet. Datumet begränsar den mängd stillestånds tid som orsakas av certifikatets förfallo datum.
1. Om programmet kan återställas automatiskt över ett certifikat, ställer du in det nya certifikatet till aktiv genom att följa dessa steg:
   1. Gå tillbaka till sidan för **SAML-signerings certifikat** .
   1. Välj ellipsen (**...**) på den nyligen sparade certifikat raden och välj sedan **gör certifikat aktivt**.
   1. Hoppa över de kommande två stegen.

1. Om appen bara kan hantera ett certifikat i taget, väljer du ett avbrotts intervall för att utföra nästa steg. (Om programmet inte automatiskt hämtar det nya certifikatet, men kan hantera fler än ett signerings certifikat, kan du utföra nästa steg när som helst.)
1. Innan det gamla certifikatet upphör att gälla följer du anvisningarna i avsnittet [Ladda upp och aktivera ett certifikat](#upload-and-activate-a-certificate) tidigare.
1. Logga in på programmet för att kontrol lera att certifikatet fungerar som det ska.

## <a name="related-articles"></a>Relaterade artiklar

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
- [Programhantering med Azure Active Directory](what-is-application-management.md)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka SAML-baserad enkel inloggning för program i Azure Active Directory](./debug-saml-sso-issues.md)
