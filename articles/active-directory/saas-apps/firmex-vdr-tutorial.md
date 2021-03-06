---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Firmex VDR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Firmex VDR.
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
ms.openlocfilehash: 6dbd39b5c56192ad2ca957c5500338b50e8c8963
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92453394"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Firmex VDR

I den här självstudien får du lära dig att integrera Firmex-VDR med Azure Active Directory (Azure AD). När du integrerar Firmex-VDR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Firmex-VDR.
* Gör det möjligt för användarna att logga in automatiskt till Firmex VDR med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Firmex VDR-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Firmex VDR stöder **SP-och IDP** -INITIERAd SSO

* När du har konfigurerat Firmex kan du framtvinga sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Lägga till Firmex VDR från galleriet

Om du vill konfigurera integreringen av Firmex-VDR i Azure AD måste du lägga till Firmex VDR från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Firmex vdr** i sökrutan.
1. Välj **FIRMEX vdr** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Konfigurera och testa enkel inloggning med Azure AD för Firmex-VDR

Konfigurera och testa Azure AD SSO med Firmex VDR med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Firmex VDR.

Om du vill konfigurera och testa Azure AD SSO med Firmex VDR slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FIRMEX vdr SSO](#configure-firmex-vdr-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa FIRMEX vdr test User](#create-firmex-vdr-test-user)** – om du vill ha en motsvarighet till B. Simon i Firmex vdr som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Firmex vdr** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://login.firmex.com`

1. Klicka på **Spara**.

1. Firmex VDR-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Firmex VDR-program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ------------ | --------- |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **set-up FIRMEX vdr** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Firmex VDR.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **FIRMEX vdr**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-firmex-vdr-sso"></a>Konfigurera Firmex VDR SSO

### <a name="before-you-get-started"></a>Innan du börjar

#### <a name="what-youll-need"></a>Vad du behöver

-   En aktiv Firmex-prenumeration
-   Azure AD som SSO-tjänst
-   IT-administratören för att konfigurera SSO
-   När SSO har Aktiver ATS måste alla användare i ditt företag Logga in på Firmex med enkel inloggning och inte använda inloggning/lösen ord.

#### <a name="how-long-will-this-take"></a>Hur lång tid tar det?

Det tar några minuter att implementera SSO. Det finns i princip inget avbrott mellan Firmex-stöd för att aktivera SSO för din webbplats och ditt företags användare som autentiserar med SSO. Följ bara stegen nedan.

### <a name="step-1---identify-your-companys-domains"></a>Steg 1 – identifiera företagets domäner

Identifiera de domäner som företagets användare loggar in med.

Exempel:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Steg 2 – kontakta Firmex-supporten med dina domäner

E- [Firmex support team](mailto:support@firmex.com) eller ring 1888 688 4042 x. 11 för att prata med Firmex-support. Skicka vidare till din domän information. Firmex-stöd kommer att lägga till domänerna i VDR som **begärda domäner**. Din administratör måste nu konfigurera SSO.

Varning! ditt företags användare kommer inte att kunna logga in på VDR förrän plats administratören har konfigurerat de begärda domänerna. Användare som inte är företags användare (det vill säga gäst användare) kan fortfarande logga in med sin e-post/sitt lösen ord. Konfigurationen bör ta några minuter.

### <a name="step-3---configure-the-claimed-domains"></a>Steg 3 – Konfigurera de begärda domänerna

1. Logga in på Firmex som plats administratör.
1. Klicka på företagets logo typ i det övre vänstra hörnet.
1. Välj fliken  **SSO**  . Välj sedan  **SSO-konfiguration**. Klicka på den domän som du vill konfigurera.

    ![Anspråk domäner](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Låt IT-administratören fylla i följande fält. Fälten bör hämtas från din identitetsprovider:  

    ![SSO-konfiguration](./media/firmex-vdr-tutorial/SSO-config.png)

    a. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    b. I text rutan **URL för identitetsprovider** klistrar du in värdet för **inloggnings-URL** , som du har kopierat från Azure Portal.

    c. **Certifikat för offentlig nyckel**  – för autentisering kan ett SAML-meddelande signeras digitalt av utfärdaren. För att verifiera signaturen i meddelandet använder meddelande mottagaren en offentlig nyckel som är känd för utfärdaren. Om du vill kryptera ett meddelande måste en offentlig krypterings nyckel som tillhör den slutgiltiga mottagaren vara känd för utfärdaren. I båda fallen – signering och kryptering – betrodda offentliga nycklar måste delas i förväg.  Detta är **X509Certificate** från **XML för federationsmetadata**

    d. Klicka på **Spara** för att slutföra SSO-konfigurationen. Ändringarna träder i kraft omedelbart.

1. För tillfället är SSO aktiverat för din webbplats.

### <a name="create-firmex-vdr-test-user"></a>Skapa Firmex VDR-test användare

I det här avsnittet skapar du en användare som heter B. Simon i Firmex. Arbeta med [Firmex support team](mailto:support@firmex.com) för att lägga till användare i Firmex-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Firmex VDR på åtkomst panelen, bör du loggas in automatiskt på Firmex-VDR som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova Firmex VDR med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Firmex med avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)