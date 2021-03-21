---
title: 'Självstudie: Azure Active Directory integration med lösen ords hanteraren i Keepr & digitalt valv | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96178049"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Självstudie: Azure Active Directory integrering med lösen ords hanteraren i Keepr & digitalt valv

I den här självstudien lär du dig att integrera Keeper Password Manager & Digital Vault med Azure Active Directory (AD Azure).
Den här integrationen ger följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Keeper Password Manager & Digital Vault.
* Du kan göra det möjligt för användarna att logga in automatiskt till att behålla lösen ords hanteraren & digitalt valv (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats: Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med lösen ords hanteraren i hand & digitalt valv behöver du:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [utvärderings period](https://azure.microsoft.com/pricing/free-trial/)på en månad.
* Keepr Password Manager & Digital Vault-prenumeration, aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Behållar lösen ords hanteraren & Digital Vault stöder SP-initierad SSO.

* Keepr Password Manager & Digital Vault stöder just-in-Time-etablering av användare.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägg till lösen ords hanteraren för lösen ord & digitala valvet från galleriet

Om du vill konfigurera integrationen av lösen ords hanteraren & digitala valvet i Azure AD, lägger du till programmet från galleriet i listan över hanterade program som en tjänst (SaaS)-appar.

1. Logga in på Azure Portal med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I **Lägg till från galleriet** skriver du **behållar lösen ords hanteraren & digitala valvet** i sökrutan.
1. Välj **behållar lösen ords hanteraren & digitalt valv** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurera och testa Azure AD SSO för lösen ords hanteraren i & Digital Vault

Konfigurera och testa Azure AD SSO med lösen ords hanteraren & digitala valvet med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i lösen ords hanteraren för behållar & digitalt valv.

Konfigurera och testa Azure AD SSO med lösen ords hanteraren & digitalt valv:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.

    * [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning för Azure AD med Britta Simon.
    * [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) så att Britta Simon kan använda enkel inloggning i Azure AD.

1. [Konfigurera lösen ords hanteraren i & Digital Vault SSO](#configure-keeper-password-manager--digital-vault-sso) för att konfigurera SSO-inställningar på program sidan.
    * [Skapa en lösen ords hanterare & Digital Vault test användare](#create-a-keeper-password-manager--digital-vault-test-user) för att få en motsvarighet till Britta Simon i behållar lösen ords hanteraren & digitalt valv som är länkat till Azure AD-representationen av användaren.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan hantera **lösen ords hanterare & Digital Vault** program integration och letar upp avsnittet **Hantera** . Välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild av konfiguration av enstaka Sign-On med SAML, med Penn ikonen markerad.](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** utför du följande steg:

    a. För **inloggnings-URL** anger du en URL som använder följande mönster:
    * För enkel inloggning i molnet: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * För lokal SSO: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. För **identifierare (enhets-ID)** anger du en URL som använder följande mönster:
    * För enkel inloggning i molnet: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * För lokal SSO: `https://<KEEPER_FQDN>/sso-connect`

    c. För **svars-URL** anger du en URL som använder följande mönster:
    * För enkel inloggning i molnet: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * För lokal SSO: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL: en, identifierare och svars-URL. Om du vill hämta dessa värden kan du kontakta [support teamet för The keepr Password Manager & Digital Vault-klienten](https://keepersecurity.com/contact.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Lösen ords hanteraren för lösen ord & Digital Vault applikation förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av användarattribut &-anspråk.](common/default-attributes.png)

1. Dessutom förväntar sig lösen ords hanteraren & Digital Vault applikation att några fler attribut skickas tillbaka i SAML-svar. Dessa visas i följande tabell. Dessa attribut har också fyllts i automatiskt, men du kan granska dem enligt dina krav.

    | Name | Källattribut|
    | ------------| --------- |
    | Första | user.givenname |
    | Sista | user.surname |
    | E-post | user.mail |

5. Välj **Hämta** i avsnittet **SAML-signeringscertifikat** om du vill **Konfigurera en enskild Sign-On med SAML**. Detta laddar ned **XML-metadata för federationsmetadata** från alternativen enligt ditt krav och sparar dem på din dator.

    ![Skärm bild av SAML-signerings certifikat med hämtning markerat.](common/metadataxml.png)

6. På sidan **Konfigurera lösen ords hanteraren & Digital Vault** kopierar du lämpliga URL: er enligt ditt krav.

    ![Skärm bild av Konfigurations hanteraren för lösen ords hanterare & digitala valvet med URL: er markerade.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet ska du skapa en test användare i den Azure Portal som kallas `B.Simon` .

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**  >  **användare**  >  **alla användare**.
1. Överst på skärmen väljer du **ny användare**.
1. I **användar** egenskaperna följer du de här stegen:
   1. Som **namn** anger du `B.Simon` .  
   1. För **användar namn** anger du `username@companydomain.extension` . Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord** och skriv sedan ned värdet som visas.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till lösen ords hanteraren för & digitalt valv.

1. I Azure Portal väljer du **företags program**  >  **alla program**.
1. I programlistan väljer du **Keeper Password Manager & Digital Vault**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare**. I **Lägg till tilldelning** väljer du **Användare och grupper**.
1. I **användare och grupper** väljer du **B. Simon** från listan över användare. Välj sedan **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från listan **Välj en roll** . Om ingen roll har ställts in för den här appen väljs standard rollen för **åtkomst** .
1. I **Lägg till tilldelning** väljer du **tilldela**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurera lösen ords hanteraren i & Digital Vault SSO

Om du vill konfigurera SSO för appen kan du läsa mer i rikt linjerna i [support guiden för keepor](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Skapa en lösen ords hanterare & Digital Vault test användare

Om du vill att Azure AD-användare ska kunna logga in på lösen ords hanteraren & digitala valvet måste du etablera dem. Programmet har stöd för just-in-Time-etablering av användare och när användare har skapats automatiskt i programmet. Kontakta [supporten](https://keepersecurity.com/contact.html)om du vill konfigurera användare manuellt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* I Azure Portal väljer du **testa det här programmet**. Detta omdirigerar till inloggnings-URL: en för lösen ords hanteraren i & digitalt valv, där du kan initiera inloggningen. 

* Du kan gå direkt till inloggnings-URL: en för programmet och initiera inloggningen därifrån.

* Du kan använda Microsoft Access-panelen. När du väljer panelen för **lösen ords hanteraren & Digital Vault** i åtkomst panelen omdirigeras du till inloggnings-URL: en för programmet. Mer information om åtkomst panelen finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat lösen ords hanteraren & Digital Vault, kan du framtvinga kontroll av sessionen. Detta skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Mer information finns i [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).