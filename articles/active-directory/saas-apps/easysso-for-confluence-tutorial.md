---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med EasySSO för Confluence | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 325f6ad7d9685fac17e17b28c4ffbe31b1245cca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734553"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EasySSO för Confluence

I den här självstudien får du lära dig hur du integrerar EasySSO för Confluence med Azure Active Directory (Azure AD). När du integrerar EasySSO för Confluence med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Confluence.
* Gör det möjligt för användarna att logga in automatiskt till Confluence med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EasySSO för Confluence-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EasySSO för Confluence stöder **SP-och IDP** -INITIERAd SSO
* EasySSO för Confluence stöder **just-in-Time** User-etablering

## <a name="adding-easysso-for-confluence-from-the-gallery"></a>Lägga till EasySSO för Confluence från galleriet

Om du vill konfigurera integreringen av EasySSO för Confluence i Azure AD måste du lägga till EasySSO för Confluence från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **EasySSO för Confluence** i sökrutan.
1. Välj **EasySSO för Confluence** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Konfigurera och testa Azure AD SSO för EasySSO för Confluence

Konfigurera och testa Azure AD SSO med EasySSO för Confluence med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EasySSO för Confluence.

Utför följande steg för att konfigurera och testa Azure AD SSO med EasySSO för Confluence:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EasySSO för Confluence SSO](#configure-easysso-for-confluence-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa EasySSO för Confluence test User](#create-easysso-for-confluence-test-user)** -om du vill ha en motsvarighet till B. Simon i EasySSO för Confluence som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **EasySSO för Confluence** . Leta reda på avsnittet **Hantera** och välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [EasySSO support team](mailto:support@techtime.co.nz) för att få dessa värden om du är tveksam. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för Confluence-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig EasySSO för Confluence-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Om dina Azure AD-användare har konfigurerat **sAMAccountName** måste du mappa **urn: OID: 0.9.2342.19200300.100.1.1** till **sAMAccountName** -attributet.
    
1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Hämta** Länkar för **certifikat (base64)** eller **federationsmetadata XML-** alternativ och sparar antingen eller alla på datorn. Du behöver den senare för att kunna konfigurera Confluence-EasySSO.

    ![Länk för nedladdning av certifikatet](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Om du planerar att utföra EasySSO för Confluence-konfiguration manuellt med certifikat, måste du också kopiera **inloggnings-URL** och **Azure AD-identifierare** från avsnittet nedan och spara dem på din dator.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EasySSO för Confluence.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **EasySSO för Confluence**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-easysso-for-confluence-sso"></a>Konfigurera EasySSO för Confluence SSO

1. Om du vill automatisera konfigurationen i EasySSO för Confluence måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera EasySSO för Confluence** för att dirigera dig till EasySSO för Confluence-program. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på EasySSO för Confluence. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-9.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera EasySSO för Confluence manuellt loggar du in på Atlassian Confluence-instansen med administratörs behörighet och navigerar till avsnittet **Hantera appar** . 

    ![Hantera appar](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. På den vänstra sidan letar du upp **EasySSO** och klickar på den. Klicka sedan på knappen **Konfigurera** .

    ![Enkel inloggning](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Välj **SAML** -alternativ. Detta tar dig till avsnittet om SAML-konfiguration.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Fliken Välj **certifikat** längst upp och visas på följande skärm: 

    ![Metadata-URL](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Nu ska du leta upp **certifikat (base64)** eller **metadatafilen** som du har sparat i de tidigare stegen i **Azure AD SSO** -konfigurationen. Du har följande alternativ för hur du går vidare:

    a. Använd den app Federation- **metadatafil** som du laddade ned till en lokal fil på din dator. Välj **Ladda upp** alternativ knapp och följ dialog rutan Ladda upp fil som är unik för ditt operativ system

    **OR**

    b. Öppna appens Federations **ETA data** för att se innehållet (i valfri text redigerare) i filen och kopiera den till Urklipp. Välj **inmatat** alternativ och klistra in innehåll i Urklipp i textfältet.
 
    **OR**

    c. Helt manuell konfiguration. Öppna appens Federations **certifikat (base64)** om du vill se innehållet (i valfri text redigerare) i filen och kopiera det till Urklipp. Klistra in det i textfältet **IDP token Signature certificates** . Gå sedan till fliken **Allmänt** och fyll **i bindnings-URL** och **entitets-ID** med respektive värden för **inloggnings-URL** och **Azure AD-identifierare** som du sparade tidigare.
 
6. Klicka på knappen **Spara** längst ned på sidan. Du kan se innehållet i metadata eller certifikatfiler parsas i konfigurations fälten. EasySSO för Confluence-konfigurationen har slutförts.

7. För bästa test upplevelse går du till fliken **titta & känsla** och markerar knappen för **SAML-inloggning** på. Detta aktiverar separat knapp på Confluence-inloggnings skärmen för att testa att Azure AD SAML-integration slutar till slutet. Du kan lämna den här knappen och konfigurera dess placering, färg och översättning för produktions läget.

    ![Titta & känsla](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > Kontakta [EasySSO support team](mailto:support@techtime.co.nz)om du har några problem.

### <a name="create-easysso-for-confluence-test-user"></a>Skapa EasySSO för Confluence test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Confluence. EasySSO för Confluence har stöd för just-in-Time User-etablering, som är **inaktive rad** som standard. Om du vill aktivera användar etablering måste du markera kryss rutan **skapa användare på ett lyckat inloggnings** alternativ i avsnittet Allmänt i konfiguration av EasySSO-plugin-programmet. Om en användare inte redan finns i Confluence skapas en ny efter autentiseringen.

Men om du inte vill aktivera automatisk användar etablering för användaren första inloggningen måste användarna finnas i backend-användargrupper som Confluence-instansen använder, till exempel LDAP eller Atlassian Fully.

![Användaretablering](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Testa SSO 

### <a name="idp-initiated-workflow"></a>IdP-initierat arbets flöde

I det här avsnittet testar du konfigurationen för enkel inloggning med Azure AD med hjälp av mina appar.

När du klickar på panelen EasySSO för Confluence i Mina appar, bör du loggas in automatiskt på Confluence-instansen som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>SP-initierat arbets flöde

I det här avsnittet ska du testa din Azure AD-konfiguration för enkel inloggning med Confluence **SAML Logid** -knappen.

![SAML-inloggning för användare](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

I det här scenariot förutsätter vi att du har aktiverat **knappen SAML-inloggning** på fliken **Sök & känner** på EasySSO-Confluence (se ovan). Öppna din inloggnings-URL för Confluence i webbläsarens Incognito-läge för att undvika störningar i befintliga sessioner. Klicka på knappen **SAML-inloggning** så omdirigeras du till Azure AD User Authentication Flow. När du har slutfört omdirigeras du tillbaka till Confluence-instansen som autentiserad användare via SAML.

Det finns en risk att du kan stöta på följande skärm när du har omdirigerat tillbaka från Azure AD

![Skärmen EasySSO-krasch](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

I det här fallet måste du följa [anvisningarna på den här sidan]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) för att få åtkomst till **Atlassian-Confluence. log** -filen. Information om felet är tillgängligt med referens-ID: t som finns på fel sidan för EasySSO.

Om du har några problem med att sammanfatta logg meddelandena kontaktar du [EasySSO support team](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat EasySSO för Confluence kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).