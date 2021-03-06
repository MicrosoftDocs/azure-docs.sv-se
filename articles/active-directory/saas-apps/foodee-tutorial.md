---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med en livsmedelsingrediens | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och en livsmedelsingrediens.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.openlocfilehash: aaa645e7399b4c53aec8b042573882c6a5da4104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-foodee"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med en livsmedelsingrediens

I den här självstudien får du lära dig hur du integrerar en livsmedelsingrediens med Azure Active Directory (Azure AD). När du integrerar en livsmedelsingrediens med Azure AD kan du:

* Kontroll i Azure AD som har till gång till en livsmedelsingrediens.
* Gör det möjligt för användarna att logga in automatiskt till en livsmedelsingrediens med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En prenumeration med enkel inloggning (SSO) för livsmedelsproducerande mat som är aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Livsmedelsingrediensen stöder **SP-och IDP** -INITIERAd SSO
* Livsmedelsingrediensen stöder **just-in-Time** User-etablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-foodee-from-the-gallery"></a>Lägga till en livsmedelsingrediens från galleriet

Du måste lägga till en livsmedelsingrediens från galleriet till din lista över hanterade SaaS-appar för att konfigurera integrering av livsmedelsingrediensen i Azure AD.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. Skriv en **livsmedelsingrediens** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **mat** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-foodee"></a>Konfigurera och testa enkel inloggning med Azure AD för mat man

Konfigurera och testa Azure AD SSO med en livsmedelsingrediens med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i en livsmedelsingrediens.

Om du vill konfigurera och testa Azure AD SSO med en livsmedelsingrediens slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera en livsmedelsingrediens](#configure-foodee-sso)** med enkel inloggning för att konfigurera inställningar för enkel inloggning på program sidan.
    1. Skapa en underställnings **[test användare](#create-foodee-test-user)** – för att få en motsvarighet till B. Simon i en livsmedelsingrediens som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/), på sidan för program integrering av **foder** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://concierge.food.ee/sso/saml/<INSTANCENAME>/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://concierge.food.ee/sso/saml/<INSTANCENAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [klient support teamet](mailto:dev@food.ee) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera en livsmedelsingrediens** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till en livsmedelsingrediens.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **mat** man.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="configure-foodee-sso"></a>Konfigurera enkel inloggning för livsmedelsingredienser

1. Om du vill automatisera konfigurationen i en livsmedelsingrediens måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren, klickar du på **skapa en livsmedelsingrediens** för att dirigera dig till programmet för livsmedelsingrediensen. Därifrån anger du autentiseringsuppgifterna för inloggning för att logga in på livsmedelsingrediensen. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-4.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera en livsmedelsingrediens manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats som administratör och utför följande steg:

4. Klicka på **profil logo typ** i det övre högra hörnet på sidan och navigera sedan till **enkel inloggning** och utför följande steg:

   ![Konfiguration av livsmedelsingrediensen](./media/foodee-tutorial/config01.png)

   1. I text rutan **namn på IDP** skriver du namnet som t. ex.: Azure.
   1. Öppna XML-metadata för federationsmetadata i anteckningar, kopiera innehållet och klistra in det i text rutan **IDP metadata-XML** .
   1. Klicka på **Spara**.

### <a name="create-foodee-test-user"></a>Skapa test användare av näring

I det här avsnittet skapas en användare som heter B. Simon i en mat man. Livsmedelsingrediensen har stöd för just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i en livsmedelsingrediens skapas en ny när du försöker få åtkomst till en livsmedelsingrediens.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för livsmedelsingredienser på åtkomst panelen, bör du loggas in automatiskt på den livsmedelsingrediens som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova livsmedelsingrediensen med Azure AD](https://aad.portal.azure.com/)