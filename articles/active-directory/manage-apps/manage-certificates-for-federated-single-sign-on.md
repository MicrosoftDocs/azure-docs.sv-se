---
title: Hantera federationscertifikat i Azure AD-| Microsoft Docs
description: Lär dig hur du anpassar förfallodatumet för dina federationscertifikat och hur du förnyar certifikat som snart upphör att gälla.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379459"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Hantera certifikat för federerad enkel inloggning i Azure Active Directory

I den här artikeln tar vi upp vanliga frågor och information om certifikat som Azure Active Directory (Azure AD) skapar för att upprätta federerad enkel inloggning (SSO) till dina SaaS-program (programvara som en tjänst). Lägg till program från Azure AD-appgalleriet eller med hjälp av en mall för program som inte är galleri. Konfigurera programmet med hjälp av alternativet federerad enkel inloggning.

Den här artikeln är endast relevant för appar som är konfigurerade att använda enkel inloggning med Azure AD [via Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML)-federation.

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Automatiskt genererat certifikat för galleriprogram och program som inte är galleriprogram

När du lägger till ett nytt program från galleriet och konfigurerar en SAML-baserad inloggning (genom att välja SAML **för** enkel inloggning från programöversiktssidan) genererar Azure AD ett certifikat för programmet som är giltigt i  >   tre år. Om du vill ladda ned det aktiva certifikatet som en säkerhetscertifikatfil **(.cer)** går du tillbaka till sidan ( SAML-baserad inloggning) och väljer en nedladdningslänk i rubriken **SAML-signeringscertifikat.**  Du kan välja mellan det råa (binära) certifikatet eller Base64-certifikatet (base 64-kodad text). För galleriprogram kan det här avsnittet också visa en länk  för att ladda ned certifikatet som federationsmetadata-XML (en XML-fil), beroende på programmets krav.

