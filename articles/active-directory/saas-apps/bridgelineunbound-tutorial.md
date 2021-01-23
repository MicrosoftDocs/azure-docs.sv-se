---
title: 'Självstudie: Azure Active Directory integrering med bridgeline obundet | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bridgeline Unbound.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 6b8ad268087d7371519e02a9d7614b207a8c4ac0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730562"
---
# <a name="tutorial-azure-active-directory-integration-with-bridgeline-unbound"></a>Självstudie: Azure Active Directory integrering med bridgeline obundet

I den här självstudien får du lära dig att integrera bridgeline-obundna med Azure Active Directory (Azure AD). När du integrerar bridgeline-obundna med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till bridgeline-obundna.
* Gör det möjligt för användarna att logga in automatiskt för att bridgeline sig kopplade till sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

Du behöver följande för att konfigurera Azure AD-integrering med Bridgeline Unbound:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Bridgeline Unbound-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Bridgeline Unbound har stöd för **SP- och IDP**-initierad enkel inloggning
* Bridgeline Unbound har stöd för **just-in-time**-användaretablering

## <a name="adding-bridgeline-unbound-from-the-gallery"></a>Lägga till Bridgeline Unbound från galleriet

För att konfigurera integrering av Bridgeline Unbound i Azure AD behöver du lägga till Bridgeline Unbound från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **bridgeline unbound** i sökrutan.
1. Välj **bridgeline obunden** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-bridgeline-unbound"></a>Konfigurera och testa Azure AD SSO för bridgeline-obundna

Konfigurera och testa Azure AD SSO med bridgeline obundet med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i bridgeline-obundna.

Utför följande steg för att konfigurera och testa Azure AD SSO med bridgeline obundet:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera bridgeline-OBUNDNA SSO](#configure-bridgeline-unbound-sso)** -om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Bridgeline Unbound-testanvändare](#create-bridgeline-unbound-test-user)** – för att ha en motsvarighet till Britta Simon i Bridgeline Unbound som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **bridgeline obunden** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)
4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `iApps_UPSTT_<ENVIRONMENTNAME>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.iapps.com/SAMLAssertionService.aspx`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.iapps.com/CommonLogin/login?<INSTANCENAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [kundsupporten för Bridgeline Unbound](mailto:support@iapps.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera Bridgeline Unbound** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till bridgeline-obundna.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Bridgeline Unbound**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .


## <a name="configure-bridgeline-unbound-sso"></a>Konfigurera bridgeline-obundet SSO

För att konfigurera enkel inloggning på **Bridgeline Unbound**-sidan behöver du skicka det nedladdade **certifikatet (Base64)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Bridgeline Unbound](mailto:support@iapps.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-bridgeline-unbound-test-user"></a>Skapa Bridgeline Unbound-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Bridgeline Unbound. Bridgeline Unbound har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Bridgeline Unbound skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till bridgeline-obunden inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till bridgeline för den obundna inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så ska du loggas in automatiskt på den bridgeline som du ställer in SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på den obundna panelen bridgeline i Mina appar, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den bridgeline som du ställer in SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat bridgeline obundet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).