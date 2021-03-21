---
title: 'Självstudie: Azure Active Directory integrering med KnowBe4 Security medvetenhet Training | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och KnowBe4 Security Awareness Training.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: jeedes
ms.openlocfilehash: 77d810fc8327e3777102f2a09f0ca32835aad4b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180772"
---
# <a name="tutorial-azure-active-directory-integration-with-knowbe4-security-awareness-training"></a>Självstudie: Azure Active Directory integrering med KnowBe4 Security medvetenhet Training

I den här självstudien lär du dig att integrera KnowBe4 Security Awareness Training med Azure Active Directory (Azure AD).
Integreringen av KnowBe4 Security Awareness Training med Azure AD medför följande fördelar:

* Du kan styra vilka som har tillgång till KnowBe4 Security Awareness Training från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på KnowBe4 Security Awareness Training (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med KnowBe4 Security Awareness Training behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En KnowBe4 Security Awareness Training-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* KnowBe4 Security Awareness Training har stöd för **SP**-initierad enkel inloggning

* KnowBe4 Security Awareness Training har stöd för **Just-in-time**-användaretablering

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-knowbe4-from-the-gallery"></a>Lägga till KnowBe4 från galleriet

Om du vill konfigurera integreringen av KnowBe4 i Azure AD måste du lägga till KnowBe4 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **KnowBe4** i sökrutan.
1. Välj **KnowBe4** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med KnowBe4 baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i KnowBe4 upprättas.

Utför följande steg för att konfigurera och testa enkel inloggning med KnowBe4 i Azure AD:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa Azure AD SSO med Britta Simon.
    * **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -Britta för att aktivera Simon för att använda Azure AD SSO.
2. **[Konfigurera KnowBe4 Security medvetenhet Training SSO](#configure-knowbe4-security-awareness-training-sso)** – för att konfigurera SSO-inställningar på program sidan.
    * **[Skapa KnowBe4 Security Awareness Training-testanvändare](#create-knowbe4-security-awareness-training-test-user)** – för att få en motsvarighet till Britta Simon i KnowBe4 Security Awareness Training som är länkad till en Azure AD-representation av användaren.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **KnowBe4** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<companyname>.KnowBe4.com/auth/saml/<instancename>`

    > [!NOTE]
    > Värdet för inloggnings-URL:en är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Hämta värdet genom att kontakta [supportteamet för KnowBe4 Security Awareness Training-klienten](mailto:support@KnowBe4.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

6. I avsnittet **Konfigurera KnowBe4 Security Awareness Training** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till KnowBe4.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **KnowBe4**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-knowbe4-security-awareness-training-sso"></a>Konfigurera KnowBe4 Security medvetenhet-utbildning SSO

För att konfigurera enkel inloggning på **KnowBe4 Security Awareness Training**-sidan behöver du skicka det nedladdade **certifikatet (RAW)** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för KnowBe4 Security Awareness Training](mailto:support@KnowBe4.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-knowbe4-security-awareness-training-test-user"></a>Skapa KnowBe4 Security Awareness Training-testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i KnowBe4. KnowBe4 stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i KnowBe4 skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till KnowBe4-inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till KnowBe4-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen KnowBe4 i åtkomst panelen omdirigeras den till KnowBe4-inloggnings-URL. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat KnowBe4 kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).