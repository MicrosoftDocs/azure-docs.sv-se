---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CloudPassage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CloudPassage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: 34e36be8ee9444b5612901439aa5dca4771f2dda
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551697"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CloudPassage

I den här självstudien får du lära dig hur du integrerar CloudPassage med Azure Active Directory (Azure AD). När du integrerar CloudPassage med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CloudPassage.
* Gör det möjligt för användarna att logga in automatiskt på CloudPassage med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CloudPassage för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CloudPassage har stöd för **SP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-cloudpassage-from-the-gallery"></a>Lägga till CloudPassage från galleriet

För att konfigurera integreringen av CloudPassage i Azure AD måste du lägga till CloudPassage från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **CloudPassage** i sökrutan.
1. Välj **CloudPassage** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>Konfigurera och testa enkel inloggning med Azure AD för CloudPassage

Konfigurera och testa Azure AD SSO med CloudPassage med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CloudPassage.

Om du vill konfigurera och testa Azure AD SSO med CloudPassage, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CLOUDPASSAGE SSO](#configure-cloudpassage-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa CloudPassage test User](#create-cloudpassage-test-user)** -om du vill ha en motsvarighet till B. Simon i CloudPassage som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **CloudPassage** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

     a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://portal.cloudpassage.com/saml/init/accountid`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://portal.cloudpassage.com/saml/consume/accountid`. Du kan hämta värdet för det här attributet genom att klicka på **SSO Setup documentation** (Dokumentation för konfiguration av enkel inloggning) i avsnittet **Inställningar för enkel inloggning** i CloudPassage-portalen.

    ![Konfigurera enkel inloggning](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [kundsupporten för CloudPassage](https://www.cloudpassage.com/company/contact/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. CloudPassage-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig CloudPassage-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | ---------------| --------------- |
    | förnamn |user.givenname |
    | efternamn |user.surname |
    | e-post |user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera CloudPassage** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CloudPassage.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **CloudPassage**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cloudpassage-sso"></a>Konfigurera CloudPassage SSO

1. I ett annat webbläsarfönster loggar du in på din CloudPassage-företagsplats som administratör.

1. På menyn längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Konfigurera enkel inloggning][12]

1. Klicka på fliken **Autentiseringsinställningar**. 
   
    ![Konfigurera enkel inloggning][13]

1. I avsnittet **Inställningar för enkel inloggning** utför du följande steg: 
   
    ![Konfigurera enkel inloggning][14]

    a. Markera kryssrutan **Enable Single sign-on(SSO)(SSO Setup Documentation)** (Aktivera enkel inloggning (SSO)(Dokumentation för konfiguration av enkel inloggning)).
    
    b. Klistra in **Azure AD-identifieraren** i text rutan för **SAML-utfärdaren** .
  
    c. Klistra in **inloggnings-URL** i textrutan **SAML endpoint URL** (URL för SAML-slutpunkt).
  
    d. Klistra in **utloggnings-URL** i textrutan **Logout landing page** (landningssida för utloggning).
  
    e. Öppna det nedladdade certifikatet i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **X.509 certificate** (X.509-certifikat).
  
    f. Klicka på **Spara**.

### <a name="create-cloudpassage-test-user"></a>Skapa CloudPassage-testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i CloudPassage.

**Utför följande steg för att skapa en användare som heter B. Simon i CloudPassage:**

1. Logga in på din **CloudPassage**-företagsplats som administratör. 

1. I verktygsfältet längst upp klickar du på **Inställningar** och sedan på **Webbplatsadministration**. 
   
    ![Skapa en CloudPassage-testanvändare][22] 

1. Klicka på fliken **Användare** och sedan på **Lägg till ny användare**. 
   
    ![Skapa en CloudPassage-testanvändare][23]

1. I avsnittet **Lägg till ny användare** utför du följande steg: 
   
    ![Skapa en CloudPassage-testanvändare][24]
    
    a. I textrutan **Förnamn** skriver du Britta. 
  
    b. I textrutan **Efternamn** skriver du Simon.
  
    c. I textrutorna **Användarnamn**, **E-post** och **Ange e-postadressen igen** skriver du Brittas användarnamn i Azure AD.
  
    d. För **Åtkomsttyp** väljer du **Enable Halo Portal Access** (Aktivera Halo Portal-åtkomst).
  
    e. Klicka på **Lägg till**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på CloudPassage-panelen i åtkomstpanelen bör du automatiskt loggas in på CloudPassage som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova CloudPassage med Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

