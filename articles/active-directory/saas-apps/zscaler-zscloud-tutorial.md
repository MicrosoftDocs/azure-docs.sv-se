---
title: 'Självstudie: Azure Active Directory integrering med Zscaler ZSCloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler ZSCloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 605f033ed48dd79fd164aabd95e326a6467d0ecd
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726251"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Självstudie: Azure Active Directory integrering med Zscaler ZSCloud

I den här självstudien får du lära dig att integrera Zscaler-ZSCloud med Azure Active Directory (Azure AD). När du integrerar Zscaler-ZSCloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler-ZSCloud.
* Gör det möjligt för användarna att logga in automatiskt till Zscaler ZSCloud med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Zscaler ZSCloud behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Zscaler ZSCloud-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler ZSCloud stöder **SP** -INITIERAd SSO

* Zscaler ZSCloud stöder **just-in-Time** User-etablering

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Lägga till Zscaler ZSCloud från galleriet

Om du vill konfigurera integreringen av Zscaler-ZSCloud i Azure AD måste du lägga till Zscaler ZSCloud från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Zscaler ZSCloud** i sökrutan.
1. Välj **Zscaler ZSCloud** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Konfigurera och testa Azure AD SSO för Zscaler-ZSCloud

Konfigurera och testa Azure AD SSO med Zscaler ZSCloud med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler ZSCloud.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zscaler ZSCloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Zscaler ZSCLOUD SSO](#configure-zscaler-zscloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Zscaler ZSCloud test User](#create-zscaler-zscloud-test-user)** – om du vill ha en motsvarighet till B. Simon i Zscaler ZSCloud som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Zscaler ZSCloud** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du den URL som användarna använder för att logga in till ditt ZScaler ZSCloud-program.

    > [!NOTE]
    > Du måste uppdatera värdet med den faktiska Sign-On-URL: en. Kontakta [Zscaler ZSCloud client support team](https://help.zscaler.com/) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ditt Zscaler ZSCloud-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![Skärm bild som visar användarattribut med redigerings ikonen vald.](common/edit-attribute.png)

6. Utöver ovan förväntar sig Zscaler ZSCloud-program att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Name | Källattribut |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar användar anspråk med alternativet att lägga till ett nytt anspråk.](common/new-save-attribute.png)

    ![Skärm bild som visar dialog rutan hantera användar anspråk där du kan ange de värden som beskrivs.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.
    
    f. Klicka på **Spara**.

    > [!NOTE]
    > Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta mer om hur du konfigurerar roller i Azure AD.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler ZSCloud** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler ZSCloud.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **Zscaler ZSCloud**.
2. I listan program väljer du **Zscaler ZSCloud**.
3. På menyn till vänster väljer du **Användare och grupper**.
4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.
5. I dialogrutan **Användare och grupper** väljer du användaren **Britta Simon** i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![Skärm bild som visar dialog rutan användare och grupper där du kan välja en användare.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_users.png)

6. I dialogrutan **Välj roll** väljer du lämplig användarroll i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

    ![Skärm bild som visar dialog rutan Välj roll där du kan välja en användar roll.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_roles.png)

7. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.

    ![Skärm bild som visar dialog rutan Lägg till tilldelning där du kan välja tilldela.](./media/zscaler-zscloud-tutorial/tutorial_zscalerzscloud_assign.png)

    >[!NOTE]
    >Standard åtkomst rollen stöds inte eftersom den här åtgärden bryter etableringen, så att standard rollen inte kan väljas när användaren tilldelas.

## <a name="configure-zscaler-zscloud-sso"></a>Konfigurera Zscaler ZSCloud SSO

1. Om du vill automatisera konfigurationen i Zscaler ZSCloud måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Setup Zscaler ZSCloud** kommer dig direkt till Zscaler ZSCloud-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler ZSCloud. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera SSO](common/setup-sso.png)

3. Om du vill konfigurera Zscaler ZSCloud manuellt öppnar du ett nytt webbläsarfönster och loggar in på Zscaler ZSCloud företags plats som administratör och utför följande steg:

4. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Skärm bild som visar Zscaler-webbplatsen med steg enligt beskrivningen.](./media/zscaler-zscloud-tutorial/ic800206.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

5. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  
            
    ![Hantera användare & autentisering](./media/zscaler-zscloud-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    ex. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    h. Klicka på **Spara**.

6. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Skärm bild som visar dialog rutan Konfigurera användarautentisering med aktivera valt.](./media/zscaler-zscloud-tutorial/ic800207.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.   
    
     ![Internet alternativ](./media/zscaler-zscloud-tutorial/ic769492.png "Internet alternativ")

3. Klicka på fliken **Anslutningar**.   
  
     ![Anslutningar](./media/zscaler-zscloud-tutorial/ic769493.png "Anslutningar")

4. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

5. I avsnittet Proxyserver utför du följande steg:   
   
    ![Proxyserver](./media/zscaler-zscloud-tutorial/ic769494.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. I text rutan adress skriver du **Gateway. Zscaler-ZSCloud.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

6. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.


### <a name="create-zscaler-zscloud-test-user"></a>Skapa Zscaler ZSCloud-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Zscaler ZSCloud. Zscaler ZSCloud stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Zscaler ZSCloud skapas en ny efter autentiseringen.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [Zscaler ZSCloud support team](https://help.zscaler.com/).

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Zscaler ZSCloud-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Zscaler ZSCloud-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Zscaler ZSCloud i Mina appar omdirigeras det till Zscaler ZSCloud-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zscaler ZSCloud kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
