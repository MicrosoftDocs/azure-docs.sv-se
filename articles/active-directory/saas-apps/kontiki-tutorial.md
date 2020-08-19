---
title: 'Självstudie: Azure Active Directory integrering med Kontiki | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kontiki.
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
ms.openlocfilehash: cb652cfdae0ce785d6a076b8b1592d9f700b3383
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549985"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Självstudie: Azure Active Directory integrering med Kontiki

I den här självstudien får du lära dig hur du integrerar Kontiki med Azure Active Directory (Azure AD).

Genom att integrera Kontiki med Azure AD får du följande fördelar:

* Du kan använda Azure AD för att kontrol lera vem som har åtkomst till Kontiki.
* Användare kan loggas in automatiskt på Kontiki med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om program vara som en tjänst (SaaS) för att integrera appar med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kontiki behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En Kontiki-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö och integrerar Kontiki med Azure AD.

Kontiki stöder följande funktioner:

* **SP-initierad enkel inloggning**
* **just-in-time-användaretablering**

## <a name="add-kontiki-in-the-azure-portal"></a>Lägg till Kontiki i Azure Portal

Om du vill integrera Kontiki med Azure AD måste du lägga till Kontiki i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. På den vänstra menyn väljer du **Azure Active Directory**.

    ![Alternativet Azure Active Directory](common/select-azuread.png)

1. Välj **företags program**  >  **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Välj **nytt program**om du vill lägga till ett program.

    ![Alternativet nytt program](common/add-new-app.png)

1. Skriv **Kontiki**i rutan Sök. I Sök resultaten väljer du **Kontiki**och väljer sedan **Lägg till**.

    ![Kontiki i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Kontiki baserat på en test användare som heter **Britta Simon**. För att enkel inloggning ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i Kontiki.

Om du vill konfigurera och testa enkel inloggning med Kontiki i Azure AD måste du slutföra följande Bygg stenar:

| Uppgift | Beskrivning |
| --- | --- |
| **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** | Gör att användarna kan använda den här funktionen. |
| **[Konfigurera Kontiki enkel inloggning](#configure-kontiki-single-sign-on)** | Konfigurerar inställningarna för enkel inloggning i programmet. |
| **[Skapa en test användare i Azure AD](#create-an-azure-ad-test-user)** | Testar enkel inloggning i Azure AD för en användare med namnet Britta Simon. |
| **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** | Gör att Britta Simon kan använda enkel inloggning med Azure AD. |
| **[Skapa en Kontiki-test användare](#create-a-kontiki-test-user)** | Skapar en motsvarighet till Britta Simon i Kontiki som är länkad till användarens Azure AD-representation. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Verifierar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet konfigurerar du enkel inloggning med Azure AD med Kontiki i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)i fönstret **Kontiki** Application Integration väljer du **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I fönstret **Välj en enkel inloggnings metod** väljer du **SAML** eller **SAML/WS-utfodras** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du **Redigera** (Penn ikonen) för att öppna fönstret **grundläggande SAML-konfiguration** .

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange en URL med följande mönster i text rutan **inloggnings-URL** i rutan **grundläggande SAML-konfiguration** :`https://<companyname>.mc.eval.kontiki.com`

    ![Information om enkel inloggning för Kontiki-domän och URL: er](common/sp-signonurl.png)

    > [!NOTE]
    > Kontakta [Kontiki-klientens support team](https://customersupport.kontiki.com/enterprise/contactsupport.html) för att få rätt värde att använda. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. I avsnittet **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , väljer du **Hämta** bredvid **XML för federationsmetadata**. Välj ett nedladdnings alternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtnings alternativet för XML-certifikat för federationsmetadata](common/metadataxml.png)

1. I avsnittet **Konfigurera Kontiki** kopierar du följande webb adresser utifrån dina krav:

    * Inloggnings-URL
    * Azure AD-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurera Kontiki enkel inloggning

Om du vill konfigurera enkel inloggning på Kontiki-sidan skickar du den hämtade XML-filen med federationsmetadata och de relevanta URL: er som du kopierade från Azure Portal till [support teamet för Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Support teamet för Kontiki använder den information som du skickar dem för att se till att den enkel inloggnings anslutningen för SAML är korrekt inställd på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Alternativen användare och alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I rutan **användar namn** anger du **brittasimon \@ \<your-company-domain> . \<extension> **. Till exempel **brittasimon \@ contoso.com**.

    1. Markera kryss rutan **Visa lösen ord** . Skriv ned värdet som visas i rutan **lösen ord** .

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du Britta Simon-åtkomst till Kontiki så att hon kan använda enkel inloggning i Azure.

1. I Azure Portal väljer du **företags program**  >  **alla program**  >  **Kontiki**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan program väljer du **Kontiki**.

    ![Kontiki i listan program](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I fönstret **användare och grupper** väljer du **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett roll värde i SAML-försäkran väljer du den relevanta rollen för användaren i listan i fönstret **Välj roll** . Välj **Välj**.

1. I fönstret **Lägg till tilldelning** väljer du **tilldela**.

### <a name="create-a-kontiki-test-user"></a>Skapa en Kontiki-test användare

Det finns inget åtgärds objekt som du kan använda för att konfigurera användar etablering i Kontiki. När en tilldelad användare försöker logga in på Kontiki med hjälp av My Apps-portalen kontrollerar Kontiki om användaren finns. Om inget användar konto hittas skapar Kontiki automatiskt användar kontot.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med hjälp av portalen Mina appar.

När du har konfigurerat enkel inloggning när du väljer **Kontiki** i portalen Mina appar loggas du automatiskt in på Kontiki. Mer information om My Apps-portalen finns i [komma åt och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Läs mer i de här artiklarna:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
