---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med RedBrick-hälsa | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RedBrick Health.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: jeedes
ms.openlocfilehash: c6acbd115dfb6a4411add844f91cff921c2a2371
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548794"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-redbrick-health"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med RedBrick-hälsa

I den här självstudien får du lära dig hur du integrerar RedBrick-hälsa med Azure Active Directory (Azure AD). När du integrerar RedBrick-hälsa med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till RedBrick-hälsa.
* Gör det möjligt för användarna att logga in automatiskt för att RedBrick hälsa med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* RedBrick-prenumeration med enkel inloggning (SSO) för hälso tillstånd.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* RedBrick Health stöder **IDP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-redbrick-health-from-the-gallery"></a>Lägga till RedBrick Health från galleriet

För att konfigurera integreringen av RedBrick Health till Azure AD behöver du lägga till RedBrick Health från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **RedBrick Health** i sökrutan.
1. Välj **RedBrick Health** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-redbrick-health"></a>Konfigurera och testa enkel inloggning med Azure AD för RedBrick-hälsa

Konfigurera och testa Azure AD SSO med RedBrick-hälsa med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RedBrick-hälsa.

Om du vill konfigurera och testa Azure AD SSO med RedBrick-hälsa slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera RedBrick Health SSO](#configure-redbrick-health-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa RedBrick Health test User](#create-redbrick-health-test-user)** -för att få en motsvarighet till B. Simon i RedBrick-hälsa som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **RedBrick Health** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. Skriv en URL i text rutan **identifierare** : `https://www.redbrickhealth.com`

    b. Skriv en URL i textrutan **Svars-URL**: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`

    För Produktionsmiljö: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`

    > [!NOTE]
    > Värdet för Vidarebefordransstatus är inte verkligt. Uppdatera det här värdet med den faktiska vidarebefordransstatusen. Hämta det här värdet genom att kontakta [supportteamet för RedBrick Health-klienten](https://home.redbrickhealth.com/contact/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. RedBrick Health-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Name | Källattribut|
    | ----------- | --------- |
    | huvudnamn | ********** |
    | klient-ID | ********** |
    | deltagar-ID | ********** |

    > [!NOTE]
    > De här värdena anges endast i referenssyfte. Du behöver definiera attributen enligt organisationens krav. Kontakta [supportteamet för RedBrick Health-klienten](https://home.redbrickhealth.com/contact/) om du behöver mer information om de anspråk som krävs.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera RedBrick-hälsa** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RedBrick-hälsa.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **RedBrick Health**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-redbrick-health-sso"></a>Konfigurera RedBrick Health SSO

För att konfigurera enkel inloggning på **RedBrick Health**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för RedBrick Health](https://home.redbrickhealth.com/contact/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-redbrick-health-test-user"></a>Skapa RedBrick Health-testanvändare

I det här avsnittet skapar du en användare som heter B. Simon i RedBrick-hälsa. Kontakta [supportteamet för RedBrick Health](https://home.redbrickhealth.com/contact/) för att lägga till användarna i RedBrick Health-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på RedBrick Health-panelen i åtkomstpanelen bör du automatiskt loggas in på RedBrick Health som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa RedBrick hälsa med Azure AD](https://aad.portal.azure.com/)