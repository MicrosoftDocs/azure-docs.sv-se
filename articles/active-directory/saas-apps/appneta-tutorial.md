---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med prestanda övervakaren för AppNeta | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppNeta Performance Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: a00087d557e4244a6551b84623e2c34461623eb3
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97916549"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med prestanda övervakaren AppNeta

I den här självstudien får du lära dig att integrera AppNeta Performance Monitor med Azure Active Directory (Azure AD). När du integrerar AppNeta prestanda övervakaren med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AppNeta prestanda övervakaren.
* Gör det möjligt för användarna att logga in automatiskt för att AppNeta prestanda övervakaren med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* AppNeta prestanda Övervakare för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* AppNeta Performance Monitor stöder **SP**-initierad enkel inloggning

* AppNeta Performance Monitor stöder **just-in-time**-användaretablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>Lägga till AppNeta Performance Monitor från galleriet

För att konfigurera integrering av AppNeta Performance Monitor med Azure AD behöver du lägga till AppNeta Performance Monitor från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **AppNeta Performance Monitor** i sökrutan.
1. Välj **AppNeta prestanda övervakaren** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-appneta-performance-monitor"></a>Konfigurera och testa Azure AD SSO för AppNeta prestanda övervakaren

Konfigurera och testa Azure AD SSO med prestanda övervakaren AppNeta med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i prestanda övervakaren i AppNeta.

Utför följande steg för att konfigurera och testa Azure AD SSO med AppNeta prestanda övervakaren:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AppNeta prestanda övervakaren SSO](#configure-appneta-performance-monitor-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa AppNeta Performance Monitor test User](#create-appneta-performance-monitor-test-user)** -för att få en motsvarighet till B. Simon i AppNeta prestanda övervakaren som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **AppNeta Performance Monitor** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.pm.appneta.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [kundsupporten för AppNeta Performance Monitor](mailto:support@appneta.com) och be om det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AppNeta för prestanda övervakaren förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig AppNeta prestanda övervaknings program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Namn | Källattribut|
    | --------| ----------------|
    | firstName| user.givenname|
    | lastName| user.surname|
    | e-post| user.userprincipalname|
    | name| user.userprincipalname|
    | grupper  | user.assignedroles |
    | phone| user.telephonenumber |
    | title| user.jobtitle|
    | | |

    > [!NOTE]
    > **grupper** refererar till den säkerhetsgrupp i Appneta som mappas till en **roll** i Azure AD. I [det här](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps#app-roles-ui) dokumentet beskrivs hur du skapar anpassade roller i Azure AD.

    1. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    1. Lämna **Namnrymd** tom.

    1. Välj Källa som **Attribut**.

    1. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    1. Klicka på **OK**

    1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera AppNeta i prestanda övervakaren** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till prestanda övervakaren AppNeta.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **AppNeta Performance Monitor**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .
## <a name="configure-appneta-performance-monitor-sso"></a>Konfigurera AppNeta prestanda övervakaren SSO

För att konfigurera enkel inloggning på **AppNeta Performance Monitor**-sidan behöver du skicka nedladdade **XML-federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för AppNeta Performance Monitor](mailto:support@appneta.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-appneta-performance-monitor-test-user"></a>Skapa AppNeta Performance Monitor-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i AppNeta Performance Monitor. AppNeta Performance Monitor stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i AppNeta Performance Monitor skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [AppNeta Performance Monitor support team](mailto:support@appneta.com).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till AppNeta Performance Monitor-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till AppNetas inloggnings-URL för prestanda övervakaren direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen prestanda övervakaren AppNeta i Mina appar omdirigeras den till AppNeta prestanda övervaknings-URL: en. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat prestanda övervakaren för AppNeta kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).