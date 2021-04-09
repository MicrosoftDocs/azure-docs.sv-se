---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med rese-och utgifts hantering i Concur | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur resor och kostnader.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: 56eab06bd1afd18bfd4e23b9acb0b44d7bd1f80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737056"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Concur rese-och utgifts hantering

I den här självstudien får du lära dig hur du integrerar Concur resor och utgifter med Azure Active Directory (Azure AD). När du integrerar Concur-resor och-utgifter med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Concur resor och utgifter.
* Gör det möjligt för användarna att logga in automatiskt för att Concur resor och utgifter med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Concur rese-och utgifts prenumeration.
* Rollen "företags administratör" under ditt Concur-användarkonto. Du kan testa om du har rätt åtkomst genom att gå till [Concur SSO Self-Service-verktyget](https://www.concursolutions.com/nui/authadmin/ssoadmin). Kontakta Concur-supporten eller implementerings projektledaren om du inte har åtkomst. 

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO.

* Concur resor och utgifter stöder **IDP** och **SP** -initierad SSO
* Concur resor och utgifter stöder testning av SSO i både produktions-och implementerings miljö 

> [!NOTE]
> ID för det här programmet är ett fast sträng värde för var och en av de tre regionerna: USA, EMEA och Kina. Det går bara att konfigurera en instans för varje region i en klient organisation. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>Lägga till Concur-resor och utgifter från galleriet

Om du vill konfigurera integreringen av Concur-resor och-utgifter i Azure AD måste du lägga till Concur resor och utgifter från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Concur resor och utgifter** i sökrutan.
1. Välj **Concur resor och utgifter** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-concur-travel-and-expense"></a>Konfigurera och testa Azure AD SSO för Concur resor och utgifter

Konfigurera och testa Azure AD SSO med Concur-resor och-utgifter med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Concur resor och kostnader.

Utför följande steg för att konfigurera och testa Azure AD SSO med Concur-resor och utgifter:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Concur resor och utgifter](#configure-concur-travel-and-expense-sso)** för enkel inloggning för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Concur rese-och utgifts test](#create-concur-travel-and-expense-test-user)** för att få en motsvarighet till B. Simon i Concur resor och utgifter som är kopplade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för **Concur rese-och utgifts** program integrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

    > [!NOTE]
    > Identifierare (entitets-ID) och svars-URL (intygets konsument tjänst-URL) är regions information. Välj baserat på data centret för din Concur-entitet. Om du inte känner till data centret för din Concur-enhet kontaktar du Concur-supporten. 

5. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/Ritstift för **användare** för att redigera inställningarna. Den unika användar identifieraren måste matcha Concur User login_id. Normalt bör du ändra **User. UserPrincipalName** till **User. mail**.

    ![Redigera användarattribut](common/edit-attribute.png)

6. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned metadata och spara dem på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Concur resor och kostnader.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Concur resor och utgifter**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-concur-travel-and-expense-sso"></a>Konfigurera Concur resor och utgifter SSO

1. Om du vill automatisera konfigurationen inom Concur-resor och-utgifter måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera Concur rese-och utgifts kostnader** leder dig till Concur rese-och utgifts program. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Concur resor och utgifter. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill ställa in Concur rese-och utgifts hantering manuellt i ett annat webbläsarfönster måste du ladda upp den hämtade **XML-koden för federationsmetadata** till [Concur SSO Self-Service-verktyget](https://www.concursolutions.com/nui/authadmin/ssoadmin) och logga in på din Concur rese-och utgifts företags plats som administratör.

1. Klicka på **Lägg till**.
1. Ange ett eget namn för din IdP, till exempel "Azure AD (US)". 
1. Klicka på **Ladda upp XML-fil** och koppla **XML för federationsmetadata** som du laddade ned tidigare.
1. Spara ändringen genom att klicka på **Lägg till metadata** .

    ![Skärm bild för Concur SSO Self-Service Tool](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Skapa Concur för rese-och utgifts test

I det här avsnittet skapar du en användare som heter B. Simon i Concur resor och utgifter. Arbeta med Concur support team för att lägga till användare i Concur rese-och utgifts plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

> [!NOTE]
> B. Simons Concur-inloggnings-ID måste matcha B. Simons unika identifierare i Azure AD. Till exempel om B. Simon s Azure AD Unique-identifierare är `B.Simon@contoso.com` . B. Simon: s Concur-inloggnings-ID måste `B.Simon@contoso.com` också vara. 

## <a name="configure-concur-mobile-sso"></a>Konfigurera Concur Mobile SSO
Om du vill aktivera Concur Mobile SSO måste du ge Concur support team **User Access URL**. Följ stegen nedan för att hämta **URL för användar åtkomst** från Azure AD:
1. Gå till **företags program**
1. Klicka på **Concur resor och utgifter**
1. Klicka på **Egenskaper**
1. Kopiera **URL för användar åtkomst** och ge denna URL till Concur-stöd

> [!NOTE]
> Self-Service alternativ för att konfigurera SSO är inte tillgängligt så arbeta med Concur support team för att aktivera enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Concur rese-och utgifts inloggnings-URL där du kan starta inloggnings flödet.

* Gå till Concur-URL för rese-och utgifts inloggning direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den Concura res och kostnad som du ställer in för SSO

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Concur rese-och utgifts hantering i Mina appar, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den Concur res och kostnad som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Concur-resor och-utgifter kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).