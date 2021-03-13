---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med en perfekt GlobalLink-instrumentpanel | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och perfekt GlobalLink-instrumentpanel.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 226570a3c752f10ceb21ca624bd428c55ff21e96
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2021
ms.locfileid: "103422478"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-transperfect-globallink-dashboard"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med perfekt GlobalLink-instrumentpanel

I den här självstudien får du lära dig hur du integrerar en perfekt GlobalLink-instrumentpanel med Azure Active Directory (Azure AD). När du integrerar en perfekt GlobalLink-instrumentpanel med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till GlobalLink-instrumentpanel.
* Gör det möjligt för användarna att automatiskt vara inloggade på en perfekt GlobalLink-instrumentpanel med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Inaktive rad prenumeration med enkel inloggning för GlobalLink-instrumentpaneler (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Perfekt GlobalLink-instrumentpanel stöder **SP-och IDP** -initierad SSO.
* Perfekt GlobalLink-instrumentpanel stöder **just-in-Time** -etablering.

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-transperfect-globallink-dashboard-from-the-gallery"></a>Lägga till perfekt GlobalLink-instrumentpanel från galleriet

Om du vill konfigurera integrering av en GlobalLink-instrumentpanel i Azure AD måste du lägga till en perfekt GlobalLink-instrumentpanel från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **GlobalLink-instrumentpanel** i sökrutan.
1. Välj en **perfekt GlobalLink-instrumentpanel** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-transperfect-globallink-dashboard"></a>Konfigurera och testa Azure AD SSO för perfekt GlobalLink-instrumentpanel

Konfigurera och testa Azure AD SSO med en perfekt GlobalLink-instrumentpanel med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i en perfekt GlobalLink-instrumentpanel.

Utför följande steg för att konfigurera och testa Azure AD SSO med en perfekt GlobalLink-instrumentpanel:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera den perfekta GlobalLink-instrument panelen SSO](#configure-transperfect-globallink-dashboard-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa en perfekt GlobalLink-instrument panel test användare](#create-transperfect-globallink-dashboard-test-user)** – om du vill ha en motsvarighet till B. Simon i en perfekt GlobalLink-instrumentpanel som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för integration av **instrument panels** program på GlobalLink och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://sso.transperfect.com`

1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till den GlobalLink instrument panelen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Överperfekt GlobalLink-instrumentpanel**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-transperfect-globallink-dashboard-sso"></a>Konfigurera den perfekta GlobalLink-instrument panelen SSO

Om du vill konfigurera enkel inloggning på en **perfekt GlobalLink-Instrumentpanels** sida måste du skicka **URL: en för appens Federations-metadata** till det [perfekta GlobalLink Dashboard-support teamet](mailto:TechOps_Consulting@transperfect.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-transperfect-globallink-dashboard-test-user"></a>Skapa en perfekt GlobalLink-instrument panel test användare

I det här avsnittet skapas en användare som heter B. Simon i en perfekt GlobalLink-instrumentpanel. Perfekt GlobalLink-instrumentpanel stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i den perfekta GlobalLink-instrumentpanelen skapas en ny när du försöker komma åt GlobalLink-instrumentpanelen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till den överperfekta GlobalLink instrument panel där du kan starta inloggnings flödet.  

* Gå till den överförda URL: en för den GlobalLink inloggnings instrument panelen direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den GlobalLink-instrumentpanel som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på den GlobalLink instrument panels panelen i Mina appar, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den GlobalLink-instrumentpanelen för vilken du ställer in SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat en perfekt GlobalLink-instrumentpanel kan du framtvinga kontroll, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


