---
title: Förstå SAML-baserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå SAML-baserad enkel inloggning (SSO) för appar i Azure Active Directory
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257700"
---
# <a name="understand-saml-based-single-sign-on"></a>Förstå SAML-baserad enkel inloggning

I [snabb starts serien](view-applications-portal.md) för program hantering har du lärt dig hur du använder Azure AD som identitets leverantör (IdP) för ett program. Den här artikeln går till mer information om SAML-baserade alternativ för enkel inloggning. 


## <a name="before-you-begin"></a>Innan du börjar

Att använda Azure AD som identitets leverantör (IdP) och konfigurera enkel inloggning (SSO) kan vara enkelt eller komplext beroende på vilket program som används. Vissa program kan konfigureras med bara några få åtgärder. Andra kräver djupgående konfiguration. Om du vill öka kunskapen snabbt kan du gå igenom [snabb starts serien](view-applications-portal.md) för program hantering. Om det program som du lägger till är enkelt, behöver du förmodligen inte läsa den här artikeln. Om det program som du lägger till kräver anpassad konfiguration för SAML-baserad SSO, är den här artikeln för dig.

I [snabb starts serien](add-application-portal-setup-sso.md)finns det en artikel om hur du konfigurerar enkel inloggning. I det här avsnittet får du lära dig hur du kommer åt sidan SAML-konfiguration för en app. Sidan SAML-konfiguration innehåller fem avsnitt. Dessa avsnitt beskrivs i detalj i den här artikeln.

> [!IMPORTANT] 
> Det finns vissa scenarier där alternativet **enkel inloggning** inte kommer att finnas i navigeringen för ett program i **företags program**. 
>
> Om programmet registrerades med **Appregistreringar** konfigureras funktionen för enkel inloggning för att använda OIDC OAuth som standard. I det här fallet visas inte alternativet för **enkel inloggning** i navigeringen under **företags program**. När du använder **Appregistreringar** för att lägga till din anpassade app konfigurerar du alternativ i manifest filen. Mer information om manifest filen finns i [Azure Active Directory app manifest](../develop/reference-app-manifest.md). Mer information om SSO-standarder finns i [autentisering och auktorisering med Microsoft Identity Platform](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform). 
>
> Andra scenarier där **enkel inloggning** kommer att saknas i navigeringen är när ett program finns i en annan klient organisation eller om ditt konto inte har de behörigheter som krävs (global administratör, moln program administratör, program administratör eller ägare till tjänstens huvud namn). Behörigheter kan också orsaka ett scenario där du kan öppna **enkel inloggning** men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Grundläggande SAML-konfiguration

Du bör hämta värdena från program leverantören. Du kan ange värdena manuellt eller ladda upp en metadatafil för att extrahera värdet för fälten.

> [!TIP]
> Många appar har redan förkonfigurerats för att fungera med Azure AD. De här apparna visas i galleriet med appar som du kan bläddra när du lägger till en app i Azure AD-klienten. [Snabb starts serien](add-application-portal-setup-sso.md) vägleder dig genom processen. För apparna i galleriet hittar du detaljerade steg-för-steg-instruktioner. Du kan komma åt stegen genom att klicka på länken på sidan SAML-konfiguration för appen enligt beskrivningen i snabb starts serien eller så kan du bläddra i en lista över alla program konfigurations guider i [självstudier för SaaS app Configuration](../saas-apps/tutorial-list.md).

| Grundläggande konfigurations inställning för SAML | SP-initierad | idP-initierad | Beskrivning |
|:--|:--|:--|:--|
| **Identifierare (entitets-ID)** | Krävs för vissa appar | Krävs för vissa appar | Identifierar programmet unikt. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet. Ange en URL som använder följande mönster: "https:// <subdomain> . contoso.com" *du hittar det här värdet som **Issuer** -element i **AuthnRequest** (SAML-begäran) som skickas av programmet.* |
| **Svarswebbadress** | Obligatorisk | Obligatorisk | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). Du kan använda ytterligare svars-URL-fält för att ange flera svars-URL: er. Du kan till exempel behöva ytterligare svars-URL: er för flera under domäner. I test syfte kan du ange flera svars-URL: er (lokala värden och offentliga URL: er) i taget. |
| **Inloggnings-URL** | Obligatorisk | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL: en för att starta programmet från Microsoft 365 eller Azure AD Mina appar. När en användare startar programmet från Microsoft 365, Azure AD Mina appar eller Azure AD SSO-URL: en är tom.|
| **Vidarebefordransstatus** | Valfritt | Valfritt | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Vanligt vis är värdet en giltig URL för programmet. Vissa program använder dock det här fältet på olika sätt. Kontakta programleverantören om du vill ha mer information.
| **Utloggnings-URL** | Valfritt | Valfritt | Används för att skicka SAML-utloggnings Svaren tillbaka till programmet.

