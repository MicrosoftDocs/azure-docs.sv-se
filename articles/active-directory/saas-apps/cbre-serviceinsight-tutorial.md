---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med CBRE ServiceInsight | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CBRE ServiceInsight.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.openlocfilehash: 328f134e1641d1ae7617df333786a2841fa513ac
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530179"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cbre-serviceinsight"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CBRE ServiceInsight

I den här självstudien får du lära dig att integrera CBRE-ServiceInsight med Azure Active Directory (Azure AD). När du integrerar CBRE-ServiceInsight med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CBRE-ServiceInsight.
* Gör det möjligt för användarna att logga in automatiskt till CBRE ServiceInsight med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CBRE ServiceInsight-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CBRE ServiceInsight stöder **SP**-initierad enkel inloggning
* CBRE ServiceInsight stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-cbre-serviceinsight-from-the-gallery"></a>Lägga till CBRE ServiceInsight från galleriet

För att konfigurera integreringen av CBRE ServiceInsight i Azure AD måste du lägga till CBRE ServiceInsight från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **CBRE ServiceInsight** i sökrutan.
1. Välj **CBRE ServiceInsight** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cbre-serviceinsight"></a>Konfigurera och testa enkel inloggning med Azure AD för CBRE-ServiceInsight

Konfigurera och testa Azure AD SSO med CBRE ServiceInsight med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CBRE ServiceInsight.

Om du vill konfigurera och testa Azure AD SSO med CBRE ServiceInsight slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CBRE SERVICEINSIGHT SSO](#configure-cbre-serviceinsight-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa CBRE ServiceInsight test User](#create-cbre-serviceinsight-test-user)** – om du vill ha en motsvarighet till B. Simon i CBRE ServiceInsight som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **CBRE ServiceInsight** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://adfs4.mainstreamsasp.com/adfs/ls/`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [CBRE ServiceInsight-kundsupporten](mailto:SISupport@cbre.com) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CBRE ServiceInsight.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **CBRE ServiceInsight** i listan med program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cbre-serviceinsight-sso"></a>Konfigurera CBRE ServiceInsight SSO

För att konfigurera enkel inloggning på **CBRE ServiceInsight**-sidan måste du skicka **URL:en för appfederationsmetadata** till [CBRE ServiceInsight-supportteamet](mailto:SISupport@cbre.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cbre-serviceinsight-test-user"></a>Skapa CBRE ServiceInsight-testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i CBRE ServiceInsight. CBRE ServiceInsight stöder JIT-etablering (Just-In-Time), som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i CBRE ServiceInsight skapas en ny när du försöker komma åt CBRE ServiceInsight.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för CBRE ServiceInsight på åtkomstpanelen bör du loggas in automatiskt i CBRE ServiceInsight-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CBRE ServiceInsight med Azure AD](https://aad.portal.azure.com/)