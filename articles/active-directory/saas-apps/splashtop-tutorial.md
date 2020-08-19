---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Splashtop | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splashtop.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 72c7a24f165d48d3ba2ea0dbcc2b41c818e3f1d7
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88524576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-splashtop"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Splashtop

I den här självstudien får du lära dig hur du integrerar Splashtop med Azure Active Directory (Azure AD). När du integrerar Splashtop med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Splashtop.
* Gör det möjligt för användarna att logga in automatiskt till Splashtop med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Splashtop för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Splashtop stöder **SP** -INITIERAd SSO

* När du har konfigurerat Splashtop kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-splashtop-from-the-gallery"></a>Lägga till Splashtop från galleriet

Om du vill konfigurera integreringen av Splashtop i Azure AD måste du lägga till Splashtop från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Splashtop** i sökrutan.
1. Välj **Splashtop** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-splashtop"></a>Konfigurera och testa enkel inloggning med Azure AD för Splashtop

Konfigurera och testa Azure AD SSO med Splashtop med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Splashtop.

Om du vill konfigurera och testa Azure AD SSO med Splashtop, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SPLASHTOP SSO](#configure-splashtop-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Splashtop test User](#create-splashtop-test-user)** -om du vill ha en motsvarighet till B. Simon i Splashtop som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Splashtop** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I rutan **Inloggnings-URL** anger du följande URL: `https://my.splashtop.com/login/sso`

1. Splashtop-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärm bild visar en lista över standardattribut, medan **NameIdentifier** mappas med **User. UserPrincipalName**. TicketManager-programmet förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **set-up-Splashtop** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Splashtop.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Splashtop**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-splashtop-sso"></a>Konfigurera Splashtop SSO

I det här avsnittet måste du använda för en ny SSO-metod från [Splashtop-webbportalen](https://my.splashtop.com/login).
1. I webb portalen för Splashtop går du till fliken **konto information**  /  **team** och bläddrar ned till avsnittet om att hitta **enkel inloggning** . Klicka sedan på **Använd för ny SSO-metod**.

    ![image](media/splashtop-tutorial/apply-for-new-SSO-method.png)

1. I fönstret tillämpa ger du ett **SSO-namn**. Till exempel New Azure och välj sedan **Azure** som IDP typ och infoga **inloggnings-URL** och **Azure AD-identifierare** som kopierats från Splashtop-programmet på Azure Portal.

    ![image](media/splashtop-tutorial/azure-sso-1.png)

1. För certifikat information högerklickar du på den certifikat fil som hämtats från Splashtop-programmet på Azure Portal, redigerar den med anteckningar och kopierar sedan innehållet och klistrar in det i fältet **Hämta certifikat (base64)** .

    ![bild ](media/splashtop-tutorial/cert-1.png) av bild ![ ](media/splashtop-tutorial/cert-2.png) ![](media/splashtop-tutorial/azure-sso-2.png)

1. Klart! Klicka på **Spara** och SPLASHTOP för SSO-validering kommer att kontakta dig för verifierings informationen och sedan aktivera SSO-metoden.

### <a name="create-splashtop-test-user"></a>Skapa Splashtop test användare

1. När SSO-metoden har Aktiver ATS kontrollerar du den nyligen skapade SSO-metoden för att aktivera den i avsnittet **enkel inloggning** .

    ![image](media/splashtop-tutorial/enable.png)

1. Bjud in test användaren, till exempel `B.Simon@contoso.com` till Splashtop-teamet med den nyligen skapade SSO-metoden.

    ![image](media/splashtop-tutorial/invite.png)

1. Du kan också ändra ett befintligt Splashtop-konto till ett SSO-konto, se [anvisningar](https://support-splashtopbusiness.splashtop.com/hc/en-us/articles/360038685691-How-to-associate-SSO-method-to-existing-team-admin-member-).

1. Klart! Du kan använda SSO-kontot för att logga in på Splashtop webb portal eller Splashtop Business app.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Splashtop på åtkomst panelen, bör du loggas in automatiskt på den Splashtop som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Splashtop med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Splashtop med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)