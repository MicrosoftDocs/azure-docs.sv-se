---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn-höjning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och LinkedIn-höjning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 1f4569a45b9ed0eee7c375e660df97925335313b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549814"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med LinkedIn-höjning

I den här självstudien får du lära dig hur du integrerar LinkedIn-höjning med Azure Active Directory (Azure AD). När du integrerar LinkedIn-höjning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till LinkedIn-höjning.
* Gör det möjligt för användarna att logga in automatiskt till LinkedIn-höjning med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* LinkedIn-höjning av en prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.



* LinkedIn-höjning stöder **SP-och IDP** -INITIERAd SSO
* LinkedIn-höjning stöder **just-in-Time** User-etablering
* LinkedIn-höjning stöder [ **Automatisk** användar etablering](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Det går inte att lägga till LinkedIn-höjning från galleriet

Om du vill konfigurera integrering av LinkedIn-höjning i Azure AD måste du lägga till LinkedIn-höjning från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **LinkedIn-höjning** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **LinkedIn Höj** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Konfigurera och testa enkel inloggning i Azure AD för LinkedIn-höjning

Konfigurera och testa Azure AD SSO med LinkedIn-höjning med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i LinkedIn-höjning.

Om du vill konfigurera och testa Azure AD SSO med LinkedIn-höjning slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LinkedIn-Höj SSO](#configure-linkedin-elevate-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa LinkedIn-Höj test användare](#create-linkedin-elevate-test-user)** – för att få en motsvarighet till B. Simon på LinkedIn upphöjt till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **LinkedIn-höjning** av program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du **entitets-ID** -värdet. du kopierar entitets-ID-värde från LinkedIn-portalen som beskrivs senare i den här självstudien.

    b. I text rutan **svars-URL** anger du URL-värdet för **intyg om konsument åtkomst (ACS)** , du kommer att kopiera URL-värdet för intyg om intygs åtkomst (ACS) från LinkedIn-portalen som beskrivs senare i den här självstudien.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. LinkedIn-höjning förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. LinkedIn-höjning förväntar sig att NameIdentifier mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen Redigera och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar LinkedIn-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | -------| -------------|
    | avdelning | user.department |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera LinkedIn-höjning** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till LinkedIn-höjning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **LinkedIn-höjning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-linkedin-elevate-sso"></a>Konfigurera LinkedIn-Höj SSO

1. Logga in på din LinkedIn-klient som administratör i ett annat webbläsarfönster.

1. Klicka på **Globala inställningar** under **Inställningar** i **Kontocenter**. Välj också alternativet **öka och Höj behörighet för AAD-test** i list rutan.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klicka på **eller klicka här för att läsa in och kopiera enskilda fält från formuläret** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopiera **entitets-ID** och klistra in det i text rutan **identifierare** i den **grundläggande SAML-konfigurationen** i Azure Portal.

    b. Kopiera **intyg om kontroll konsument åtkomst (ACS) URL** och klistra in den i text rutan **SVARs-URL** i den **grundläggande SAML-konfigurationen** i Azure Portal.

1. Gå till avsnittet **LinkedIn-administratörsinställningar**. Ladda upp XML-filen som du har laddat ned från Azure Portal genom att klicka på alternativet överför XML-fil.

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Aktivera enkel inloggning genom att klicka på **På**. SSO-status ändras från **inte ansluten** till **ansluten**

    ![Konfigurera enkel inloggning](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Skapa LinkedIn-höjning av test användare

LinkedIn-höjning-applikation stöder just-in-Time-etablering och efter att användare av autentisering skapas i programmet automatiskt. På sidan administratörs inställningar på portalen på LinkedIn-höjning vänder du växeln **automatiskt till att tilldela licenser** till aktiv just-in-Time-etablering. då tilldelas även en licens till användaren. LinkedIn-höjning har även stöd för automatisk användar etablering. du hittar mer information [här](linkedinelevate-provisioning-tutorial.md) om hur du konfigurerar automatisk användar etablering.

   ![Skapa en testanvändare för Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen LinkedIn-höjning i åtkomst panelen, bör du loggas in automatiskt på den LinkedIn-höjning som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova LinkedIn-höjning med Azure AD](https://aad.portal.azure.com/)

