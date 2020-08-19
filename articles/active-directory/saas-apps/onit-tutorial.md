---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Onit | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Onit.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.openlocfilehash: 0a04b893af41f15d0757b781716909535f43239e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Onit

I den här självstudien får du lära dig hur du integrerar Onit med Azure Active Directory (Azure AD). När du integrerar Onit med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Onit.
* Gör det möjligt för användarna att logga in automatiskt till Onit med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Onit för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Onit stöder **SP** -INITIERAd SSO

## <a name="adding-onit-from-the-gallery"></a>Lägga till Onit från galleriet

Om du vill konfigurera integreringen av Onit i Azure AD måste du lägga till Onit från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Onit** i sökrutan.
1. Välj **Onit** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Konfigurera och testa enkel inloggning med Azure AD för Onit

Konfigurera och testa Azure AD SSO med Onit med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Onit.

Om du vill konfigurera och testa Azure AD SSO med Onit, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ONIT SSO](#configure-onit-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Onit test User](#create-onit-test-user)** -om du vill ha en motsvarighet till B. Simon i Onit som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Onit** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.onit.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<sub-domain>.onit.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Onit client support team](https://www.onit.com/support) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. I avsnittet **SAML-signeringscertifikat** , kopierar du **värdet tumavtryck** och sparar det på din dator.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I avsnittet **Konfigurera Onit** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Onit.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Onit**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-onit-sso"></a>Konfigurera Onit SSO

1. Logga in på din Onit-företags webbplats som administratör i ett annat webbläsarfönster.

2. Klicka på **Administration**i menyn högst upp.
   
    ![Administration](./media/onit-tutorial/IC791174.png "Administration")

3. Klicka på **Redigera Corporation**.
   
    ![Redigera företag](./media/onit-tutorial/IC791175.png "Redigera företag")
   
4. Klicka på **Säkerhets**-fliken.
    
    ![Redigera företags information](./media/onit-tutorial/IC791176.png "Redigera företags information")

5. Utför följande steg på fliken **säkerhet** :

    ![Enkel inloggning](./media/onit-tutorial/IC791177.png "för Aha!")

    a. Som **strategi för autentisering**väljer du **enkel inloggning och lösen ord**.
    
    b. I text rutan för **IDP mål-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    c. I text rutan för **IDP-utloggnings webb adress** klistrar du in värdet för  **URL för utloggning**som du har kopierat från Azure Portal.

    d. I text rutan **IDP cert finger avtryck (SHA1)** klistrar du in  **tumavtrycket** för certifikat, som du har kopierat från Azure Portal.

### <a name="create-onit-test-user"></a>Skapa Onit test användare

För att Azure AD-användare ska kunna logga in på Onit måste de tillhandahållas i Onit. När det gäller Onit är etableringen en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på din **Onit** -företags webbplats som administratör.

2. Klicka på **Lägg till användare**.

    ![Administration](./media/onit-tutorial/IC791180.png "Administration")

3. Utför följande steg på dialog sidan **Lägg till användare** :

    ![Lägg till användare](./media/onit-tutorial/IC791181.png "Lägg till användare")

    a. Skriv **namnet** och **e-postadressen** för ett giltigt Azure AD-konto som du vill etablera i de relaterade text rutorna.

    b. Klicka på **Skapa**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Onit på åtkomst panelen, bör du loggas in automatiskt på den Onit som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Onit med Azure AD](https://aad.portal.azure.com/)