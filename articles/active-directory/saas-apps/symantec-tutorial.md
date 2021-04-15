---
title: 'Självstudie: Azure Active Directory integrering med Symantec Web Security Service (WSS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Symantec Web Security Service (WSS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: af7d126bfdc9ff8edf6b498747fab9c7f497a0f4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484854"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Självstudie: Azure Active Directory integrering med Symantec Web Security Service (WSS)

I den här självstudien får du lära dig hur du integrerar ditt Symantec Web Security Service-konto (WSS) med ditt Azure Active Directory-konto (Azure AD) så att WSS kan autentisera en slutanvändare som har etablerats i Azure AD med hjälp av SAML-autentisering och tillämpa principregler på användar- eller gruppnivå.

Integreringen av Symantec Web Security Service (WSS) med Azure AD medför följande fördelar:

- Hantera alla slutanvändare och grupper som används av WSS-kontot från Azure AD-portalen.

- Låt slutanvändarna autentisera sig själva i WSS med sina autentiseringsuppgifter för Azure AD.

- Tillämpa de principregler på användar- och gruppnivå som definierats i ditt WSS-konto.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Symantec Web Security Service -prenumeration (WSS) med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Symantec Web Security Service (WSS) stöder **IDP-initierad** enkel inloggning.

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Lägga till Symantec Web Security Service (WSS) från galleriet

För att konfigurera integreringen av Symantec Web Security Service (WSS) med Azure AD måste du lägga till Symantec Web Security Service (WSS) från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Symantec Web Security Service (WSS)** i sökrutan.
1. Välj **Symantec Web Security Service (WSS) i** resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-symantec-web-security-service-wss"></a>Konfigurera och testa Azure AD SSO för Symantec Web Security Service (WSS)

Konfigurera och testa enkel inloggning i Azure AD med Symantec Web Security Service (WSS) med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Symantec Web Security Service (WSS).

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Symantec Web Security Service (WSS):

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Symantec Web Security Service (WSS)](#configure-symantec-web-security-service-wss-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Symantec Web Security Service (WSS)-testanvändare](#create-symantec-web-security-service-wss-test-user)** – för att ha en motsvarighet till B.Simon i Symantec Web Security Service (WSS) som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal programintegreringssidan **för Symantec Web Security Service (WSS)** går du till avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialogrutan **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I **textrutan** Identifierare skriver du url:en: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. I **textrutan Svars-URL** skriver du url:en: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Kontakta [Symantec Web Security Service-kundsupporten](https://www.symantec.com/contact-us) om värdena för **Identifierare** och **Svars-URL** inte fungerar. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal med namnet B.Simon.

1. I den vänstra rutan i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Symantec Web Security Service (WSS).

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Symantec Web Security Service (WSS).**
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst" vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-symantec-web-security-service-wss-sso"></a>Konfigurera enkel inloggning för Symantec Web Security Service (WSS)

Information om hur du konfigurerar enkel inloggning på Symantec Web Security Service-sidan finns i onlinedokumentationen för WSS. Den nedladdade **XML:en med federationsmetadata** måste importeras till WSS-portalen. Kontakta [Symantec Web Security Service-supportteamet (WSS)](https://www.symantec.com/contact-us) om du behöver hjälp med konfigurationen på WSS-portalen.

### <a name="create-symantec-web-security-service-wss-test-user"></a>Skapa Symantec Web Security Service-testanvändare (WSS)

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Symantec Web Security Service (WSS). Det associerade slutanvändarnamnet kan skapas manuellt på WSS-portalen eller så kan du vänta tills användarna/grupperna som har etablerats i Azure AD synkroniseras med WSS-portalen efter några minuter (cirka 15 minuter). Användare måste skapas och aktiveras innan du använder enkel inloggning. Slutanvändardatorns offentliga IP-adress, som kommer att användas för att navigera på webbplatser, måste också etableras på Symantec Web Security Service-portalen (WSS).

> [!NOTE]
> Klicka [här](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) för att hämta datorns offentliga IP-adress.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ.

* Klicka på Testa det här programmet i Azure Portal så bör du automatiskt loggas in på Symantec Web Security Service (WSS) som du har ställt in enkel inloggning för.

* Du kan använda Microsoft Mina appar. När du klickar på Symantec Web Security Service -panelen (WSS) i Mina appar bör du automatiskt loggas in på Symantec Web Security Service (WSS) som du har ställt in enkel inloggning för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Symantec Web Security Service (WSS) kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
