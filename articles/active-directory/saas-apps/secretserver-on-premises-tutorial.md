---
title: 'Självstudie: Azure Active Directory integration med Secret Server (On-Premises) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Secret Server (On-Premises).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101647029"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Självstudie: integrera Secret Server (On-Premises) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Secret Server (On-Premises) med Azure Active Directory (Azure AD). När du integrerar Secret Server (On-Premises) med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Secret Server (On-Premises).
* Gör det möjligt för användarna att logga in automatiskt för att Secret Server (On-Premises) med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Secret Server (On-Premises) enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Secret Server (On-Premises) stöder **SP-och IDP** -INITIERAd SSO

## <a name="add-secret-server-on-premises-from-the-gallery"></a>Lägg till Secret Server (On-Premises) från galleriet

Om du vill konfigurera integreringen av Secret Server (On-Premises) i Azure AD måste du lägga till Secret Server (On-Premises) från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Secret Server (on-premises)** i sökrutan.
1. Välj **Secret Server (on-premises)** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Konfigurera och testa Azure AD SSO för Secret Server (On-Premises)

Konfigurera och testa Azure AD SSO med Secret Server (On-Premises) med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Secret Server (On-Premises).

Utför följande steg för att konfigurera och testa Azure AD SSO med Secret Server (On-Premises):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Secret Server (on-PREMISES) SSO](#configure-secret-server-on-premises-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Secret Server (on-premises) testa användare](#create-secret-server-on-premises-test-user)** – om du vill ha en motsvarighet till B. Simon i Secret Server (on-premises) som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **Secret Server (on-premises)** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. Skriv webb adressen i text rutan **identifierare** : `https://secretserveronpremises.azure`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Det entitets-ID som visas ovan är bara ett exempel och du kan välja ett unikt värde som identifierar din hemliga Server instans i Azure AD. Du måste skicka det här entitets-ID: t till [Secret Server (on-premises) klient support teamet](https://thycotic.force.com/support/s/) och de konfigurerar det på deras sida. Läs [den här artikeln](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)om du vill ha mer information.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [Secret Server (on-premises) klient support teamet](https://thycotic.force.com/support/s/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på **Redigera** -ikonen för att öppna dialog rutan för **SAML-signerings certifikat** .

    ![Skärm bild som visar avsnittet "S A M L signerings certifikat&quot; med åtgärden &quot;certifikat (base64") markerat.)](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Välj **signerings alternativ** som **signerat SAML-svar och kontroll**.

    ![Signerings alternativ](./media/secretserver-on-premises-tutorial/signing-option.png)

1. I avsnittet **konfigurera Secret Server (on-premises)** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Secret Server (On-Premises).

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Secret Server (on-premises)**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-secret-server-on-premises-sso"></a>Konfigurera Secret Server (On-Premises) SSO

Om du vill konfigurera enkel inloggning på **Secret Server (on-premises)** sidan måste du skicka det nedladdade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Secret Server (on-premises) support teamet](https://thycotic.force.com/support/s/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-secret-server-on-premises-test-user"></a>Skapa Secret Server (On-Premises) test användare

I det här avsnittet skapar du en användare som kallas Britta Simon i Secret Server (On-Premises). Arbeta med [Secret Server (on-premises) support team](https://thycotic.force.com/support/s/) för att lägga till användare i Secret Server (on-premises)s plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Secret Server (On-Premises) inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Secret Server (On-Premises) inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så ska du loggas in automatiskt på den Secret Server (on-premises) som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Secret Server (On-Premises) i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den Secret Server (On-Premises) som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Secret Server (On-Premises) du använda sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).