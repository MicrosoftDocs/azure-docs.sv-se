---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med identifierings förmåner SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och identifierings förmåner SSO.
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
ms.openlocfilehash: 3beefb467f7875e6bc76765811c6525f5dab9393
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88536250"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-discovery-benefits-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med identifierings förmåner SSO

I den här självstudien får du lära dig hur du integrerar identifierings förmåner SSO med Azure Active Directory (Azure AD). När du integrerar identifierings förmåner SSO med Azure AD kan du:

* Kontroll i Azure AD som har till gång till identifierings förmåner SSO.
* Gör det möjligt för användarna att logga in automatiskt till identifierings förmåner SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Identifierings förmåner SSO enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Identifierings förmåner SSO stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-discovery-benefits-sso-from-the-gallery"></a>Lägga till identifierings förmåner SSO från galleriet

Om du vill konfigurera integrationen av identifierings förmånerna i Azure AD måste du lägga till identifierings förmåner SSO från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **identifierings förmåner SSO** i sökrutan.
1. Välj **identifierings förmåner SSO** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-discovery-benefits-sso"></a>Konfigurera och testa enkel inloggning med Azure AD för inloggning med identifierings förmåner

Konfigurera och testa Azure AD SSO med identifierings förmåner SSO med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SSO med identifierings förmåner.

Om du vill konfigurera och testa Azure AD SSO med SSO med identifierings förmåner slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera identifierings förmåner SSO SSO](#configure-discovery-benefits-sso-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa identifierings förmåner SSO-test användare](#create-discovery-benefits-sso-test-user)** – om du vill ha en motsvarighet till B. Simon i identifierings förmåner SSO som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **identifierings förmåner för SSO** -programintegration i [Azure Portal](https://portal.azure.com/)letar du reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration**   är programmet förkonfigurerat i **IDP**   initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara**   .

1. SSO-programmet för identifierings förmåner förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

    a. Klicka på **Redigera**  -ikonen för att öppna dialog rutan **unik användar identifierare (namn-ID)** .

    ![SSO-konfiguration för identifierings förmåner](./media/discovery-benefits-sso-tutorial/attribute01.png)

    ![SSO-konfiguration för identifierings förmåner](./media/discovery-benefits-sso-tutorial/attribute02.png)

    b. Klicka på **Redigera** -ikonen för att öppna dialog rutan **Hantera omvandling** .

    c. I text rutan **omvandling** skriver du **ToUppercase ()** som visas för den raden.

    d. I text rutan **parameter 1** anger du parametern som `<Name Identifier value>` .

    e. Klicka på **Lägg till**.

    > [!NOTE]
    > Identifierings förmåner SSO kräver ett fast sträng värde som ska skickas i **ett unikt användar-ID (namn-ID)** för att integreringen ska fungera. Azure AD har för närvarande inte stöd för den här funktionen, och du kan använda **ToUpper** -eller **ToLower** -transformeringar av NameID för att ange ett fast sträng värde som visas ovan i skärm bilden.

    f. Vi har automatiskt fyllt i de ytterligare anspråk som krävs för SSO-konfiguration ( `SSOInstance` och `SSOID` ). Använd **redigerings** ikonen för att mappa värdena enligt din organisation.

    ![SSO-konfiguration för identifierings förmåner](./media/discovery-benefits-sso-tutorial/attribute03.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera identifierings förmåner SSO** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till identifierings förmåner SSO.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **identifierings förmåner SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-discovery-benefits-sso-sso"></a>Konfigurera identifierings förmåner SSO SSO

Om du vill konfigurera enkel inloggning på SSO-sidan för **identifierings förmåner** , måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [support teamet för identifierings förmåner SSO](mailto:Jsimpson@DiscoveryBenefits.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-discovery-benefits-sso-test-user"></a>Skapa identifierings förmåner SSO test användare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i INLOGGNINGs förmåner för identifiering. Arbeta med [identifierings teamet för identifiering av identifierings möjligheter](mailto:Jsimpson@DiscoveryBenefits.com) för att lägga till användare i inloggnings plattformen för identifierings förmåner. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på INLOGGNINGs panelen för identifierings förmåner på åtkomst panelen, bör du loggas in automatiskt till INLOGGNINGen med identifierings förmåner för vilka du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova identifierings förmåner SSO med Azure AD](https://aad.portal.azure.com/)

