---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Lexonis TalentScape | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lexonis TalentScape.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2021
ms.author: jeedes
ms.openlocfilehash: f321b18e498cf0b1f12c921feed8e0d95c410874
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104953433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lexonis-talentscape"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Lexonis TalentScape

I den här självstudien får du lära dig att integrera Lexonis-TalentScape med Azure Active Directory (Azure AD). När du integrerar Lexonis-TalentScape med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Lexonis-TalentScape.
* Gör det möjligt för användarna att logga in automatiskt till Lexonis TalentScape med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Lexonis TalentScape-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Lexonis TalentScape stöder **SP-och IDP** -INITIERAd SSO
* Lexonis TalentScape stöder **just-in-Time** User-etablering

## <a name="adding-lexonis-talentscape-from-the-gallery"></a>Lägga till Lexonis TalentScape från galleriet

Om du vill konfigurera integreringen av Lexonis-TalentScape i Azure AD måste du lägga till Lexonis TalentScape från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Lexonis TalentScape** i sökrutan.
1. Välj **Lexonis TalentScape** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-lexonis-talentscape"></a>Konfigurera och testa Azure AD SSO för Lexonis-TalentScape

Konfigurera och testa Azure AD SSO med Lexonis TalentScape med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Lexonis TalentScape.

Utför följande steg för att konfigurera och testa Azure AD SSO med Lexonis TalentScape:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Lexonis TALENTSCAPE SSO](#configure-lexonis-talentscape-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Lexonis TalentScape test User](#create-lexonis-talentscape-test-user)** – om du vill ha en motsvarighet till B. Simon i Lexonis TalentScape som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Lexonis TalentScape** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<CUSTOMER_NAME>.lexonis.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CUSTOMER_NAME>.lexonis.com/saml2/acs`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<CUSTOMER_NAME>.lexonis.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [Lexonis TalentScape client support team](mailto:support@lexonis.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Lexonis TalentScape-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Lexonis TalentScape-program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | ----------------- | --------- |
    | befattning | user.jobtitle |
    | roller | user.assignedroles |

    > [!NOTE]
    > Lexonis TalentScape förväntar sig roller för användare som tilldelats programmet. Konfigurera de här rollerna i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md).

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)
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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Lexonis TalentScape.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Lexonis TalentScape**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-lexonis-talentscape-sso"></a>Konfigurera Lexonis TalentScape SSO

Om du vill konfigurera enkel inloggning på **Lexonis TalentScape** -sidan måste du skicka **URL: en för app Federation-Metadata** till [Lexonis TalentScape support team](mailto:support@lexonis.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-lexonis-talentscape-test-user"></a>Skapa Lexonis TalentScape-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i Lexonis TalentScape. Lexonis TalentScape stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Lexonis TalentScape skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Lexonis TalentScape-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till Lexonis TalentScape-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Lexonis-TalentScape som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Lexonis TalentScape i Mina appar, om det kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på Lexonis-TalentScape som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Lexonis TalentScape kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
