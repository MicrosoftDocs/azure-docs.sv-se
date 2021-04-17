---
title: 'Självstudie: Azure Active Directory integrering med M-Files | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och M-Files.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 680380722746522a0eb3fe6518452472be952075
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482849"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Självstudie: Azure Active Directory integrering med M-Files

I den här självstudien lär du dig att integrera M-Files med Azure Active Directory (Azure AD). När du integrerar M-Files med Azure AD kan du:

* Kontrollera vem som har åtkomst till M-Files i Azure AD.
* Gör så att dina användare automatiskt loggas in på M-Files med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* M-Files-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* M-Files stöder **SP-initierad** enkel inloggning.

## <a name="add-m-files-from-the-gallery"></a>Lägga till M-Files från galleriet

För att konfigurera integreringen av M-Files i Azure AD måste du lägga till M-Files från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **M-Files** i sökrutan.
1. Välj **M-Files** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Konfigurera och testa enkel inloggning för Azure AD för M-Files

Konfigurera och testa enkel inloggning i Azure AD med M-Files med hjälp av en testanvändare med **namnet B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i M-Files.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med M-Files:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för M-Files](#configure-m-files-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa M-Files-testanvändare](#create-m-files-test-user)** – för att ha en motsvarighet för B.Simon i M-Files som är länkad till en Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal programintegreringssidan **för M-Files** letar du upp **avsnittet Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenantname>.cloudvault.m-files.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för M-Files](mailto:support@m-files.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera M-Files** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till M-Files.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **M-Files**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-m-files-sso"></a>Konfigurera enkel inloggning för M-Files

1. För att få enkel inloggning konfigurerat för ditt program kontaktar du [supportteamet för M-Files](mailto:support@m-files.com) och ge dem nedladdade metadata.
   
    >[!NOTE]
    >Följ stegen nedan om du vill konfigurera enkel inloggning för M-Files-skrivbordsprogrammet. Det krävs inga fler steg om du bara vill konfigurera enkel inloggning för webbversionen av M-Files.  

1. Följ stegen nedan om du vill konfigurera M-Files-skrivbordsprogrammet för att aktivera enkel inloggning med Azure AD. För att ladda ned M-Files går du till sidan för [nedladdning av M-Files](https://www.m-files.com/customers/product-downloads/download-update-links/).

1. Öppna fönstret **M-Files Desktop Settings** (M-Files-skrivbordsinställningar). Klicka sedan på Lägg **till**.
   
    ![Skärmbild som visar M-Files Desktop-inställningar där du kan välja Lägg till.](./media/m-files-tutorial/settings.png)

1. I fönstret **Document Vault Connection Properties** (Anslutningsegenskaper för Document Vault) utför du följande steg:
   
    ![Skärmbild som visar Anslutningsegenskaper för Document Vault där du kan ange de värden som beskrivs.](./media/m-files-tutorial/general.png)  

    Under avsnittet Server skriver du värdena på följande sätt:  

    a. För **Användarnamn** skriver `<tenant-name>.cloudvault.m-files.com`. 
 
    b. För **Portnummer** skriver du **4466**. 

    c. För **Protokoll** väljer du **HTTPS**. 

    d. I fältet **Autentisering** väljer du **Specific Windows user** (Specifik Windows-användare). Då visas en inloggningssida. Infoga dina autentiseringsuppgifter för Azure AD. 

    e. För **valvet på servern** väljer du motsvarande valv på servern.
 
    f. Klicka på **OK**.

### <a name="create-m-files-test-user"></a>Skapa testanvändare för M-Files

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i M-Files. Ta hjälp av [supportteamet för M-Files](mailto:support@m-files.com) för att lägga till användarna i M-Files.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL:en för M-Files där du kan initiera inloggningsflödet. 

* Gå till inloggnings-URL:en för M-Files direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på M-Files-panelen i Mina appar omdirigeras detta till inloggnings-URL för M-Files. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat M-Files kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
