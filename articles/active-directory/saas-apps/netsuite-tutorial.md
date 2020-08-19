---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Netsuite | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetSuite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: c5be6ef39a3e980852aa3373dbc54da63aa3983e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554523"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Självstudie: integrera enkel inloggning med Azure AD (SSO) med Netsuite

I den här självstudien får du lära dig hur du integrerar Netsuite med Azure Active Directory (Azure AD). När du integrerar Netsuite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Netsuite.
* Gör det möjligt för användarna att logga in automatiskt till Netsuite med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En enkel inloggnings prenumeration (SSO) med Netsuite.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

NetSuite stöder:

* IDP-initierad SSO.
* JIT (just-in-Time) användar etablering.
* [Automatiserad användar etablering](NetSuite-provisioning-tutorial.md).
* När du har konfigurerat netsvit kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Eftersom ID: t för det här programmet är ett fast sträng värde kan endast en instans konfigureras i en klient.

## <a name="add-netsuite-from-the-gallery"></a>Lägg till Netsuite från galleriet

Om du vill konfigurera Netsuite-integrering i Azure AD lägger du till Netsuite från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Netsuite** i sökrutan.
1. I resultat fönstret väljer du **Netsuite**och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Konfigurera och testa enkel inloggning med Azure AD för Netsuite

Konfigurera och testa Azure AD SSO med Netsuite genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Netsuite.

Om du vill konfigurera och testa Azure AD SSO med Netsuite slutför du följande Bygg stenar:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
    * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD med User B. Simon.  
    * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera användare B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera Netsuite SSO](#configure-netsuite-sso) för att konfigurera inställningarna för enkel inloggning på program sidan.
    * [Skapa Netsuite-testet](#create-the-netsuite-test-user) för att få en motsvarighet till användare B. Simon i Netsuite som är länkat till användarens Azure AD-representation.
1. [Testa SSO](#test-sso) för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Gör så här om du vill aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Netsuite** -programintegration letar du reda på avsnittet **Hantera** och väljer sedan **enkel inloggning**.
1. I fönstret **Välj en metod för enkel inloggning** väljer du **SAML**.
1. I fönstret **Konfigurera enkel inloggning med SAML** väljer du ikonen **Redigera** (Penn) bredvid den **grundläggande SAML-konfigurationen**.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , i text rutan **svars-URL** , anger du en URL i något av följande format:

    ```https
    https://<Instance ID>.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.NetSuite.com/saml2/acs
    https://<Instance ID>.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs
    https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs
    ```

    * Du får **<`Instance ID`>** värdet i avsnittet Netsuite-konfiguration, som beskrivs senare i självstudien vid steg 8 under Netsuite-konfiguration. Du hittar den exakta domänen (till exempel system.na0.netsuite.com i det här fallet).

        ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Värdena i föregående URL: er är inte verkliga. Uppdatera dem med den faktiska svars-URL: en. Om du vill hämta värdet kontaktar du [support teamet för Netsuite-klienten](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). Du kan också se de format som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. NetSuite-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar Netsuite-programmet fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut |
    | ---------------| --------------- |
    | konto  | `account id` |

    > [!NOTE]
    > Värdet för konto attributet är inte verkligt. Du kommer att uppdatera det här värdet, vilket förklaras senare i den här självstudien.

1. På sidan Konfigurera enkel inloggning med SAML, i avsnittet SAML-signeringscertifikat, letar du upp XML för federationsmetadata och väljer Hämta för att ladda ned certifikatet och spara det på din dator.

    ![Länken Hämta certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera Netsuite** kopierar du lämplig URL eller URL, beroende på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.

1. Välj **ny användare** överst på skärmen.

1. I fönstret **användar** egenskaper följer du dessa steg:

   a. I rutan **namn** anger du **B. Simon**.  
   b. I rutan **användar namn** anger du username@companydomain.extension (till exempel B.Simon@contoso.com ).  
   c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.  
   d. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du användare B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Netsuite.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Netsuite**.
1. I översikts fönstret, letar du efter avsnittet **Hantera** och väljer sedan länken **användare och grupper** .

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan i fönstret **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Knappen Lägg till användare](common/add-assign-user.png)

1. I **list rutan användare** och grupper i fönstret användare **och grupper** väljer du **B. Simon**och väljer sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-försäkran gör du följande:

   a. I list rutan **Välj roll** i list rutan väljer du lämplig roll för användaren.  
   b. Klicka på knappen **Välj** längst ned på skärmen.
1. I fönstret **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-netsuite-sso"></a>Konfigurera Netsuite SSO

1. Öppna en ny flik i webbläsaren och logga in på företags platsen för Netsuite som administratör.

2. I det övre navigerings fältet väljer du **konfiguration**och väljer sedan **företags**  >  **aktiverings funktioner**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setupsaml.png)

3. I verktygsfältet i mitten av sidan väljer du **SuiteCloud**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Under **hantera autentisering**markerar du kryss rutan **SAML enkel inloggning** för att aktivera alternativet för enkel inloggning för SAML i Netsuite.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-ticksaml.png)

5. I det övre navigerings fältet väljer du **Inställningar**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

6. Välj **integration**i listan **installations uppgifter** .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-integration.png)

7. Under **hantera autentisering**väljer du **enkel inloggning med SAML**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml.png)

8. I fönstret **SAML-installation** under **Netsuite-konfiguration**gör du följande:

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Markera kryss rutan **primär autentiseringsmetod** .

    b. Under **SAMLV2 Identity Provider metadata**väljer du **Ladda upp IDP metadatafil**och väljer sedan **Bläddra** för att ladda upp den metadatafil som du laddade ned från Azure Portal.

    c. Välj **Skicka**.

9. I det övre navigerings fältet i Netsuite väljer du **installation**och sedan **företags**  >  **information**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-account-id.png)

    b. I den högra kolumnen i rutan **företags information** kopierar du **konto-ID-** värdet.

    c. Klistra in det **konto-ID** som du kopierade från Netsuite-kontot till rutan **ATTRIBUTVÄRDE** i Azure AD.

10. Innan användare kan utföra enkel inloggning till NetSuite så måste de först tilldelas lämpliga behörigheter i NetSuite. Gör så här för att tilldela dessa behörigheter:

    a. I det övre navigerings fältet väljer du **Inställningar**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    b. Välj **användare/roller**i den vänstra rutan och välj sedan **hantera roller**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Välj **ny roll**.

    d. Ange ett **namn** för den nya rollen.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-new-role.png)

    e. Välj **Spara**.

    f. I det övre navigerings fältet väljer du **behörigheter**. Välj sedan **installation**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-sso.png)

    ex. Välj **SAML enkel inloggning**och välj sedan **Lägg till**.

    h. Välj **Spara**.

    i. I det övre navigerings fältet väljer du **installation**och sedan **installations hanteraren**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    j. I den vänstra rutan väljer **du användare/roller**och väljer sedan **Hantera användare**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Välj en test användare, Välj **Redigera**och välj sedan fliken **åtkomst** .

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-edit-user.png)

    l. I fönstret **roller** tilldelar du rätt roll som du har skapat.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-add-role.png)

    m. Välj **Spara**.

### <a name="create-the-netsuite-test-user"></a>Skapa Netsuite-test användare

I det här avsnittet skapas en användare som heter B. Simon i Netsuite. NetSuite stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärds objekt i det här avsnittet. Om det inte redan finns någon användare i NetSuite så skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer Netsuite-panelen på åtkomst panelen, bör du loggas in automatiskt på Netsuite som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Testa Netsuite med Azure AD](https://aad.portal.azure.com/)
- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Netsuite med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)