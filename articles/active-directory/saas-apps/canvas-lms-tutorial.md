---
title: 'Självstudie: Azure Active Directory integrering med arbets ytan | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och arbetsytor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: a71dac55c860348f31ce8da27ab050a6c71a5c68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653076"
---
# <a name="tutorial-azure-active-directory-integration-with-canvas"></a>Självstudie: Azure Active Directory integrering med arbets yta

I den här självstudien får du lära dig hur du integrerar arbets ytor med Azure Active Directory (Azure AD). När du integrerar en arbets yta med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till arbets ytan.
* Gör det möjligt för användarna att logga in automatiskt på arbets ytan med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:
 
* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Enkel inloggning för arbets yta (SSO) – aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Canvas stöder **SP**-initierad enkel inloggning

## <a name="add-canvas-from-the-gallery"></a>Lägg till arbets yta från galleriet

För att konfigurera integreringen av Canvas i Azure AD måste du lägga till Canvas från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **arbets yta** i sökrutan.
1. Välj **arbets ytan** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-canvas"></a>Konfigurera och testa Azure AD SSO för arbets ytan

Konfigurera och testa Azure AD SSO med arbets ytan med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i arbets ytan.

Utför följande steg för att konfigurera och testa Azure AD SSO med arbets ytan:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera arbets ytan SSO](#configure-canvas-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa arbets ytans test användare](#create-canvas-test-user)** – om du vill ha en motsvarighet till B. Simon på arbets ytan som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **arbets ytans** program integrering letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Canvas-domäner och -URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenant-name>.instructure.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<tenant-name>.instructure.com/saml2`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Canvas kundsupport](https://community.canvaslms.com/community/help) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-thumbprint.png)

7. I avsnittet **Konfigurera Canvas** kopierar du lämpliga URL:er baserat på dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till arbets ytan.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Canvas** i listan med program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-canvas-sso"></a>Konfigurera SSO-arbetsyta

1. Öppna ett nytt webbläsarfönster och logga in på din Canvas-företagswebbplats som administratör.

2. Gå till **Kurser \> Hanterade konton \> Microsoft**.

    ![Arbetsyta](./media/canvas-lms-tutorial/ic775990.png "Arbetsyta")

3. Välj **Autentisering** i navigeringsfönstret till vänster och klicka sedan på **Lägg till ny SAML-konfiguration**.

    ![Autentisering](./media/canvas-lms-tutorial/ic775991.png "Autentisering")

4. Utför följande steg på sidan Aktuell integration:

    ![Aktuell integrering](./media/canvas-lms-tutorial/save.png "Aktuell integrering")

    a. I textrutan **IdP Entity ID** (Entitets-ID för IdP) klistrar du in värdet för **Azure Ad-identifieraren**, som du har kopierat från Azure-portalen.

    b. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. I textrutan för **utloggnings-URL** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. I textrutan för **länken för lösenordsändring** klistrar du in värdet för **URL:en för lösenordsändring** som du har kopierat från Azure-portalen.

    e. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in **tumavtrycksvärdet** för certifikatet som du har kopierat från Azure-portalen.

    f. I listan med **attribut för inloggning** väljer du **NameID**.

    ex. Välj **emailAddress** i listan med **format för identifierare**.

    h. Klicka på **Spara autentiseringsinställningar**.

### <a name="create-canvas-test-user"></a>Skapa Canvas-testanvändare

För att Azure AD-användare ska kunna logga in i Canvas måste de vara etablerade i Canvas. När det gäller Canvas är etableringen av användare en manuell åtgärd.

**Gör följande för att etablera ett användarkonto:**

1. Logga in i din **Canvas**-klientorganisation.

2. Gå till **Kurser \> Hanterade konton \> Microsoft**.

   ![Arbetsyta](./media/canvas-lms-tutorial/ic775990.png "Arbetsyta")

3. Klicka på **Användare**.

   ![Skärm bild som visar arbets ytans meny med valda användare.](./media/canvas-lms-tutorial/ic775995.png "Användare")

4. Klicka på **Lägg till ny användare**.

   ![Skärm bild som visar knappen Lägg till en ny användare.](./media/canvas-lms-tutorial/ic775996.png "Användare")

5. Utför följande steg i dialogrutan Lägg till en ny användare:

   ![Lägg till användare](./media/canvas-lms-tutorial/ic775997.png "Lägg till användare")

   a. I textrutan **Fullständigt namn** anger du namnet på användaren, t.ex. **BrittaSimon**.

   b. I text rutan **e-post** anger du e-postadressen till användaren som **brittasimon \@ contoso.com**.

   c. I text rutan **inloggning** anger du användarens Azure AD-e-postadress som **brittasimon \@ contoso.com**.

   d. Välj alternativet för att **meddela användaren via e-post när kontot skapats**.

   e. Klicka på **Lägg till användare**.

> [!NOTE]
> Du kan använda andra verktyg för skapande av användar konton eller API: er från arbets ytan för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till inloggnings-URL: en för arbets ytan där du kan starta inloggnings flödet. 

* Gå till arbets ytans inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på arbets ytans ikon i Mina appar, bör du loggas in automatiskt på den arbets yta som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat arbets ytan kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).