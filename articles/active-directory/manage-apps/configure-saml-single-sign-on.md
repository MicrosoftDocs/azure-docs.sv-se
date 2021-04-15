---
title: Förstå SAML-baserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå SAML-baserad enkel inloggning (SSO) för appar i Azure Active Directory
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: iangithinji
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b7468f33c75dd58e70c344f3ef19c51e220a7abb
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374496"
---
# <a name="understand-saml-based-single-sign-on"></a>Förstå SAML-baserad enkel inloggning

I [snabbstartsserien](view-applications-portal.md) om programhantering lärde du dig att använda Azure AD som identitetsprovider (IdP) för ett program. Den här artikeln innehåller mer information om det SAML-baserade alternativet för enkel inloggning. 


## <a name="before-you-begin"></a>Innan du börjar

Det kan vara enkelt eller komplext att använda Azure AD som identitetsprovider (IdP) och konfigurera enkel inloggning (SSO) beroende på vilket program som används. Vissa program kan konfigureras med bara några få åtgärder. Andra kräver djupgående konfiguration. Om du vill få kunskap snabbt kan du gå igenom [snabbstartsserien](view-applications-portal.md) om programhantering. Om programmet du lägger till är enkelt behöver du förmodligen inte läsa den här artikeln. Om det program som du lägger till kräver anpassad konfiguration för SAML-baserad enkel inloggning är den här artikeln något för dig.

I [snabbstartsserien](add-application-portal-setup-sso.md)finns det en artikel om hur du konfigurerar enkel inloggning. I den får du lära dig hur du kommer åt SAML-konfigurationssidan för en app. SIDAN SAML-konfiguration innehåller fem avsnitt. Dessa avsnitt beskrivs i detalj i den här artikeln.

> [!IMPORTANT] 
> Det finns vissa scenarier där **alternativet Enkel inloggning inte** finns i navigeringen för ett program i **Företagsprogram.** 
>
> Om programmet registrerades med **Appregistreringar** är funktionen för enkel inloggning konfigurerad att använda OIDC OAuth som standard. I det här fallet **visas inte alternativet Enkel** inloggning i navigeringen under **Företagsprogram**. När du använder **Appregistreringar för** att lägga till din anpassade app konfigurerar du alternativ i manifestfilen. Mer information om manifestfilen finns i Azure Active Directory [appmanifest](../develop/reference-app-manifest.md). Mer information om standarder för enkel inloggning finns i Autentisering [och auktorisering med Microsoft Identity Platform.](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform) 
>
> Andra scenarier **där** enkel inloggning saknas i navigeringen är när ett program finns i en annan klientorganisation eller om ditt konto inte har de behörigheter som krävs (global administratör, molnprogramadministratör, programadministratör eller ägare av tjänstens huvudnamn). Behörigheter kan också orsaka ett scenario där du **kan öppna enkel** inloggning men inte kan spara. Mer information om administrativa roller i Azure AD finns i ( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) .


## <a name="basic-saml-configuration"></a>Grundläggande SAML-konfiguration

Du bör hämta värdena från programleverantören. Du kan ange värdena manuellt eller ladda upp en metadatafil för att extrahera värdet för fälten.

> [!TIP]
> Många appar har redan förkonfigureras för att fungera med Azure AD. Dessa appar visas i galleriet med appar som du kan bläddra bland när du lägger till en app i din Azure AD-klientorganisation. [Snabbstartsserien](add-application-portal-setup-sso.md) vägled dig genom processen. För apparna i galleriet hittar du detaljerade stegvisa instruktioner. Om du vill komma åt stegen kan du klicka på länken på SAML-konfigurationssidan för appen enligt beskrivningen i snabbstartsserien eller bläddra i en lista över alla självstudier om appkonfiguration i Självstudier för [SaaS-appkonfiguration.](../saas-apps/tutorial-list.md)

