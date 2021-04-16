---
title: 'Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med 4me-| Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 4me.
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
ms.openlocfilehash: f2a171460defb3a8befba5a674a2f1aadde636af
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517835"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-4me"></a>Självstudie: Azure Active Directory integrering med enkel inloggning (SSO) med 4me

I den här självstudien lär du dig att integrera 4me med Azure Active Directory (Azure AD). När du integrerar 4me med Azure AD kan du:

* Kontrollera i Azure AD vem som har åtkomst till 4me.
* Gör så att dina användare automatiskt loggas in på 4me med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)
* 4me-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* 4me stöder **SP-initierad** enkel inloggning.
* 4me stöder **just-in-time-användareablering.**

## <a name="add-4me-from-the-gallery"></a>Lägga till 4me från galleriet

När du ska konfigurera integreringen av 4me i Azure AD måste du lägga till 4me från galleriet i listan med hanterade SaaS-appar.

1. Logga in på Azure Portal med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory** tjänst.
1. Gå till **Företagsprogram** och välj sedan **Alla program.**
1. Om du vill lägga till ett nytt program väljer **du Nytt program**.
1. I avsnittet **Lägg till från** galleriet skriver du **4me** i sökrutan.
1. Välj **4me** i resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-sso-for-4me"></a>Konfigurera och testa enkel inloggning med Azure AD för 4me

Konfigurera och testa enkel inloggning för Azure AD med 4me med hjälp av en testanvändare med **namnet B.Simon.** För att enkel inloggning ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i 4me.

Slutför följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD med 4me:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att B.Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera 4me SSO](#configure-4me-sso)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
    1. **[Skapa 4me-testanvändare](#create-4me-test-user)** – för att ha en motsvarighet för B.Simon i 4me som är länkad till En Azure AD-representation av användaren.
1. **[Testa enkel inloggning](#test-sso)** – för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera enkel inloggning med Azure AD i Azure Portal.

1. På Azure Portal **4me-programintegreringssidan** hittar du avsnittet **Hantera** och väljer **enkel inloggning.**
1. På sidan **Välj en metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML klickar** du på pennikonen för Grundläggande **SAML-konfiguration för** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    a. I **textrutan Inloggnings-URL** skriver du en URL med något av följande mönster:

    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    b. I **textrutan Identifierare (entitets-ID)** anger du en URL med något av följande mönster:

    | Miljö| URL|
    |---|---|
    | PRODUKTION | `https://<SUBDOMAIN>.4me.com`|
    | QA| `https://<SUBDOMAIN>.4me.qa`|
    | | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [4me-supportteamet](mailto:support@4me.com) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. 4me-programmet förväntar sig SAML-försäkran i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig 4me-programmet att några fler attribut skickas tillbaka i SAML-svaret som visas nedan. Dessa attribut är också ifyllda i förväg, men du kan granska dem enligt dina behov.

    | Name | Källattribut|
    | ---------------| --------------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

1. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

1. I **avsnittet Konfigurera 4me** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet gör du det möjligt för B.Simon att använda enkel inloggning med Azure genom att ge åtkomst till 4me.

1. I Azure Portal väljer du **Företagsprogram** och sedan **Alla program**.
1. Välj **4me** i listan med program.
1. På appens översiktssida hittar du avsnittet **Hantera** och väljer **Användare och grupper.**
1. Välj **Lägg till** användare och sedan Användare och **grupper** i dialogrutan Lägg **till** tilldelning.
1. I **dialogrutan Användare och** grupper väljer du **B.Simon** i  listan Användare och klickar sedan på knappen Välj längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas till användarna kan du välja den i **listrutan Välj en** roll. Om ingen roll har ställts in för den här appen visas rollen "Standardåtkomst".
1. I dialogrutan **Lägg till** tilldelning klickar du på **knappen** Tilldela.

## <a name="configure-4me-sso"></a>Konfigurera 4me SSO

1. I ett annat webbläsarfönster loggar du in på 4me som administratör.

1. Klicka på **Inställningar** uppe till vänster och på **Enkel inloggning** i det vänstra sidofältet.

    ![4me-inställningar](./media/4me-tutorial/settings.png)

1. På sidan **Enkel inloggning** utför du följande steg:

    ![4me enkel inloggning](./media/4me-tutorial/single-sign-on.png)

    a. Välj alternativet **Aktiverad**.

    b. I textrutan **Remote Logout URL** (URL för fjärrutloggning) klistrar du in värdet för **Utloggnings-URL** som du kopierade från Azure-portalen.

    c. Under avsnittet **SAML**, i textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in värdet för **Inloggnings-URL** som du kopierade från Azure Portal.

    d. I textrutan **Fingeravtryck för certifikat** klistrar du in **TUMAVTRYCK**-värdet avgränsat med kolon i dubbletter (AA:BB:CC:DD:EE:FF:GG:HH:II), som du kopierade från Azure Portal.

    e. Klicka på **Spara**.

### <a name="create-4me-test-user"></a>Skapa 4me-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i 4me. 4me har stöd för användaretablering enligt just-in-time, och det är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om användaren inte redan finns i 4me skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för 4me.](mailto:support@4me.com)

## <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen av enkel inloggning med Azure AD med följande alternativ. 

* Klicka på **Testa det här programmet** i Azure Portal. Detta omdirigerar till 4me inloggnings-URL där du kan initiera inloggningsflödet. 

* Gå till 4me Inloggnings-URL direkt och initiera inloggningsflödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på 4me-panelen i Mina appar omdirigeras detta till 4me inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat 4me kan du framtvinga sessionskontroll, vilket skyddar exfiltrering och infiltrering av organisationens känsliga data i realtid. Sessionskontrollen utökas från villkorlig åtkomst. [Lär dig hur du framtvingar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
