---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med iProva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iProva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552851"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med iProva

I den här självstudien får du lära dig hur du integrerar iProva med Azure Active Directory (Azure AD). När du integrerar iProva med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till iProva.
* Gör det möjligt för användarna att logga in automatiskt till iProva med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* iProva för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* iProva stöder **SP**-initierad enkel inloggning

* När du har konfigurerat iProva kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-iprova-from-the-gallery"></a>Lägga till iProva från galleriet

För att kunna konfigurera integreringen av iProva i Azure AD måste du lägga till iProva från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **iProva** i sökrutan.
1. Välj **iProva** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>Konfigurera och testa enkel inloggning med Azure AD för iProva

Konfigurera och testa Azure AD SSO med iProva med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i iProva.

Om du vill konfigurera och testa Azure AD SSO med iProva, slutför du följande Bygg stenar:

1. **[Hämta konfigurationsinformation från](#retrieve-configuration-information-from-iprova)** – som förberedelse för nästa steg.
1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Skapa iProva test User](#create-iprova-test-user)** -om du vill ha en motsvarighet till B. Simon i iProva som är länkad till Azure AD-representation av användare.
1. **[Konfigurera IPROVA SSO](#configure-iprova-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="retrieve-configuration-information-from-iprova"></a>Hämta konfigurationsinformation från iProva

I det här avsnittet hämtar du information från iProva för att konfigurera enkel inloggning i Azure AD.

1. Öppna en webbläsare och gå till sidan **SAML2-info** i iProva med hjälp av följande URL-mönster:

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![Visa SAML2-informationssidan i iProva](media/iprova-tutorial/iprova-saml2-info.png)

1. Låt webbläsarfliken vara öppen medan du fortsätter med nästa steg i en annan webbläsarflik.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **iProva** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. Fyll i rutan **Inloggnings-URL** med det värde som visas bakom etiketten **Inloggnings-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    b. Fyll i rutan **Identifierare** med det värde som visas bakom etiketten **EntityID** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

    c. Fyll i rutan **Svars-URL** med det värde som visas bakom etiketten **Svars-URL** på sidan **iProva SAML2 info** (SAML2-info för iProva). Den här sidan är fortfarande öppen på den andra webbläsarfliken.

1. iProva-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig iProva-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut| Namnområde  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

## <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till iProva.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **iProva** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="create-iprova-test-user"></a>Skapa iProva-testanvändare

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Användare** i panelen **Användare och användargrupper**.

5. Välj **Lägg till**.

6. I rutan **användar namn** anger du användar namnet för användaren `B.Simon@contoso.com` .

7. I rutan **fullständigt namn** anger du det fullständiga namnet på användaren, t. ex. **B. Simon**.

8. Välj alternativet **Inget lösenord (använd enkel inloggning)**.

9. I rutan **e-postadress** anger du e-postadressen till användaren `B.Simon@contoso.com` .

10. Rulla ned till slutet av sidan och välj **Slutför**.

## <a name="configure-iprova-sso"></a>Konfigurera iProva SSO

1. Logga in på iProva med hjälp av **administratörskontot**.

2. Öppna menyn **Gå till**.

3. Välj **Programhantering**.

4. Välj **Allmänt** i panelen **Systeminställningar**.

5. Välj **Redigera**.

6. Rulla ned till **Åtkomstkontroll**.

    ![Inställningar för åtkomstkontroll i iProva](media/iprova-tutorial/iprova-accesscontrol.png)

7. Hitta inställningen **Users are automatically logged on with their network accounts** (Användare loggas automatiskt in med nätverkskonton) och ändra det till **Yes, authentication via SAML** (Ja, autentisering visa SAML). Ytterligare alternativ visas nu.

8. Välj **Konfigurera**.

9. Välj **Nästa**.

10. iProva frågar dig om du vill ladda ned federationsdata från en URL eller ladda upp dem från en fil. Välj alternativet **Från URL**.

    ![Ladda ned Azure AD-metadata](media/iprova-tutorial/iprova-download-metadata.png)

11. Klistra in den metadata-URL som du sparade i det sista steget i kapitlet Konfigurera enkel inloggning med Azure AD.

12. Välj den pilformade knappen för att ladda ned metadata från Azure AD.

13. När nedladdningen är slutförd visas bekräftelsemeddelandet om **Valid Federation Data file downloaded** (Giltig federationsdatafil har laddats ned).

14. Välj **Nästa**.

15. Hoppa över alternativet **Testa inloggning** för tillfället och välj **Nästa**.

16. I listrutan **Claim to use** (Anspråk att använda) väljer du **windowsaccountname**.

17. Välj **Slutför**.

18. Nu kan du gå tillbaka till sidan **Redigera allmänna inställningar**. Rulla ned till slutet av sidan och välj **OK** för att spara konfigurationen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på iProva-panelen i åtkomstpanelen så bör du automatiskt loggas in på den iProva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova iProva med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du iProva med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)