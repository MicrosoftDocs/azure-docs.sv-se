---
title: 'Självstudie: Azure Active Directory integration med OneTrust-program för sekretess hantering | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OneTrust-program för sekretess hantering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: f0a145b0eb9dd9dbed0927ce825a21d8f47c48ab
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101648449"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Självstudie: Azure Active Directory integration med OneTrust-program för sekretess hantering

I den här självstudien får du lära dig att integrera OneTrust-program för sekretess hantering med Azure Active Directory (Azure AD). När du integrerar OneTrust-program för sekretess hantering med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till OneTrust-program för sekretess hantering.
* Gör det möjligt för användarna att logga in automatiskt till OneTrust-program för sekretess hantering med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med OneTrust-program för sekretess hantering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* OneTrust-prenumeration för sekretess hantering för program vara med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* OneTrust-program för sekretess hantering stöder **SP** -och **IDP** -initierad SSO

* OneTrust Privacy Management-programvara stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="add-onetrust-privacy-management-software-from-the-gallery"></a>Lägg till OneTrust Privacy Management-programvara från galleriet

Om du vill konfigurera integrering av OneTrust-program för sekretess hantering i Azure AD måste du lägga till OneTrust Privacy Management-programvara från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **OneTrust Privacy Management Software** i sökrutan.
1. Välj **OneTrust-program för sekretess hantering** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-onetrust-privacy-management-software"></a>Konfigurera och testa Azure AD SSO för OneTrust-program för sekretess hantering

Konfigurera och testa Azure AD SSO med OneTrust-program för sekretess hantering med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i OneTrust Privacy Management-programvara.

Utför följande steg för att konfigurera och testa Azure AD SSO med OneTrust Privacy Management-programvara:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera OneTrust Privacy Management Software SSO](#configure-onetrust-privacy-management-software-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa OneTrust Privacy Management Software test User](#create-onetrust-privacy-management-software-test-user)** – för att få en motsvarighet till B. Simon InOneTrust Privacy Management-programvara som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.
 
1. I Azure Portal går du till sidan för program integrering för **OneTrust-Sekretesspolicy** och letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj en enda Sign-On metod** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. Skriv webb adressen i text rutan **identifierare** : `https://www.onetrust.com/saml2`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska svars-URL:en och inloggnings-URL:en. Kontakta [OneTrust Software support team support team](mailto:support@onetrust.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. I avsnittet **Konfigurera OneTrust för sekretess hantering** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.
1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryss rutan **Visa lösen ord** och skriv sedan lösen ordet.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till OneTrust-program för sekretess hantering.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan program väljer du **OneTrust Privacy Management-programvara**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="configure-onetrust-privacy-management-software-sso"></a>Konfigurera OneTrust Privacy Management Software SSO

Om du vill konfigurera enkel inloggning på **OneTrust för sekretess hantering** måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [OneTrust support team för sekretess Management Software](mailto:support@onetrust.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Skapa OneTrust Privacy Management Software test User

I det här avsnittet skapas en användare som heter Britta Simon i OneTrust Privacy Management-programvaran. OneTrust Privacy Management-programvara stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OneTrust-program för sekretess hantering, skapas en ny efter autentiseringen.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [OneTrust Privacy Management support team](mailto:support@onetrust.com).

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till OneTrust för program vara för sekretess hantering där du kan starta inloggnings flödet.  
 
* Gå till OneTrust för sekretess hanteringens inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den OneTrust-Sekretesspolicy för program vara som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen OneTrust Privacy Management Software i Mina appar, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till OneTrust Privacy Management-programvaran som du konfigurerar SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat OneTrust Privacy Management-programvara kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).