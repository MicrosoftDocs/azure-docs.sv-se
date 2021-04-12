---
title: 'Självstudie: Azure Active Directory integration med Shmoop for skolor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shmoop för skolor.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: d1d29e939ab01f6311b665eaec8425b7cd38f207
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98729502"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Självstudie: integrera Shmoop för skolor med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Shmoop för skolor med Azure Active Directory (Azure AD). När du integrerar Shmoop för skolor med Azure AD kan du:

* Kontroll i Azure AD som har till gång till Shmoop för skolor.
* Gör det möjligt för användarna att logga in automatiskt till Shmoop för skolor med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Shmoop för skolor för enkel inloggning (SSO) med aktiverade prenumerationer.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Shmoop for skolor stöder **SP** -INITIERAd SSO
* Shmoop för skolor stöder **just-in-Time** User-etablering

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Lägga till Shmoop för skolor från galleriet

Om du vill konfigurera integreringen av Shmoop för skolor i Azure AD måste du lägga till Shmoop för skolor från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Shmoop för skolor** i sökrutan.
1. Välj **Shmoop för skolor** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-shmoop-for-schools"></a>Konfigurera och testa Azure AD SSO för Shmoop för skolor

Konfigurera och testa Azure AD SSO med Shmoop för skolor som använder en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Shmoop för skolor.

Utför följande steg för att konfigurera och testa Azure AD SSO med Shmoop för skolor:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera Shmoop för skolor SSO](#configure-shmoop-for-schools-sso)** – för att konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa Shmoop för skolors test användare](#create-shmoop-for-schools-test-user)** – om du vill ha en motsvarighet till B. Simon i Shmoop för skolor som är kopplade till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Shmoop för skolor** -program integration, hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Shmoop för skolor kund support](mailto:support@shmoop.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Shmoop for skolor-appen förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig Shmoop för skolor-programmet få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | --------- | --------------- |
    | roll      | user.assignedroles |

    > [!NOTE]
    > Shmoop for School stöder två roller för användare: **lärare** och **student**. Konfigurera de här rollerna i Azure AD så att användarna kan tilldelas lämpliga roller. Information om hur du konfigurerar roller i Azure AD finns [här](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview).

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Shmoop för skolor.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Shmoop för skolor**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du har konfigurerat rollerna enligt beskrivningen i ovanstående, kan du välja den från List rutan **Välj en roll** .
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-shmoop-for-schools-sso"></a>Konfigurera Shmoop för skolor SSO

Om du vill konfigurera enkel inloggning på **Shmoop för skolor** -sidan måste du skicka **URL: en för appens Federations-metadata** till [Shmoop for skolor support team](mailto:support@shmoop.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-shmoop-for-schools-test-user"></a>Skapa Shmoop för skolor test användare

I det här avsnittet skapas en användare som heter B. Simon i Shmoop för skolor. Shmoop för skolor har stöd för just-in-Time User-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Shmoop för skolor, skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kan du kontakta [support teamet för Shmoop for skolor](mailto:support@shmoop.com).

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till Shmoop för skolors inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Shmoop för skolornas inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Shmoop för skolor i Mina appar omdirigeras det till Shmoop för skolornas inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Shmoop för skolor kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
