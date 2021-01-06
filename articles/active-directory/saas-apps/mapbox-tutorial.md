---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Mapbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mapbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: fcee52d585d465b06e7b0dc8d70dc35fb66d2615
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916175"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Mapbox

I den här självstudien får du lära dig hur du integrerar Mapbox med Azure Active Directory (Azure AD). När du integrerar Mapbox med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Mapbox.
* Gör det möjligt för användarna att logga in automatiskt till Mapbox med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Mapbox för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Mapbox stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-mapbox-from-the-gallery"></a>Lägga till Mapbox från galleriet

Om du vill konfigurera integreringen av Mapbox i Azure AD måste du lägga till Mapbox från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Mapbox** i sökrutan.
1. Välj **Mapbox** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Konfigurera och testa Azure AD SSO för Mapbox

Konfigurera och testa Azure AD SSO med Mapbox med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Mapbox.

Utför följande steg för att konfigurera och testa Azure AD SSO med Mapbox:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera MAPBOX SSO](#configure-mapbox-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Mapbox test User](#create-mapbox-test-user)** -om du vill ha en motsvarighet till B. Simon i Mapbox som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Mapbox** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Mapbox-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Mapbox-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn   |  Källattribut|
    | -----|--------- |
    | roll | user.assignedroles |
    | | |

    > [!NOTE]
    > Information om hur du konfigurerar roller i Azure AD finns [här](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera Mapbox** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Mapbox.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Mapbox**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-mapbox-sso"></a>Konfigurera Mapbox SSO

1. Logga in på Mapbox som administratör i ett annat webbläsarfönster.

1. Klicka på fliken **Inställningar**.

    ![Fliken Inställningar för Mapbox](./media/mapbox-tutorial/configure1.png)

1. Klicka på fliken **säkerhet** i det vänstra navigerings fönstret.

    ![Fliken Mapbox säkerhet](./media/mapbox-tutorial/configure2.png)

1. Klicka på **Redigera enkel inloggning**.

    ![Mapbox redigera enkel inloggning](./media/mapbox-tutorial/configure3.png)

1. Rulla ned till **steg 3: Konfigurera SAML enkel inloggning för Mapbox** och utför följande steg:

    ![Mapbox-konfiguration](./media/mapbox-tutorial/configure4.png)

    1. I text rutan för **inloggnings-URL för IDP** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    1. I text rutan **utfärdar-ID** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    1. Öppna den hämtade **certifikat filen (RAW)** från Azure Portal i anteckningar och kopiera innehållet i certifikat filen och klistra in den i rutan **X. 509-certifikat** .

    1. Klicka på **Spara inställningar för enkel inloggning**.

### <a name="create-mapbox-test-user"></a>Skapa Mapbox test användare

I det här avsnittet skapar du en användare som heter Britta Simon i Mapbox. Arbeta med [Mapbox support team](mailto:help@mapbox.com) för att lägga till användare i Mapbox-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den Mapbox som du har konfigurerat SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Mapbox i Mina appar, bör du loggas in automatiskt på den Mapbox som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Mapbox kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).