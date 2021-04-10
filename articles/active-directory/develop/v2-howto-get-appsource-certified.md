---
title: Hämta AppSource Certified för Azure Active Directory | Microsoft Docs
description: Lär dig mer om hur du får ditt program AppSource certifierat för Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/23/2020
ms.author: ryanwi
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7731aa2eb1a962d4674ed382911ba0058e1ebdaf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647514"
---
# <a name="get-appsource-certified-for-azure-active-directory"></a>Hämta AppSource Certified för Azure Active Directory

[Microsoft AppSource](https://appsource.microsoft.com/) är ett mål för företags användare som kan upptäcka, testa och hantera branschspecifika SaaS program (fristående SaaS och tillägg till befintliga Microsoft SaaS-produkter).

Om du vill visa en lista över ett fristående SaaS-program på AppSource måste ditt program acceptera enkel inloggning från arbets konton från alla företag eller organisationer som har Azure Active Directory (Azure AD). Inloggnings processen måste använda [OpenID Connect](v2-protocols-oidc.md) -eller [OAuth 2,0](v2-oauth2-auth-code-flow.md) -protokoll. SAML-integrering accepteras inte för AppSource-certifiering.

## <a name="multi-tenant-applications"></a>Program för flera innehavare

Ett *program med flera innehavare* är ett program som godkänner inloggningar från användare från ett företag eller en organisation som har Azure AD utan att kräva en separat instans, konfiguration eller distribution. AppSource rekommenderar att program implementerar flera innehavare *för att aktivera den kostnads* fria utvärderings versionen.

Följ dessa steg om du vill aktivera flera innehavare av ditt program:
1. Ange `Multi-Tenanted` egenskapen till `Yes` för program registreringens information i [Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps). Som standard konfigureras program som skapats i Azure Portal som *[en enda klient](#single-tenant-applications)*.
1. Uppdatera din kod för att skicka begär anden till `common` slut punkten. Det gör du genom att uppdatera slut punkten från `https://login.microsoftonline.com/{yourtenant}` till `https://login.microsoftonline.com/common*` .
1. För vissa plattformar, t. ex. ASP.NET, behöver du också uppdatera koden för att godkänna flera utfärdare.

Mer information om flera innehavare finns i [så här loggar du in en Azure Active Directory (Azure AD)-användare med program mönstret för flera innehavare](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Program med en enda klient

Ett *program med en enda klient* är ett program som bara accepterar inloggningar från användare av en DEFINIERAD Azure AD-instans. Externa användare (inklusive arbets-eller skol konton från andra organisationer eller personliga konton) kan logga in i ett program med en enda klient organisation efter att ha lagt till varje användare som gäst konto i Azure AD-instansen som programmet har registrerats. 

Du kan lägga till användare som gäst konton i Azure AD via [Azure AD B2B-samarbete](../external-identities/what-is-b2b.md) och du kan göra detta [program mässigt](../../active-directory-b2c/code-samples.md). När du använder B2B kan du skapa en självbetjänings portal som inte kräver någon inbjudan att logga in. Mer information finns i [självbetjänings portalen för integration av Azure AD B2B-samarbete](../external-identities/self-service-portal.md).

Program med en enda klient organisation kan aktivera funktionen *kontakta mig* , men om du vill aktivera den kostnads fria utvärderings versionen som AppSource rekommenderar aktiverar du flera innehavare i programmet i stället.

## <a name="appsource-trial-experiences"></a>AppSource utvärderings upplevelser

### <a name="free-trial-customer-led-trial-experience"></a>Kostnads fri utvärderings version (kund ledande utvärderings version)

Den här utvärderings versionen av kund indikatorn är den erfarenhet som AppSource rekommenderar eftersom den ger en enkel klickning till ditt program. I följande exempel visas hur den här upplevelsen ser ut:

1.  En användare hittar ditt program på AppSource-webbplatsen och väljer sedan alternativet för **kostnads fri utvärdering** .

    ![Visar en kostnads fri utvärderings version av kundens ledande utvärderings miljö](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png)

2.  AppSource omdirigerar användaren till en URL på webbplatsen. Webbplatsen startar processen för *enkel inloggning* automatiskt (vid sid inläsning).

    ![Visar hur användaren omdirigeras till en URL på webbplatsen](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png)

3.  Användaren omdirigeras till Microsoft-inloggnings sidan och användaren anger autentiseringsuppgifter för att logga in.

    ![Visar Microsoft-inloggnings Sidan](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png)

4. Användaren får ett medgivande för ditt program.

    ![Exempel: medgivande sida för ett program](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png)

5.  Inloggningen är klar och användaren omdirigeras tillbaka till webbplatsen.  Användaren startar den kostnads fria utvärderings versionen.

    ![Visar hur användarna ser när de omdirigeras tillbaka till din webbplats](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="contact-me-partner-led-trial-experience"></a>Kontakta mig (en utvärderings version av partner lampan)

Du kan använda utvärderings versionen av partner när en manuell eller långsiktig åtgärd måste utföras för att etablera användaren/företaget, till exempel att ditt program måste etablera virtuella datorer, databas instanser eller åtgärder som tar lång tid att slutföra. I det här fallet, efter att användaren har valt **utvärderings** knappen för begäran och fyller i ett formulär, skickar AppSource dig användarens kontakt information. När du får den här informationen kan du etablera miljön och skicka instruktionerna till användaren om hur du får åtkomst till utvärderings versionen:<br/><br/>

1. En användare hittar ditt program på AppSource webbplats och väljer sedan **kontakta mig**.

    ![Visar kontakta mig för en utvärderings version av en partner lampa](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png)

2. Användaren fyller i ett formulär med kontakt information.

    ![Visar ett exempel formulär med kontakt information](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png)

3. Du får användarens information, ställer in en utvärderings instans och skickar hyperlänken för att få åtkomst till ditt program till användaren.

    ![Visar plats hållare för användar information](./media/active-directory-devhowto-appsource-certified/usercontact.png)

4. Användaren får åtkomst till ditt program och slutför processen för enkel inloggning.

    ![Visar inloggnings skärmen för programmet](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png)

5. Användaren får ett medgivande för ditt program.

    ![Visar ett exempel på en godkännande sida för ett program](./media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png)

6. Inloggningen är klar och användaren omdirigeras tillbaka till webbplatsen. Användaren startar den kostnads fria utvärderings versionen.

    ![Visar hur användarna ser när de omdirigeras tillbaka till din webbplats](./media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png)

### <a name="more-information"></a>Mer information

Mer information om utvärderings versionen av AppSource finns i [den här videon](https://aka.ms/trialexperienceforwebapps). 

## <a name="get-support"></a>Få support

För Azure AD-integrering använder vi [Microsoft Q&A](/answers/topics/azure-active-directory.html) med communityn för att få support.

Vi rekommenderar starkt att du ställer dina frågor på [Microsoft Q&en](/answers/topics/azure-active-directory.html) första och söker efter befintliga ärenden för att se om någon har bett din fråga tidigare. Kontrol lera att dina frågor eller kommentarer är taggade med [`[azure-active-directory]`](/answers/topics/azure-active-directory.html) .

Använd följande kommentarer-avsnitt för att ge feedback och hjälpa oss att förfina och forma vårt innehåll.

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar program som stöder Azure AD-inloggningar finns i [autentiserings scenarier för Azure AD](authentication-flows-app-scenarios.md).
- Information om hur du listar ditt SaaS-program i AppSource finns i [AppSource partner information](https://appsource.microsoft.com/partners)