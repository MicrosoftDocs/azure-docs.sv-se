---
title: Felsöka B2B-samarbete – Azure Active Directory | Microsoft Docs
description: Lösningar på vanliga problem med Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 02/12/2021
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60cd944ecb144a30e872259f6e959a11c3ea6319
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365437"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Felsöka Azure Active Directory B2B-samarbete

Här följer några lösningar på vanliga problem med Azure Active Directory (Azure AD) B2B-samarbete.

   > [!IMPORTANT]
   > - Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **Från den 2021 oktober** kommer Microsoft inte längre att stödja inlösen av inbjudningar genom att skapa ohanterade Azure AD-konton och klienter för B2B-samarbets scenarier. Vi rekommenderar att kunderna väljer [autentisering med e-post med eng ång slö sen ord](one-time-passcode.md). Vi välkomnar din feedback om den här offentliga för hands versionen och är glada att skapa ännu fler sätt att samar beta.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>Jag har lagt till en extern användare men ser inte dem i den globala adress boken eller i person väljaren

I de fall där externa användare inte är ifyllda i listan kan det ta några minuter att replikera objektet.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>En B2B-gäst användare visas inte i SharePoint Online/OneDrive People-väljare

Möjligheten att söka efter befintliga gäst användare i användar väljaren för SharePoint Online (SPO) är inaktive rad som standard för att matcha äldre beteende.

Du kan aktivera den här funktionen genom att använda inställningen "ShowPeoplePickerSuggestionsForGuestUsers" på klient-och webbplats samlings nivå. Du kan ställa in funktionen med hjälp av Set-SPOTenant-och Set-SPOSite-cmdlet: ar, vilket gör det möjligt för medlemmar att söka igenom alla befintliga gäst användare i katalogen. Ändringar i klient omfånget påverkar inte redan etablerade SPO-webbplatser.

## <a name="invitations-have-been-disabled-for-directory"></a>Inbjudningar har inaktiverats för katalogen

Om du får ett meddelande om att du inte har behörighet att bjuda in användare, verifiera att ditt användar konto har behörighet att bjuda in externa användare under Azure Active Directory > användar inställningar > externa användare > hantera inställningar för externt samarbete:

![Skärm bild som visar inställningarna för externa användare](media/troubleshoot/external-user-settings.png)

Om du nyligen har ändrat de här inställningarna eller tilldelat rollen gäst deltagare till en användare kan det uppstå en fördröjning på 15-60 minuter innan ändringarna börjar gälla.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>Användaren som jag har bjudit in får ett fel under inlösen

Vanliga fel är:

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>Inbjudans administratör har inte tillåtet EmailVerified-användare från att skapas i sin klient organisation

När du bjuder in användare vars organisation använder Azure Active Directory, men där den särskilda användarens konto inte finns (till exempel om användaren inte finns i Azure AD contoso.com). Administratören för contoso.com kan ha en princip på plats som hindrar användare från att skapas. Användaren måste kontakta administratören för att avgöra om externa användare tillåts. Den externa användarens administratör kan behöva tillåta e-postverifierade användare i sin domän (se den här [artikeln](/powershell/module/msonline/set-msolcompanysettings) om att tillåta e-postverifierade användare).

