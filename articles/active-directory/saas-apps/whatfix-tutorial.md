---
title: 'Självstudie: Azure Active Directory integrering med Whatfix | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Whatfix.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/17/2019
ms.author: jeedes
ms.openlocfilehash: 273151cf08bd149da427e9631764f4dae2759b12
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523353"
---
# <a name="tutorial-integrate-whatfix-with-azure-active-directory"></a>Självstudie: integrera Whatfix med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Whatfix med Azure Active Directory (Azure AD). När du integrerar Whatfix med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Whatfix.
* Gör det möjligt för användarna att logga in automatiskt till Whatfix med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Whatfix för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Whatfix stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-whatfix-from-the-gallery"></a>Lägga till Whatfix från galleriet

Om du vill konfigurera integreringen av Whatfix i Azure AD måste du lägga till Whatfix från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Whatfix** i sökrutan.
1. Välj **Whatfix** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Whatfix med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Whatfix.

Om du vill konfigurera och testa Azure AD SSO med Whatfix, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera WHATFIX SSO](#configure-whatfix-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Whatfix test User](#create-whatfix-test-user)** – om du vill ha en motsvarighet till Britta Simon i Whatfix som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.


### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Whatfix** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    1. Klicka på **Ange ytterligare URL:er**.
    1. I text rutan **relä tillstånd** anger du den angivna URL: en för vidarebefordrande tillstånd för kunden.
    
    > [!NOTE]
    > Kontakta [Whatfix client support team](https://support.whatfix.com) för att hämta URL-värde för vidarebefordrings tillstånd.

1. Klicka på **Ange ytterligare URL:er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://whatfix.com`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera URL för **metadata för app Federation**.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-whatfix-sso"></a>Konfigurera Whatfix SSO

Om du vill konfigurera enkel inloggning på **Whatfix** sida måste du skicka **URL: en för appens Federations-metadata** till [support teamet för Whatfix](https://support.whatfix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Whatfix.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Whatfix**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-whatfix-test-user"></a>Skapa Whatfix test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Whatfix. Arbeta med [Whatfix support team](https://support.whatfix.com) för att lägga till användare i Whatfix-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Whatfix på åtkomst panelen, bör du loggas in automatiskt på den Whatfix som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
