---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med TrendMiner | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TrendMiner.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: 7f7e7126c482038907e5e986d0779827957cb093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182180"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trendminer"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med TrendMiner

I den här självstudien får du lära dig hur du integrerar TrendMiner med Azure Active Directory (Azure AD). När du integrerar TrendMiner med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TrendMiner.
* Gör det möjligt för användarna att logga in automatiskt till TrendMiner med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* TrendMiner för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* TrendMiner stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-trendminer-from-the-gallery"></a>Lägga till TrendMiner från galleriet

Om du vill konfigurera integreringen av TrendMiner i Azure AD måste du lägga till TrendMiner från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **TrendMiner** i sökrutan.
1. Välj **TrendMiner** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-trendminer"></a>Konfigurera och testa Azure AD SSO för TrendMiner

Konfigurera och testa Azure AD SSO med TrendMiner med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i TrendMiner.

Utför följande steg för att konfigurera och testa Azure AD SSO med TrendMiner:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera TRENDMINER SSO](#configure-trendminer-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa TrendMiner test User](#create-trendminer-test-user)** -om du vill ha en motsvarighet till B. Simon i TrendMiner som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **TrendMiner** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<CUSTOMER>.trendminer.cloud/security/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CUSTOMER>.trendminer.cloud/security/saml/SSO`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<CUSTOMER>.trendminer.cloud/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [TrendMiner client support team](mailto:support@trendminer.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera TrendMiner** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TrendMiner.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **TrendMiner**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-trendminer-sso"></a>Konfigurera TrendMiner SSO

1. Öppna ett nytt webbläsarfönster och logga in på din TrendMiner företags webbplats som administratör.

1. På den vänstra menyn väljer du **säkerhets > identitets leverantör**

1. Klicka på **SAML** på sidan **identitetsprovider** och klicka sedan på **Nästa steg**.

    ![Välj SAML](./media/trendminer-tutorial/saml.png)

1. Klicka på **Nästa steg** på sidan **SAML-säkerhetskopiering** .

    ![Välj SAML-säkerhetskopiering](./media/trendminer-tutorial/saml-backup.png)

1. Klicka på **Nästa steg** för **självsignerade certifikat**.

    ![Sidan Self-Signed certifikat](./media/trendminer-tutorial/self-signed-certificate.png)

1. Du kan **hoppa över** uppladdning av en signerings nyckel.

    ![laddar upp en signerings nyckel](./media/trendminer-tutorial/signing-key.png)

1. På skärmen **SAML-konfiguration** i **entitetens bas-URL** anger du domän-URL som `https://trendminer.domain.com/`

1. I **metadata för identitetsprovider** laddar du upp den **Azure-metadatafil** som du kopierade från Azure Portal och klickar sedan på **Nästa steg**.

    ![SAML-konfiguration](./media/trendminer-tutorial/saml-configuration.png)

1. I avsnittet **SAML-användar mappning** anger du användar namn som ska användas för inloggning och klickar på **Slutför**.

    ![SAML-användar mappning](./media/trendminer-tutorial/user-mapping.png)

### <a name="create-trendminer-test-user"></a>Skapa TrendMiner test användare

I det här avsnittet skapar du en användare som heter Britta Simon i TrendMiner. Arbeta med [TrendMiner support team](mailto:support@trendminer.com) för att lägga till användare i TrendMiner-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till TrendMiner-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till TrendMiner-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den TrendMiner som du har konfigurerat SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen TrendMiner i åtkomst panelen, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på TrendMiner som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat TrendMiner kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).