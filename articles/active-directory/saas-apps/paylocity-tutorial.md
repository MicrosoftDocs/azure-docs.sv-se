---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Paylocity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Paylocity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2021
ms.author: jeedes
ms.openlocfilehash: a0d1debc7baa15aeb765f9539bbbf0774f22268d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647080"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-paylocity"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Paylocity

I den här självstudien får du lära dig hur du integrerar Paylocity med Azure Active Directory (Azure AD). När du integrerar Paylocity med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Paylocity.
* Gör det möjligt för användarna att logga in automatiskt till Paylocity med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Paylocity för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Paylocity stöder **SP-och IDP** -INITIERAd SSO

## <a name="add-paylocity-from-the-gallery"></a>Lägg till Paylocity från galleriet

Om du vill konfigurera integreringen av Paylocity i Azure AD måste du lägga till Paylocity från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Paylocity** i sökrutan.
1. Välj **Paylocity** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-paylocity"></a>Konfigurera och testa Azure AD SSO för Paylocity

Konfigurera och testa Azure AD SSO med Paylocity med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Paylocity.

Utför följande steg för att konfigurera och testa Azure AD SSO med Paylocity:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PAYLOCITY SSO](#configure-paylocity-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa Paylocity test User](#create-paylocity-test-user)** -om du vill ha en motsvarighet till B. Simon i Paylocity som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Paylocity** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du följande URL: `https://access.paylocity.com/`

1. Klicka på **Spara**.

1. Paylocity-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Paylocity-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du måste uppdatera dessa attribut med de verkliga värdena.

    | Namn |  Källattribut|
    | ---------------| --------------- |
    | Partner | `P8000010` |
    | PaylocityUser | `user.mail`|
    | PaylocityEntity | < `PaylocityEntity` > |

    > [!NOTE]
    > PaylocityEntity är Paylocity företags-ID.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signerings certifikat** , klickar du på **Redigera ikon**.

    ![Skärm bild som visar "S A M L signerings certifikat" med åtgärden "Ladda ned" för "Federation Metadata X M L" valt.](./media/paylocity-tutorial/edit-samlassertion.png)

1. Välj **signerings alternativ** som **signera SAML-svar och kontroll** och klicka på **Spara**.

    ![Redigera certifikat för SAML-signering](./media/paylocity-tutorial/saml-assertion.png)

1. I avsnittet **Konfigurera Paylocity** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Paylocity.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Paylocity**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-paylocity-sso"></a>Konfigurera Paylocity SSO

Så här konfigurerar du enkel inloggning på **Paylocity** -Sidan

1. Hämta **XML-metadata för federationsmetadata**.
1. I Paylocity navigerar du till konfiguration av användar åtkomst för **HR & löne**  >  **användare**  >  .
1. Välj **Lägg till SSO-integrering** under **SSO-integreringar**. En ny låda öppnas.
1. Välj **Microsoft Azure** som SSO-provider från listruta.
1. Välj **status** från List rutan.
1. Dra och släpp metadatafilen i släpp fältet. Paylocity försöker parsa utfärdaren, efter omdirigering och bindnings-URL: er och säkerhets certifikat.
1. Klicka på **Spara** för att bekräfta ändringarna. Integrationen ska visas under **SSO-integreringar**.

### <a name="create-paylocity-test-user"></a>Skapa Paylocity test användare

I det här avsnittet skapar du en användare som heter B. Simon i Paylocity. Arbeta med [Paylocity support team](mailto:service@paylocity.com) för att lägga till användare i Paylocity-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Paylocity-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Paylocity-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Paylocity som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Paylocity i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Paylocity som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Paylocity kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).