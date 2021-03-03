---
title: 'Självstudie: Azure Active Directory integrering med Proofpoint på begäran | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Proofpoint on Demand.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: jeedes
ms.openlocfilehash: e9beb2c9e265982a9e10f3e6abee6bc3cba4a4a3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648483"
---
# <a name="tutorial-azure-active-directory-integration-with-proofpoint-on-demand"></a>Självstudie: Azure Active Directory integrering med Proofpoint på begäran

I den här självstudien får du lära dig hur du integrerar Proofpoint på begäran med Azure Active Directory (Azure AD). När du integrerar Proofpoint på begäran med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Proofpoint på begäran.
* Gör det möjligt för användarna att logga in automatiskt till Proofpoint på begäran med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Proofpoint för enkel inloggning på begäran (SSO) aktive rad prenumeration.

> [!NOTE]
> Om du använder MFA eller lösenordsbaserad autentisering med Azure AD stänger du av AuthnContext-värdet i SAML-begäran. Annars genererar Azure AD fel vid matchning av AuthnContext och skickar inte tillbaka token till programmet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Proofpoint on Demand har stöd för **SP**-initierad enkel inloggning

## <a name="add-proofpoint-on-demand-from-the-gallery"></a>Lägg till Proofpoint på begäran från galleriet

För att kunna konfigurera integreringen av Proofpoint on Demand i Azure AD måste du lägga till Proofpoint on Demand från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Proofpoint på begäran** i sökrutan.
1. Välj **Proofpoint på begäran** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-proofpoint-on-demand"></a>Konfigurera och testa Azure AD SSO för Proofpoint på begäran

Konfigurera och testa Azure AD SSO med Proofpoint på begäran med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Proofpoint på begäran.

Utför följande steg för att konfigurera och testa Azure AD SSO med Proofpoint på begäran:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Proofpoint på begäran SSO](#configure-proofpoint-on-demand-sso)** -om du vill konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Proofpoint på begäran-testa användare](#create-proofpoint-on-demand-test-user)** -om du vill ha en motsvarighet till B. Simon i Proofpoint på begäran som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för program integration i **Proofpoint på begäran** , hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Proofpoint on Demand – enkel inloggning-information för domän och -URL:er](common/sp-identifier-reply.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<hostname>.pphosted.com/ppssamlsp_hostname`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<hostname>.pphosted.com/ppssamlsp`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<hostname>.pphosted.com:portnumber/v1/samlauth/samlconsumer`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Proofpoint on Demand-kundsupporten](https://www.proofpoint.com/us/support-services) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Proofpoint on Demand** kopierar du en eller flera lämpliga URL:er, enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Proofpoint på begäran.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Proofpoint on Demand** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-proofpoint-on-demand-sso"></a>Konfigurera Proofpoint vid inloggning på begäran

För att kunna konfigurera enkel inloggning på **Proofpoint on Demand**-sidan måste du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [Proofpoint on Demand-supporten](https://www.proofpoint.com/us/support-services). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-proofpoint-on-demand-test-user"></a>Skapa Proofpoint on Demand-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Proofpoint on Demand. Kontakta [Proofpoint on Demand-kundsupporten](https://www.proofpoint.com/us/support-services) för att lägga till användare på Proofpoint on Demand-plattformen.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Proofpoint på begäran inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Proofpoint på begäran inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Proofpoint på begäran i Mina appar, bör du loggas in automatiskt på den Proofpoint på begäran som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Proofpoint på begäran kan du framtvinga en sessionshantering som skyddar exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).