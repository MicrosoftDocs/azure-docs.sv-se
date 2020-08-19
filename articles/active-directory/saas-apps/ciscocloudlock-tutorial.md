---
title: 'Självstudie: Azure Active Directory integrering med Cloud Security Fabric | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och molnets säkerhets infrastruktur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/18/2019
ms.author: jeedes
ms.openlocfilehash: 5c1a4ecea6acfa260cc576ffc25a67bb6c6503f0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551268"
---
# <a name="tutorial-integrate-the-cloud-security-fabric-with-azure-active-directory"></a>Självstudie: integrera infrastrukturen för moln säkerhet med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Cloud Security Fabric med Azure Active Directory (Azure AD). När du integrerar Cloud Security Fabric med Azure AD kan du:

* Kontroll i Azure AD som har till gång till molnets säkerhets infrastruktur.
* Gör det möjligt för användarna att logga in automatiskt till infrastrukturen för moln säkerhet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumerationen för enkel inloggning (SSO) för Cloud Security Fabric.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cloud Security Fabric stöder **SP** -INITIERAd SSO

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Lägga till moln säkerhets infrastruktur från galleriet

Om du vill konfigurera en integrering av infrastrukturen för moln säkerhet i Azure AD måste du lägga till moln säkerhets infrastruktur från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , anger **du moln säkerhets infrastruktur** i sökrutan.
1. Välj **moln säkerhets infrastruktur** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Cloud Security Fabric med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cloud Security Fabric.

Om du vill konfigurera och testa Azure AD SSO med Cloud Security Fabric, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Cloud Security Fabric SSO](#configure-the-cloud-security-fabric-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa test användaren för Cloud Security Fabric](#create-the-cloud-security-fabric-test-user)** – om du vill ha en motsvarighet till B. Simon i moln säkerhets infrastrukturen som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **Cloud Security Fabric** application integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I rutan **Inloggnings-URL** anger du en URL: 

      ```http
      https://platform.cloudlock.com
      https://app.cloudlock.com
      ```

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

      ```http
      https://platform.cloudlock.com/gate/saml/sso/<subdomain>
      https://app.cloudlock.com/gate/saml/sso/<subdomain>
      ```

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [support teamet för Cloud Security Fabric-klienten](mailto:support@cloudlock.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

5. Om du vill ändra **signerings** alternativen enligt ditt krav klickar du på knappen **Redigera** för att öppna dialog rutan certifikat för **SAML-signering** .

    ![SAML-svar](./media/ciscocloudlock-tutorial/saml.png)

    a. Välj alternativet **signera SAML-svar och assert** för **signering**.

    b. Välj alternativet **SHA-256** för **signeringsalgoritmen**.

    c. Klicka på **Spara**.  

6. I avsnittet **Konfigurera Cloud Security Fabric** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-the-cloud-security-fabric-sso"></a>Konfigurera Cloud Security Fabric SSO

Om du vill konfigurera enkel inloggning på **infrastruktur sidan för moln säkerhet** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för Cloud Security Fabric](mailto:support@cloudlock.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till molnets säkerhets infrastruktur.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer **du moln säkerhets infrastruktur**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-the-cloud-security-fabric-test-user"></a>Skapa test användaren för Cloud Security Fabric

I det här avsnittet skapar du en användare som heter B. Simon i Cloud Security Fabric. Arbeta med [Cloud Security Fabric support team](mailto:support@cloudlock.com) för att lägga till användare i Cloud Security Fabric-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Cloud Security Fabric i åtkomst panelen, bör du loggas in automatiskt på den moln säkerhets infrastruktur som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
