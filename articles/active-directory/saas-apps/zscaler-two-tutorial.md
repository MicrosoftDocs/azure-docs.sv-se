---
title: 'Självstudie: Azure Active Directory integrering med Zscaler Two | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Two.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: b4d39492a05ee90c0ba2e081336d7b04579a5c43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519103"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Självstudie: Azure Active Directory integrering med Zscaler Two

I den här självstudien lär du dig att integrera Zscaler Two med Azure Active Directory (Azure AD). När du integrerar Zscaler Two med Azure AD kan du:

* Kontrollera vem som har åtkomst till Zscaler Two från Azure AD.
* Gör så att dina användare automatiskt loggas in på Zscaler Two med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Zscaler Two behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Zscaler Två prenumerationer med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler Two har stöd för **SP-initierad** enkel inloggning.

* Zscaler Two stöder **just-in-time-användareablering.**

> [!NOTE]
> Identifierare för det här programmet är ett fast strängvärde så att endast en instans kan konfigureras i en klientorganisation.

## <a name="add-zscaler-two-from-the-gallery"></a>Lägga till Zscaler Two från galleriet

För att konfigurera integreringen av Zscaler Two i Azure AD måste du lägga till Zscaler Two från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Zscaler Two** i sökrutan.
1. Välj **Zscaler Two** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Konfigurera och testa Azure AD SSO för Zscaler Two

Konfigurera och testa enkel inloggning i Azure AD med Zscaler Two med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zscaler Two.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med Zscaler Two:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera Zscaler Two SSO](#configure-zscaler-two-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Zscaler Two-testanvändare](#create-zscaler-two-test-user)** – för att ha en motsvarighet för B.Simon i Zscaler Two som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal sidan **Zscaler Two application integration (Zscaler** Two-programintegrering) hittar du avsnittet **Hantera** och **väljer enkel inloggning**.
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    I **textrutan Inloggnings-URL** skriver du den URL som används av användarna för att logga in på ditt ZScaler Two-program.

    > [!NOTE]
    > Du uppdaterar värdet med den faktiska Sign-On URL:en. Kontakta [kundsupporten för Zscaler Two och](https://www.zscaler.com/company/contact) be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ditt Zscaler Two-program förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka **på ikonen** Redigera för att öppna dialogrutan **Användarattribut.**

    ![Skärmbild som visar Användarattribut med ikonen Redigera markerad.](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig Zscaler Two-programmet att några fler attribut skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Name | Källattribut |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärmbild som visar användargränssnittet med alternativet lägg till nytt anspråk.](common/new-save-attribute.png)

    ![Skärmbild som visar dialogrutan Hantera användaranspråk där du kan ange de värden som beskrivs.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.
    
    f. Klicka på **Spara**.

    > [!NOTE]
    > Klicka här [om](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) du vill veta hur du konfigurerar rollen i Azure AD.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I **avsnittet Konfigurera Zscaler Two** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare i den Azure Portal med namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Zscaler Two.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Zscaler Two**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-zscaler-two-sso"></a>Konfigurera Zscaler Two SSO

1. Om du vill automatisera konfigurationen i Zscaler Two måste du installera **Mina appar** för säker inloggning genom att klicka **på Installera tillägget**.

    ![Mina appar tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren klickar du **på Konfigurera Zscaler Two** så dirigeras du till Zscaler Two-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Zscaler Two. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–6.

    ![Konfigurera enkel inloggning](common/setup-sso.png)

3. Om du vill konfigurera Zscaler Two manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Zscaler Two-företagswebbplats som administratör och utför följande steg:

4. Gå till **Administration > Autentisering > Autentiseringsinställningar** och utför följande steg:
   
    ![Skärmbild som visar Zscaler One-webbplatsen med stegen enligt beskrivningen.](./media/zscaler-two-tutorial/administrator.png "Administration")

    a. Under autentiseringstyp väljer du **SAML**.

    b. Klicka på **Konfigurera SAML**.

5. I fönstret **Redigera SAML** utför du följande steg och klickar på Spara.  
            
    ![Hantera användare & autentisering](./media/zscaler-two-tutorial/authentication.png "Hantera användare & autentisering")
    
    a. I textrutan för **URL:en för SAML-portalen** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    b. I textrutan för **attribut för inloggningsnamn** anger du **NameID**.

    c. Klicka på **Ladda upp** för att ladda upp Azure SAML-signeringscertifikatet som du har laddat ned från Azure-portalen i det **offentliga SSL-certifikatet**.

    d. Reglera **Enable SAML Auto-Provisioning** (Aktivera automatisk etablering av SAML).

    e. I textrutan för **användarens visningsnamnattribut** anger du **displayName** om du vill aktivera automatisk SAML-etablering för attributet displayName.

    f. I textrutan **User Display Name Attribute** (Gruppnamnattribut) anger du **memberOf** om du vill aktivera automatisk SAML-etablering för attributet memberOf.

    ex. I textrutan för **avdelningsnamnattribut** anger du **department** om du vill aktivera automatisk SAML-etablering för department-attribut.

    h. Klicka på **Spara**.

6. I dialogrutan **Konfigurera användarautentisering** utför du följande steg:

    ![Skärmbild som visar dialogrutan Konfigurera användarautentisering med Aktivera valt.](./media/zscaler-two-tutorial/activation.png)

    a. Hovra över menyn **Aktivering** längst ned till vänster.

    b. Klicka på **Aktivera**.

## <a name="configuring-proxy-settings&quot;></a>Konfigurera proxyinställningar

### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>För att konfigurera proxyinställningar i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** i menyn **Verktyg** för att öppna dialogrutan **Internetalternativ**.   
    
     ![Internetalternativ](./media/zscaler-two-tutorial/internet.png &quot;Internetalternativ")

3. Klicka på fliken **Anslutningar**.   
  
     ![Anslutningar](./media/zscaler-two-tutorial/ic769493.png "Anslutningar")

4. Klicka på **LAN-inställningar** för att öppna dialogrutan **LAN-inställningar**.

5. I avsnittet Proxyserver utför du följande steg:   
   
    ![Proxyserver](./media/zscaler-two-tutorial/proxy.png "Proxyserver")

    a. Välj **Use a proxy server for your LAN** (Använd en proxyserver för ditt lokala nätverk).

    b. I textrutan Adress skriver du **gateway. Zscaler Two.net**.

    c. I textrutan Port skriver du **80**.

    d. Välj **Bypass proxy server for local addresses** (Kringgå proxyservern för lokala adresser).

    e. Klicka på **OK** för att stänga dialogrutan **Local Area Network (LAN) Settings** (Inställningar för lokalt nätverk (LAN)).

6. Klicka på **OK** för att stänga dialogrutan **Internetalternativ**.


### <a name="create-zscaler-two-test-user"></a>Skapa Zscaler Two-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Zscaler Two. Zscaler Two stöder just-in-time-användareablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Zscaler Two skapas en ny efter autentisering.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [Zscaler Two-supportteamet](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Zscaler Två inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till Zscaler Två inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Zscaler Two-panelen i Mina appar omdirigeras den till Zscaler Two Sign-on URL (Zscaler Två inloggnings-URL). Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zscaler Two kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
