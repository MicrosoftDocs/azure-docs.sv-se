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
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673864"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Självstudie: Azure Active Directory integration med Sectigo Certificate Manager

I den här självstudien får du lära dig att integrera Sectigo Certificate Manager (även kallat SCM) med Azure Active Directory (Azure AD).

Genom att integrera Sectigo Certificate Manager med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Sectigo Certificate Manager.
* Användare kan loggas in automatiskt till Sectigo Certificate Manager med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om program vara som en tjänst (SaaS) för att integrera appar med Azure AD finns i [enkel inloggning till program i Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sectigo Certificate Manager behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Sectigo Certificate Manager-konto.

> [!NOTE]
> Sectigo kör flera instanser av Sectigo Certificate Manager. Huvud instansen av Sectigo Certificate Manager är  **https: \/ /cert-Manager.com**, och denna URL används i den här självstudien.  Om ditt konto finns på en annan instans måste du justera webb adresserna efter behov.

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

1. Välj **nytt program** om du vill lägga till ett program.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **Sectigo Certificate Manager** i rutan Sök. I Sök resultaten väljer du **Sectigo Certificate Manager** och väljer sedan **Lägg till**.

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

1. I fönstret **Konfigurera enkla Sign-On med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** slutför du följande steg:

    1. I rutan **identifierare (enhets-ID)** för den huvudsakliga Sectigo Certificate Manager-instansen anger du **https: \/ /cert-Manager.com/Shibboleth**.

    1. Skriv **https: \/ /cert-Manager.com/Shibboleth.SSO/SAML2/post** i rutan **svars-URL** för den huvudsakliga Sectigo Certificate Manager-instansen.
        
    > [!NOTE]
    > Även om **inloggnings-URL: en** är i allmänhet obligatorisk för *SP-initierat läge* behöver du inte logga in från Sectigo Certificate Manager.        

1. Du kan också i avsnittet **grundläggande SAML-konfiguration** konfigurera *IDP-initierat läge* och för att tillåta att **testet** fungerar, genom att utföra följande steg:

    1. Välj **Ange ytterligare URL: er**.

    1. I rutan **vidarebefordra tillstånd** anger du en kundspecifik URL för Sectigo Certificate Manager. För main Sectigo Certificate Manager-instans anger du **https: \/ /cert-Manager.com/Customer/ \<customerURI\> /IDP**.

    ![Information om enkel inloggning för Sectigo Certificate Manager-domän och URL: er](common/idp-relay.png)

1. Utför följande steg i avsnittet **användarattribut &-anspråk** :

    1. Ta bort alla **Ytterligare anspråk**.
    
    1. Välj **Lägg till nytt anspråk** och Lägg till följande fyra anspråk:
    
        | Name | Namnområde | Källa | Källattribut | Beskrivning |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | tomt | Attribut | user.userprincipalname | Måste matcha fältet **IDP person-ID** i Sectigo Certificate Manager för administratörer. |
        | e-post | tomt | Attribut | user.mail | Obligatorisk |
        | förnamn | tomt | Attribut | user.givenname | Valfritt |
        | sn | tomt | Attribut | user.surname | Valfritt |

       ![Sectigo Certificate Manager – Lägg till fyra nya anspråk](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. I avsnittet **SAML-signerings certifikat** väljer du **Hämta** bredvid **XML för federationsmetadata**. Spara XML-filen på din dator.

    ![Hämtnings alternativ för XML-metadata för federationsmetadata](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurera enkel inloggning för Sectigo Certificate Manager

Om du vill konfigurera enkel inloggning på sidan Sectigo Certificate Manager skickar du den hämtade XML-filen med federationsmetadata till [support teamet för Sectigo Certificate Manager](https://sectigo.com/support). Support teamet för Sectigo Certificate Manager använder den information som du skickar dem för att se till att den enkel inloggnings anslutningen för SAML är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Alternativen användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **brittasimon \@ \<your-company-domain> . \<extension\>**. Till exempel **brittasimon \@ contoso.com**.

    1. Markera kryss rutan **Visa lösen ord** . Registrera värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du Britta Simon-åtkomst till Sectigo Certificate Manager så att användaren kan använda enkel inloggning i Azure.

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

I det här avsnittet ska du testa konfigurationen för enkel inloggning i Azure AD.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Testa från Sectigo Certificate Manager (SP-initierad enkel inloggning)

Bläddra till din kundspecifika URL (för den huvudsakliga Sectigo Certificate Manager-instansen, https: \/ /cert-Manager.com/Customer/ \<customerURI\> /och välj knappen nedan **eller logga in med**.  Om den är korrekt konfigurerad loggas du in automatiskt på Sectigo Certificate Manager.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Testa från Azures konfiguration för enkel inloggning (IDP enkel inloggning)

I fönstret **Sectigo Certificate Manager** Application Integration väljer du **enkel inloggning** och klickar på knappen **testa** .  Om den är korrekt konfigurerad loggas du in automatiskt på Sectigo Certificate Manager.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testa med hjälp av mina apps-portalen (IDP enkel inloggning)

Välj **Sectigo Certificate Manager** i portalen Mina appar.  Om den är korrekt konfigurerad loggas du in automatiskt i Sectigo Certificate Manager. Mer information om My Apps-portalen finns i [komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs mer i de här artiklarna:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](./tutorial-list.md)
- [Enkel inloggning till program i Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)