---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SharingCloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och direkt Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/10/2021
ms.author: jeedes
ms.openlocfilehash: f663a81a86aae2fe11611aa1d6ab19f89ebcf6d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950050"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sharingcloud"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SharingCloud

I den här självstudien får du lära dig hur du integrerar SharingCloud med Azure Active Directory (Azure AD). När du integrerar SharingCloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SharingCloud.
* Gör det möjligt för användarna att logga in automatiskt till SharingCloud med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Sapient för enkel inloggning (SSO) aktive rad.


## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SharingCloud stöder **SP-och IDP** -INITIERAd SSO
* SharingCloud stöder **just-in-Time** User-etablering

## <a name="adding-sharingcloud-from-the-gallery"></a>Lägga till SharingCloud från galleriet

Om du vill konfigurera integreringen av SharingCloud i Azure AD måste du lägga till SharingCloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SharingCloud** i sökrutan.
1. Välj **SharingCloud** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-sharingcloud"></a>Konfigurera och testa Azure AD SSO för SharingCloud

Konfigurera och testa Azure AD SSO med SharingCloud med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SharingCloud.

Utför följande steg för att konfigurera och testa Azure AD SSO med SharingCloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SHARINGCLOUD SSO](#configure-sharingcloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa SharingCloud test User](#create-sharingcloud-test-user)** -om du vill ha en motsvarighet till B. Simon i SharingCloud som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **SharingCloud** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    Ladda upp metadatafilen med XML-filen från SharingCloud. Kontakta [SharingCloud-klientens support team](mailto:support@sharingcloud.com) för att hämta filen.

    ![Skärm bild av användar gränssnittet för den grundläggande SAML-konfigurationen med * * Ladda upp metadatafilen * * länk markerad.](common/upload-metadata.png)
    
    Välj den metadatafil som angetts och klicka på **överför**.

    ![Skärm bild av användar gränssnittet för metadatafilen som har tillhandahållits, med knappen Välj fil och * * överför * * markerat.](common/browse-upload-metadata.png)

1. SharingCloud-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![Skärm bild av användar gränssnittet för användarattribut med redigerings ikonen markerad.](common/edit_attribute.png)

1. Utöver ovan förväntar sig SharingCloud-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Källattribut|
    | ---------------| --------- |
    | urn: sharingcloud: SSO: FirstName | user.givenname |
    | urn: sharingcloud: SSO: LastName | user.surname |
    | urn: sharingcloud: SSO: email | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Kopiera** ikon för att kopiera **URL: en för federationsmetadata** från de angivna alternativen enligt ditt krav.

    ![URL för metadata som ska kopieras](common/copy_metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SharingCloud.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **SharingCloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sharingcloud-sso"></a>Konfigurera SharingCloud SSO

Om du vill konfigurera enkel inloggning på **SharingCloud** sida måste du skicka den kopierade **federationens metadata-URL** från Azure Portal till [support teamet för SharingCloud](mailto:support@sharingcloud.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-sharingcloud-test-user"></a>Skapa SharingCloud test användare

I det här avsnittet skapas en användare som kallas Britta Simon i SharingCloud. SharingCloud stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i SharingCloud skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till SharingCloud-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till SharingCloud-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den SharingCloud som du har konfigurerat SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen SharingCloud i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på SharingCloud som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat SharingCloud kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).