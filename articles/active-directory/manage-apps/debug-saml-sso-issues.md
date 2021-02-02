---
title: Felsöka SAML-baserad enkel inloggning Azure Active Directory
description: Felsöka SAML-baserad enkel inloggning till program i Azure Active Directory.
services: active-directory
ms.author: kenwith
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: luleon, hirsin, paulgarn
ms.openlocfilehash: 4a5fbef3380db8c9b357c83b4cb8240a41a0012c
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259295"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserad enkel inloggning för program i Azure Active Directory

Lär dig hitta och åtgärda problem med [enkel inloggning](what-is-single-sign-on.md) för program i Azure Active Directory (Azure AD) som använder SAML-baserad enkel inloggning. 

## <a name="before-you-begin"></a>Innan du börjar

Vi rekommenderar att du installerar [tillägget Mina appar säker inloggning](../user-help/my-apps-portal-end-user-troubleshoot.md#im-having-trouble-installing-the-my-apps-secure-sign-in-extension). Det här webb läsar tillägget gör det enkelt att samla in SAML-begäran och SAML-svars information som du behöver för att lösa problem med enkel inloggning. Om du inte kan installera tillägget visar den här artikeln hur du löser problem både med och utan tillägget installerat.

Använd någon av följande länkar för att ladda ned och installera säkra inloggnings tillägg för Mina appar.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

## <a name="test-saml-based-single-sign-on"></a>Testa SAML-baserad enkel inloggning

Så här testar du SAML-baserad enkel inloggning mellan Azure AD och ett mål program:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör eller annan administratör som har behörighet att hantera program.
1. Välj **Azure Active Directory** i det vänstra bladet och välj sedan **företags program**. 
1. I listan över företags program väljer du det program som du vill testa enkel inloggning för och sedan från alternativen till vänster väljer du **enkel inloggning**.
1. Om du vill öppna test miljön för SAML-baserad enkel inloggning går du till **Testa enkel inloggning** (steg 5). Om **test** knappen är nedtonad, måste du fylla i och spara de nödvändiga attributen först i avsnittet **grundläggande SAML-konfiguration** .
1. På bladet **Testa enkel inloggning** använder du företagets autentiseringsuppgifter för att logga in på mål programmet. Du kan logga in som aktuell användare eller som en annan användare. Om du loggar in som en annan användare kommer du att uppmanas att autentisera.

    ![Skärm bild som visar sidan testa SAML SSO](./media/debug-saml-sso-issues/test-single-sign-on.png)

Testet har slutförts om du har loggat in. I det här fallet utfärdade Azure AD en SAML-svars-token till programmet. Programmet använde SAML-token för att logga in dig.

Om du har ett fel på sidan för företagets inloggning eller på programmets sida använder du något av följande avsnitt för att lösa problemet.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>Lösa ett inloggnings fel på företagets inloggnings sida

När du försöker logga in kan du se ett fel på din inloggnings sida för företaget som liknar följande exempel.

![Exempel på hur ett fel visas på företagets inloggnings sida](./media/debug-saml-sso-issues/error.png)

Om du vill felsöka det här felet behöver du fel meddelandet och SAML-begäran. Det säkra inloggnings tillägget Mina appar samlar in den här informationen automatiskt och visar lösnings vägledningen för Azure AD.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>Så här löser du det här inloggnings felet med det säkra inloggnings tillägget Mina appar installerade

1. När ett fel uppstår omdirigerar tillägget tillbaka till bladet Azure AD **-test för enkel inloggning** .
1. På bladet **Testa enkel inloggning** väljer du **Hämta SAML-begäran**.
1. Du bör se detaljerad lösnings vägledning baserat på felet och värdena i SAML-begäran.
1. Du kommer att se en **Åtgärds** knapp för att automatiskt uppdatera konfigurationen i Azure AD för att lösa problemet. Om du inte ser den här knappen är inloggnings problemet inte på grund av en felaktig konfiguration i Azure AD.

Om det inte finns någon lösning för inloggnings felet föreslår vi att du använder text rutan feedback för att informera oss.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>Så här löser du felet utan att installera tillägget Mina appar säker inloggning

1. Kopiera fel meddelandet i det nedre högra hörnet på sidan. Fel meddelandet innehåller:
    - Ett CorrelationID och en tidsstämpel. Dessa värden är viktiga när du skapar ett support ärende med Microsoft eftersom de hjälper teknikerna att identifiera ditt problem och ge en korrekt lösning på problemet.
    - En instruktion som identifierar rotor saken till problemet.
1. Gå tillbaka till Azure AD och hitta bladet för **enkel inloggning på test** .
1. I text rutan ovan **Hämta lösnings vägledning** klistrar du in fel meddelandet.
1. Klicka på **Hämta lösnings vägledning** för att visa steg för att lösa problemet. Rikt linjerna kan kräva information från SAML-begäran eller SAML-svar. Om du inte använder säker inloggnings tillägg för Mina appar kan du behöva ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att hämta SAML-begäran och-svar.
1. Kontrol lera att målet i SAML-begäran motsvarar den URL för tjänsten SAML Single Sign-On som hämtades från Azure AD.
1. Kontrol lera att utfärdaren i SAML-begäran är samma identifierare som du har konfigurerat för programmet i Azure AD. Azure AD använder utfärdaren för att hitta ett program i din katalog.
1. Kontrol lera att AssertionConsumerServiceURL är den plats där programmet förväntar sig att ta emot SAML-token från Azure AD. Du kan konfigurera det här värdet i Azure AD, men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="resolve-a-sign-in-error-on-the-application-page"></a>Lösa ett inloggnings fel på program Sidan

Du kan logga in och sedan se ett fel på programmets sida. Detta inträffar när Azure AD utfärdade en token till programmet, men programmet inte accepterar svaret.

Lös problemet genom att följa dessa steg eller titta på den här [korta videon om hur du använder Azure AD för att FELSÖKA SAML SSO](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8):

1. Om programmet finns i Azure AD-galleriet kontrollerar du att du har följt alla steg för att integrera programmet med Azure AD. Information om hur du hittar integrerings anvisningarna för ditt program finns i [listan över självstudier för SaaS-programintegration](../saas-apps/tutorial-list.md).
1. Hämta SAML-svaret.
    - Om tillägget Mina appar säker inloggning är installerat går du till bladet **Testa enkel inloggning** och klickar på **Hämta SAML-svaret**.
    - Om tillägget inte är installerat använder du ett verktyg som [Fiddler](https://www.telerik.com/fiddler) för att hämta SAML-svaret.
1. Lägg märke till dessa element i SAML-svars-token:
   - Användarens unika identifierare för NameID värde och format
   - Anspråk som utfärdats i token
   - Certifikat som används för att signera token.

     Mer information om SAML-svar finns i [SAML-protokoll för enkel inloggning](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

1. Nu när du har granskat SAML-svaret, se [fel på ett programs sida efter att du har loggat in](application-sign-in-problem-application-error.md) för vägledning om hur du löser problemet. 
1. Om du fortfarande inte kan logga in korrekt kan du be program leverantören vad som saknas i SAML-svaret.

## <a name="next-steps"></a>Nästa steg

Nu när enkel inloggning fungerar i ditt program kan du [Automatisera användar etablering och avetablering för SaaS-program](../app-provisioning/user-provisioning.md) eller [komma igång med villkorlig åtkomst](../conditional-access/app-based-conditional-access.md).
