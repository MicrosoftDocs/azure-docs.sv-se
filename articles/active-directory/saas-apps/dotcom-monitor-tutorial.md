---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Dotcom-Monitor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Dotcom-Monitor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 6f7b2abecdfae1e8f943cd45df7e4c42f1ddd978
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736635"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-dotcom-monitor"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Dotcom-Monitor

I den här självstudien får du lära dig hur du integrerar Dotcom-Monitor med Azure Active Directory (Azure AD). När du integrerar Dotcom-Monitor med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Dotcom-Monitor.
* Gör det möjligt för användarna att logga in automatiskt för att Dotcom-Monitor med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Dotcom-Monitor enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Dotcom-Monitor stöder **SP** -INITIERAd SSO

* Dotcom-Monitor stöder **just-in-Time** User-etablering

## <a name="adding-dotcom-monitor-from-the-gallery"></a>Lägga till Dotcom-Monitor från galleriet

Om du vill konfigurera integreringen av Dotcom-Monitor i Azure AD måste du lägga till Dotcom-Monitor från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Dotcom-Monitor** i sökrutan.
1. Välj **Dotcom-Monitor** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-dotcom-monitor"></a>Konfigurera och testa Azure AD SSO för Dotcom-Monitor

Konfigurera och testa Azure AD SSO med Dotcom-Monitor med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Dotcom-Monitor.

Utför följande steg för att konfigurera och testa Azure AD SSO med Dotcom-övervakaren:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Dotcom Monitor SSO](#configure-dotcom-monitor-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Dotcom Monitor test User](#create-dotcom-monitor-test-user)** -om du vill ha en motsvarighet till B. Simon i Dotcom-Monitor som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Dotcom-Monitor-** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://userauth.dotcom-monitor.com/Login.ashx?cidp=<CUSTOM_GUID>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Dotcom – övervaka klient support teamet](mailto:vadimm@dana-net.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Dotcom-Monitor programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Dotcom-Monitor-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name  |  Källattribut|
    | ------|--------- |
    | Roller | user.assignedroles |

    > [!NOTE]
    > Det finns mer vägledning [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) om hur du skapar anpassade roller i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Dotcom-Monitor** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Dotcom-Monitor.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Dotcom-Monitor**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-dotcom-monitor-sso"></a>Konfigurera Dotcom-Monitor SSO

Om du vill konfigurera enkel inloggning på **Dotcom-Monitor** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [support teamet för Dotcom-övervakning](mailto:vadimm@dana-net.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-dotcom-monitor-test-user"></a>Skapa Dotcom-Monitor test användare

I det här avsnittet skapas en användare som heter B. Simon i Dotcom-Monitor. Dotcom-Monitor stöder just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Dotcom-övervakaren, skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Dotcom-Monitor inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Dotcom-Monitor inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Dotcom-Monitor i Mina appar omdirigeras det till Dotcom-Monitor inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Dotcom-Monitor du använda sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
