---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PerimeterX | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PerimeterX.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2020
ms.author: jeedes
ms.openlocfilehash: 29ffaaa1b1b6efbcd5523a76018c92645e13d187
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181806"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeterx"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PerimeterX

I den här självstudien får du lära dig hur du integrerar PerimeterX med Azure Active Directory (Azure AD). När du integrerar PerimeterX med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PerimeterX.
* Gör det möjligt för användarna att logga in automatiskt till PerimeterX med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PerimeterX för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.


* PerimeterX stöder **IDP** INITIERAd SSO

## <a name="adding-perimeterx-from-the-gallery"></a>Lägga till PerimeterX från galleriet

Om du vill konfigurera integreringen av PerimeterX i Azure AD måste du lägga till PerimeterX från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **PerimeterX** i sökrutan.
1. Välj **PerimeterX** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-perimeterx"></a>Konfigurera och testa Azure AD SSO för PerimeterX

Konfigurera och testa Azure AD SSO med PerimeterX med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PerimeterX.

Utför följande steg för att konfigurera och testa Azure AD SSO med PerimeterX:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PERIMETERX SSO](#configure-perimeterx-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PerimeterX test User](#create-perimeterx-test-user)** -om du vill ha en motsvarighet till B. Simon i PerimeterX som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **PerimeterX** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. PerimeterX-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig PerimeterX-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut|
    | ------------ | --------- |
    | firstName | user.givenname |
    | lastName  | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera PerimeterX** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PerimeterX.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **PerimeterX**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-perimeterx-sso"></a>Konfigurera PerimeterX SSO

1. Logga in på PerimeterX-konsolen med administratörs behörighet.

1. Gå till **Admin >-konton** 

    ![PerimeterX SSO](./media/perimeterx-tutorial/accounts.png)

1. Klicka på **Redigera**

4.  I dialog rutan Redigera konto utför du följande steg.

    ![PerimeterX SSO Edit-konto](./media/perimeterx-tutorial/saml-sso.png)

    a.  Markera **aktivera enkel Sign-On (SSO)**

    b.  Välj **Azure SAML**.

    c.  I text rutan **SAML-slutpunkt** klistrar du in värdet för **inloggnings-URL** som du kopierade Azure Portal.

    d. I text rutan **utfärdare** klistrar du in värdet för Azure AD-identifieraren som kopierats från Azure Portal.

    e. Öppna det hämtade **certifikatet (base64)** från Azure Portal i anteckningar och klistra in innehållet i text rutan **X. 509-certifikat** .

    f. Klicka på **Spara ändringar**

### <a name="create-perimeterx-test-user"></a>Skapa PerimeterX test användare

I [guiden PerimeterX hanterings användare](https://docs.perimeterx.com/pxconsole/docs/managing-users) finns anvisningar om hur du skapar PerimeterX-test användaren.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

1. Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den PerimeterX som du har konfigurerat SSO för.

1. Du kan använda Microsoft Access-panelen. När du klickar på panelen PerimeterX på åtkomst panelen, bör du loggas in automatiskt på den PerimeterX som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat PerimeterX kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).