| Grundläggande SAML-konfigurationsinställning | SP-initierad | idP-initierad | Beskrivning |
|:--|:--|:--|:--|
| **Identifierare (entitets-ID)** | Krävs för vissa appar | Krävs för vissa appar | Identifierar programmet unikt. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet. Ange en URL som använder följande mönster: "https:// .contoso.com" Du hittar det här värdet som issuer-elementet i <subdomain> *den **AuthnRequest** (SAML-begäran)* som skickas av programmet. |
| **Svarswebbadress** | Obligatorisk | Obligatorisk | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). Du kan använda de ytterligare fälten för svars-URL för att ange flera svars-URL:er. Du kan till exempel behöva ytterligare svars-URL:er för flera underdomäner. Eller så kan du i testsyfte ange flera svars-URL:er (lokal värd och offentliga URL:er) i taget. |
| **Inloggnings-URL** | Obligatorisk | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Microsoft 365 eller Azure AD Mina appar. När det är tomt gör Azure AD en IdP-initierad inloggning när en användare startar programmet från Microsoft 365, Azure AD Mina appar eller URL:en för enkel inloggning i Azure AD.|
| **Vidarebefordransstatus** | Valfritt | Valfritt | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Vanligtvis är värdet en giltig URL för programmet. Vissa program använder dock det här fältet på olika sätt. Kontakta programleverantören om du vill ha mer information.
| **Utloggnings-URL** | Valfritt | Valfritt | Används för att skicka SAML-utloggningssvaren tillbaka till programmet.

## <a name="user-attributes-and-claims"></a>Användarattribut och anspråk 

När en användare autentiserar till programmet utfärdar Azure AD en SAML-token med information (eller anspråk) om användaren som identifierar dem unikt. Som standard innehåller den här informationen användarens användarnamn, e-postadress, förnamn och efternamn. Du kan behöva anpassa dessa anspråk om till exempel programmet kräver specifika anspråksvärden eller ett annat **namnformat** än användarnamn. 

> [!IMPORTANT]
> Många appar är redan förkonfigurerade och finns i appgalleriet och du behöver inte bekymra dig om att ställa in användar- och gruppanspråk. [Snabbstartsserien vägled](add-application-portal.md) dig genom att lägga till och konfigurera appar.


Identifierarvärdet **för unik användaridentifierare (namn-ID)** är ett obligatoriskt anspråk och är viktigt. Standardvärdet är *user.userprincipalname*. Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

Mer information om hur du anpassar SAML-anspråk finns i Anpassa anspråk som utfärdats i [SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)

Du kan lägga till nya anspråk. Mer information finns i [Lägga](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims) till programspecifika anspråk eller lägga till gruppanspråk i [Konfigurera gruppanspråk.](../hybrid/how-to-connect-fed-group-claims.md)


> [!NOTE]
> Fler sätt att anpassa SAML-token från Azure AD till ditt program finns i följande resurser.
>- Information om hur du skapar anpassade roller via Azure Portal finns [i Konfigurera rollanspråk](../develop/active-directory-enterprise-app-role-management.md).
>- Om du vill anpassa anspråken via PowerShell kan [du se Anpassa anspråk – PowerShell.](../develop/active-directory-claims-mapping.md)
>- Information om hur du ändrar applikationsmanifestet för att konfigurera valfria anspråk för ditt program [finns i Konfigurera valfria anspråk](../develop/active-directory-optional-claims.md).
>- Information om hur du anger livslängdsprinciper för token för uppdateringstoken, åtkomsttoken, sessionstoken och ID-token finns i [Konfigurera livslängd för token.](../develop/active-directory-configurable-token-lifetimes.md) Om du vill begränsa autentiseringssessioner via villkorsstyrd åtkomst för Azure AD kan du också gå till funktioner [för hantering av autentiseringssessioner.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="saml-signing-certificate"></a>SAML-signeringscertifikat

Azure AD använder ett certifikat för att signera de SAML-token som det skickar till programmet. Du behöver det här certifikatet för att konfigurera förtroendet mellan Azure AD och programmet. Mer information om certifikatformatet finns i programmets SAML-dokumentation. Mer information finns i Hantera [certifikat för federerad enkel inloggning och](manage-certificates-for-federated-single-sign-on.md) Avancerade alternativ för [certifikatsignering i SAML-token](certificate-signing-options.md).

> [!IMPORTANT]
> Många appar är redan förkonfigurerade och i appgalleriet och du behöver inte fördjupa dig i certifikat. [Snabbstartsserien vägled](add-application-portal.md) dig genom att lägga till och konfigurera appar.

Från Azure AD kan du ladda ned det aktiva certifikatet i Base64- eller Raw-format direkt från huvudsidan Konfigurera **enkel Sign-On med SAML.** Du kan också hämta det aktiva certifikatet genom att ladda ned XML-filen med programmetadata eller genom att använda URL:en för appfederationsmetadata. Följ dessa steg om du vill visa, skapa eller ladda ned certifikat (aktiva eller inaktiva).

Några vanliga saker att kontrollera för att verifiera ett certifikat är: 
   - *Rätt förfallodatum.* Du kan konfigurera förfallodatumet för upp till tre år framåt.
   - *Statusen aktiv för rätt certifikat.* Om statusen är **Inaktiv** ändrar du statusen till **Aktiv.** Om du vill ändra statusen högerklickar du på certifikatraden och väljer **Gör certifikatet aktivt.**
   - *Rätt signeringsalternativ och algoritm.*
   - *Rätt e-postadress för meddelanden.* När det aktiva certifikatet är nära utgångsdatumet skickar Azure AD ett meddelande till den e-postadress som konfigurerats i det här fältet.

Ibland kan du behöva ladda ned certifikatet. Var dock försiktig med var du sparar den! Om du vill ladda ned certifikatet väljer du något av alternativen för Base64-format, Raw-format eller XML för federationsmetadata. Azure AD tillhandahåller även **URL:en för appfederationsmetadata** där du kan komma åt de metadata som är specifika för programmet i formatet `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` .

> [!NOTE]
> Programmet ska kunna hantera byteordningsmarkören som finns i XML:en som återges när du använder https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} . Byteordningsmärket representeras som ett icke utskrivbart ASCII-tecken »» och i Hex representeras det som EF BB BF vid granskning av XML-data.

