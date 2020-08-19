---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cisco WebEx | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 4fd6b46353cdeb4493c1a767c92e75d602e52843
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528908"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cisco WebEx

I den här självstudien får du lära dig hur du integrerar Cisco WebEx med Azure Active Directory (Azure AD). När du integrerar Cisco WebEx med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco WebEx.
* Gör det möjligt för användarna att logga in automatiskt till Cisco WebEx med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cisco WebEx-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cisco WebEx stöder **SP** -initierad SSO.
* Cisco WebEx har stöd för **Automatisk** användar etablering.
* När du har konfigurerat Cisco WebEx kan du framtvinga kontroll av sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Cisco WebEx** i sökrutan.
1. Välj **Cisco WebEx** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Konfigurera och testa enkel inloggning med Azure AD för Cisco WebEx

Konfigurera och testa Azure AD SSO med Cisco WebEx med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco WebEx.

Om du vill konfigurera och testa Azure AD SSO med Cisco WebEx slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
2. **[Konfigurera Cisco-WebEx](#configure-cisco-webex)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa Cisco WebEx-test](#create-cisco-webex-test-user)** för att få en motsvarighet till B. Simon i Cisco WebEx som är länkat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Cisco WebEx** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du hämtar metadata-filen för tjänst leverantören från avsnittet **Konfigurera Cisco-WebEx** , som beskrivs senare i självstudien. 

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    I text rutan för **inloggnings-URL** klistrar du in värdet för **svars-URL**, som automatiskt fylls med fil uppladdning av SP-metadatafil.

1. Cisco Webex-programmet förväntar sig att hitta SAML-försäkringar i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattributen. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar Cisco WebEx-programmet fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
  
    | Name |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Cisco WebEx** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco WebEx.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Cisco Webex** i listan över program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cisco-webex"></a>Konfigurera Cisco-WebEx

1. Om du vill automatisera konfigurationen i Cisco WebEx måste du installera **webb läsar tillägget Mina appar säker inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera Cisco WebEx** för att dirigera dig till Cisco WebEx-programmet. Därifrån anger du de autentiseringsuppgifter som krävs för att logga in på Cisco WebEx. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-8.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Cisco WebEx manuellt loggar du in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) med dina fullständiga administratörsautentiseringsuppgifter.

4. Välj **Settings** (Inställningar) och klicka på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Välj **integrera en identitets leverantör från tredje part. (Avancerat)** och gå till nästa skärm.

6. På sidan **Import Idp Metadata** (Importera ldp-metadata) kan du antingen dra och släppa Azure AD-metadatafilen på sidan eller använda filbläddraren för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

8. Gå tillbaka till fliken för **Cisco Cloud Collaboration Management** browser. Om testet lyckades väljer du **det här testet lyckades. Aktivera alternativ för enkel inloggning** och klicka på **Nästa**.

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet skapar du en användare som heter B. Simon i Cisco WebEx. I det här avsnittet skapar du en användare som heter B. Simon i Cisco WebEx.

1. Logga in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. I rutan **Manage Users** (Hantera användare) väljer du **Manually add or modify users** (Ändra eller lägga till användare manuellt) och klickar på **Next** (Nästa).

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. I text rutan **förnamn** skriver du första namnet på användaren, t. ex. **B**.

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress: b.simon@contoso.com.

5. Klicka på plus tecknet för att lägga till B. Simon. Klicka sedan på **Nästa**.

6. I rutan **Add Services for Users** (Lägg till tjänster för användare) klickar du på **Save** (Spara) och sedan på **Finish** (Slutför).

## <a name="test-sso"></a>Testa SSO

När du väljer panelen Cisco-WebEx på åtkomst panelen, bör du loggas in automatiskt på Cisco-WebEx som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Cisco WebEx med Azure AD](https://aad.portal.azure.com)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Cisco WebEx med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-webex)