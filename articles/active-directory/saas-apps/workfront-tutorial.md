---
title: 'Självstudie: Azure Active Directory integrering med Workfront | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workfront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478480"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Självstudie: Azure Active Directory integrering med Workfront

I den här självstudien lär du dig att integrera Workfront med Azure Active Directory (Azure AD). När du integrerar Workfront med Azure AD kan du:

* Kontrollera vem som har åtkomst till Workfront i Azure AD.
* Gör så att dina användare automatiskt loggas in på Workfront med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Workfront behöver du följande:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* Workfront-prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Workfront har stöd för **SP-initierad** enkel inloggning.

## <a name="add-workfront-from-the-gallery"></a>Lägg till Workfront från galleriet

För att konfigurera integreringen av Workfront i Azure AD måste du lägga till Workfront från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller en personlig Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **Workfront** i sökrutan.
1. Välj **Workfront** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Konfigurera och testa azure AD SSO for Workfront

Konfigurera och testa enkel inloggning för Azure AD med Workfront med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Workfront.

Utför följande steg för att konfigurera och testa enkel inloggning med Azure AD för Workfront:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – för att göra det möjligt för B.Simon att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Workfront](#configure-workfront-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa Workfront-testanvändare](#create-workfront-test-user)** – för att ha en motsvarighet för B.Simon i Workfront som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. I Azure Portal går du till **sidan för Workfront-programintegrering,** hittar avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.attask-ondemand.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Workfront och](https://www.workfront.com/services-and-support) be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I **avsnittet Konfigurera Workfront** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare i Azure Portal namnet B.Simon.

1. I det vänstra fönstret i Azure Portal väljer **du Azure Active Directory**, **väljer Användare** och sedan **Alla användare.**
1. Välj **Ny** användare överst på skärmen.
1. Följ dessa **steg** i Användaregenskaper:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I **fältet Användarnamn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till Workfront.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. I programlistan väljer du **Workfront**.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i listrutan **Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-workfront-sso"></a>Konfigurera enkel inloggning för Workfront

1. Logga in på din Start-företagsplats som administratör.

2. Gå till **Konfiguration av enkel inloggning.**

3. I **dialogrutan Enkel inloggning** utför du följande steg
    
    ![Konfigurera enkel inloggning](./media/workfront-tutorial/single-sign-on.png)
   
    a. Som **Typ** väljer du **SAML 2.0**.
   
    b. Välj **Tjänstleverantörs-ID.**
   
    c. Klistra in **inloggnings-URL:en** i **textrutan Inloggningsportal-URL.**
   
    d. Klistra **in utloggnings-URL** **i textrutan Utloggnings-URL.**
   
    e. Klistra **in URL:en för** att ändra lösenord i **textrutan Ändra lösenords-URL.**
   
    f. Klicka på **Spara**.

### <a name="create-workfront-test-user"></a>Skapa Workfront-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Workfront.

**Utför följande steg för att skapa en användare med namnet Britta Simon i Workfront:**

1. Logga in på din Workfront-företagsplats som administratör.
 
2. I menyn längst upp klickar du på **Personer**.
 
3. Klicka **på Ny person**. 

4. I dialogrutan Ny person utför du följande steg:
   
    ![Skapa en Workfront-testanvändare](./media/workfront-tutorial/add-person.png)
   
    a. I **textrutan Förnamn** skriver du "Britta".
   
    b. I **textrutan Efternamn** skriver du "Simon".
   
    c. I **textrutan E-postadress** skriver du Britta Simons e-postadress i Azure Active Directory.
   
    d. Klicka på **Lägg till person**.

## <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till Inloggnings-URL för Workfront där du kan initiera inloggningsflödet. 

* Gå till inloggnings-URL för Workfront direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Workfront i Mina appar omdirigeras den till inloggnings-URL för Workfront. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Workfront kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
