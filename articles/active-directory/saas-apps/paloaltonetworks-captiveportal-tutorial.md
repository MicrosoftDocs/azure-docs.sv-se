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
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 1b9c9182b8ffc6e9ea2e05fd4863783f111f81dd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98727156"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Självstudie: Azure Active Directory integrering med Palo-nätverk, integrerat på portalen

I den här självstudien lär du dig att integrera Palo Alto Networks Captive Portal med Azure Active Directory (AD Azure).
Integrering av Palo-nätverkets integrerade Portal med Azure AD ger följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Palo-portalen för nätverk.
* Du kan göra det möjligt för användarna att logga in automatiskt till Palo-nätverk, som har inbyggd portal (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Krav

För att integrera Azure AD med Palo Alto Networks Captive Portal behöver du följande:

* En Azure Active Directory-prenumeration. Om du inte har Azure AD kan du få [en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).
* En Palo Alto Networks Captive Portal-prenumeration med enkel inloggning (SSO) aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo-nätverkets företags intern Portal stöder **IDP** INITIERAd SSO
* Palo-nätverkets inbyggda Portal stöder **just-in-Time** User-etablering

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Lägga till Palo-nätverk med-portalen från galleriet

Om du vill konfigurera integreringen av Palo-nätverk i Azure AD måste du lägga till Palo-nätverk på företags portalen från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Palo-nätverk** som har inbyggd portal i sökrutan.
1. Välj **Palo-nätverk** , uppsamlad portal från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Palo-nätverk, som bygger på en test användare som kallas **B. Simon**.
För att enkel inloggning ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Palo-nätverkets inbyggda Portal.

Utför följande steg för att konfigurera och testa enkel inloggning för Azure AD med Palo-nätverk som har inbyggd Portal:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – aktivera användaren för att använda den här funktionen.
    * **[Skapa en Azure AD-test användare](#create-an-azure-ad-test-user)** -testa enkel inloggning med Azure AD med User B. Simon.
    * **[Tilldela Azure AD-test User](#assign-the-azure-ad-test-user)** -set B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Palo-nätverk, inbyggd Portal SSO](#configure-palo-alto-networks-captive-portal-sso)** – konfigurera inställningarna för enkel inloggning i programmet.
    * **[Skapa ett Palo-nätverk som är inbyggt på en portal test användare](#create-a-palo-alto-networks-captive-portal-test-user)** – för att få en motsvarighet till B. Simon i Palo-nätverk, som är kopplat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan Palo- **nätverk** , integrerat Portal program integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I fönstret **grundläggande SAML-konfiguration** utför du följande steg:

   1. För **Identifierare** anger du en URL som har mönstret `https://<customer_firewall_host_name>/SAML20/SP`.

   2. För **Svars-URL** anger du en URL som har mönstret `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Uppdatera platshållarvärdena i det här steget med den faktiska identifieraren och svars-URL:erna. Hämta de faktiska värdena genom att kontakta [kundsupporten för Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. I avsnittet **SAML-signeringscertifikat** letar du upp **XML:en med federationsmetadata** och väljer **Ladda ned** intill det. Spara den nedladdade filen till datorn.

    ![Nedladdningslänk för XML:en med federationsmetadata](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Palo-nätverk som har inbyggd Portal.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Palo-nätverk**, inbyggd Portal.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Konfigurera Palo-nätverk,-portalen SSO

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

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Skapa en Palo Alto Networks Captive Portal-testanvändare

Skapa sedan en användare med namnet *Britta Simon* i Palo Alto Networks Captive Portal. Palo Alto Networks Captive Portal stöder just-in-time-användaretablering, vilket är aktiverat som standard. Du behöver inte utföra några uppgifter i det här avsnittet. Om det inte redan finns en användare i Palo Alto Networks – Captive Portal skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kontaktar du [kundsupporten för Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till Palo-nätverkets inbyggda portal där du konfigurerar SSO

* Du kan använda Microsoft Mina appar. När du klickar på panelen Palo-nätverkets inbyggda portal i Mina appar, bör du loggas in automatiskt på Palo-nätverkets inbyggda portal där du konfigurerar SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat portalen Palo-nätverk, kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).