## <a name="user-attributes-and-claims"></a>Användarattribut och anspråk 

När en användare autentiserar sig till programmet utfärdar Azure AD programmet en SAML-token med information (eller anspråk) om den användare som unikt identifierar dem. Som standard innehåller den här informationen användarens användar namn, e-postadress, förnamn och efter namn. Du kan behöva anpassa dessa anspråk om exempelvis programmet kräver specifika anspråks värden eller ett annat **namn** än användar namn. 

> [!IMPORTANT]
> Många appar är redan förkonfigurerade och i app-galleriet och du behöver inte bekymra dig om att ange användar-och grupp anspråk. [Snabb starts serien](add-application-portal.md) vägleder dig genom att lägga till och konfigurera appar.


ID-värdet för den **unika användar identifieraren (namn-ID)** är ett obligatoriskt anspråk och är viktigt. Standardvärdet är *User. UserPrincipalName*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

Mer information om hur du anpassar SAML-anspråk finns i [så här gör du: anpassa anspråk som utfärdats i SAML-token för företags program](../develop/active-directory-saml-claims-customization.md).

Du kan lägga till nya anspråk. mer information finns i avsnittet om [att lägga till programspecifika anspråk](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) eller lägga till grupp anspråk finns i [Konfigurera grupp anspråk](../hybrid/how-to-connect-fed-group-claims.md).


> [!NOTE]
> Ytterligare sätt att anpassa SAML-token från Azure AD till ditt program finns i följande resurser.
>- Information om hur du skapar anpassade roller via Azure Portal finns i [Konfigurera roll anspråk](../develop/active-directory-enterprise-app-role-management.md).
>- Information om hur du anpassar anspråk via PowerShell finns i [Anpassa anspråk – PowerShell](../develop/active-directory-claims-mapping.md).
>- Om du vill ändra applikations manifestet för att konfigurera valfria anspråk för programmet, se [Konfigurera valfria anspråk](../develop/active-directory-optional-claims.md).
>- Om du vill ange livs längds principer för token för uppdateringstoken, åtkomsttoken, sessionstoken och ID-token, se [Konfigurera livs längd för token](../develop/active-directory-configurable-token-lifetimes.md). Eller, för att begränsa autentisering av sessioner via villkorlig åtkomst för Azure AD, se [hanterings funktioner för autentisering](../conditional-access/howto-conditional-access-session-lifetime.md).

## <a name="saml-signing-certificate"></a>SAML-signeringscertifikat

Azure AD använder ett certifikat för att signera de SAML-token som skickas till programmet. Du behöver det här certifikatet för att konfigurera förtroendet mellan Azure AD och programmet. Mer information om certifikat formatet finns i programmets SAML-dokumentation. Mer information finns i [Hantera certifikat för federerad enkel inloggning](manage-certificates-for-federated-single-sign-on.md) och [avancerade certifikat signerings alternativ i SAML-token](certificate-signing-options.md).

> [!IMPORTANT]
> Många appar är redan förkonfigurerade och i app-galleriet och du behöver inte tänka på certifikat. [Snabb starts serien](add-application-portal.md) vägleder dig genom att lägga till och konfigurera appar.

Från Azure AD kan du ladda ned det aktiva certifikatet i base64-eller RAW-format direkt från huvud **uppsättningen med enkla Sign-On med SAML** -sidan. Du kan också hämta det aktiva certifikatet genom att ladda ned XML-filen med metadata för programmet eller genom att använda URL: en för app Federation-metadata. Följ dessa steg om du vill visa, skapa eller ladda ned dina certifikat (aktiva eller inaktiva).