![Nedladdningsalternativ för AKTIVT SAML-signeringscertifikat](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

Du kan också ladda ned ett aktivt eller inaktivt  certifikat genom att välja rubriken Redigera för SAML-signeringscertifikat (en penna), som visar **sidan SAML-signeringscertifikat.**  Välj ellipsen (**...**) bredvid det certifikat som du vill ladda ned och välj sedan vilket certifikatformat du vill ha. Du har ytterligare alternativ för att ladda ned certifikatet i PEM-format (Privacy-Enhanced Mail). Det här formatet är identiskt med Base64 men med filnamnstillägget **.pem,** som inte känns igen i Windows som ett certifikatformat.

![Nedladdningsalternativ för SAML-signeringscertifikat (aktiv och inaktiv)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Anpassa utgångsdatumet för federationscertifikatet och återställ det till ett nytt certifikat

Som standard konfigurerar Azure ett certifikat så att det upphör att gälla efter tre år när det skapas automatiskt under SAML-konfiguration för enkel inloggning. Eftersom du inte kan ändra datumet för ett certifikat när du har sparat det måste du:

1. Skapa ett nytt certifikat med önskat datum.
1. Spara det nya certifikatet.
1. Ladda ned det nya certifikatet i rätt format.
1. Ladda upp det nya certifikatet till programmet.
1. Gör det nya certifikatet aktivt i Azure Active Directory portalen.

Följande två avsnitt hjälper dig att utföra dessa steg.

### <a name="create-a-new-certificate"></a>Skapa ett nytt certifikat

Skapa och spara först ett nytt certifikat med ett annat förfallodatum:

1. Logga in på [Azure Active Directory portalen.](https://aad.portal.azure.com/) Sidan **Azure Active Directory administrationscenter** visas.
1. I den vänstra rutan väljer du **Företagsprogram**. En lista över företagsprogram i ditt konto visas.
1. Välj det berörda programmet. En översiktssida för programmet visas.
1. I den vänstra rutan på programöversiktssidan väljer **du Enkel inloggning.**
1. Om sidan **Välj en metod för enkel inloggning** visas väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning Sign-On SAML –** förhandsversion, leta upp rubriken **SAML-signeringscertifikat** och välj ikonen **Redigera** (en penna). Sidan **SAML-signeringscertifikat** visas, som visar status (**Aktiv** eller **Inaktiv**), förfallodatum och tumavtryck (en hash-sträng) för varje certifikat.
1. Välj **Nytt certifikat.** En ny rad visas under certifikatlistan, där förfallodatumet som standard är exakt tre år efter det aktuella datumet. (Ändringarna har inte sparats ännu, så du kan fortfarande ändra förfallodatumet.)
1. Hovra över kolumnen förfallodatum på den nya certifikatraden och välj ikonen **Välj datum** (en kalender). En kalenderkontroll visas som visar dagarna i en månad för den nya radens aktuella förfallodatum.
1. Använd kalenderkontrollen för att ange ett nytt datum. Du kan ange ett datum mellan det aktuella datumet och tre år efter det aktuella datumet.
1. Välj **Spara**. Det nya certifikatet visas nu med **statusen Inaktiv,** det utgångsdatum som du har valt och ett tumavtryck.
1. Välj **X för** att återgå till **sidan Konfigurera enkel Sign-On med SAML – förhandsversion.**

### <a name="upload-and-activate-a-certificate"></a>Ladda upp och aktivera ett certifikat

Ladda sedan ned det nya certifikatet i rätt format, ladda upp det till programmet och gör det aktivt i Azure Active Directory:

1. Visa programmets ytterligare konfigurationsanvisningar för SAML-inloggning genom att antingen:

   - Välja **konfigurationsguidens** länk för att visa i ett separat webbläsarfönster eller en separat flik, eller
   - Gå till **rubriken konfigurera och** välj Visa **stegvisa** instruktioner för att visa i en sidopanel.

1. I anvisningarna noterar du det kodningsformat som krävs för att ladda upp certifikatet.
1. Följ anvisningarna i avsnittet [Automatiskt genererade certifikat för galleri- och icke-galleriprogram](#auto-generated-certificate-for-gallery-and-non-gallery-applications) tidigare. Det här steget laddar ned certifikatet i det kodningsformat som krävs för uppladdning av programmet.
1. När du vill återställa till det nya certifikatet går du tillbaka till sidanSAML-signeringscertifikat. På den nyligen sparade certifikatraden väljer du ellipsen ( ... ) och sedan Gör certifikatet **aktivt.**  Statusen för det nya certifikatet ändras **till Aktiv** och det tidigare aktiva certifikatet ändras till **statusen Inactive**.
1. Fortsätt att följa programmets konfigurationsanvisningar för SAML-inloggning som du visade tidigare, så att du kan ladda upp SAML-signeringscertifikatet i rätt kodningsformat.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Lägga till e-postadresser för förfallodatum för certifikat

Azure AD skickar ett e-postmeddelande 60, 30 och 7 dagar innan SAML-certifikatet upphör att gälla. Du kan lägga till fler än en e-postadress för att ta emot meddelanden. Så här anger du den eller de e-postadresser som du vill att meddelandena ska skickas till:

1. På sidan **SAML-signeringscertifikat** går du till rubriken **e-postadresser för meddelanden.** Som standard använder den här rubriken endast e-postadressen för den administratör som lade till programmet.
1. Under den slutliga e-postadressen skriver du den e-postadress som ska ta emot certifikatets förfallomeddelande och trycker sedan på Retur.
1. Upprepa föregående steg för varje e-postadress som du vill lägga till.
1. För varje e-postadress som du vill ta bort väljer du **ikonen Ta** bort (en papperskorgen) bredvid e-postadressen.
1. Välj **Spara**.

Du kan lägga till upp till 5 e-postadresser i meddelandelistan (inklusive e-postadressen för den administratör som lade till programmet). Om du vill att fler personer ska meddelas kan du använda e-postmeddelanden i distributionslistan.

Du får e-postmeddelandet från aadnotification@microsoft.com . För att undvika att e-postmeddelandet skickas till din skräppostplats lägger du till det här e-postmeddelandet till dina kontakter.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Förnya ett certifikat som snart upphör att gälla

Om ett certifikat snart upphör att gälla kan du förnya det med hjälp av en procedur som inte resulterar i någon betydande stilleståndstid för dina användare. Så här förnyar du ett certifikat som går ut:

1. Följ anvisningarna i avsnittet [Skapa ett nytt certifikat](#create-a-new-certificate) tidigare, med ett datum som överlappar det befintliga certifikatet. Det datumet begränsar mängden stilleståndstid som orsakas av certifikatets upphörande.
1. Om programmet automatiskt kan förnya ett certifikat anger du att det nya certifikatet ska vara aktivt genom att följa dessa steg:
   1. Gå tillbaka till sidan **SAML-signeringscertifikat.**
   1. På den nyligen sparade certifikatraden väljer du ellipsen (**...**) och sedan **Gör certifikatet aktivt.**
   1. Hoppa över de kommande två stegen.

1. Om appen bara kan hantera ett certifikat i taget väljer du ett stilleståndsintervall för att utföra nästa steg. (Om programmet inte automatiskt hämtar det nya certifikatet men kan hantera fler än ett signeringscertifikat kan du utföra nästa steg när som helst.)
1. Innan det gamla certifikatet upphör att gälla följer du anvisningarna i [avsnittet Ladda upp och aktivera ett](#upload-and-activate-a-certificate) certifikat tidigare.
1. Logga in på programmet för att kontrollera att certifikatet fungerar korrekt.

## <a name="related-articles"></a>Relaterade artiklar

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md)
- [Programhantering med Azure Active Directory](what-is-application-management.md)
- [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md)
- [Felsöka SAML-baserad enkel inloggning för program i Azure Active Directory](./debug-saml-sso-issues.md)