Om du vill göra certifikatändringar väljer du knappen Redigera. Det finns flera saker du kan göra på sidan **SAML-signeringscertifikat:**
   - Skapa ett nytt certifikat: **välj Nytt** certifikat, välj **Förfallodatum** och välj sedan **Spara.** Om du vill aktivera certifikatet väljer du snabbmenyn (**...**) och sedan **Gör certifikatet aktivt.**
   - Ladda upp ett certifikat med privat nyckel och pfx-autentiseringsuppgifter: **välj Importera** certifikat och bläddra till certifikatet. Ange **PFX-lösenordet** och välj sedan Lägg **till**.  
   - Konfigurera avancerad certifikatsignering. Mer information om dessa alternativ finns i Avancerade [alternativ för certifikatsignering.](certificate-signing-options.md)
   - Meddela ytterligare personer när det aktiva certifikatet är nära förfallodatumet: ange e-postadresserna i fälten **E-postadresser för avisering.**

## <a name="set-up-the-application-to-use-azure-ad"></a>Konfigurera programmet att använda Azure AD

I **avsnittet \<applicationName> Konfigurera** visas de värden som måste konfigureras i programmet så att azure AD används som SAML-identitetsprovider. Du anger värdena på konfigurationssidan på programwebbplatsen. Om du till exempel konfigurerar GitHub går du till github.com och anger värdena. Om programmet redan är förkonfigurerat och i Azure AD-galleriet hittar du en länk till Visa **stegvisa instruktioner.** Annars måste du hitta dokumentationen för det program som du konfigurerar. 

Värdena **för inloggnings-URL** **och utloggnings-URL** matchar båda till samma slutpunkt, vilket är slutpunkten för SAML-begärandehantering för Azure AD-klientorganisationen. 

**Azure AD-identifieraren** är värdet för **Utfärdaren i** DEN SAML-token som utfärdats till programmet.

## <a name="test-single-sign-on"></a>Testa enkel inloggning

När du har konfigurerat ditt program att använda Azure AD som en SAML-baserad identitetsprovider kan du testa inställningarna för att se om enkel inloggning fungerar för ditt konto. 

Välj **Test** och välj sedan att testa med den inloggade användaren eller som någon annan. 

Om inloggningen lyckas är du redo att tilldela användare och grupper till ditt SAML-program. Grattis!

Om ett felmeddelande visas utför du följande steg:

1. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. Välj **Hämta lösningsvägledning.** Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

3. Läs lösningsvägledningen och försök sedan åtgärda problemet.

4. Kör testet igen tills det slutförts helt.

Mer information finns i [Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory](./debug-saml-sso-issues.md).


## <a name="next-steps"></a>Nästa steg

- [Snabbstartsserie om programhantering](view-applications-portal.md)
- [Tilldela användare eller grupper till programmet](./assign-user-or-group-access-portal.md)
- [Konfigurera automatisk användarkontoetablering](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md)
