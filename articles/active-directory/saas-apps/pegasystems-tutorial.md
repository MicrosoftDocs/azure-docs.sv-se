---
title: 'Självstudie: Azure Active Directory integrering med PEGA-system | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och PEGA-system.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 802bd61d499f64a128a4d1c0585363cb1962f8a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650047"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Självstudie: Azure Active Directory integrering med PEGA-system

I den här självstudien får du lära dig att integrera PEGA-system med Azure Active Directory (Azure AD). När du integrerar PEGA-system med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PEGA-system.
* Gör det möjligt för användarna att logga in automatiskt till PEGA-system med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PEGA system enkel inloggning (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning i Azure AD i en test miljö.

* PEGA-system har stöd för SP-initierad och IdP-initierad SSO.

## <a name="add-pega-systems-from-the-gallery"></a>Lägg till PEGA system från galleriet

Om du vill konfigurera integreringen av PEGA-system i Azure AD måste du lägga till PEGA-system från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **PEGA Systems** i sökrutan.
1. Välj **PEGA system** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-pega-systems"></a>Konfigurera och testa Azure AD SSO för PEGA-system

Konfigurera och testa Azure AD SSO med PEGA-system med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i PEGA-system.

Utför följande steg för att konfigurera och testa Azure AD SSO med PEGA-system:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PEGA-system SSO](#configure-pega-systems-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa PEGA Systems test User](#create-pega-systems-test-user)** – om du vill ha en motsvarighet till B. Simon i PEGA-system som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **PEGA-system** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialog rutan **grundläggande SAML-konfiguration** kan du utföra följande steg om du vill konfigurera programmet i IDP-initierat läge.

    ![Dialog rutan grundläggande SAML-konfiguration](common/idp-intiated.png)

    1. I rutan **identifierare** anger du en URL i det här mönstret:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. I rutan **svars-URL** anger du en URL i följande mönster:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Om du vill konfigurera programmet i SP-initierat läge väljer du **Ange ytterligare URL: er** och utför följande steg.

    ![Information om enkel inloggning för PEGA Systems-domäner och URL: er](common/both-advanced-urls.png)

    1. I rutan **inloggnings-URL** anger du URL-värdet för inloggning.

    1. I rutan **relä tillstånd** anger du en URL i det här mönstret: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > De värden som anges här är plats hållare. Du måste använda den faktiska identifieraren, svars-URL: en, inloggnings-URL: en och URL: en för Relay status. Du kan hämta ID-och svars-URL-värden från ett PEGA-program, som beskrivs senare i den här självstudien. Kontakta [PEGA Systems support team](https://www.pega.com/contact-us)för att hämta värdet för relä status. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. PEGA Systems-programmet måste ha SAML-försäkran som ett särskilt format. För att få dem i rätt format måste du lägga till anpassade mappningar för attribut i konfigurationen för SAML-token. Följande skärm bild visar standardattributen. Välj **redigerings** ikonen för att öppna dialog rutan **användarattribut** :

    ![Användarattribut](common/edit-attribute.png)

7. Förutom de attribut som visas i föregående skärm bild, kräver PEGA Systems applikationen några fler attribut att skickas tillbaka i SAML-svaret. I avsnittet **användar anspråk** **i dialog rutan användarattribut utför** du följande steg för att lägga till dessa SAML-token:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Dessa värden är bara aktuella för din organisation. Ange lämpliga värden.

    1. Välj **Lägg till nytt anspråk** för att öppna dialog rutan **hantera användar anspråk** :

    ![Välj Lägg till nytt anspråk](common/new-save-attribute.png)

    ![Dialogrutan Hantera användaranspråk](common/new-attribute-details.png)

    1. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    1. Lämna rutan **namn område** tom.

    1. För **källan** väljer du **attribut**.

    1. I listan **källattribut** väljer du det attributvärde som visas för den raden.

    1. Välj **OK**.

    1. Välj **Spara**.

8. På sidan **Konfigurera enskilda Sign-On med SAML** , i avsnittet SAML- **signeringscertifikat** , väljer du länken **Hämta** bredvid **XML för federationsmetadata**, enligt dina krav och sparar certifikatet på datorn:

    ![Länk för nedladdning av certifikat](common/metadataxml.png)

9. I avsnittet **Konfigurera PEGA system** kopierar du lämpliga URL: er baserat på dina krav.

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PEGA-system.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **PEGA system**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-pega-systems-sso"></a>Konfigurera PEGA-system SSO

1. Om du vill konfigurera enkel inloggning på **PEGA system** -sidan loggar du in på PEGA-portalen med ett administratörs konto i ett annat webbläsarfönster.

2. Välj **skapa**  >  **sysadmin**  >  **-autentiseringstjänst**:

    ![Välj autentiseringstjänst](./media/pegasystems-tutorial/admin.png)
    
3. Utför följande steg på skärmen **skapa autentiseringstjänst** .

    ![Fönstret Skapa autentiseringstjänst](./media/pegasystems-tutorial/admin1.png)

    1. I listan **typ** väljer du **SAML 2,0**.

    1. I rutan **namn** anger du ett namn (till exempel **Azure AD SSO**).

    1. Ange en beskrivning i rutan **kort beskrivning** .  

    1. Välj **skapa och öppna**.
    
4. I avsnittet **information om identitets leverantör (IdP)** väljer du **Importera IDP metadata** och bläddrar till den metadatafil som du laddade ned från Azure Portal. Klicka på **Skicka** för att läsa in metadata:

    ![Avsnittet information om identitets leverantör (IdP)](./media/pegasystems-tutorial/admin2.png)
    
    Importen fyller i IdP-data så som visas här:

    ![Importerade IdP-data](./media/pegasystems-tutorial/idp.png)
    
6. Slutför följande steg i avsnittet **tjänst leverantör (SP) inställningar** .

    ![Inställningar för tjänst leverantör](./media/pegasystems-tutorial/sp.png)

    1. Kopiera **entitetens identifierings** värde och klistra in det i rutan **identifierare** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    1. Kopiera plats värdet för **intygs mottagar tjänsten (ACS)** och klistra in det i rutan **svars-URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    1. Välj **inaktivera signering av förfrågningar**.

7. Välj **Spara**.

### <a name="create-pega-systems-test-user"></a>Skapa PEGA Systems test användare

Därefter måste du skapa en användare med namnet Britta Simon i PEGA-system. Arbeta med [PEGA Systems support-teamet](https://www.pega.com/contact-us) för att skapa användare.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till PEGA Systems-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till PEGA Systems inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på de PEGA-system som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen PEGA Systems i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på PEGA-systemen som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat PEGA system kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).