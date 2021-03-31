---
title: Så här skaffar du AppSource Certified för Azure Active Directory | Microsoft Docs
description: Information om hur du får ditt program AppSource certifierat för Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: how-to
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: d9a4da6fe65fda07609c7399518fa324017ea44c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649353"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Så här skaffar du AppSource Certified för Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

[Microsoft AppSource](https://appsource.microsoft.com/) är ett mål för företags användare som kan upptäcka, testa och hantera branschspecifika SaaS program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill visa en lista över ett fristående SaaS-program på AppSource måste ditt program acceptera enkel inloggning från arbets konton från alla företag eller organisationer som har Azure Active Directory (Azure AD). Inloggnings processen måste använda [OpenID Connect](v1-protocols-openid-connect-code.md) -eller [OAuth 2,0](v1-protocols-oauth-code.md) -protokoll. SAML-integrering accepteras inte för AppSource-certifiering.

## <a name="guides-and-code-samples"></a>Guider och kod exempel

Om du vill lära dig mer om hur du integrerar ditt program med Azure AD med hjälp av öppen ID Connect, följer du våra guider och kod exempel i [Azure Active Directory Developer ' s guide](v1-overview.md#get-started "Kom igång med Azure AD för utvecklare").

## <a name="multi-tenant-applications"></a>Program för flera innehavare

Ett *program med flera innehavare* är ett program som godkänner inloggningar från användare från ett företag eller en organisation som har Azure AD utan att kräva en separat instans, konfiguration eller distribution. AppSource rekommenderar att program implementerar flera innehavare *för att aktivera den kostnads* fria utvärderings versionen.

Följ dessa steg om du vill aktivera flera innehavare av ditt program:
1. Ange `Multi-Tenanted` egenskapen till `Yes` för program registreringens information i [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Som standard konfigureras program som skapats i Azure Portal som *[en enda klient](#single-tenant-applications)*.
1. Uppdatera din kod för att skicka begär anden till `common` slut punkten. Det gör du genom att uppdatera slut punkten från `https://login.microsoftonline.com/{yourtenant}` till `https://login.microsoftonline.com/common*` .
1. För vissa plattformar, t. ex. ASP .NET, behöver du också uppdatera koden för att godkänna flera utfärdare.

Mer information om flera innehavare finns i [så här loggar du in en Azure Active Directory (Azure AD)-användare med program mönstret för flera innehavare](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

### <a name="single-tenant-applications"></a>Program med en enda klient

Ett *program med en enda klient* är ett program som bara accepterar inloggningar från användare av en DEFINIERAD Azure AD-instans. Externa användare (inklusive arbets-eller skol konton från andra organisationer eller personliga konton) kan logga in i ett program med en enda klient organisation efter att ha lagt till varje användare som gäst konto i Azure AD-instansen som programmet har registrerats. 

Du kan lägga till användare som gäst konton i Azure AD via [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md) och du kan göra detta [program mässigt](../../active-directory-b2c/code-samples.md). När du använder B2B kan du skapa en självbetjänings portal som inte kräver någon inbjudan att logga in. Mer information finns i [självbetjänings portalen för integration av Azure AD B2B-samarbete](../external-identities/self-service-portal.md).

Program med en enda klient organisation kan aktivera funktionen *kontakta mig* , men om du vill aktivera den kostnads fria utvärderings versionen som AppSource rekommenderar aktiverar du flera innehavare i programmet i stället.

## <a name="appsource-trial-experiences"></a>AppSource utvärderings upplevelser

### <a name="free-trial-customer-led-trial-experience"></a>Kostnads fri utvärderings version (kund ledande utvärderings version)

Den här utvärderings versionen av kund indikatorn är den erfarenhet som AppSource rekommenderar eftersom den ger en enkel klickning till ditt program. I följande exempel visas hur den här upplevelsen ser ut:

<table >
<tr>    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt="Shows Free trial for customer-led trial experience."/><ul><li>Användaren hittar ditt program på AppSource webbplats</li><li>Väljer alternativet för gratis utvärderings version</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt="Shows how user is redirected to a URL in your web site."/><ul><li>AppSource omdirigerar användaren till en URL på webbplatsen</li><li>Webbplatsen startar processen för <i>enkel inloggning</i> automatiskt (vid sid inläsning)</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt="Shows the Microsoft sign-in page."/><ul><li>Användaren omdirigeras till Microsoft-inloggnings sida</li><li>Användaren anger autentiseringsuppgifter för inloggning</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt="Example: Consent page for an application."/><ul><li>Användaren får ett medgivande för ditt program</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site."/><ul><li>Inloggningen är klar och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren startar den kostnads fria utvärderings versionen</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Kontakta mig (en utvärderings version av partner lampan)

Du kan använda utvärderings versionen av partner när en manuell eller långsiktig åtgärd måste utföras för att etablera användaren/företaget, till exempel att ditt program måste etablera virtuella datorer, databas instanser eller åtgärder som tar lång tid att slutföra. I det här fallet, efter att användaren har valt **utvärderings** knappen för begäran och fyller i ett formulär, skickar AppSource dig användarens kontakt information. När du får den här informationen kan du etablera miljön och skicka instruktionerna till användaren om hur du får åtkomst till utvärderings versionen:<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt="Shows Contact me for partner-led trial experience"/><ul><li>Användaren hittar ditt program på AppSource webbplats</li><li>Väljer alternativet kontakta mig</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt="Shows an example form with contact info"/><ul><li>Fyller i ett formulär med kontakt information</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/usercontact.png" width="55%" alt="Shows placeholder for user information"/></td>
            <td>Du får användar information</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/setupenv.png" width="55%" alt="Shows placeholder for setup environment info"/></td>
            <td>Konfigurera miljön</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/contactcustomer.png" width="55%" alt="Shows placeholder for trial info"/></td>
            <td>Kontakta användare med utvärderings information</td>
        </tr>
        </table><br/><br/>
        <ul><li>Du får användarens information och installations utvärderings instans</li><li>Du skickar hyperlänken för att få åtkomst till ditt program till användaren</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt="Shows the application sign-in screen"/><ul><li>Användaren får åtkomst till ditt program och slutför processen för enkel inloggning</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt="Shows an example consent page for an application"/><ul><li>Användaren får ett medgivande för ditt program</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt="Shows the experience the user sees when redirected back to your site"/><ul><li>Inloggningen är klar och användaren omdirigeras tillbaka till din webbplats</li><li>Användaren startar den kostnads fria utvärderings versionen</li></ul></td>  
</tr>
</table>

### <a name="more-information"></a>Mer information

Mer information om utvärderings versionen av AppSource finns i [den här videon](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar program som stöder Azure AD-inloggningar finns i [autentiserings scenarier för Azure AD](./v1-authentication-scenarios.md).
- Information om hur du listar ditt SaaS-program i AppSource finns i [AppSource partner information](https://appsource.microsoft.com/partners)

## <a name="get-support"></a>Få support

För Azure AD-integrering använder vi [Microsoft Q&A](/answers/products/) med communityn för att få support.

Vi rekommenderar starkt att du ställer dina frågor på Microsoft Q&en första och söker efter befintliga ärenden för att se om någon har bett din fråga tidigare. Kontrol lera att dina frågor eller kommentarer är taggade med [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Använd följande kommentarer-avsnitt för att ge feedback och hjälpa oss att förfina och forma vårt innehåll.

<!--Reference style links -->
[AAD-Auth-Scenarios]:v1-authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:v1-authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->