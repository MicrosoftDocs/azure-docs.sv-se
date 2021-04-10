---
title: 'Självstudie: Azure Active Directory integrering med Andromeda | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Andromeda.
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
ms.openlocfilehash: 678bb46efb19f3451566306ddbbe372c631c717e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736045"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudie: Azure Active Directory integrering med Andromeda

I den här självstudien får du lära dig hur du integrerar Andromeda med Azure Active Directory (Azure AD).
Genom att integrera Andromeda med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Andromeda.
* Du kan göra det möjligt för användarna att logga in automatiskt till Andromeda (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Andromeda behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* Andromeda-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Andromeda stöder **SP-och IDP** -INITIERAd SSO
* Andromeda stöder **just-in-Time** User-etablering

## <a name="adding-andromeda-from-the-gallery"></a>Lägga till Andromeda från galleriet

Om du vill konfigurera integreringen av Andromeda i Azure AD måste du lägga till Andromeda från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Andromeda** i sökrutan.
1. Välj **Andromeda** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Konfigurera och testa Azure AD SSO för Andromeda

Konfigurera och testa Azure AD SSO med Andromeda med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Andromeda.

Utför följande steg för att konfigurera och testa Azure AD SSO med Andromeda:


1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera Andromeda SSO](#configure-andromeda-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa Andromeda test User](#create-andromeda-test-user)** – om du vill ha en motsvarighet till Britta Simon i Andromeda som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Andromeda** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<tenantURL>.ngcxpress.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Skärm bild som visar ytterligare U R LS där du kan ange ett tecken på U R L.](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du uppdaterar värdet med den faktiska identifieraren, svars-URL: en och Sign-On URL som beskrivs senare i självstudien.

6. Andromeda-programmet förväntar sig SAML-intyg i ett särskilt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![Skärm bild som visar användarattribut som givenName User. givenName och EmailAddress User. mail.](common/edit-attribute.png)

    > [!Important]
    > Ta bort namn områdes definitionerna när du ställer in dessa.

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Name | Källattribut|
    | ------ | -----------|
    | roll        | App-speciell roll |
    | typ        | Apptyp |
    | company       | CompanyName |

    > [!NOTE]
    > Andromeda förväntar sig roller för användare som tilldelats programmet. Konfigurera de här rollerna i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild som visar användar anspråk med alternativ för att lägga till ett nytt anspråk och spara.](common/new-save-attribute.png)

    ![Skärm bild som visar hantera användar anspråk där du kan ange värden som beskrivs I det här steget.](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Andromeda** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Andromeda.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Andromeda**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-andromeda-sso"></a>Konfigurera Andromeda SSO

1. Logga in på din Andromeda företags webbplats som administratör.

2. Klicka på **administratör** överst på Meny raden och navigera till **Administration**.

    ![Andromeda-administratör](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

3. Klicka på **SAML-konfiguration** på vänster sida av verktygsfältet under **gränssnitt** .

    ![Andromeda SAML](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

4. Utför följande steg på sidan **SAML-konfigurations** avsnitt:

    ![Andromeda-konfiguration](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrol lera **Aktivera SSO med SAML**.

    b. Under avsnittet **Andromeda information** kopierar du värdet för **SP-identitet** och klistrar in det i text rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** .

    c. Kopiera **konsumentens URL** -värde och klistra in det i text rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** .

    d. Kopiera värdet för **inloggnings-URL** och klistra in det i text rutan **inloggnings-URL** i avsnittet **grundläggande SAML-konfiguration** .

    e. Skriv ditt IDP namn under avsnittet **SAML Identity Provider** .

    f. I text rutan för **enkel inloggning i slut punkt** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    ex. Öppna det nedladdade **base64-kodade certifikatet** från Azure Portal i anteckningar, klistra in det i text rutan **X 509-certifikat** .
    
    h. Mappa följande attribut med respektive värde för att under lätta SSO-inloggning från Azure AD. Attributet **User ID** krävs för inloggning. För etablering, krävs **e-post**, **företag**, **UserType** och **roll** . I det här avsnittet definierar vi attribut mappning (namn och värden) som motsvarar de som definierats i Azure Portal

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.


### <a name="create-andromeda-test-user"></a>Skapa Andromeda test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Andromeda. Andromeda stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Andromeda skapas en ny efter autentiseringen. Om du behöver skapa en användare manuellt kontaktar du [Andromeda client support team](https://www.ngcsoftware.com/support/).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Andromeda-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Andromeda-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Andromeda som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Andromeda i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Andromeda som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Andromeda kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
