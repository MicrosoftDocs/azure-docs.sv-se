---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EBSCO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EBSCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2395f3c8b46f69105a81cd2d866ee4e330f4791e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555509"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EBSCO

I den här självstudien får du lära dig hur du integrerar EBSCO med Azure Active Directory (Azure AD). När du integrerar EBSCO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EBSCO.
* Gör det möjligt för användarna att logga in automatiskt till EBSCO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EBSCO för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EBSCO stöder **SP-och IDP** -INITIERAd SSO
* EBSCO stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-ebsco-from-the-gallery"></a>Lägga till EBSCO från galleriet

Om du vill konfigurera integreringen av EBSCO i Azure AD måste du lägga till EBSCO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Ebsco** i sökrutan.
1. Välj **Ebsco** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Konfigurera och testa enkel inloggning med Azure AD för EBSCO

Konfigurera och testa Azure AD SSO med EBSCO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EBSCO.

Om du vill konfigurera och testa Azure AD SSO med EBSCO, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Ebsco SSO](#configure-ebsco-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Ebsco test User](#create-ebsco-test-user)** -om du vill ha en motsvarighet till B. Simon i Ebsco som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Ebsco** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    I textrutan **Identifierare** skriver du en URL: `pingsso.ebscohost.com`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Ebsco client support team](mailto:support@ebsco.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    o   **unika element:**  

    o   **Custid** = ange unikt Ebsco kund-ID 

    o   **Profile** = klienter kan skräddarsy länken för att dirigera användare till en speciell profil (beroende på vad de köper från Ebsco). De kan ange ett angivet profil-ID. Huvud-ID: t är EDS (EBSCO Discovery service) och eHost (EBSOCOhost-databaser). Instruktioner för samma anges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. EBSCO-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

    > [!Note]
    > Namnattributet **är** obligatoriskt och det mappas med namn- **ID-värdet** i Ebsco-programmet. Detta läggs till som standard, så du behöver inte lägga till det manuellt.

1. Utöver ovan förväntar sig EBSCO-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | FirstName   | user.givenname |
    | LastName   | user.surname |
    | E-post   | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Ebsco** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EBSCO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Ebsco**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-ebsco-sso"></a>Konfigurera EBSCO SSO

Om du vill konfigurera enkel inloggning på **Ebsco** sida måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för Ebsco](mailto:support@ebsco.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-ebsco-test-user"></a>Skapa EBSCO test användare

När det gäller EBSCO är användar etableringen automatisk.

**Gör följande för att etablera ett användarkonto:**

Azure AD överför nödvändiga data till EBSCO-program. Användar etableringen för EBSCO kan vara automatisk eller kräver ett formulär med en tids period. Det beror på om klienten har massor av redan befintliga EBSCOhost-konton med personliga inställningar sparade. Samma sak kan diskuteras med [Ebsco support team](mailto:support@ebsco.com) under implementeringen. Oavsett hur behöver klienten inte skapa några EBSCOhost-konton innan testningen.

   > [!Note]
   > Du kan automatisera EBSCOhost användar etablering/anpassning. Kontakta [Ebsco support team](mailto:support@ebsco.com) om just-in-Time-etablering av användare.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

1. När du klickar på panelen EBSCO på åtkomst panelen, bör du få automatiskt inloggad till ditt EBSCO-program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

1. När du har loggat in på programmet klickar du på knappen **Logga in** i det övre högra hörnet.

    ![EBSCO-inloggningen i program listan](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Du får en eng ång slö period för att para ihop institutionella/SAML-inloggningen med en **länk ditt befintliga MyEBSCOhost-konto till ditt institutions konto nu** eller **skapa ett nytt MyEBSCOhost-konto och länka det till ditt institutions konto**. Kontot används för anpassning av EBSCOhost-programmet. Välj alternativet **skapa ett nytt konto** så ser du att formuläret för anpassningen har slutförts med värdena från SAML-svaret som visas på skärm bilden nedan. Klicka på **Fortsätt** för att spara det här alternativet.
    
     ![EBSCO-användaren i program listan](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. När du har slutfört ovanstående konfiguration rensar du cookies/cache och loggar in igen. Du behöver inte logga in manuellt igen och anpassnings inställningarna sparas.

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova EBSCO med Azure AD](https://aad.portal.azure.com/)