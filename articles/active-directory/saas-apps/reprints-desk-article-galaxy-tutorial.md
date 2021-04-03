---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med återutskrift – artikel galax | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och skriver om Desk-artikel galax.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.openlocfilehash: e28281b783c66f8dbb0bc4842679eeec43755508
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515010"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-reprints-desk---article-galaxy"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med omskrivna av skriv bord – artikel galax

I den här självstudien får du lära dig hur du integrerar utskrift av skriv bord – artikel Galaxy med Azure Active Directory (Azure AD). När du integrerar utskrift av skriv bord – artikel Galaxy med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till reprinter – artikel galax.
* Gör det möjligt för användarna att vara inloggade automatiskt för att skriva om Skriv bords artikel galax med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Skriver ut skriv bord – artikel Galaxy-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Skriv ut skriv bord – artikel Galaxy stöder **IDP** INITIERAd SSO

* Skriv ut skriv bord – artikel Galaxy stöder **just-in-Time** User-etablering

* [När du har konfigurerat omskrivning av skriv bord – artikel Galaxy kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-reprints-desk---article-galaxy-from-the-gallery"></a>Lägga till Skriv ut skriv bord – artikel Galaxy från galleriet

Om du vill konfigurera integrering av omskrivning av skriv bord – artikel Galaxy i Azure AD måste du lägga till reprinter – artikel Galaxy från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du Skriv **ut skriv bord – artikel galax** i sökrutan.
1. Välj **Skriv ut på Skriv bordet – artikel Galaxy** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-reprints-desk---article-galaxy"></a>Konfigurera och testa enkel inloggning med Azure AD för att skriva om skriv bord – artikel galax

Konfigurera och testa Azure AD SSO med reprinter – artikel Galaxy med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i reprinter Desk-artikel galax.

Om du vill konfigurera och testa Azure AD SSO med reprinter-artikel Galaxy, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Skriv ut Skriv ut artikel Galaxy SSO](#configure-reprints-desk-article-galaxy-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Skriv ut Skriv om Skriv bords artikel Galaxy-test användare](#create-reprints-desk-article-galaxy-test-user)** – om du vill ha en motsvarighet till B. Simon i reprinting – artikel Galaxy som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för att skriva om Skriv **bord – artikel Galaxy-** programintegration, letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .


1. Skriv ut skriv bord – artikel Galaxy-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Förutom över, förväntar sig Skriv bordet-artikel Galaxy-program att några fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ------------ | --------- |
    | förnamn | user.givenname |
    | efternamn | user.surname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. På sidan **Konfigurera omskrivning av skriv bord – artikel galax** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till reprinting Desk-artikel galax.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Skriv om skriv bord – artikel galax**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-reprints-desk-article-galaxy-sso"></a>Konfigurera Skriv ut Skriv ut artikel Galaxy SSO

Om du vill konfigurera enkel inloggning på Skriv **bordet – artikel galax** , måste du skicka den hämtade **XML-koden för federationsmetadata** och lämpliga kopierade url: er från Azure Portal för att [skriva om Skriv bordet – artikel Galaxy support team](mailto:customersupport@reprintsdesk.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-reprints-desk-article-galaxy-test-user"></a>Skapa skriva om skriv bord artikel Galaxy-test användare

I det här avsnittet skapas en användare som heter B. Simon i reprinter – artikel galax. Skriv ut skriv bord – artikel Galaxy stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i reprinter – artikel Galaxy, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Skriv ut – artikel galax på åtkomst panelen, bör du loggas in automatiskt på den Skriv bords-artikel Galaxy som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Försök skriva ut på Skriv bordet – artikel galax med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du utskriften av skriv bord – artikel Galaxy med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)