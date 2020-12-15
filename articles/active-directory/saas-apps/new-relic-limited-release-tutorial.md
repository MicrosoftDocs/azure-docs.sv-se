---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nya Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 29e19eea51b5ee55831bf1d694a9a6473a62d471
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504057"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nya Relic

I den här självstudien får du lära dig att integrera nya Relic med Azure Active Directory (Azure AD). När du integrerar nya Relic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till nya Relic.
* Gör det möjligt för användarna att logga in automatiskt till nya Relic med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Om du vill veta mer om SaaS (Software as a Service) med Azure AD, se [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Krav

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En ny Relic-prenumeration som är aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* New Relic stöder SSO som initieras av antingen tjänst leverantören eller identitets leverantören.
* När du har konfigurerat nya Relic kan du framtvinga kontroll av sessioner, som skyddar mot exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-from-the-gallery"></a>Lägg till nya Relic från galleriet

Om du vill konfigurera integrering av nya Relic i Azure AD måste du lägga till **nya Relic (efter organisation)** från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Välj tjänsten **Azure Active Directory** .
1. Välj **företags program**  >  **nytt program**.
1. På sidan Sök i **Azure AD-galleriet** skriver du **New Relic (av organisation)** i sökrutan.
1. Välj **ny Relic (efter organisation)** från resultaten och välj sedan **skapa**. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurera och testa Azure AD SSO för nya Relic

Konfigurera och testa Azure AD SSO med nya Relic genom att använda en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i nya Relic.

Konfigurera och testa Azure AD SSO med New Relic:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) så att användarna kan använda den här funktionen.
   1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa enkel inloggning i Azure AD med B. Simon.
   1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) att aktivera B. Simon för att använda enkel inloggning i Azure AD.
1. [Konfigurera nya Relic SSO](#configure-new-relic-sso) för att konfigurera inställningarna för enkel inloggning på den nya Relic-sidan.
   1. [Skapa en ny Relic-testanvändare](#create-a-new-relic-test-user) som ska ha en motsvarighet för B. Simon i New Relic som är länkad till Azure AD-användaren.
1. [Testa SSO](#test-sso) för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **ny Relic efter organisations** program integrering letar du upp avsnittet **Hantera** . Välj sedan **enkel inloggning**.

1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.

1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild av konfiguration av enstaka Sign-On med SAML, med Penn ikonen markerad.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** fyller du i värden för **identifierare** och **svars-URL**.

   * Hämta dessa värden med hjälp av den nya Relic **My Organization** -appen. Så här använder du programmet:
      1. [Logga in](https://login.newrelic.com/) på nya Relic.
      1. På den översta menyn väljer du **appar**.
      1. I avsnittet **appar väljer du** **Mina organisationers**  >  **autentiserings domäner**.
      1. Välj den domän för autentisering som du vill att Azure AD SSO ska ansluta till (om du har fler än en domän för autentisering). De flesta företag har bara en autentiserings domän som kallas **standard**. Om det bara finns en domän för autentisering behöver du inte välja något.
      1. I avsnittet **autentisering** innehåller **intygets konsument-URL** det värde som ska användas för **svars-URL**.
      1. I avsnittet **Authentication** innehåller **vårt entitets-ID** det värde som ska användas för **identifierare**.

1. I avsnittet **användarattribut &-anspråk** kontrollerar du att **unikt användar-ID** är mappat till ett fält som innehåller den e-postadress som används vid nya Relic.

   * Standard fältet **User. UserPrincipalName** fungerar för dig om dess värden är samma som de nya e-postadresserna för Relic.
   * Fält  **användaren. mail** kan fungera bättre för dig om **User. UserPrincipalName** inte är den nya e-postadressen för Relic.

1. I avsnittet **SAML-signeringscertifikat för certifikat** kopierar du **URL för app Federation-Metadata** och sparar värdet för senare användning.

1. I avsnittet **Konfigurera nya Relic per organisation** , kopiera **inloggnings-URL** och spara värdet för senare användning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Så här skapar du en test användare i Azure Portal som kallas B. Simon.

1. Välj **Azure Active Directory** från Azure Portal.
1. Välj **användare**  >  **ny användare**.
1. På sidan **ny användare** :
   1. I fältet **användar namn** anger du `username@companydomain.extension` . Ett exempel är `b.simon@contoso.com`. Detta bör matcha e-postadressen som du kommer att använda på den nya Relic-sidan.
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Välj **Visa lösen ord** och spara sedan värdet som visas.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

Så här gör du för att aktivera B. Simon för att använda enkel inloggning med Azure AD genom att bevilja åtkomst till den nya Relic av organisations program.

1. Välj **Azure Active Directory** från Azure Portal.
1. Välj **företags program**  >  **nya Relic efter organisation**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera där användare och grupper är markerade.](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I **Lägg till tilldelning** väljer **du användare och grupper** (eller **användare**, beroende på din plan nivå).

   ![Skärm bild av alternativet Lägg till användare.](common/add-assign-user.png)

1. I **användare och grupper** (eller **användare**) väljer du **B. Simon** från listan **användare** och väljer sedan **Välj** längst ned på skärmen.
1. I **Lägg till tilldelning** väljer du **tilldela**.

## <a name="configure-new-relic-sso"></a>Konfigurera New Relic SSO

Följ de här stegen för att konfigurera SSO vid nya Relic.

1. [Logga in](https://login.newrelic.com/) på nya Relic.

1. På den översta menyn väljer du **appar**.

1. I avsnittet **appar väljer du** **Mina organisationers**  >  **autentiserings domäner**.

1. Välj den domän för autentisering som du vill att Azure AD SSO ska ansluta till (om du har fler än en domän för autentisering). De flesta företag har bara en autentiserings domän som kallas **standard**. Om det bara finns en domän för autentisering behöver du inte välja något.

1. I avsnittet **autentisering** väljer du **Konfigurera**.

   1. För **källan för SAML-metadata** anger du det värde som du tidigare sparade från fältet URL för Azure AD **app Federation Metadata** .

   1. För **SSO-mål-URL** anger du det värde som du tidigare sparade från fältet för **inloggnings-URL** för Azure AD.

   1. När du har verifierat att inställningarna ser bättre ut på både Azure AD och nya Relic sidor väljer du **Spara**. Om båda sidorna inte är korrekt konfigurerade kan användarna inte logga in på nya Relic.

### <a name="create-a-new-relic-test-user"></a>Skapa en ny Relic-test användare

I det här avsnittet skapar du en användare som heter B. Simon i New Relic.

1. [Logga in](https://login.newrelic.com/) på nya Relic.

1. På den översta menyn väljer du **appar**.

1. I avsnittet **appar väljer du** **användar hantering**.

1. Välj **Lägg till användare**.

   1. För **namn** anger du **B. Simon**.
   
   1. För **e-post** anger du det värde som ska skickas av Azure AD SSO.
   
   1. Välj en användar **typ** och en användar **grupp** för användaren. För en test användare är **grundläggande användare** för typ och **användare** i gruppen rimliga val.
   
   1. Spara användaren genom att välja **Lägg till användare**.

## <a name="test-sso"></a>Testa SSO 

Så här testar du konfigurationen för enkel inloggning med Azure AD med hjälp av åtkomst panelen.

När du väljer **ny Relic efter organisation** i åtkomst panelen, bör du loggas in automatiskt på nya Relic. Mer information om åtkomst panelen finns i [Logga in och starta appar från portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova nya Relic med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
