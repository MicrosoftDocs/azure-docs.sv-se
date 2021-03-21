---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med AWS ClientVPN | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AWS ClientVPN.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/29/2020
ms.author: jeedes
ms.openlocfilehash: 794934e9c3e45f2aeed9310636a0f3ec30daf7de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735353"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-clientvpn"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med AWS ClientVPN

I den här självstudien får du lära dig att integrera AWS-ClientVPN med Azure Active Directory (Azure AD). När du integrerar AWS-ClientVPN med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AWS-ClientVPN.
* Gör det möjligt för användarna att logga in automatiskt till AWS ClientVPN med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* AWS ClientVPN-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* AWS ClientVPN stöder **SP** -INITIERAd SSO

* AWS ClientVPN stöder **just-in-Time** User-etablering

## <a name="adding-aws-clientvpn-from-the-gallery"></a>Lägga till AWS ClientVPN från galleriet

Om du vill konfigurera integreringen av AWS-ClientVPN i Azure AD måste du lägga till AWS ClientVPN från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **AWS ClientVPN** i sökrutan.
1. Välj **AWS ClientVPN** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-aws-clientvpn"></a>Konfigurera och testa Azure AD SSO för AWS-ClientVPN

Konfigurera och testa Azure AD SSO med AWS ClientVPN med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AWS ClientVPN.

Utför följande steg för att konfigurera och testa Azure AD SSO med AWS ClientVPN:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AWS CLIENTVPN SSO](#configure-aws-clientvpn-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa AWS ClientVPN test User](#create-aws-clientvpn-test-user)** – om du vill ha en motsvarighet till B. Simon i AWS ClientVPN som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **AWS ClientVPN** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<LOCALHOST>`

    b. I text rutan **svars-URL** skriver du en URL med något av följande mönster:

    | Svars-URL |
    |------------|
    | `http://<LOCALHOST>` |
    | `https://self-service.clientvpn.amazonaws.com/api/auth/sso/saml` |
    |

    > [!NOTE]
    > Dessa värden är inte verkliga.  Uppdatera värdena med den faktiska inloggnings-URL: en och svars-URL: en.  URL: en för inloggning och svars-URL kan ha samma värde ( http://127.0.0.1:35001) .  Mer information finns i [AWS client VPN-dokumentationen](https://docs.aws.amazon.com/vpn/latest/clientvpn-admin/client-authentication.html#ad) .   Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Kontakta [AWS ClientVPN support team](https://aws.amazon.com/contact-us/) för eventuella konfigurations problem. 

1. I Azure Active Directory tjänst navigerar du till **Appregistreringar** och väljer sedan **alla program**.

1. Skriv **AWS ClientVPN** i sökrutan och välj **AWS ClientVPN** från Sök panelen.

1. Klicka på **manifest** och du måste behålla svars-URL: en som **http** i stället för **https** för att integrationen ska fungera. Klicka på **Spara**.

    ![manifest sida](./media/aws-clientvpn-tutorial/reply-url.png)

1. AWS ClientVPN-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig AWS ClientVPN-program att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name |  Källattribut|
    | -------------- | --------- |
    | memberOf | användare. grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera AWS ClientVPN** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till AWS ClientVPN.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **AWS ClientVPN**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-aws-clientvpn-sso"></a>Konfigurera AWS ClientVPN SSO

Om du vill konfigurera enkel inloggning på **AWS ClientVPN** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [AWS ClientVPN support team](https://aws.amazon.com/contact-us/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-aws-clientvpn-test-user"></a>Skapa AWS ClientVPN-test användare

I det här avsnittet skapas en användare som kallas Britta Simon i AWS ClientVPN. AWS ClientVPN stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i AWS ClientVPN skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till AWS ClientVPN-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till AWS ClientVPN-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen AWS ClientVPN i Mina appar omdirigeras det till AWS ClientVPN-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AWS ClientVPN kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).