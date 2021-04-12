---
title: 'Självstudie: Azure Active Directory integrering med Zscaler beta | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler Beta.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 4e8dce970c9e7cfb2aa7887cf1fb3ed6ef9030a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735615"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Självstudie: Azure Active Directory integrering med Zscaler beta

I den här självstudien lär du dig att integrera Zscaler Beta med Azure Active Directory (AD Azure).
När du integrerar Zscaler beta med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Zscaler-beta.
* Tillåt att användarna loggas in automatiskt till Zscaler beta med sina Azure AD-konton. Den här åtkomst kontrollen kallas enkel inloggning (SSO).
* Hantera dina konton på en central plats med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Zscaler Beta behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Zscaler beta-prenumeration som använder enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zscaler beta stöder **SP** -initierad SSO.
* Zscaler beta stöder **just-in-Time** User-etablering.

## <a name="adding-zscaler-beta-from-the-gallery"></a>Lägga till Zscaler Beta från galleriet

För att konfigurera integreringen av Zscaler Beta med Azure AD måste du lägga till SAML SSO for Zscaler Beta från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Zscaler beta** i sökrutan.
1. Välj **Zscaler beta** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Konfigurera och testa Azure AD SSO för Zscaler beta

Konfigurera och testa Azure AD SSO med Zscaler beta med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Zscaler beta.

Utför följande steg för att konfigurera och testa Azure AD SSO med Zscaler beta:


1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Zscaler beta SSO](#configure-zscaler-beta-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Zscaler beta test User](#create-zscaler-beta-test-user)** -om du vill ha en motsvarighet till B. Simon i Zscaler beta som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Zscaler beta** -programintegration i Azure Portal letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I rutan **inloggnings-URL** anger du den URL som används av användarna för att logga in på ditt Zscaler beta beta-program.

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med värdet faktiskt inloggnings-URL. Kontakta [support teamet för Zscaler beta client](https://www.zscaler.com/company/contact)för att få värdet.

5. Zscaler beta-programmet förväntar sig SAML-intyg i ett särskilt format. Du måste lägga till anpassade mappningar för attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Välj **Redigera** för att öppna dialog rutan **användarattribut** .

    ![Dialogrutan Användarattribut](common/edit-attribute.png)

6. Zscaler beta-programmet förväntar sig att fler attribut ska skickas tillbaka i SAML-svar. I avsnittet **användar anspråk** **i dialog rutan användarattribut,** följer du dessa steg för att lägga till attributet SAML-token, som visas i följande tabell.
    
    | Name | Källattribut | 
    | ---------------| --------------- |
    | memberOf  | user.assignedroles |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    c. Lämna rutan **namn område** tom.

    d. För **källa** väljer du **attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    ex. Välj **Spara**.

    > [!NOTE]
    > Klicka [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om du vill veta mer om hur du konfigurerar roller i Azure AD.

7. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du **Hämta** för att ladda ned **certifikatet (base64)**. Spara den på din dator.

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera Zscaler beta** kopierar du de webb adresser som du behöver för dina behov:

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Zscaler beta.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Zscaler beta**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-zscaler-beta-sso"></a>Konfigurera Zscaler beta SSO

1. Om du vill automatisera konfigurationen i Zscaler beta installerar du **tillägget Mina appar säker inloggnings webbläsare** genom att välja **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren kan du välja **Konfigurera Zscaler beta** leder dig till Zscaler beta-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Zscaler beta. Webb läsar tillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3 till 6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Öppna ett nytt webbläsarfönster om du vill konfigurera Zscaler-beta manuellt. Logga in på din Zscaler beta-företags webbplats som administratör och följ de här stegen.

4. Gå till autentiseringsinställningarna för **administrations**  >  **autentisering**  >  och följ de här stegen.
   
    ![Administration](./media/zscaler-beta-tutorial/ic800206.png "Administration")

    a. Under **Autentiseringstyp** väljer du **SAML**.

    b. Välj **Konfigurera SAML**.

5. I fönstret **Redigera SAML** följer du dessa steg: 
            
    ![Hantera användare & autentisering](./media/zscaler-beta-tutorial/ic800208.png "Hantera användare & autentisering")
    
    a. I rutan **URL för SAML-Portal** klistrar du in den **inloggnings-URL** som du kopierade från Azure Portal.

    b. I rutan **inloggnings namn** anger du **NameID**.

    c. I rutan **offentligt SSL-certifikat** väljer du **Ladda upp** för att ladda upp det Azure SAML-signeringscertifikat som du laddade ned från Azure Portal.

    d. Växla **Aktivera SAML Auto-Provisioning**.

    e. I rutan **användarens visnings namn** anger du **DisplayName** om du vill aktivera autoetablering för SAML för DisplayName-attribut.

    f. I rutan **gruppnamn-attribut** anger du **memberOf** om du vill aktivera SAML autoetablering för memberOf-attribut.

    ex. I rutan **avdelnings namn** anger du **avdelning** om du vill aktivera SAML autoetablering för avdelnings-attribut.

    h. Välj **Spara**.

6. Följ dessa steg på dialog sidan **Konfigurera användarautentisering** :

    ![Aktiverings menyn och knappen Aktivera](./media/zscaler-beta-tutorial/ic800207.png)

    a. Hovra över **aktiverings** menyn längst ned till vänster.

    b. Välj **Aktivera**.

## <a name="configure-proxy-settings&quot;></a>Konfigurera proxyinställningar
Följ dessa steg om du vill konfigurera proxyinställningarna i Internet Explorer.

1. Starta **Internet Explorer**.

2. Öppna dialog rutan **Internet alternativ** genom att välja **Internet alternativ** på **verktyg** -menyn. 
    
     ![Dialog rutan Internet alternativ](./media/zscaler-beta-tutorial/ic769492.png &quot;Internet alternativ")

3. Välj fliken **anslutningar** . 
  
     ![Fliken Anslutningar](./media/zscaler-beta-tutorial/ic769493.png "Anslutningar")

4. Välj **LAN-inställningar** för att öppna dialog rutan **Inställningar för lokalt nätverk (LAN)** .

5. I avsnittet **proxyserver** följer du dessa steg: 
   
    ![Avsnittet proxy server](./media/zscaler-beta-tutorial/ic769494.png "Proxyserver")

    a. Markera kryss rutan **Använd en proxyserver för ditt lokala nätverk** .

    b. I rutan **adress** anger du **Gateway. Zscaler-Beta.net**.

    c. I rutan **port** anger du **80**.

    d. Markera kryss rutan **Använd ingen proxyserver för lokala adresser** .

    e. Välj **OK** för att stänga dialog rutan **Inställningar för lokalt nätverk (LAN)** .

6. Välj **OK** för att stänga dialog rutan **Internet alternativ** .

### <a name="create-zscaler-beta-test-user"></a>Skapa Zscaler Beta-testanvändare

I det här avsnittet skapas User Britta Simon i Zscaler beta. Zscaler Beta stöder **just-in-time-användaretablering**, vilket är aktiverat som standard. Det finns inget som du kan göra i det här avsnittet. Om det inte redan finns någon användare i Zscaler Beta skapas en ny efter autentisering.

>[!Note]
>Kontakta [support teamet för Zscaler beta](https://www.zscaler.com/company/contact)för att skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Zscaler beta-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Zscaler beta-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på Zscaler beta-panelen i Mina appar omdirigeras den till Zscaler beta-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Zscaler beta kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
