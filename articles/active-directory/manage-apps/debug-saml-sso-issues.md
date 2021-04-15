---
title: Felsöka SAML-baserad enkel inloggning – Azure Active Directory
description: Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory.
services: active-directory
ms.author: iangithinji
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: aa86bbcec0dc6523ae701e5237f2c55d14db38e4
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374326"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserad enkel inloggning för program i Azure Active Directory

Lär dig hur du hittar [och](what-is-single-sign-on.md) åtgärdar problem med enkel inloggning för program i Azure Active Directory (Azure AD) som använder SAML-baserad enkel inloggning. 

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du [Mina appar tillägget för säker inloggning.](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension) Det här webbläsartillägget gör det enkelt att samla in information om SAML-begäran och SAML-svar som du behöver för att lösa problem med enkel inloggning. Om du inte kan installera tillägget visar den här artikeln hur du löser problem både med och utan tillägget installerat.

Använd någon av följande länkar Mina appar hämta och installera tillägget för säker inloggning.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testa SAML-baserad enkel inloggning

Testa SAML-baserad enkel inloggning mellan Azure AD och ett målprogram:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör eller annan administratör som har behörighet att hantera program.
1. På det vänstra bladet **väljer Azure Active Directory** och sedan **Företagsprogram**. 
1. I listan över företagsprogram väljer du det program som du vill testa enkel inloggning för och väljer sedan Enkel inloggning bland **alternativen till vänster.**
1. Öppna den SAML-baserade testupplevelsen för enkel inloggning genom att **gå till Testa enkel inloggning** (steg 5). Om knappen **Test** är nedtonad måste du fylla i och spara de attribut som krävs först i **avsnittet Grundläggande SAML-konfiguration.**
1. På **bladet Testa enkel inloggning använder** du företagets autentiseringsuppgifter för att logga in på målprogrammet. Du kan logga in som den aktuella användaren eller som en annan användare. Om du loggar in som en annan användare uppmanas du att autentisera dig.

    ![Skärmbild som visar testsidan för SAML SSO](./media/debug-saml-sso-issues/test-single-sign-on.png)

Om du har loggat in har testet slutförts. I det här fallet utfärdade Azure AD en SAML-svarstoken till programmet. Programmet använde SAML-token för att logga in dig.

Om du har ett fel på företagets inloggningssida eller på programmets sida kan du använda något av följande avsnitt för att lösa felet.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Lösa ett inloggningsfel på företagets inloggningssida

När du försöker logga in kan du se ett fel på företagets inloggningssida som liknar följande exempel.

![Exempel som visar ett fel på företagets inloggningssida](./media/debug-saml-sso-issues/error.png)

Om du vill felsöka det här felet behöver du felmeddelandet och SAML-begäran. Tillägget Mina appar Säker inloggning samlar automatiskt in den här informationen och visar lösningsvägledning om Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Så här löser du inloggningsfelet med Mina appar för säker inloggning installerat

1. När ett fel uppstår omdirigerar tillägget dig tillbaka till bladet för enkel inloggning med Azure AD **Test.**
1. På **bladet Testa enkel inloggning väljer** du Ladda ned **SAML-begäran.**
1. Du bör se specifika lösningsvägledning baserat på felet och värdena i SAML-begäran.
1. Du ser knappen **Åtgärda det för** att automatiskt uppdatera konfigurationen i Azure AD för att lösa problemet. Om du inte ser den här knappen beror inloggningsproblemet inte på en felaktig konfiguration i Azure AD.

Om det inte finns någon lösning på inloggningsfelet föreslår vi att du använder textrutan feedback för att informera oss.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Så här löser du felet utan att Mina appar tillägget för säker inloggning

1. Kopiera felmeddelandet i det nedre högra hörnet på sidan. Felmeddelandet innehåller:
    - Ett CorrelationID och en tidsstämpel. Dessa värden är viktiga när du skapar ett supportfall hos Microsoft eftersom de hjälper teknikerna att identifiera problemet och ge en korrekt lösning på problemet.
    - En instruktion som identifierar rotorsaken till problemet.
1. Gå tillbaka till Azure AD och leta upp **bladet Testa enkel** inloggning.
1. I textrutan ovan Hämta **lösningsvägledning** klistrar du in felmeddelandet.
1. Klicka **på Få lösningsvägledning** för att visa stegen för att lösa problemet. Vägledningen kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder tillägget Mina appar säker inloggning kan du behöva ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att hämta SAML-begäran och -svaret.
1. Kontrollera att målet i SAML-begäran motsvarar SAML Single Sign-On Service URL som hämtas från Azure AD.
1. Kontrollera att utfärdaren i SAML-begäran är samma identifierare som du har konfigurerat för programmet i Azure AD. Azure AD använder utfärdaren för att hitta ett program i din katalog.
1. Kontrollera att AssertionConsumerServiceURL är den plats där programmet förväntar sig att ta emot SAML-token från Azure AD. Du kan konfigurera det här värdet i Azure AD, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Lösa ett inloggningsfel på programsidan

Du kan logga in och sedan se ett fel på programmets sida. Detta inträffar när Azure AD utfärdade en token till programmet, men programmet inte accepterar svaret.

Lös felet genom att följa dessa steg eller titta på den här korta videon om hur du använder [Azure AD för att felsöka SAML SSO:](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)

1. Om programmet finns i Azure AD-galleriet kontrollerar du att du har följt alla steg för att integrera programmet med Azure AD. Information om hur du hittar integreringsanvisningar för ditt program finns i listan med självstudier för [SaaS-programintegrering.](../saas-apps/tutorial-list.md)
1. Hämta SAML-svaret.
    - Om tillägget Mina appar Säker inloggning är installerat på **bladet Testa** enkel inloggning klickar du på Hämta **SAML-svaret**.
    - Om tillägget inte är installerat använder du ett verktyg som [Fiddler för](https://www.telerik.com/fiddler) att hämta SAML-svaret.
1. Observera följande element i SAML-svarstoken:
   - Användarens unika identifierare för NameID-värde och format
   - Anspråk utfärdade i token
   - Certifikat som används för att signera token.

     Mer information om SAML-svaret finns i [SAML-protokoll för enkel inloggning.](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)

1. Nu när du har granskat SAML-svaret kan du läsa Fel på ett [programs](application-sign-in-problem-application-error.md) sida efter inloggningen för vägledning om hur du löser problemet. 
1. Om du fortfarande inte kan logga in kan du fråga programleverantören vad som saknas i SAML-svaret.

## <a name="next-steps"></a>Nästa steg

Nu när enkel inloggning fungerar i ditt program kan du automatisera användareablering och avetablering för [SaaS-program](../app-provisioning/user-provisioning.md) eller komma igång med [villkorlig åtkomst.](../conditional-access/app-based-conditional-access.md)
