---
title: 'Självstudie: Azure Active Directory-integrering med hjälp program varan | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hjälp information.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.openlocfilehash: 89467dcca75c72c3a868478cc3e46b9cb474ed19
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88540107"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Självstudie: integrera hjälp med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar hjälp program hjälp med Azure Active Directory (Azure AD). När du integrerar hjälp program varan med Azure AD kan du:

* Kontroll i Azure AD som har till gång till hjälp information.
* Gör det möjligt för användarna att logga in automatiskt till hjälp med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumerationen för enkel inloggning (SSO) för hjälper – hjälp.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Hjälp program varan stöder **SP-och IDP** -initierad SSO och stöder **just-in-Time** User-etablering.

## <a name="adding-helper-helper-from-the-gallery"></a>Lägga till hjälp program hjälp från galleriet

Om du vill konfigurera hjälpen till Azure AD måste du lägga till hjälp information från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från Galleri skriver du** **Hjälp** för hjälp i rutan Sök.
1. Välj **hjälp program hjälp** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med hjälp av hjälp programmet för en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i hjälp avsnittet.

Om du vill konfigurera och testa Azure AD SSO med hjälp av hjälp programmet, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera hjälp programmet](#configure-helper-helper)** som hjälper dig att konfigurera SSO-inställningar på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa](#create-helper-helper-test-user)** supportavdelningen som är en motsvarighet till B. Simon i hjälpen som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan **Hjälp** för program integration och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du har en **metadata-fil för Service Provider** och vill konfigurera i **IDP** initierat läge utför du följande steg:

    >[!NOTE]
    >Gå till URL: en `https://sso.helperhelper.com/saml/<customer_id>` för att hämta metadata-filen för tjänste leverantören. Kontakta [support teamet](mailto:info@helperhelper.com) för supportavdelningen för `<customer_id>` .

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration.

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [support teamet](mailto:info@helperhelper.com) för supportavdelningen för att få det här värdet. Du kan också se de mönster som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal. l.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och sparar den i anteckningar.

   ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

1. Kopiera lämpliga URL: er baserat på ditt krav i avsnittet **Konfigurera hjälp** avsnitt.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Konfigurera hjälp information

Om du vill konfigurera enkel inloggning på **Hjälp** sidans hjälp sida måste du skicka **URL: en för appens Federations-metadata** till [Hjälp programmet support team](mailto:info@helperhelper.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till hjälp information.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Hjälp**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-helper-helper-test-user"></a>Skapa test användare för hjälp

I det här avsnittet skapas en användare som kallas Britta Simon i hjälpen. Hjälp program varan stöder just-in-Time-dataetablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i hjälpen, skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

När du väljer Hjälp panelen för hjälp i åtkomst panelen, bör du loggas in automatiskt till hjälp program hjälp för vilka du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)