Några vanliga saker att kontrol lera för att kontrol lera att ett certifikat är: 
   - *Rätt utgångs datum.* Du kan konfigurera utgångs datumet för upp till tre år i framtiden.
   - *Status aktiv för rätt certifikat.* Om statusen är **inaktiv** ändrar du statusen till **aktiv**. Om du vill ändra statusen högerklickar du på certifikatets rad och väljer **gör certifikat aktivt**.
   - *Rätt signerings alternativ och algoritm.*
   - *Rätt e-postadress för e-post.* När det aktiva certifikatet är nära utgångs datumet skickar Azure AD ett meddelande till den e-postadress som kon figurer ATS i det här fältet.

Ibland kan du behöva ladda ned certifikatet. Var försiktig där du sparar det! Om du vill hämta certifikatet väljer du ett av alternativen för base64-format, RAW-format eller XML för federationsmetadata. Azure AD tillhandahåller även **URL: en för app Federation-Metadata** där du kan komma åt metadata som är specifika för programmet i formatet `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Programmet ska kunna hantera bytes order markör i XML-filen som återges när den används https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Byte ordnings tecken visas som ett icke utskrivbart ASCII-tecken» ¿och i hex representeras som EF BB BF vid visning av XML-data.

Välj knappen Redigera om du vill göra ändringar i certifikatet. Det finns flera saker du kan göra på sidan **SAML-signerings certifikat** :
   - Skapa ett nytt certifikat: Välj **nytt certifikat**, Välj **förfallo datum** och välj sedan **Spara**. Om du vill aktivera certifikatet väljer du snabb menyn (**...**) och väljer **gör certifikat aktivt**.
   - Ladda upp ett certifikat med privat nyckel och PFX-autentiseringsuppgifter: Välj **Importera certifikat** och bläddra till certifikatet. Ange **PFX-lösenordet** och välj sedan **Lägg till**.  
   - Konfigurera avancerad certifikat signering. Mer information om de här alternativen finns i [alternativ för avancerad certifikat signering](certificate-signing-options.md).
   - Meddela ytterligare personer när det aktiva certifikatet snart upphör att gälla: Ange e-postadresserna i fälten för **e-postadressen för aviseringar** .

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurera programmet för att använda Azure AD

I avsnittet **Konfigurera \<applicationName>** konfiguration visas de värden som måste konfigureras i programmet så att Azure AD används som en SAML-identitetsprovider. Du ställer in värdena på sidan konfiguration på program webbplatsen. Om du till exempel konfigurerar GitHub går du till github.com-platsen och anger värdena. Om programmet redan är förkonfigurerat och i Azure AD-galleriet hittar du en länk för att **Visa steg-för-steg-instruktioner**. Annars måste du hitta dokumentationen för det program som du konfigurerar. 

Värdena för **inloggnings-URL:** en och **utloggnings-URL: er** matchas båda till samma slut punkt, vilket är slut punkten för SAML Request-hantering för Azure AD 

**Azure AD-identifieraren** är värdet för **UTFÄRDAren** i SAML-token som utfärdats till programmet.

## <a name="test-single-sign-on"></a>Testa enkel inloggning

När du har konfigurerat ditt program till att använda Azure AD som en SAML-baserad identitetsprovider, kan du testa inställningarna för att se om enkel inloggning fungerar för ditt konto. 

Välj **test** och välj sedan att testa med den för tillfället inloggade användaren eller som någon annan. 

Om inloggningen är klar är du redo att tilldela användare och grupper till ditt SAML-program. Grattis!

Om ett fel meddelande visas utför du följande steg:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Välj **Hämta lösnings vägledning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

3. Läs lösnings vägledningen och försök sedan att åtgärda problemet.

4. Kör testet igen tills det slutförts helt.

Mer information finns i [FELSÖKA SAML-baserad enkel inloggning till program i Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Nästa steg

- [Snabb starts serie för program hantering](view-applications-portal.md)
- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md)
