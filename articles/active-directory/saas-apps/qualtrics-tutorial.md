---
title: 'Självstudie: Azure Active Directory integration med SAP Qualtrics | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 170997099f1194bbc75d6d61a7c29fc1d18b462a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552228"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SAP Qualtrics

I den här självstudien får du lära dig hur du integrerar SAP Qualtrics med Azure Active Directory (Azure AD). När du integrerar SAP Qualtrics med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Qualtrics.
* Gör det möjligt för användarna att logga in automatiskt på SAP-Qualtrics med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En SAP Qualtrics-prenumeration har Aktiver ATS för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAP Qualtrics stöder **SP** -och **IDP** -initierad SSO.
* SAP Qualtrics stöder **just-in-Time** User-etablering.
* När du har konfigurerat SAP-Qualtrics kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>Lägg till SAP-Qualtrics från galleriet

Om du vill konfigurera integreringen av SAP-Qualtrics i Azure AD måste du lägga till SAP Qualtrics från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **SAP Qualtrics** i sökrutan.
1. Välj **SAP-Qualtrics** från resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Konfigurera och testa enkel inloggning med Azure AD för SAP Qualtrics

Konfigurera och testa Azure AD SSO med SAP Qualtrics genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i SAP Qualtrics.

Om du vill konfigurera och testa Azure AD SSO med SAP Qualtrics slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera SAP QUALTRICS SSO](#configure-sap-qualtrics-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. [Skapa en SAP Qualtrics-test](#create-sap-qualtrics-test-user) för att få en motsvarighet till B. Simon i SAP Qualtrics som är länkad till användarens Azure AD-representation.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **SAP Qualtrics** och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML Page med Penn ikonen markerad](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:
    
    a. I text rutan **identifierare** anger du en URL som använder följande mönster:

    `https://< DATACENTER >.qualtrics.com`
   
    b. I text rutan **svars-URL** anger du en URL som använder följande mönster:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. I text rutan **vidarebefordra tillstånd** anger du en URL som använder följande mönster:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Välj **Ange ytterligare URL: er**och utför följande steg om du vill konfigurera programmet i **SP** -initierat läge:

    I text rutan **inloggnings-URL** anger du en URL som använder följande mönster:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL: en, identifierare, svars-URL och relä status. Kontakta [Qualtrics-klientens support team](https://www.qualtrics.com/support/)för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du kopierings ikonen för att kopiera URL för **metadata för app Federation** och spara den på din dator.

    ![Skärm bild av SAML-signerings certifikat med kopiera-ikonen markerad](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Qualtrics.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **SAP Qualtrics**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurera SAP Qualtrics SSO

Om du vill konfigurera enkel inloggning på SAP Qualtrics-sidan skickar du den kopierade **metadata-URL: en för appens Federation** från Azure Portal till [support teamet för SAP Qualtrics](https://www.qualtrics.com/support/). Support teamet ser till att SAML SSO-anslutningen är korrekt inställd på båda sidor.

### <a name="create-sap-qualtrics-test-user"></a>Skapa SAP Qualtrics-test användare

SAP Qualtrics stöder just-in-Time-etablering, som är aktiverat som standard. Det finns ingen ytterligare åtgärd att vidta. Om en användare inte redan finns i SAP-Qualtrics skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer panelen SAP-Qualtrics i åtkomst panelen loggas du automatiskt in på SAP-Qualtrics som du ställer in SSO för. Mer information finns i [Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Qualtrics med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda SAP-Qualtrics med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