![Fel som anger att klienten inte tillåter e-postverifierade användare](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>Extern användare finns inte redan i en federerad domän

Om du använder Federations autentisering och användaren inte redan finns i Azure Active Directory kan användaren inte bjudas in.

För att lösa det här problemet måste den externa användarens administratör synkronisera användarens konto för att Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>Hur fungerar \# , vilket inte normalt är ett giltigt Character, synkronisera med Azure AD?

" \# " är ett reserverat Character i UPN för Azure AD B2B-samarbete eller externa användare eftersom det inbjudna kontot user@contoso.com blir user_contoso. com # ext # @fabrikam.onmicrosoft.com . Därför \# går det inte att logga in på Azure Portal i UPN: er som kommer från lokala platser. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Jag får ett fel meddelande när jag lägger till externa användare i en synkroniserad grupp

Externa användare kan bara läggas till i "tilldelade" eller "säkerhets" grupper och inte till grupper som är hanterade lokalt.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>Min externa användare fick inget e-postmeddelande för att lösa in

Inbjudan bör kontrol lera med Internet leverantören eller skräp post filtret för att säkerställa att följande adress tillåts: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Jag märker att det anpassade meddelandet inte inkluderas med Inbjudnings meddelanden vid tillfällen

För att efterleva sekretess lagarna innehåller API: erna inte anpassade meddelanden i e-postinbjudan när:

- Inbjudan har ingen e-postadress i den bjudande klienten
- När ett AppService-objekt skickar inbjudan

Om det här scenariot är viktigt för dig kan du utelämna e-postmeddelandet för API-inbjudan och skicka det via e-postmekanismen som du väljer. Kontakta organisationens juridiska rådgivning för att se till att e-post som du skickar på det här sättet också uppfyller sekretess lagar.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Du får ett "AADSTS65005"-fel när du försöker logga in på en Azure-resurs

En användare som har ett gäst konto kan inte logga in och får följande fel meddelande:

```plaintext
    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.
```

Användaren har ett Azure-användarkonto och är en viral-klient som har övergivits eller inte hanteras. Det finns dessutom inga globala administratörer i klient organisationen.

För att lösa det här problemet måste du ta över den övergivna klienten. Se  [ta över en ohanterad katalog som administratör i Azure Active Directory](../enterprise-users/domains-admin-takeover.md). Du måste också ha åtkomst till Internet-riktad DNS för domänsuffix i fråga för att kunna tillhandahålla direkt bevis som du styr av namn området. När klienten har returnerats till ett hanterat tillstånd kan du diskutera med kunden om du lämnar användarna och det verifierade domän namnet är det bästa alternativet för deras organisation.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>En gäst användare med en just-in-Time-eller "viral"-klient kan inte återställa sina lösen ord

Om identitets klienten är en just-in-Time (JIT) eller virus klient (vilket innebär att det är en separat, ohanterad Azure-klient) kan bara gäst användaren återställa sina lösen ord. Ibland tar en organisation [över hanteringen av viral-klienter](../enterprise-users/domains-admin-takeover.md) som skapas när anställda använder sina arbets-e-postadresser för att registrera sig för tjänster. När organisationen har tagit över en virus klient kan bara en administratör i organisationen återställa användarens lösen ord eller aktivera SSPR. Vid behov kan du, som bjuda in organisationen, ta bort gäst användar kontot från katalogen och skicka en inbjudan igen.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>En gäst användare kan inte använda AzureAD PowerShell v1-modulen

Från och med 18 november 2019 blockeras gäst användare i din katalog (definierade som användar konton där **userType** -egenskapen är lika med **gäst**) från att använda AzureAD PowerShell v1-modulen. Användaren måste antingen vara en medlems användare (där **userType** är lika med **medlem**) eller använda AzureAD PowerShell V2-modulen.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>Jag kan inte bjuda in gäst användare för B2B-samarbete i en Azure-klient för amerikanska myndigheter

I Azure-molnet för amerikanska myndigheter stöds för närvarande inte B2B-samarbete mellan klienter som båda är i Azures offentliga moln för amerikanska myndigheter och som båda stöder B2B-samarbete. Om du bjuder in en användare i en klient som inte är en del av Azure-molnet för amerikanska myndigheter eller som ännu inte stöder B2B-samarbete får du ett fel meddelande. Mer information och begränsningar finns i [Azure Active Directory Premium P1-och P2-variationer](../../azure-government/compare-azure-government-global-azure.md#azure-active-directory-premium-p1-and-p2).

## <a name="i-receive-the-error-that-azure-ad-cannot-find-the-aad-extensions-app-in-my-tenant"></a>Jag får ett fel meddelande om att Azure AD inte kan hitta AAD-Extensions-appen i min klient organisation

När du använder självbetjänings registrerings funktioner, t. ex. anpassade användarattribut eller användar flöden, skapas automatiskt en app som heter `aad-extensions-app. Do not modify. Used by AAD for storing user data.` . Den används av externa Azure AD-identiteter för att lagra information om användare som registrerar sig för och anpassade attribut som samlas in.

Om du har tagit bort `aad-extensions-app` av misstag har du 30 dagar på dig att återställa den. Du kan återställa appen med hjälp av Azure AD PowerShell-modulen.

1. Starta Azure AD PowerShell-modulen och kör `Connect-AzureAD` .
1. Logga in som global administratör för den Azure AD-klient som du vill återställa den borttagna appen för.
1. Kör PowerShell-kommandot `Get-AzureADDeletedApplication` .
1. Hitta programmet i listan där visnings namnet börjar med `aad-extensions-app` och kopiera dess `ObjectId` egenskaps värde.
1. Kör PowerShell-kommandot `Restore-AzureADDeletedApplication -ObjectId {id}` . Ersätt `{id}` delen av kommandot med `ObjectId` från föregående steg.

Du bör nu se den återställda appen i Azure Portal.

## <a name="next-steps"></a>Nästa steg

[Få stöd för B2B-samarbete](../fundamentals/active-directory-troubleshooting-support-howto.md)
