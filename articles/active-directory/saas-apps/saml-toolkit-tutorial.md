---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Azure AD SAML Toolkit | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Azure AD SAML Toolkit.
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
ms.openlocfilehash: 26618382223a87f779f95452000a39126f37efbb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92675420"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Azure AD SAML Toolkit

I den här självstudien får du lära dig hur du integrerar Azure AD SAML Toolkit med Azure Active Directory (Azure AD). När du integrerar Azure AD SAML Toolkit med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Azure AD SAML Toolkit.
* Gör det möjligt för användarna att logga in automatiskt till Azure AD SAML Toolkit med deras Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Azure AD SAML Toolkit-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Azure AD SAML Toolkit stöder **SP** -INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Lägga till Azure AD SAML Toolkit från galleriet

Om du vill konfigurera integreringen av Azure AD SAML Toolkit i Azure AD måste du lägga till Azure AD SAML Toolkit från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Azure AD SAML Toolkit** i sökrutan.
1. Välj **Azure AD SAML Toolkit** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-azure-ad-saml-toolkit"></a>Konfigurera och testa Azure AD SSO för Azure AD SAML Toolkit

Konfigurera och testa Azure AD SSO med Azure AD SAML Toolkit med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Azure AD SAML Toolkit.

Utför följande steg för att konfigurera och testa Azure AD SSO med Azure AD SAML Toolkit:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa test användare i Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** – om du vill ha en motsvarighet till B. Simon i Azure AD SAML Toolkit som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Azure AD SAML Toolkit** -programintegration i Azure Portal, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://samltoolkit.azurewebsites.net/`

    b. Skriv webb adressen i text rutan **svars-URL** : `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera Azure AD SAML Toolkit** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Azure AD SAML Toolkit.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Azure AD SAML Toolkit**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurera Azure AD SAML Toolkit SSO

1. Öppna ett nytt webbläsarfönster, om du inte har registrerat på webbplatsen för Azure AD SAML Toolkit, första registreringen genom att klicka på **registret**. Om du redan har registrerat loggar du in på företags webbplatsen för Azure AD SAML Toolkit med de registrerade inloggnings uppgifterna.

    ![Azure AD SAML Toolkit-register](./media/saml-toolkit-tutorial/register.png)

1. Klicka på **SAML-konfigurationen**.

    ![SAML-konfiguration för Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klicka på **Skapa**.

    ![Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/createsso.png)

1. Utför följande steg på sidan **konfiguration av SAML SSO** :

    ![Azure AD SAML Toolkit skapa SSO-konfiguration](./media/saml-toolkit-tutorial/fill-details.png)

    1. I text rutan **inloggnings-URL** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    1. I text rutan för **Azure AD-identifierare** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    1. I textrutan **Utloggnings-URL** klistrar du värdet för **Utloggnings-URL**, som du har kopierat från Azure-portalen.

    1. Klicka på **Välj fil** och överför **certifikat filen (RAW)** som du har laddat ned från Azure Portal.

    1. Klicka på **Skapa**.

    1. Kopiera inloggnings-URL, identifierare och ACS URL-värden på sidan för konfiguration av SAML Toolkit SSO och klistra in i respekterade text rutor i **avsnittet grundläggande SAML-konfiguration** i Azure Portal.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Skapa test användare för Azure AD SAML Toolkit

I det här avsnittet skapas en användare som heter B. Simon i Azure AD SAML Toolkit. Skapa en test användare i verktyget genom att registrera en ny användare och ange all användar information. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till SAML Toolkit inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till SAML Toolkit inloggnings-URL direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen SAML Toolkit på åtkomst panelen, bör du loggas in automatiskt till det SAML-verktyg som du konfigurerade SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Azure AD SAML Toolkit kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)