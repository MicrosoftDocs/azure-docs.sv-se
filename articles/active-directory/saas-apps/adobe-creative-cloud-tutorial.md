---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Adobe Creative Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Creative Cloud.
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
ms.openlocfilehash: 553f77cdb42aa0adb230ee3efd7bec7e9fbfa972
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Adobe Creative Cloud

I den här självstudien får du lära dig hur du integrerar Adobe Creative Cloud med Azure Active Directory (Azure AD). När du integrerar Adobe Creative Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Adobe Creative Cloud.
* Gör det möjligt för användarna att logga in automatiskt för att Adobe Creative Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Adobe Creative Cloud enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Adobe Creative Cloud stöder **SP**-initierad enkel inloggning

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Lägg till Adobe Creative Cloud från galleriet

Om du vill konfigurera integreringen av Adobe Creative Cloud i Azure Active Directory måste du lägga till Adobe Creative Cloud från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Adobe Creative Cloud** i sökrutan.
1. Välj **Adobe Creative Cloud** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Konfigurera och testa Azure AD SSO för Adobe Creative Cloud

Konfigurera och testa Azure AD SSO med Adobe Creative Cloud med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Adobe Creative Cloud.

Utför följande steg för att konfigurera och testa Azure AD SSO med Adobe Creative Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Adobe Creative Cloud SSO](#configure-adobe-creative-cloud-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Adobe Creative Cloud testa användare](#create-adobe-creative-cloud-test-user)** – om du vill ha en motsvarighet till B. Simon i Adobe Creative Cloud som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för **Adobe Creative Cloud** program integrering och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **Inloggnings-URL** anger du följande URL: `https://adobe.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://www.okta.com/saml2/service-provider/<token>`

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Följ anvisningarna i avsnittet steg 4 i **Konfigurera Adobe Cloud SSO** . I kan du öppna **XML-filen för federationsmetadata** och hämta ENTITETS-ID-värdet från den och placera det som ett ID-värde i Azure AD-konfigurationen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Adobe Creative Cloud programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig Adobe Creative Cloud-program att fler attribut skickas tillbaka i SAML-svar, som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |

    > [!NOTE]
    > Användare måste ha en giltig Microsoft 365 ExO-licens för att e-postanspråk svärdet ska vara ifyllt i SAML-svaret.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **Federations data XML** och väljer sedan **Ladda ned** för att ladda ned XML-metadatafilen och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **konfigurera Adobe Creative Cloud** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Adobe Creative Cloud.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I listan med program väljer du **Adobe Creative Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer du sedan **användare och grupper**.
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-adobe-creative-cloud-sso"></a>Konfigurera Adobe Creative Cloud SSO

1. Logga in på [Adobe admin-konsolen](https://adminconsole.adobe.com) som system administratör i ett annat webbläsarfönster.

1. Gå till **Inställningar** i det övre navigerings fältet och välj sedan **identitet**. Listan över kataloger öppnas. Välj den federerade katalogen som du vill använda.

1. På sidan **katalog information** väljer du **Konfigurera**.

1. Kopiera entitets-ID: t och ACS-URL: en (intygs mottagarens URL eller svars-URL). Ange webb adresserna i lämpliga fält i Azure Portal.

    ![Konfigurera enkel inloggning på App-sidan](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Använd värdet för entitets-ID: t Adobe förutsatt att du har **identifierat** i dialog rutan **Konfigurera app-inställningar** .

    b. Använd värdet för ACS URL (intygets konsument tjänst-URL) för att ange en **svars-URL** i dialog rutan **Konfigurera app-inställningar** .

1. Längst ned på sidan laddar du upp den **XML-fil för Federations data** som du laddade ned från Azure Portal. 

    ![Federations data XML-fil](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "XML för IdP metadata")

1. Välj **Spara**.

### <a name="create-adobe-creative-cloud-test-user"></a>Skapa testanvändare för Adobe Creative Cloud

För att Azure AD-användare ska kunna logga in på Adobe Creative Cloud måste de tillhandahållas i Adobe Creative Cloud. När det gäller Adobe Creative Cloud är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla ett användarkonto:

1. Logga in på [Adobe-administratörskonsolen](https://adminconsole.adobe.com) som administratör.

2. Lägg till användaren i Adobe-konsolen som externt ID och tilldela dem till en produktprofil. Detaljerad information om hur du lägger till användare finns i [lägga till användare i Adobe admin-konsolen](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. I det här läget skriver du din e-postadress/UPN i Adobe-inloggnings formuläret, trycker på TABB och du ska återställa till Azure AD:
   * Webb åtkomst: www- \. adobe.com > inloggning
   * I verktyget skrivbordsapp > Logga in
   * I programmet > Hjälp > Logga in

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Adobe Creative Cloud inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Adobe Creative Cloud inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Adobe Creative Cloud i Mina appar omdirigeras det till Adobe Creative Cloud inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Adobe Creative Cloud kan du framtvinga kontroll över sessioner, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)