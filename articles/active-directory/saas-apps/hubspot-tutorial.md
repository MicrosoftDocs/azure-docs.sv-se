---
title: 'Självstudie: Azure Active Directory integrering med HubSpot | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HubSpot.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 51e27526b10bcdd74d6e2bffb8bf620d7b022aac
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551480"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Självstudie: Azure Active Directory integrering med HubSpot

I den här självstudien får du lära dig hur du integrerar HubSpot med Azure Active Directory (Azure AD).

Genom att integrera HubSpot med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till HubSpot.
* Användare kan loggas in automatiskt på HubSpot med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om program vara som en tjänst (SaaS) för att integrera appar med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HubSpot behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En HubSpot-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar HubSpot med Azure AD.

HubSpot stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-hubspot-in-the-azure-portal"></a>Lägg till HubSpot i Azure Portal

Om du vill integrera HubSpot med Azure AD måste du lägga till HubSpot i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På den vänstra menyn väljer du **Azure Active Directory**.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **nytt program**om du vill lägga till ett program.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **HubSpot**i rutan Sök. I Sök resultaten väljer du **HubSpot**och väljer sedan **Lägg till**.

    ![HubSpot i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med HubSpot baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i HubSpot.

Om du vill konfigurera och testa enkel inloggning med HubSpot i Azure AD måste du slutföra följande Bygg stenar:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör att användarna kan använda den här funktionen. |
| **[Konfigurera HubSpot enkel inloggning](#configure-hubspot-single-sign-on)** | Konfigurerar inställningarna för enkel inloggning i programmet. |
| **[Skapa en test användare i Azure AD](#create-an-azure-ad-test-user)** | Testar enkel inloggning i Azure AD för en användare med namnet Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör att Britta Simon kan använda enkel inloggning med Azure AD. |
| **[Skapa en HubSpot-test användare](#create-a-hubspot-test-user)** | Skapar en motsvarighet till Britta Simon i HubSpot som är länkad till användarens Azure AD-representation. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du enkel inloggning med Azure AD med HubSpot i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)i fönstret **HubSpot** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML** eller **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** konfigurerar du *IDP-initierat läge*genom att utföra följande steg:

    1. I rutan **identifierare** anger du en URL som har följande mönster: https: \/ /API.HubSpot.com/login-API/v1/SAML/login?portalId = \<CUSTOMER ID\> .

    1. I rutan **svars-URL** anger du en URL som har följande mönster: https: \/ /API.HubSpot.com/login-API/v1/SAML/ACS?portalId = \<CUSTOMER ID\> .

    ![Information om enkel inloggning för HubSpot-domän och URL: er](common/idp-intiated.png)

    > [!NOTE]
    > Om du vill formatera URL: erna kan du även se de mönster som visas i fönstret **grundläggande SAML-konfiguration** i Azure Portal.

1. Så här konfigurerar du programmet i *SP-initierat* läge:

    1. Välj **Ange ytterligare URL: er**.

    1. I rutan **inloggnings-URL** anger du **https: \/ /app.HubSpot.com/login**.

    ![Alternativet Ange ytterligare URL: er](common/metadata-upload-additional-signon.png)

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du **Hämta** bredvid **certifikat (base64)**. Välj ett nedladdnings alternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtnings alternativet för certifikat (base64)](common/certificatebase64.png)

1. I avsnittet **Konfigurera HubSpot** kopierar du följande webb adresser utifrån dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurera HubSpot enkel inloggning

1. Öppna en ny flik i webbläsaren och logga in på ditt HubSpot-administratörs konto.

1. Välj **inställnings** ikonen i det övre högra hörnet på sidan.

    ![Inställnings ikonen i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **standardinställningar för kontot**.

    ![Alternativet konto inställningar i HubSpot](./media/hubspot-tutorial/config2.png)

1. Rulla ned till avsnittet **säkerhet** och välj sedan **Konfigurera**.

    ![Alternativet Konfigurera i HubSpot](./media/hubspot-tutorial/config3.png)

1. I avsnittet **Konfigurera enkel inloggning** utför du följande steg:

    1. I rutan **målgrupps-URL (enhets-ID för Service Provider)** väljer du **Kopiera** för att kopiera värdet. Klistra in värdet i rutan **identifierare** i rutan **grundläggande SAML-konfiguration** i Azure Portal.

    1. I rutan **Logga in URL, ACS, mottagare eller omdirigering** väljer du **Kopiera** för att kopiera värdet. I Azure Portal i fönstret **grundläggande SAML-konfiguration** klistrar du in värdet i rutan **svars-URL** .

    1. I HubSpot, i rutan ID för **identitets leverantör eller utfärdare** , klistrar du in värdet för **Azure AD-identifieraren** som du kopierade i Azure Portal.

    1. I HubSpot, i rutan **URL för enkel inloggning i identitetsprovider** , klistrar du in värdet för **inloggnings-URL** som du kopierade i Azure Portal.

    1. Öppna den certifikat fil (base64) som du laddade ned i Windows Anteckningar. Markera och kopiera innehållet i filen. I HubSpot klistrar du sedan in det i rutan **X. 509-certifikat** .

    1. Välj **Verifiera**.

        ![Avsnittet Konfigurera enkel inloggning i HubSpot](./media/hubspot-tutorial/config4.png)

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

I det här avsnittet beviljar du Britta Simon-åtkomst till HubSpot så att hon kan använda enkel inloggning i Azure.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **HubSpot**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **HubSpot**.

    ![HubSpot i listan program](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett roll värde i SAML-försäkran väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll** . Välj **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-hubspot-test-user"></a>Skapa en HubSpot-test användare

Användaren måste vara etablerad i HubSpot för att Azure AD ska kunna logga in på HubSpot. I HubSpot är etableringen en manuell uppgift.

Så här etablerar du ett användar konto i HubSpot:

1. Logga in på din HubSpot företags webbplats som administratör.

1. Välj **inställnings** ikonen i det övre högra hörnet på sidan.

    ![Inställnings ikonen i HubSpot](./media/hubspot-tutorial/config1.png)

1. Välj **användare & team**.

    ![Alternativet användare & Teams i HubSpot](./media/hubspot-tutorial/user1.png)

1. Välj **skapa användare**.

    ![Alternativet Skapa användare i HubSpot](./media/hubspot-tutorial/user2.png)

1. I rutan **Lägg till e-addess (ES)** anger du användarens e-postadress i formatet brittasimon \@ contoso.com och väljer sedan **Nästa**.

    ![Rutan Lägg till e-postadress (er) i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user3.png)

1. I avsnittet **skapa användare** väljer du varje flik. Ange de relevanta alternativen och behörigheterna för användaren på varje flik. Välj sedan **Nästa**.

    ![Flikar i avsnittet Skapa användare i HubSpot](./media/hubspot-tutorial/user4.png)

1. Om du vill skicka inbjudan till användaren väljer du **Skicka**.

    ![Alternativet Skicka i HubSpot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Användaren aktive ras när användaren har accepterat inbjudan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning när du väljer **HubSpot** i portalen Mina appar loggas du automatiskt in på HubSpot. Mer information om My Apps-portalen finns i [komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs mer i de här artiklarna:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
