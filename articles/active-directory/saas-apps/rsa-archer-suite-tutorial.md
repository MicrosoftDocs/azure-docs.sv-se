---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med RSA Archer Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RSA Archer Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2020
ms.author: jeedes
ms.openlocfilehash: 397d59c60ed90e0e25df671baa3d46660eff1d47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181585"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rsa-archer-suite"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med RSA Archer Suite

I den här självstudien får du lära dig hur du integrerar RSA Archer Suite med Azure Active Directory (Azure AD). När du integrerar RSA Archer Suite med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RSA Archer Suite.
* Gör det möjligt för användarna att logga in automatiskt till RSA Archer Suite med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning (SSO) för RSA Archer Suite-aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RSA Archer Suite stöder **SP** -INITIERAd SSO
* RSA Archer Suite stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-rsa-archer-suite-from-the-gallery"></a>Lägga till RSA Archer Suite från galleriet

Om du vill konfigurera integreringen av RSA Archer Suite i Azure AD måste du lägga till RSA Archer Suite från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **RSA Archer Suite** i sökrutan.
1. Välj **RSA Archer Suite** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-rsa-archer-suite"></a>Konfigurera och testa Azure AD SSO för RSA Archer Suite

Konfigurera och testa Azure AD SSO med RSA Archer Suite med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RSA Archer Suite.

Utför följande steg för att konfigurera och testa Azure AD SSO med RSA Archer Suite:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RSA Archer Suite SSO](#configure-rsa-archer-suite-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa RSA Archer Suite-test](#create-rsa-archer-suite-test-user)** för att få en motsvarighet till B. Simon i RSA Archer Suite som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **RSA Archer Suite** -programintegration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<BASE_URL>/default.aspx?IDP=<REALM_NAME>`

    b. I text rutan **identifierare (enhets-ID)** anger du värdet: `RSAArcherSuite_TENANT_STRING`

    > [!NOTE]
    > Värdet för inloggnings-URL är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [RSA Archer Suite-support teamet](mailto:archersupport@rsa.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. RSA Archer Suite-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar RSA Archer Suite-program fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | PhoneNumber | user.telephonenumber |
    | City | User. City |
    | Postummer | User. Postnr |
    | Tillstånd | User. State |
    | Gata | User. StreetAddress |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera RSA Archer Suite** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RSA Archer Suite.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **RSA Archer Suite**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har ställts in för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-rsa-archer-suite-sso"></a>Konfigurera RSA Archer Suite SSO

1. Logga in på RSA Archer Suite-webbplatsen i en annan webbläsare som administratör.

1. Utför följande steg på följande sida.

    ![Konfigurera RSA Archer Suite SSO](./media/rsa-archer-suite-tutorial/configuring-saml-sso.png)

    a. Gå till fliken **enkel inloggning** och välj **SAML** som ett **enda Sign-On läge** i list rutan.

    b. Markera kryss rutan **Tillåt manuellt kringgående** .

    c. Ange ett giltigt namn i text rutan **instansens entitets-ID** .

    d. Klistra in **tumavtryck-värdet** i text rutan **tumavtryck för certifikatet** .

    e. Klicka på knappen **Välj** och överför hämtade **XML-metadata för federationsmetadata** från Azure Portal.

    f. **Spara** inställningarna för den enskilda Sign-On. 

### <a name="create-rsa-archer-suite-test-user"></a>Skapa RSA Archer Suite-test användare

I det här avsnittet skapas en användare som heter B. Simon i RSA Archer Suite. RSA Archer Suite stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i RSA Archer Suite skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till RSA Archer Suite-inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till RSA Archer Suite-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen RSA Archer Suite på åtkomst panelen bör du loggas in automatiskt till RSA Archer-sviten som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

 När du har konfigurerat RSA Archer Suite kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).