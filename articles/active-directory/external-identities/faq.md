---
title: Vanliga frågor och svar om B2B-samarbete – Azure Active Directory | Microsoft Docs
description: Få svar på vanliga frågor om Azure Active Directory B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 09/23/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90cc15dc3944efd82d025df1bb5072c57346055e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355638"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Vanliga frågor och svar om Azure Active Directory B2B-samarbete

Vanliga frågor och svar (FAQ) om Azure Active Directory (B2B-samarbete från Business till företag) uppdateras regelbundet för att inkludera nya ämnen.

> [!IMPORTANT]
> - Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).
> - Från och med den **31 mars 2021** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Kan vi anpassa vår inloggnings sida så att det är intuitivt för våra B2B-användare av B2B-samarbete?
Helt! Se vårt [blogg inlägg om den här funktionen](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). Mer information om hur du anpassar din organisations inloggnings sida finns i avsnittet [lägga till företags anpassning för inloggnings-och åtkomst panel sidor](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>Kan B2B-samarbets användare få åtkomst till SharePoint Online och OneDrive?
Ja. Möjligheten att söka efter befintliga gäst användare i SharePoint Online med hjälp av person väljaren är **inaktive rad** som standard. Om du vill aktivera alternativet för att söka efter befintliga gäst användare ställer du in **ShowPeoplePickerSuggestionsForGuestUsers** på **på**. Du kan aktivera den här inställningen antingen på klient nivån eller på webbplats samlings nivå. Du kan ändra den här inställningen med hjälp av Set-SPOTenant-och Set-SPOSite-cmdletar. Med dessa cmdletar kan medlemmar söka igenom alla befintliga gäst användare i katalogen. Ändringar i klient omfånget påverkar inte SharePoint Online-webbplatser som redan har etablerats.

### <a name="is-the-csv-upload-feature-still-supported"></a>Stöds funktionen för att ladda upp CSV fortfarande?
Ja. Mer information om hur du använder fil uppladdnings funktionen i. csv finns i [det här PowerShell-exemplet](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hur kan jag anpassa e-postinbjudan?
Du kan anpassa nästan allt om Inbjudnings processen genom att använda [API: er för API för B2B](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Kan gäst användare återställa sin Multi-Factor Authentication-metod?
Ja. Gäst användare kan återställa sin Multi-Factor Authentication-metod på samma sätt som vanliga användare.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Vilken organisation ansvarar för Multi-Factor Authentication-licenser?
Den bjudande organisationen utför Multi-Factor Authentication. Den bjudande organisationen måste se till att organisationen har tillräckligt med licenser för sina B2B-användare som använder Multi-Factor Authentication.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Vad händer om en partner organisation redan har konfigurerat Multi-Factor Authentication? Kan vi lita på sin Multi-Factor Authentication och inte använda vår egen Multi-Factor Authentication?
Den här funktionen stöds inte för närvarande. Om åtkomst till organisationens resurser kräver Multi-Factor Authentication måste partner organisationen registrera sig för Multi-Factor Authentication i din (bjudande) organisation.

### <a name="how-can-i-use-delayed-invitations"></a>Hur kan jag använda fördröjda inbjudningar?
En organisation kanske vill lägga till B2B-samarbets användare, etablera dem för program efter behov och sedan skicka inbjudningar. Du kan använda API: et för samverkan med B2B för att anpassa onboarding-arbetsflödet.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Kan jag göra gäst användare synliga i den globala adress listan i Exchange?
Ja. Gäst objekt visas inte i din organisations globala adress lista som standard, men du kan använda Azure Active Directory PowerShell för att göra dem synliga. Ser [kan jag göra gäst objekt synliga i den globala adress listan?](/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Kan jag göra en gäst användare till en begränsad administratör?
Absolut. Mer information finns i [lägga till gäst användare till en roll](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Tillåter Azure AD B2B-samarbete att B2B-användare kommer åt Azure Portal?
Om en användare inte har tilldelats rollen begränsad administratör behöver inte B2B-samarbets användare åtkomst till Azure Portal. B2B-samarbets användare som tilldelas rollen som begränsad administratör kan dock komma åt portalen. Om en gäst användare som inte har tilldelats någon av dessa administratörs roller får åtkomst till portalen, kan användaren dessutom få åtkomst till vissa delar av upplevelsen. Gäst användar rollen har vissa behörigheter i katalogen.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Kan jag blockera åtkomst till Azure Portal för gäst användare?

Ja! Du kan skapa en princip för villkorlig åtkomst som blockerar alla gäst-och externa användare från att få åtkomst till Azure Portal. När du konfigurerar den här principen bör du vara noga med att undvika att blockera åtkomst till medlemmar och administratörer av misstag.

1. Logga in på [Azure Portal](https://portal.azure.com/) som säkerhets administratör eller administratör för villkorlig åtkomst.
2. Välj **Azure Active Directory** i Azure Portal. 
3. Under **Hantera** väljer du **säkerhet**.
4. Under **skydda** väljer du **villkorlig åtkomst**. Välj **ny princip**.
5. På sidan **nytt** i text rutan **namn** anger du ett namn för principen (till exempel "blockera gäster från åtkomst till portalen").
6. Under **Tilldelningar** väljer du **Användare och grupper**.
7. På fliken **Inkludera** väljer du **Välj användare och grupper** och väljer sedan **alla gäst-och externa användare (för hands version)**.
9. Välj **Klar**.
10. På sidan **ny** i avsnittet **tilldelningar** väljer du **molnappar eller åtgärder**.
11. Välj **Välj appar** på sidan **molnappar eller åtgärder** och välj sedan **Välj**.
12. Välj **Microsoft Azure-hantering** på sidan **Välj** och välj sedan **Välj**.
13. På sidan **molnappar eller åtgärder** väljer du **Slutför**.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Stöder Azure AD B2B-samarbete Multi-Factor Authentication och e-postkonton för konsumenter?
Ja. Multi-Factor Authentication-och konsument-e-postkonton stöds både för Azure AD B2B-samarbete.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Har du stöd för lösen ords återställning för Azure AD B2B Collaboration-användare?
Om din Azure AD-klient är hem katalogen för en användare kan du [återställa användarens lösen ord](../fundamentals/active-directory-users-reset-password-azure-portal.md) från Azure Portal. Men du kan inte återställa ett lösen ord direkt för en gäst användare som loggar in med ett konto som hanteras av en annan Azure AD-katalog eller extern identitets leverantör. Endast gäst användaren eller en administratör i användarens arbets katalog kan återställa lösen ordet. Här följer några exempel på hur lösen ords återställning fungerar för gäst användare:
 
* Gäst användare som loggar in med en Microsoft-konto (till exempel guestuser@live.com ) kan återställa sina egna lösen ord med hjälp av Microsoft-konto självbetjäning för återställning av lösen ord (SSPR). Se [hur du återställer Microsoft-konto-lösenordet](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Gäst användare som loggar in med ett Google-konto eller en annan extern identitetsprovider kan återställa sina egna lösen ord med hjälp av identitets leverantörens SSPR-metod. Till exempel kan en gäst användare med Google-kontot guestuser@gmail.com återställa sitt lösen ord genom att följa anvisningarna i [ändra eller återställa ditt lösen ord](https://support.google.com/accounts/answer/41078).
* Om identitets klienten är en just-in-Time-klient (JIT) eller "viral"-klient (vilket innebär att det är en separat, ohanterad Azure-klient) kan bara gäst användaren återställa sina lösen ord. Ibland tar en organisation [över hanteringen av viral-klienter](../enterprise-users/domains-admin-takeover.md) som skapas när anställda använder sina arbets-e-postadresser för att registrera sig för tjänster. När organisationen har tagit över en virus klient kan bara en administratör i organisationen återställa användarens lösen ord eller aktivera SSPR. Vid behov kan du, som bjuda in organisationen, ta bort gäst användar kontot från katalogen och skicka en inbjudan igen.

* Om gäst användarens Hem Katalog är din Azure AD-klient kan du återställa användarens lösen ord. Du kan till exempel ha skapat en användare eller synkroniserat en användare från din lokala Active Directory och ange sina UserType till gäst. Eftersom den här användaren är i hem katalogen kan du återställa lösen ordet från Azure Portal.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Ger Microsoft Dynamics 365 support online för Azure AD B2B-samarbete?
Ja, Dynamics 365 (online) stöder Azure AD B2B-samarbete. Mer information finns i Dynamics 365-artikeln [Bjud in användare med Azure AD B2B-samarbete](/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Vad är livs längden för ett inledande lösen ord för en nyligen skapad B2B Collaboration-användare?
Azure AD har en fast uppsättning av lösen ord, lösen ords styrka och konto utelåsnings krav som gäller för alla Azure AD Cloud-användarkonton. Moln användar konton är konton som inte är federerade med en annan identitets leverantör, t. ex. 
* Microsoft-konto
* Facebook
* Active Directory Federation Services
* En annan moln klient organisation (för B2B-samarbete)

För federerade konton är lösen ords principen beroende av principen som tillämpas i den lokala innehavaren och användarens Microsoft-konto inställningar.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>En organisation kanske vill ha olika upplevelser i sina program för klient användare och gäst användare. Finns det någon standard vägledning för detta? Har identitets leverantörens anspråk rätt modell att använda?
En gäst användare kan använda valfri identitets leverantör för att autentisera. Mer information finns i [Egenskaper för en B2B-samarbets användare](user-properties.md). Använd egenskapen **UserType** för att fastställa användar upplevelsen. **UserType** -anspråket ingår för närvarande inte i token. Program ska använda Microsoft Graph API för att fråga katalogen för användaren och hämta UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Var kan jag hitta en B2B-samarbets community för att dela lösningar och skicka idéer?
Vi lyssnar ständigt på din feedback för att förbättra B2B-samarbetet. Dela användar scenarier, metod tips och vad du tycker om Azure AD B2B-samarbete. Delta i diskussionen i [Microsoft Tech community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Vi bjuder också in dig att skicka in dina idéer och rösta på framtida funktioner i [B2B-samarbets idéer](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Kan vi skicka en inbjudan som har lösts automatiskt, så att användaren bara är redo att sätta igång? Eller behöver användaren alltid klicka dig igenom till inlösnings-URL: en?
Du kan bjuda in andra användare i partner organisationen med hjälp av användar gränssnittet, PowerShell-skripten eller API: erna. Sedan kan du skicka gäst användaren en direkt länk till en delad app. I de flesta fall behöver du inte längre öppna e-postinbjudan och klicka på en inlösnings-URL. Se [Azure Active Directory B2B-samarbets inbjudan till inbjudan](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hur fungerar B2B-samarbete när den inbjudna partnern använder Federation för att lägga till sin egen lokala autentisering?
Om partnern har en Azure AD-klient som är federerad för den lokala autentiserings infrastrukturen, uppnås lokalt enkel inloggning (SSO) automatiskt. Om partnern inte har en Azure AD-klient skapas ett Azure AD-konto för nya användare. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Jag trodde att Azure AD B2B inte accepterade gmail.com-och outlook.com-e-postadresser och att B2C användes för dessa typer av konton?
Vi tar bort skillnaderna mellan B2B-och B2C-samarbete (Business-to-Consumer) i termer för vilka identiteter som stöds. Den identitet som används är inte en korrekt anledning att välja mellan att använda B2B eller med hjälp av B2C. Information om hur du väljer samarbets alternativ finns i [jämföra B2B-samarbete och B2C i Azure Active Directory](compare-with-b2c.md).

### <a name="can-an-azure-ad-b2c-local-account-be-invited-to-an-azure-ad-tenant-for-b2b-collaboration"></a>Kan ett Azure AD B2C lokalt konto bjudas in till en Azure AD-klient för B2B-samarbete?
Nej. Ett Azure AD B2C lokalt konto kan bara användas för att logga in på Azure AD B2C klienten. Kontot kan inte användas för att logga in på en Azure AD-klient. Det finns inte stöd för att bjuda in ett Azure AD B2C lokalt konto till en Azure AD-klient för B2B-samarbete.

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Vilka program och tjänster stöder Azure B2B-gäst användare?
Alla Azure AD-integrerade program kan ha stöd för Azure B2B-gäst användare, men de måste använda en slut punkt som har kon figurer ATS som en klient för att autentisera gäst användare. Du kan också behöva [Anpassa anspråk](claims-mapping.md) i SAML-token som utfärdas när en gäst användare autentiserar sig till appen. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kan vi tvinga Multi-Factor Authentication för B2B-gäst användare om våra partners inte har Multi-Factor Authentication?
Ja. Mer information finns i [villkorlig åtkomst för B2B-samarbets användare](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>I SharePoint kan du definiera listan "Tillåt" eller "Neka" för externa användare. Kan vi göra detta i Azure?
Ja. Azure AD B2B-samarbete stöder listor över tillåtna listor och nekade listor. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Vilka licenser behöver vi för att använda Azure AD B2B?
Information om vilka licenser din organisation behöver för att använda Azure AD B2B finns i [prissättning för externa identiteter](external-identities-pricing.md).

### <a name="next-steps"></a>Nästa steg

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)