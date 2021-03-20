---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Prisma Cloud SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och prisma-molnet SSO.
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
ms.openlocfilehash: d92ca345b0800523d86ce7b42220209a1f237103
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651445"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-prisma-cloud-sso"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Prisma Cloud SSO

I den här självstudien får du lära dig att integrera Prisma-moln-SSO med Azure Active Directory (Azure AD). När du integrerar Prisma-molnet SSO med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Prisma-molnet SSO.
* Gör det möjligt för användarna att logga in automatiskt för att Prisma i molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prisma-prenumeration med enkel inloggning (SSO) för molnet med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Prisma Cloud SSO stöder **IDP** initierad SSO.

* Prisma Cloud SSO stöder **just-in-Time** User-etablering.

## <a name="add-prisma-cloud-sso-from-the-gallery"></a>Lägg till Prisma Cloud SSO från galleriet

Om du vill konfigurera integrationen av Prisma i Azure AD måste du lägga till Prisma Cloud SSO från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Prisma Cloud SSO** i sökrutan.
1. Välj **Prisma Cloud SSO** från panelen resultat och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-prisma-cloud-sso"></a>Konfigurera och testa Azure AD SSO för Prisma-moln-SSO

Konfigurera och testa Azure AD SSO med Prisma i molnet SSO med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i prisma-molnet SSO.

Utför följande steg för att konfigurera och testa Azure AD SSO med Prisma Cloud SSO:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Prisma Cloud SSO](#configure-prisma-cloud-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa Prisma Cloud SSO test User](#create-prisma-cloud-sso-test-user)** – för att få en motsvarighet till B. Simon i prisma Cloud SSO som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **Prisma Cloud SSO** -programintegration i Azure Portal hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://app2.prismacloud.io/customer/<CUSTOMERID>`

    b. **Svars-URL** -värdena är fasta och har redan fyllts i för Azure Portal. Du måste välja en lämplig webb adress enligt ditt krav.

    > [!NOTE]
    > Identifierarvärdet är inte verkligt. Uppdatera värdet med den faktiska identifieraren. Kontakta [Prisma Cloud SSO client support team](mailto:support@paloaltonetworks.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Prisma Cloud SSO** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Prisma-molnet SSO.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Prisma Cloud SSO**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-prisma-cloud-sso"></a>Konfigurera Prisma-molnets SSO

Om du vill konfigurera enkel inloggning på **Prisma-molnets SSO** -sida måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [Prisma Cloud SSO support team](mailto:support@paloaltonetworks.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-prisma-cloud-sso-test-user"></a>Skapa Prisma Cloud SSO test User

I det här avsnittet skapas en användare som heter B. Simon i prisma Cloud SSO. Prisma-molnets SSO stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i prisma-molnet SSO, skapas en ny när du försöker komma åt Prisma-molnet SSO.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt till det Prisma-moln-SSO som du konfigurerade SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på Prisma för molnets SSO-panel i Mina appar, bör du loggas in automatiskt till Prisma-molnets SSO för vilken du ställer in SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Prisma-molnet SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).