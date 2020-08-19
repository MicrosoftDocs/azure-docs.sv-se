---
title: 'Självstudie: Azure Active Directory integrering med Palo-nätverk, integrerad Portal | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks Captive Portal.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: 1dad0ecc80302ae6b48d420664723a3a03fc9ea5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554035"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Självstudie: Azure Active Directory integrering med Palo-nätverk, integrerat på portalen

I den här självstudien lär du dig att integrera Palo Alto Networks Captive Portal med Azure Active Directory (AD Azure).

Genom att integrera Palo Alto Networks Captive Portal med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Palo Alto Networks Captive Portal.
* Du kan automatiskt logga in användare i Palo Alto Networks Captive Portal (enkel inloggning) med hjälp av Azure AD-konton.
* Du kan hantera dina konton på en enda central plats: Azure-portalen.

Läs mer om SaaS-appintegrering (programvara som en tjänst) med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

För att integrera Azure AD med Palo Alto Networks Captive Portal behöver du följande:

* En Azure Active Directory-prenumeration. Om du inte har Azure AD kan du få [en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).
* En Palo Alto Networks Captive Portal-prenumeration med enkel inloggning (SSO) aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

Palo Alto Networks Captive Portal stöder följande scenarier:

* **IDP-initierad enkel inloggning**
* **just-in-time-användaretablering**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Lägga till Palo Alto Networks Captive Portal från galleriet

Kom igång genom att i galleriet lägga till Palo Alto Networks Captive Portal till listan över hanterade SaaS-appar:

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra menyn och väljer **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Välj **företags program**  >  **alla program**.

    ![Alternativ för företagsprogram i menyn](common/enterprise-applications.png)

3. Välj **Nytt program**.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Palo Alto Networks Captive Portal**. I sökresultatet väljer du **Palo Alto Networks – Captive Portal** och sedan **Lägg till**.

     ![Palo Alto Networks – Captive Portal i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Du konfigurerar och testar enkel inloggning i Azure AD med Palo Alto Networks Captive Portal baserat på en testanvändare som heter *Britta Simon*. För att enkel inloggning ska fungera måste du upprätta en relation mellan en Azure AD-användare och samma användare Palo Alto Networks Captive Portal. 

För att konfigurera och testa enkel inloggning för Azure AD med Palo Alto Networks Captive Portal behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)**: gör det möjligt för användaren att använda den här funktionen.
2. **[Konfigurera Palo-nätverk, enkel inloggning för portalen](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Konfigurera inställningar för enkel inloggning i programmet.
3. **[Skapa en Azure AD-test användare](#create-an-azure-ad-test-user)**: Testa enkel inloggning med Azure AD med användaren *Britta Simon*.
4. **[Tilldela Azure AD-test användaren](#assign-the-azure-ad-test-user)**: Konfigurera Britta Simon för att använda enkel inloggning med Azure AD.
5. **Skapa ett Palo-nätverk inbyggt Portal test användare**: skapa en motsvarighet för användare *Britta Simon* i Palo-nätverk, som är inkopplad till Azure AD-användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**: kontrol lera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

Först aktiverar du enkel inloggning med Azure AD i Azure-portalen:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Palo Alto Networks – Captive Portal**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I fönstret **Välj en metod för enkel inloggning** väljer du **SAML**.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. I fönstret **Konfigurera enkel inloggning med SAML** väljer du pennikonen **Redigera**.

    ![Pennikonen Redigera](common/edit-urls.png)

4. I fönstret **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Fönstret Grundläggande SAML-konfiguration för Palo Alto Networks Captive Portal](common/idp-intiated.png)

   1. För **Identifierare** anger du en URL som har mönstret `https://<customer_firewall_host_name>/SAML20/SP`.

   2. För **Svars-URL** anger du en URL som har mönstret `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Uppdatera platshållarvärdena i det här steget med den faktiska identifieraren och svars-URL:erna. Hämta de faktiska värdena genom att kontakta [kundsupporten för Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. I avsnittet **SAML-signeringscertifikat** letar du upp **XML:en med federationsmetadata** och väljer **Ladda ned** intill det. Spara den nedladdade filen till datorn.

    ![Nedladdningslänk för XML:en med federationsmetadata](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurera enkel inloggning för Palo Alto Networks Captive Portal

Sedan konfigurerar du enkel inloggning för Palo Alto Networks Captive Portal:

1. I ett annat webbläsarfönster loggar du in på Palo Alto Networks-webbplatsen som administratör.

2. Välj fliken **Enhet**.

    ![Fliken Enheter för Palo Alto Networks-webbplatsen](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. I menyn väljer du **SAML-identitetsprovider** och sedan **Importera**.

    ![Knappen Importera](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. I dialogrutan **SAML Identity Provider Server Profile Import** (Importera serverprofil för SAML-identitetsprovider) utför du följande steg:

    ![Konfigurera enkel inloggning för Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. För **Profilnamn** anger du ett namn, till exempel **AzureAD-CaptivePortal**.
    
    2. Intill **Identity Provider Metadata** (Metadata för identitetsprovider) väljer du **Bläddra**. Välj den metadata.xml-fil som du laddade ned i Azure-portalen.
    
    3. Välj **OK**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Skapa sedan en testanvändare med namnet *Britta Simon* i Azure-portalen:

1. I Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare**.

    ![Knappen Ny användare](common/new-user.png)

3. I fönstret **Användare** utför du följande steg:

    ![Dialogrutan Användare](common/user-properties.png)

    1. För **Namn** anger du **BrittaSimon**.
  
    2. För **användar namn**anger du **BrittaSimon \@ \<your_company_domain\> **. Till exempel **BrittaSimon \@ contoso.com**.

    3. För **Lösenord** anger du ett lösenord. Vi rekommenderar att du antecknar det lösenord du anger. Du kan markera kryssrutan **Visa lösenord** för att visa lösenordet.

    4. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Sedan beviljar du åtkomst till Palo Alto Networks Captive Portal så att Britta Simon kan använda enkel inloggning med Azure:

1. I Azure Portal väljer du **företags program**  >  **alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

2. I programlistan anger du **Palo Alto Networks – Captive Portal** och väljer sedan programmet.

    ![Palo Alto Networks – Captive Portal-länk i programlistan](common/all-applications.png)

3. På menyn väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I fönstret **Användare och grupper** går du till listan **Användare** och väljer **Britta Simon**. Välj **Välj**.

6. För att lägga till ett rollvärde i SAML-försäkran lägger du i fönstret **Välj roll** till relevant roll för användaren. Välj **Välj**.

7. I fönstret **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Skapa en Palo Alto Networks Captive Portal-testanvändare

Skapa sedan en användare med namnet *Britta Simon* i Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal stöder just-in-time-användaretablering, vilket är aktiverat som standard. Du behöver inte utföra några uppgifter i det här avsnittet. Om det inte redan finns en användare i Palo Alto Networks – Captive Portal skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kontaktar du [kundsupporten för Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

Palo Alto Networks Captive Portal installeras bakom brandväggen på en virtuella Windows-dator. För att testa enkel inloggning i Palo Alto Networks Captive Portal loggar du in på den virtuella Windows-datorn med hjälp av Remote Desktop Protocol (RDP). I RDP-sessionen öppnar du en webbläsare och går till valfri webbplats. URL:en för enkel inloggning öppnas, och du uppmanas att autentisera. När autentiseringen är klar kan du komma åt webbplatser.

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i följande artiklar:

- [Självstudier om att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Villkorlig åtkomst i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

