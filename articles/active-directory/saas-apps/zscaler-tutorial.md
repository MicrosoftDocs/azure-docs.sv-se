---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Zscaler | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.openlocfilehash: e22d86f85b82af1109e79789073d7e442c51cf24
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545768"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler med Azure Active Directory (Azure AD). När du integrerar Zscaler med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler.
* Gör det möjligt för användarna att logga in automatiskt till Zscaler med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zscaler för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Zscaler stöder **SP** -INITIERAd SSO
* Zscaler stöder **just-in-Time** User-etablering

## <a name="adding-zscaler-from-the-gallery"></a>Lägga till Zscaler från galleriet

Om du vill konfigurera integreringen av Zscaler i Azure AD måste du lägga till Zscaler från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Zscaler** i sökrutan.
1. Välj **Zscaler** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zscaler"></a>Konfigurera och testa enkel inloggning med Azure AD för Zscaler

Konfigurera och testa Azure AD SSO med Zscaler med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler.

Om du vill konfigurera och testa Azure AD SSO med Zscaler, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ZSCALER SSO](#configure-zscaler-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Zscaler test User](#create-zscaler-test-user)** -om du vill ha en motsvarighet till B. Simon i Zscaler som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Zscaler** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyname>.zscaler.net`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zscaler client support team](https://www.zscaler.com/company/contact) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ditt Zscaler-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig Zscaler-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut |
    | ---------| ------------ |
    | memberOf     | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar en Roll i Azure AD

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Zscaler** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **Zscaler**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler**.

    ![Zscaler-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du användaren **Britta Simon** i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_users.png)

6. I dialogrutan **Välj roll** väljer du lämplig användarroll i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.

    ![image](./media/zscaler-tutorial/tutorial_zscaler_assign.png)

## <a name="configure-zscaler-sso"></a>Konfigurera Zscaler SSO

1. Om du vill automatisera konfigurationen i Zscaler måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **installations Zscaler** för att dirigera dig till Zscaler-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera SSO](common/setup-sso.png)

1. Om du vill konfigurera Zscaler manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Zscaler-företags webbplats som administratör och utför följande steg:

1. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:

    ![Administration](./media/zscaler-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

1. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  

    ![Hantera användare & autentisering](./media/zscaler-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    ex. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    h. Klicka på **Spara**.

1. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Administration](./media/zscaler-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

1. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.

    ![Internet alternativ](./media/zscaler-tutorial/ic769492.png "Internet alternativ")

1. Klicka på fliken **Anslutningar**.
  
    ![Anslutningar](./media/zscaler-tutorial/ic769493.png "Anslutningar")

1. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

1. I avsnittet Proxyserver utför du följande steg:   

    ![Proxyserver](./media/zscaler-tutorial/ic769494.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. I text rutan adress skriver du **Gateway.Zscaler.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

1. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.

### <a name="create-zscaler-test-user"></a>Skapa Zscaler test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Zscaler. Zscaler stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Zscaler skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [Zscaler support team](https://www.zscaler.com/company/contact)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler på åtkomst panelen, bör du loggas in automatiskt på den Zscaler som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Zscaler med Azure AD](https://aad.portal.azure.com/)
