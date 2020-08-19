---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Biodlings Azure AD SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Biodlings Azure AD SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.openlocfilehash: ac881d19ee4ba7efc2dcbab93775f59cfec5effa
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549299"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beekeeper-azure-ad-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Biodlings Azure AD SSO

I den här självstudien får du lära dig att integrera Biodlings Azure AD SSO med Azure Active Directory (Azure AD). När du integrerar Biodlings Azure AD SSO med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Biodlings Azure AD SSO.
* Gör det möjligt för användarna att logga in automatiskt till Biodlings Azure AD SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Biodlings prenumeration med enkel inloggning (SSO) för Azure AD SSO.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Biodlings Azure AD SSO stöder **SP-och IDP** -INITIERAd SSO
* Biodlings Azure AD SSO stöder **just-in-Time** User-etablering
* När du har konfigurerat Biodlings Azure AD SSO kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-beekeeper-azure-ad-sso-from-the-gallery"></a>Lägga till Biodlings Azure AD SSO från galleriet

Om du vill konfigurera integrering av Biodlings Azure AD SSO i Azure AD måste du lägga till Biodlings Azure AD SSO från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Biodlings Azure AD SSO** i sökrutan.
1. Välj **Biodlings Azure AD SSO** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beekeeper-azure-ad-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för Biodlings Azure AD SSO

Konfigurera och testa Azure AD SSO med Biodlings Azure AD SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Biodlings Azure AD SSO.

Om du vill konfigurera och testa Azure AD SSO med Biodlings Azure AD SSO, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Biodlings Azure AD SSO](#configure-beekeeper-azure-ad-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa användare av gruppen Azure AD SSO](#create-beekeeper-azure-ad-sso-test-user)** för att få en motsvarighet till B. Simon i biodlingen Azure AD SSO som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **Biodlings sidan för Azure AD SSO** -programintegration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en **metadata-fil för Service Provider** och vill konfigurera i **IDP** initierat läge, utför du följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration.

    ![image](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<your_company>.beekeeper.io/login`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Biodlings gruppen Azure AD SSO client support](mailto:support@beekeeper.io) för att få det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I Biodlings Azure AD SSO-programmet förväntas SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar Biodlings Azure AD SSO-programmet fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ------------ | --------- |
    | förnamn | user.givenname |
    | efternamn | user.surname |
    | e-post | user.mail |
    | användarnamn | User. PrincipalName |
    | position | user.jobtitle |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Biodlings Azure AD SSO** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Biodlings Azure AD SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Biodlings Azure AD SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-beekeeper-azure-ad-sso"></a>Konfigurera Biodlings Azure AD SSO

Om du vill konfigurera enkel inloggning på **Biodlings Azure AD SSO** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [Biodlings Azure AD SSO support team](mailto:support@beekeeper.io). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-beekeeper-azure-ad-sso-test-user"></a>Skapa en användare med Biodlings Azure AD SSO-test

I det här avsnittet skapas en användare som kallas Britta Simon i Biodlings Azure AD SSO. Biodlings Azure AD SSO stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i biodlingen Azure AD SSO, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Biodlings Azure AD SSO på åtkomst panelen, bör du loggas in automatiskt till Biodlings Azure AD SSO som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Biodlings Azure AD SSO med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)