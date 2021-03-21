---
title: 'Självstudie: Azure Active Directory integration med SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform Identity Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: dc0cd57eb32baaeac0850337bbead3a73dec9292
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897353"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med SAP Cloud Platform Identity Authentication

I den här självstudien får du lära dig hur du integrerar SAP Cloud Platform Identity Authentication med Azure Active Directory (Azure AD). När du integrerar SAP Cloud Platform Identity Authentication med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Cloud Platform Identity Authentication.
* Gör det möjligt för användarna att logga in automatiskt till SAP Cloud Platform Identity Authentication med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP Cloud Platform Identity Authentication-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud Platform Identity Authentication stöder **SP**- och **IDP**-initierad enkel inloggning

Innan du går in närmare på de tekniska detaljerna är det viktigt att förstå de begrepp som kommer att påträffa. Med SAP Cloud Platform Identity Authentication och Active Directory Federation Services kan du implementera enkel inloggning över flera program eller tjänster som skyddas av Azure AD (som IdP) med SAP-program och -tjänster som skyddas av SAP Cloud Platform Identity Authentication.

För närvarande fungerar SAP Cloud Platform Identity Authentication som Proxy Identity Provider (proxyidentitetsprovider) för SAP-program. Azure Active Directory fungerar i sin tur som ledande identitetsprovider i den här konfigurationen. 

Följande diagram illustrerar den här relationen:

![Skapa en testanvändare för Azure AD](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Med den här konfigurationen är din klientorganisation för SAP Cloud Platform Identity Authentication konfigurerad som ett betrott program i Azure Active Directory.

Alla SAP-program och -tjänster som du vill skydda på det här sättet konfigureras sedan i hanteringskonsolen för SAP Cloud Platform Identity Authentication.

Därför behöver auktoriseringen för att bevilja åtkomst till SAP-program och -tjänster ske i SAP Cloud Platform Identity Authentication (i stället för i Azure Active Directory).

Genom att konfigurera SAP Cloud Platform Identity Authentication som ett program via Azure Active Directory Marketplace behöver du inte konfigurera enskilda anspråk eller SAML-intyg.

> [!NOTE]
> För närvarande har endast enkel inloggning på webben testats av båda parter. Flöden som är nödvändiga för kommunikation app-till-API eller API-till-app bör fungera men har inte testats ännu. De kommer att testas under senare aktiviteter.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägga till SAP Cloud Platform Identity Authentication från galleriet

För att konfigurera integrering av SAP Cloud Platform Identity Authentication med Azure AD behöver du lägga till SAP Cloud Platform Identity Authentication från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **SAP Cloud Platform Identity Authentication** i sökrutan.
1. Välj **SAP Cloud Platform Identity Authentication** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-sap-cloud-platform-identity-authentication"></a>Konfigurera och testa Azure AD SSO för SAP Cloud Platform Identity Authentication

Konfigurera och testa Azure AD SSO med SAP Cloud Platform Identity Authentication med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud Platform Identity Authentication.

Utför följande steg för att konfigurera och testa Azure AD SSO med SAP Cloud Platform Identity Authentication:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera inloggnings information för SAP Cloud Platform Authentication](#configure-sap-cloud-platform-identity-authentication-sso)** – om du vill konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Azure Cloud Platform Identity Authentication test User](#create-sap-cloud-platform-identity-authentication-test-user)** -för att få en motsvarighet till B. Simon i SAP Cloud Platform Identity Authentication som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **SAP Cloud Platform Identity Authentication** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera i **IDP** läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<IAS-tenant-id>.accounts.ondemand.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) och be om dessa värden. Om du inte förstår identifierarvärde läser du dokumentationen för SAP Cloud Platform Identity Authentication om [SAML 2.0-konfiguration för klientorganisation](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klicka på **Ange ytterligare URL: er** och utför följande steg om du vill konfigurera programmet i **SP**-initierat läge:

    ![SAP Cloud Platform Identity Authentication-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Använd inloggnings-URL för ditt specifika affärsprogram. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) om du behöver hjälp.

1. SAP Cloud Platform Identity Authentication Application förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar till dina SAML token-konfiguration av attribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig SAP Cloud Platform Identity Authentication Application några fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. I avsnittet **Konfigurera SAP Cloud Platform Identity Authentication** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Cloud Platform Identity Authentication.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **SAP Cloud Platform Identity Authentication**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.

1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Konfigurera SAP Cloud Platform Identity Authentication SSO

1. Om du vill automatisera konfigurationen i SAP Cloud Platform Identity Authentication måste du installera **webb läsar tillägget för Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera SAP Cloud Platform Identity Authentication** för att dirigera dig till SAP Cloud Platform Identity Authentication Application. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på SAP Cloud Platform Identity Authentication. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera SAP Cloud Platform Identity Authentication manuellt går du till administrations konsolen SAP Cloud Platform Identity Authentication i ett annat webbläsarfönster. URL:en har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Läs sedan dokumentationen om SAP Cloud Platform Identity Authentication i avsnittet om [integrering med Microsoft Azure AD](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. I Azure-portalen väljer du knappen **Spara**.

3. Fortsätt endast med följande om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa stegen från avsnittet **Lägga till SAP Cloud Platform Identity Authentication från galleriet**.

4. I Azure-portalen går du till programintegreringssidan för **SAP Cloud Platform Identity Authentication** och väljer **Linked Sign-on** (Länkad inloggning).

    ![Konfigurera länkad inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked-sign-on.png)

5. Spara konfigurationen.

> [!NOTE]
> Det nya programmet använder konfigurationen för enkel inloggning från föregående SAP-program. Se till att du använder samma företagsidentitetsprovidrar (Corporate Identity Providers) i administrationskonsolen för SAP Cloud Platform Identity Authentication.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare för SAP Cloud Platform Identity Authentication

Du behöver inte skapa en användare i SAP Cloud Platform Identity Authentication. Användare som finns i Azure AD-användararkivet kan använda funktionen för enkel inloggning.

SAP Cloud Platform Identity Authentication stöder alternativet för identitetsfederation. Det här alternativet gör att programmet kan kontrollera huruvida användare som autentiseras av företagsidentitetsprovidern finns i användararkivet för SAP Cloud Platform Identity Authentication.

Alternativet för identitetsfederation är inaktiverat som standard. Om identitetsfederation är aktiverat kan endast de användare som importeras i SAP Cloud Platform Identity Authentication få åtkomst till programmet.

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform Identity Authentication finns i ”Aktivera identitetsfederation med SAP Cloud Platform Identity Authentication” i avsnittet om att [konfigurera identitetsfederation med användararkivet för SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SAP Cloud Platform Identity Authentication Sign on URL där du kan starta inloggnings flödet.

* Gå till SAP Cloud Platform Identity Authentication inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till den SAP Cloud Platform Identity-autentisering som du konfigurerade SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SAP Cloud Platform Identity Authentication i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till den SAP Cloud Platform Identity-autentisering som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SAP Cloud Platform Identity Authentication kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)