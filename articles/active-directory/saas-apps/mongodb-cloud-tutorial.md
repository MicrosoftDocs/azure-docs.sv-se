---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med MongoDB-molnet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och MongoDB-molnet.
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
ms.openlocfilehash: 6a59df062adf9b5c2e511db29cb8601d4a4df70e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554574"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med MongoDB Cloud

I den här självstudien får du lära dig att integrera MongoDB Cloud med Azure Active Directory (Azure AD). När du integrerar MongoDB Cloud med Azure AD kan du:

* Kontroll i Azure AD som har till gång till MongoDB-molnet, MongoDB Atlas, MongoDB community, MongoDB University och MongoDB support.
* Gör det möjligt för användarna att logga in automatiskt till MongoDB-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En MongoDB moln organisation som är aktive rad för enkel inloggning (SSO), du kan registrera dig för ett [kostnads fritt kluster](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* MongoDB Cloud stöder **SP** -och **IDP** -initierad SSO.
* MongoDB Cloud stöder **just-in-Time** User-etablering.
* När du har konfigurerat MongoDB-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Lägg till MongoDB-moln från galleriet

Om du vill konfigurera integreringen av MongoDB-molnet i Azure AD måste du lägga till MongoDB Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du **Azure Active Directory**.
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **MongoDB Cloud** i sökrutan.
1. Välj **MongoDB Cloud** från resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Konfigurera och testa enkel inloggning med Azure AD för MongoDB-molnet

Konfigurera och testa Azure AD SSO med MongoDB Cloud genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i MongoDB-molnet.

Om du vill konfigurera och testa Azure AD SSO med MongoDB-molnet slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera MongoDB Cloud SSO](#configure-mongodb-cloud-sso) för att konfigurera inställningar för enkel inloggning på program sidan.
    1. [Skapa en MongoDB för moln test](#create-a-mongodb-cloud-test-user) för att få en motsvarighet till B. Simon i MongoDB-molnet som är länkat till användarens Azure AD-representation.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **MongoDB Cloud** Application Integration letar du upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML Page med Penn ikonen markerad](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL som använder följande mönster: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. I text rutan **svars-URL** anger du en URL som använder följande mönster: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Välj **Ange ytterligare URL: er**och utför följande steg om du vill konfigurera programmet i **SP** -initierat läge:

    I text rutan **inloggnings-URL** anger du en URL som använder följande mönster:  `https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Kontakta [MongoDB Cloud client support team](https://support.mongodb.com/)för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Moln programmet MongoDB förväntar sig att SAML-intygen ska vara i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av standardattribut](common/default-attributes.png)

1. Förutom föregående attribut förväntar sig Cloud-MongoDB några fler attribut att skickas tillbaka i SAML-svaret. Dessa attribut har också fyllts i automatiskt, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | e-post | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Skärm bild av avsnittet SAML-signerings certifikat med hämtnings länken markerad](common/metadataxml.png)

1. I avsnittet **konfigurera MongoDB Cloud** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Skärm bild av avsnittet Konfigurera Mongo DB-molnet med URL: er markerat](common/copy-configuration-urls.png)
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

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MongoDB-molnet.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **MongoDB Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.

    ![Skärm bild av sidan användare och grupper där Lägg till användare är markerat](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurera MongoDB-molnets SSO

Om du vill konfigurera enkel inloggning på MongoDB-molnet måste du ha rätt URL: er kopierad från Azure Portal. Du måste också konfigurera Federations programmet för din MongoDB-moln organisation. Följ anvisningarna i [MongoDB Cloud-dokumentationen](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Om du har problem kan du kontakta [MongoDB Cloud Support Team](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Skapa en MongoDB Cloud test User

MongoDB Cloud stöder just-in-Time-etablering, som är aktiverat som standard. Det finns ingen ytterligare åtgärd att vidta. Om en användare inte redan finns i MongoDB-molnet skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer moln panelen MongoDB i åtkomst panelen loggas du automatiskt in till MongoDB-molnet som du ställer in SSO för. Mer information finns i [Logga in och starta appar från portalen Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Registrera dig för MongoDB-Atlas på Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mongodb_atlas_may_2020?tab=Overview)

- [Prova MongoDB Cloud med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda MongoDB-molnet med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

