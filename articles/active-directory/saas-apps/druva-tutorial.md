---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med druva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.openlocfilehash: d0dffe602aef01af2fb2ef8ee9462a0bb99c1ae0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555696"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med druva

I den här självstudien får du lära dig hur du integrerar druva med Azure Active Directory (Azure AD). När du integrerar druva med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till druva.
* Gör det möjligt för användarna att logga in automatiskt till druva med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Druva för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Druva stöder **IDP** INITIERAd SSO
* När du har konfigurerat druva SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-druva-from-the-gallery"></a>Lägga till Druva från galleriet

För att konfigurera integrering av Druva i Azure AD behöver du lägga till Druva från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **druva** i sökrutan.
1. Välj **druva** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Konfigurera och testa enkel inloggning med Azure AD för druva

Konfigurera och testa Azure AD SSO med druva med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i druva.

Om du vill konfigurera och testa Azure AD SSO med druva, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera druva SSO](#configure-druva-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa druva test User](#create-druva-test-user)** -om du vill ha en motsvarighet till B. Simon i druva som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **druva** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **identifierare (enhets-ID)** anger du sträng värde: `DCP-login` .
    
    b. I text rutan **svars-URL: en (intyg om mottagar tjänst)** anger du URL: en: `https://cloud.druva.com/wrsaml/consume` .

1. Klicka på **Spara**.

1. Druva-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig druva-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ------------------- | -------------------- |
    | emailAddress | User. email |
    | druva_auth_token | SSO-token genereras från DCP-administratörskonsolen, utan citat tecken.  Exempel: X-XXXXX-XXXX-S-A-M-P-L-E + TXOXKXEXNX =. Azure lägger automatiskt till citat tecken runt auth-token. |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera druva** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till druva.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Druva**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-druva-sso"></a>Konfigurera druva SSO

1. Logga in på din druva-företags webbplats som administratör i ett annat webbläsarfönster.

1. Klicka på druva-logotypen i det övre vänstra hörnet och klicka sedan på **druva Cloud Settings**.

    ![Inställningar](./media/druva-tutorial/ic795091.png "Inställningar")

1. På fliken **enkel inloggning** klickar du på **Redigera**.

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795092.png "Inställningar för enkel inloggning")

1. Utför följande steg på sidan **Redigera inställningar för enkel inloggning** :

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795095.png "Inställningar för enkel inloggning")

    1. I textrutan **ID Provider Login URL** (Inloggnings-URL för ID-provider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    1. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **ID Provider Certificate** (Certifikat för ID-provider)

       > [!NOTE]
       > Om du vill aktivera enkel inloggning för administratörer väljer du **Administratörer loggar in på druva Cloud via SSO-providern** och **ger Failsafe åtkomst till druva Cloud administrators (rekommenderas)** kryss rutor. Druva rekommenderar att du aktiverar **Failsafe för administratörer** så att de har åtkomst till DCP-konsolen i händelse av eventuella problem i IDP. Det gör det också möjligt för administratörer att använda både SSO-och DCP-lösenord för att komma åt DCP-konsolen.

    1. Klicka på **Spara**. Detta ger åtkomst till druva Cloud Platform med SSO.

### <a name="create-druva-test-user"></a>Skapa Druva-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i druva. Druva stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i druva skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Druva-panelen i åtkomstpanelen bör du automatiskt loggas in på Druva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova druva med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)