---
title: 'Självstudie: Azure Active Directory integrering med EY GlobalOne | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EY GlobalOne.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 7079ffd5cb2fe643d93dea0f5c9384a7931dc8a6
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550691"
---
# <a name="tutorial-integrate-ey-globalone-with-azure-active-directory"></a>Självstudie: integrera EY GlobalOne med Azure Active Directory

I den här självstudien får du lära dig att integrera EY-GlobalOne med Azure Active Directory (Azure AD). När du integrerar EY-GlobalOne med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EY-GlobalOne.
* Gör det möjligt för användarna att logga in automatiskt till EY GlobalOne med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EY GlobalOne-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.
* EY GlobalOne stöder **SP-och IDP** -INITIERAd SSO 
* EY GlobalOne stöder **just-in-Time** User-etablering.
* När du har konfigurerat EY GlobalOne kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-ey-globalone-from-the-gallery"></a>Lägga till EY GlobalOne från galleriet

Om du vill konfigurera integreringen av EY-GlobalOne i Azure AD måste du lägga till EY GlobalOne från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ey GlobalOne** i sökrutan.
1. Välj **ey GlobalOne** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>Konfigurera och testa Azure AD SSO för EY-GlobalOne

Konfigurera och testa Azure AD SSO med EY GlobalOne med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EY GlobalOne.

Om du vill konfigurera och testa Azure AD SSO med EY GlobalOne slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. **[Konfigurera ey GlobalOne](#configure-ey-globalone)** för att konfigurera SSO-inställningarna på program sidan.
    * **[Skapa ey GlobalOne test User](#create-ey-globalone-test-user)** för att ha en motsvarighet till B. Simon i ey GlobalOne som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ey GlobalOne** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. EY GlobalOne-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig EY GlobalOne-program att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |
    | Företag | `<YOUR COMPANY NAME>` |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera ey GlobalOne** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B. Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EY GlobalOne.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ey GlobalOne**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-ey-globalone"></a>Konfigurera EY-GlobalOne

Om du vill konfigurera enkel inloggning på **ey GlobalOne** -sidan måste du skicka det hämtade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [ey GlobalOne support team](mailto:globalone.support@ey.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ey-globalone-test-user"></a>Skapa EY GlobalOne-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i EY GlobalOne. EY GlobalOne stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i EY GlobalOne skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

När du väljer panelen EY GlobalOne på åtkomst panelen, bör du loggas in automatiskt på EY-GlobalOne som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)