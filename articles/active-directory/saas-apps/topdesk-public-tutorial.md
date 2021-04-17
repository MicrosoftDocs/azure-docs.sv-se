---
title: 'Självstudie: Azure Active Directory integrering med TOPdesk – | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk – Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 5d16fd87b01db69d3f55e22aad573b7847b9048c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518147"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Självstudie: Azure Active Directory integrering med TOPdesk – offentlig

I den här självstudien lär du dig att integrera TOPdesk – Public med Azure Active Directory (Azure AD). När du integrerar TOPdesk – Public med Azure AD kan du:

* I Azure AD kan du styra vem som har åtkomst till TOPdesk – public.
* Gör så att dina användare automatiskt loggas in på TOPdesk – offentligt med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* TOPdesk – Offentlig prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TOPdesk – public har stöd för **SP-initierad** enkel inloggning.

## <a name="add-topdesk---public-from-the-gallery"></a>Lägg till TOPdesk – Public från galleriet

För att konfigurera integreringen av TOPdesk – Public i Azure AD måste du lägga till TOPdesk – Public från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **TOPdesk – Public** i sökrutan.
1. Välj **TOPdesk – Public** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Konfigurera och testa Azure AD SSO för TOPdesk – offentlig

Konfigurera och testa enkel inloggning för Azure AD med TOPdesk – offentligt med hjälp av en testanvändare med namnet **B.Simon**. För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i TOPdesk – Public.

Utför följande steg för att konfigurera och testa enkel inloggning i Azure AD med TOPdesk – Public:

1. **[Konfigurera enkel inloggning för Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera TOPdesk – offentlig enkel inloggning](#configure-topdesk---public-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa TOPdesk – Offentlig testanvändare](#create-topdesk---public-test-user)** – för att ha en motsvarighet för B.Simon i TOPdesk – Offentlig som är länkad till Azure AD-representationen av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal du avsnittet Hantera på sidan TOPdesk –  Public application integration **(TOPdesk** – offentlig programintegrering) **och väljer enkel inloggning**.
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4.  I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får metadatafilen **för tjänstleverantören** från avsnittet **Configure TOPdesk - Public Single Sign-On (Konfigurera TOPdesk –** offentlig enkel inloggning) som beskrivs senare i självstudien.

    a. Klicka på **Ladda upp metadatafil**.
    
    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls värdena **Identifierare** och **Svars-URL** i automatiskt i avsnittet Grundläggande SAML-konfiguration.

    d. I **textrutan Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net`

    e. I **textrutan Identifierar-URL** fyller du i den TOPdesk-metadata-URL som du kan hämta från TOPdesk-konfigurationen. Det bör använda följande mönster: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Om värdena **Identifierare** och Svars-URL inte fylls i automatiskt måste du ange dem manuellt.  För Identifierare följer du mönstret som nämns ovan och du får svars-URL-värdet från avsnittet Konfigurera **TOPdesk – offentlig** enkel inloggning, vilket förklaras senare i självstudien. Värdet **för inloggnings-URL:en** är inte verkligt, så du måste uppdatera värdet med den faktiska Sign-On URL:en. Kontakta [supportteamet för TOPdesk – Public Client](https://help.topdesk.com/saas/enterprise/user/) och be om värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I **avsnittet Konfigurera TOPdesk – Public** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i den Azure Portal med namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. I **Användaregenskaper** följer du dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till TOPdesk – Public.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **TOPdesk – Public**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och välj sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den från **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen &quot;Standardåtkomst&quot; vald.
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name=&quot;configure-topdesk---public-sso&quot;></a>Konfigurera TOPdesk – offentlig enkel inloggning

1. Logga in på din **TOPdesk – offentlig** företagswebbplats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.
   
    ![Inställningar](./media/topdesk-public-tutorial/menu.png &quot;Inställningar")

3. Klicka på **Inloggningsinställningar**.
   
    ![Inloggningsinställningar](./media/topdesk-public-tutorial/login.png "Inloggningsinställningar")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.
   
    ![Allmänna inställningar](./media/topdesk-public-tutorial/general.png "Allmänna inställningar")

5. I avsnittet **Offentligt** i **konfigurationsavsnittet för SAML-inloggning** utför du följande steg:
   
    ![Tekniska inställningar](./media/topdesk-public-tutorial/public.png "Tekniska inställningar")
   
    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.
   
    b. Öppna den nedladdade metadatafilen och leta upp **noden AssertionConsumerService.**

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Kopiera värdet **AssertionConsumerService** och klistra in det här värdet i textrutan **Svars-URL** i **avsnittet Grundläggande SAML-konfiguration.**      
   
6. Skapa en certifikatfil genom att utföra följande steg:
    
    ![Certifikat](./media/topdesk-public-tutorial/certificate-file.png "Certifikat")
    
    a. Öppna den nedladdade metadatafilen från Azure-portalen.
    
    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.
    
    c. Kopiera värdet för noden **X509Certificate**.
    
    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.
    
    ![SAML-inloggning](./media/topdesk-public-tutorial/add.png "SAML-inloggning")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:
    
    ![SAML-konfigurationsassistent](./media/topdesk-public-tutorial/configuration.png "SAML-konfigurationsassistent")
    
    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    d. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    f. Klicka på **Spara**.

### <a name="create-topdesk---public-test-user"></a>Skapa TOPdesk – offentlig testanvändare

För att Azure AD-användare ska kunna logga in på TOPdesk – Public måste de etableras i TOPdesk – Public. När det gäller TOPdesk – Public är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din **TOPdesk – offentlig** företagswebbplats som administratör.

2. Klicka på TOPdesk New Support Files Person (Ny **\> \> supportfil) \> person** på menyn längst upp.
   
    ![Person](./media/topdesk-public-tutorial/files.png "Person")

3. I dialogrutan Ny person utför du följande steg:
   
    ![Ny person](./media/topdesk-public-tutorial/new.png "Ny person")
   
    a. Klicka på fliken Allmänt.

    b. I **textrutan Surname** (Efternamn) skriver du Surname (Efternamn) för användaren: Simon
 
    c. Välj en **plats** för kontot.
 
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra VERKTYG eller API:er för TOPdesk – Offentligt användarkonto som tillhandahålls av TOPdesk – Public för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till TOPdesk – offentlig inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till TOPdesk – offentlig inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen TOPdesk – Public i Mina appar omdirigeras detta till TOPdesk – offentlig inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat TOPdesk – Public kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
