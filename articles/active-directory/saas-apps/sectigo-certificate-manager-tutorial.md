---
title: 'Självstudie: Azure Active Directory integration med Sectigo Certificate Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sectigo Certificate Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: d68e5335fff0341d8808e581061519977e1bb517
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543286"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Självstudie: Azure Active Directory integration med Sectigo Certificate Manager

I den här självstudien får du lära dig att integrera Sectigo Certificate Manager med Azure Active Directory (Azure AD).

Genom att integrera Sectigo Certificate Manager med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Sectigo Certificate Manager.
* Användare kan loggas in automatiskt till Sectigo Certificate Manager med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om program vara som en tjänst (SaaS) för att integrera appar med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sectigo Certificate Manager behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Sectigo Certificate Manager-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar Sectigo Certificate Manager med Azure AD.

Sectigo Certificate Manager har stöd för följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Lägg till Sectigo Certificate Manager i Azure Portal

Om du vill integrera Sectigo Certificate Manager med Azure AD måste du lägga till Sectigo Certificate Manager i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På den vänstra menyn väljer du **Azure Active Directory**.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **nytt program**om du vill lägga till ett program.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **Sectigo Certificate Manager**i rutan Sök. I Sök resultaten väljer du **Sectigo Certificate Manager**och väljer sedan **Lägg till**.

    ![Sectigo Certificate Manager i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Sectigo Certificate Manager baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och en relaterad användare i Sectigo Certificate Manager.

Om du vill konfigurera och testa enkel inloggning med Azure AD med Sectigo Certificate Manager måste du slutföra följande Bygg stenar:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör att användarna kan använda den här funktionen. |
| **[Konfigurera enkel inloggning för Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Konfigurerar inställningarna för enkel inloggning i programmet. |
| **[Skapa en test användare i Azure AD](#create-an-azure-ad-test-user)** | Testar enkel inloggning i Azure AD för en användare med namnet Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör att Britta Simon kan använda enkel inloggning med Azure AD. |
| **[Skapa en test användare för Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Skapar en motsvarighet till Britta Simon i Sectigo Certificate Manager som är länkad till användarens Azure AD-representation. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du enkel inloggning med Azure AD med Sectigo Certificate Manager i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)i fönstret **Sectigo Certificate Manager** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML** eller **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** konfigurerar du *IDP-initierat läge*genom att utföra följande steg:

    1. I rutan **identifierare** anger du en av följande URL: er:
       * https: \/ /cert-Manager.com/Shibboleth
       * https: \/ /Hard.cert-Manager.com/Shibboleth

    1. I rutan **svars-URL** anger du en av följande URL: er:
        * https: \/ /cert-Manager.com/Shibboleth.SSO/SAML2/post
        * https: \/ /Hard.cert-Manager.com/Shibboleth.SSO/SAML2/post

    1. Välj **Ange ytterligare URL: er**.

    1. I rutan **relä tillstånd** anger du en av följande URL: er:
       * https: \/ /cert-Manager.com/Customer/SSLSupport/IDP
       * https: \/ /Hard.cert-Manager.com/Customer/SSLSupport/IDP

    ![Information om enkel inloggning för Sectigo Certificate Manager-domän och URL: er](common/idp-relay.png)

1.  Slutför följande steg för att konfigurera programmet i *SP-initierat läge*:

    * I rutan **inloggnings-URL** anger du en av följande URL: er:
      * https: \/ /cert-Manager.com/Shibboleth.SSO/login
      * https: \/ /Hard.cert-Manager.com/Shibboleth.SSO/login

      ![Information om enkel inloggning för Sectigo Certificate Manager-domän och URL: er](common/both-signonurl.png)

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du **Hämta** bredvid **certifikat (base64)**. Välj ett nedladdnings alternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtnings alternativet för certifikat (base64)](common/certificatebase64.png)

1. I avsnittet **Konfigurera Sectigo Certificate Manager** kopierar du följande webb adresser utifrån dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurera enkel inloggning för Sectigo Certificate Manager

Om du vill konfigurera enkel inloggning på sidan Sectigo Certificate Manager skickar du den hämtade certifikat filen (base64) och relevanta URL: er som du kopierade från Azure Portal till [support teamet för Sectigo Certificate Manager](https://sectigo.com/support). Support teamet för Sectigo Certificate Manager använder den information som du skickar dem för att se till att den enkel inloggnings anslutningen för SAML är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Alternativen användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **brittasimon \@ \<your-company-domain> . \<extension\> **. Till exempel **brittasimon \@ contoso.com**.

    1. Markera kryss rutan **Visa lösen ord** . Skriv ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du Britta Simon-åtkomst till Sectigo Certificate Manager så att hon kan använda enkel inloggning i Azure.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **Sectigo Certificate Manager**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager i listan program](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett roll värde i SAML-försäkran väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll** . Välj **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Skapa en test användare för Sectigo Certificate Manager

I det här avsnittet skapar du en användare med namnet Britta Simon i Sectigo Certificate Manager. Arbeta med [Sectigo Certificate Manager Support Team](https://sectigo.com/support) för att lägga till användaren på Sectigo Certificate Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning när du väljer **Sectigo Certificate Manager** i portalen Mina appar, loggas du automatiskt in på Sectigo Certificate Manager. Mer information om My Apps-portalen finns i [komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs mer i de här artiklarna:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


