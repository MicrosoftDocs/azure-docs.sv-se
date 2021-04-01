---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Fivetran | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fivetran.
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
ms.openlocfilehash: 88f6da7e43e269816c7e4ae2fd7bbc3f5e26522b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453101"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fivetran"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Fivetran

I den här självstudien får du lära dig hur du integrerar Fivetran med Azure Active Directory (Azure AD). När du integrerar Fivetran med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Fivetran.
* Gör det möjligt för användarna att logga in automatiskt till Fivetran med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett Fivetran-konto.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Fivetran stöder **IDP** INITIERAd SSO
* Fivetran stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-fivetran-from-the-gallery"></a>Lägg till Fivetran från galleriet

Om du vill konfigurera integreringen av Fivetran i Azure AD måste du lägga till Fivetran från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Fivetran** i sökrutan.
1. Välj **Fivetran** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-fivetran"></a>Konfigurera och testa Azure AD SSO för Fivetran

Konfigurera och testa Azure AD SSO med Fivetran med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Fivetran.

Utför följande steg för att konfigurera och testa Azure AD SSO med Fivetran:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FIVETRAN SSO](#configure-fivetran-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Fivetran test User](#create-fivetran-test-user)** -om du vill ha en motsvarighet till B. Simon i Fivetran som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Fivetran** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. Fivetran-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Fivetran-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | -------------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Fivetran** kopierar du **inloggnings-URL: en** och **Azure AD Identifier** -värden.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Fivetran.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Fivetran**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har ställts in för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-fivetran-sso"></a>Konfigurera Fivetran SSO

I det här avsnittet ska du konfigurera enkel inloggning på **Fivetran** -sidan.

1. Logga in på ditt Fivetran-konto som konto ägare i ett annat webbläsarfönster.
1. Välj pilen i det övre vänstra hörnet i fönstret och välj sedan **Hantera konto** i den nedrullningsbara listan.

   ![Skärm bild som visar meny alternativet hantera konto valt.](media/fivetran-tutorial/fivetran-1.png)

1. Gå till **SAML config** -avsnittet på sidan **Inställningar** .

   ![Skärm bild som visar SAML config-fönstret med konfigurations alternativ markerat.](media/fivetran-tutorial/fivetran-2.png)

   1. För **Aktivera SAML-autentisering** väljer du **på**.
   1. I **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en** som du kopierade från Azure Portal.
   1. I **utfärdare** klistrar du in värdet för **Azure AD-identifierare** som du kopierade från Azure Portal.
   1. Öppna den hämtade certifikat filen i en text redigerare, kopiera certifikatet till Urklipp och klistra in det i text rutan **publikt certifikat** .
   1. Välj **Spara konfiguration**.

### <a name="create-fivetran-test-user"></a>Skapa Fivetran test användare

I det här avsnittet skapas en användare som heter B. Simon i Fivetran. Fivetran stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Fivetran skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Fivetran som du har konfigurerat SSO för. 

2. Du kan använda Microsoft Access-panelen. När du klickar på panelen Fivetran på åtkomst panelen, bör du loggas in automatiskt på den Fivetran som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Fivetran kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).