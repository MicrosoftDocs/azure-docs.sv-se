---
title: 'Självstudie: Azure Active Directory integrering med verktyg för att tänka på verktyg | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och verktyg för att tänka på verktyg.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 10773a2a379291fa861dbb2adfdda2ba3f63117f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550647"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>Självstudie: Azure Active Directory integrering med verktyg för att tänka på verktyg

I den här självstudien får du lära dig hur du integrerar tools Toolkit med Azure Active Directory (Azure AD).

Med den här integreringen kan du:

* Kontroll i Azure AD som har till gång till verktyg för att tänka på verktyg.
* Gör det möjligt för användarna att logga in automatiskt på verktyg Toolkit (enkel inloggning) med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande om du vill konfigurera Azure AD-integrering med verktyg för att tänka på verktyg:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En verktyg Toolkit-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Verktyget för att tänka på verktyg stöder SP-initierad SSO.
* Verktyget för att tänka på verktyg stöder just-in-Time-etablering av användare.
* När du har konfigurerat verktyg för att skapa verktyg kan du framtvinga kontroll av sessioner. Den här kontrollen skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>Lägg till verktyg Toolkit från galleriet

Om du vill konfigurera en integrering av verktyg för att skapa en integrering i Azure AD, måste du lägga till verktyg för att skapa verktyg från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I navigerings fönstret längst till vänster väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , ange **verktyg Toolkit** i rutan Sök.
1. Välj **verktyg** för att tänka på verktyg från Sök resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med hjälp av en test användare som heter **B. Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och en relaterad användare i verktyg för att tänka verktyg.

Om du vill konfigurera och testa enkel inloggning med en Azure AD-inloggning med verktyg verktyget, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera åtanke tools Toolkit SSO](#configure-mind-tools-toolkit-sso)** för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en](#create-a-mind-tools-toolkit-test-user)** plan för verktyg som du kan använda för att få en motsvarighet till B. Simon i åtanke tools Toolkit. Den här motsvarigheten är länkad till användarens Azure AD-representation.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet konfigurerar du enkel inloggning med Azure AD med verktyg för att tänka på följande sätt:

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan **verktyg** för program integrering och väljer **enkel inloggning**.

    ![Avsnittet hantera med enkel inloggning markerat](common/select-sso.png)

1. I dialog rutan **Välj metod för enkel inloggning** väljer du **SAML/WS-utfodras** för att aktivera enkel inloggning.

    ![Dialog rutan Välj metod för enkel inloggning med SAML markerat](common/select-saml-option.png)

1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Sidan konfigurera enkel inloggning med SAML, med Penn ikonen för grundläggande SAML-konfiguration markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** i rutan **inloggnings-URL** anger du en URL som har mönstret `https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` .

    > [!NOTE]
    > **Inloggnings-URL** -värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL: en. Kontakta The for [Tools Toolkit-klientens support team](mailto:support@goodpractice.com) för att hämta värdet.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat** . Till höger om **XML för federationsmetadata**väljer du **Ladda ned** för att ladda ned XML-texten och spara den på din dator. XML-innehållet beror på vilka alternativ du väljer.

    ![Avsnittet SAML-signeringscertifikat med hämtning har marker ATS bredvid XML för federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera verktyg Toolkit** kopierar du det du behöver för följande URL: er.

    * **Inloggnings-URL**

    * **Azure AD-identifierare**

    * **Utloggnings-URL**

    ![Avsnittet Konfigurera verktyg Toolkit med konfigurations-URL: er markerade](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare som heter B. Simon i Azure Portal:

1. På den vänstra sidan av Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. I fältet **namn** anger du **B. Simon**.  
   1. I fältet **användar namn** anger du **B. Simon@**_companydomain_**.** _tillägg_. Till exempel B.Simon@contoso.com.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till verktyg för att tänka på verktyg.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **verktyg Toolkit**.
1. På sidan Översikt för appen går du till avsnittet **Hantera** och väljer **användare och grupper**.

   ![Avsnittet hantera, där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

   ![Fönstret användare och grupper, där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare. Välj sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** en lämplig roll för användaren i listan. Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-mind-tools-toolkit-sso"></a>Konfigurera åtanke tools Toolkit SSO

Om du vill konfigurera enkel inloggning på verktyg-sidan för **verktyg** , skickar du den hämtade **XML-** texten för federationsmetadata och de tidigare kopierade URL: erna till [support teamet](mailto:support@goodpractice.com)för verktyget. De konfigurerar den här inställningen att ha SAML SSO-anslutningen korrekt inställd på båda sidorna.

### <a name="create-a-mind-tools-toolkit-test-user"></a>Skapa en test användare av verktyg Toolkit

I det här avsnittet skapar du en användare som heter B. Simon i åtanke tools Toolkit.

Verktyget för att tänka på verktyg stöder just-in-Time-etablering, som är aktiverat som standard. Det finns ingen åtgärd att ta med i det här avsnittet. Om en användare inte redan finns i verktyg för att tänka på verktyg, skapas en ny när du försöker komma åt verktyg verktyget för att komma åt verktyg.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du väljer verktygs panelen för verktyg i My Apps-portalen loggas du automatiskt in på verktyg-verktygslådan som du ställer in SSO för. Mer information om mina apps-portalen finns i [Introduktion till portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova verktyg Toolkit med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda verktyg för att tänka på verktyg med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
