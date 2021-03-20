---
title: 'Självstudie: Azure Active Directory integrering med BambooHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BambooHR.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180349"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Självstudie: Azure Active Directory integrering med BambooHR

I den här självstudien får du lära dig hur du integrerar BambooHR med Azure Active Directory (Azure AD). När du integrerar BambooHR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BambooHR.
* Gör det möjligt för användarna att logga in automatiskt till BambooHR med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* BambooHR för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* BambooHR stöder **SP**-initierad enkel inloggning

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.


## <a name="adding-bamboohr-from-the-gallery"></a>Lägga till BambooHR från galleriet

För att konfigurera integreringen av BambooHR i Azure AD, behöver du lägga till BambooHR från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **BambooHR** i sökrutan.
1. Välj **BambooHR** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>Konfigurera och testa Azure AD SSO för BambooHR

Konfigurera och testa Azure AD SSO med BambooHR med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i BambooHR.

Utför följande steg för att konfigurera och testa Azure AD SSO med BambooHR:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera BAMBOOHR SSO](#configure-bamboohr-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    * **[Skapa en testanvändare för BambooHR](#create-bamboohr-test-user)** – för att få en motsvarighet till Britta Simon i BambooHR som är länkad till en Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **BambooHR** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.bamboohr.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    | Svars-URL |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och svars-URL. Kontakta [BambooHR client support team](https://www.bamboohr.com/contact.php) för att hämta värdena. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera BambooHR** kopierar du lämpliga URL:er enligt dina behov.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BambooHR.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **BambooHR**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-bamboohr-sso"></a>Konfigurera BambooHR SSO

1. I ett nytt webbläsarfönster loggar du in på din BambooHR-företagsplats som administratör.

2. Gör följande på startsidan:
   
    ![Sidan BambooHR-Sign-On](./media/bamboo-hr-tutorial/ic796691.png "för Aha!")   

    a. Välj **Appar**.
   
    b. I fönstret **Appar** väljer du **Enkel inloggning**.
   
    c. Välj **Enkel inloggning med SAML**.

3. I fönstret **Enkel inloggning med SAML** gör du följande:
   
    ![Fönstret SAML Single Sign-On](./media/bamboo-hr-tutorial/IC796692.png "SAML, enkel Sign-On")
   
    a. I rutan för **inloggnings-Url för enkel inloggning** klistrar du in den **inloggnings-URL** som du kopierade från Azure-portalen i steg 6.
      
    b. I Anteckningar öppnar du det base64-kodade certifikat som du laddade ned från Azure-portalen, kopierar innehållet och klistrar sedan in det i rutan för **X.509-certifikatet**.
   
    c. Välj **Spara**.

### <a name="create-bamboohr-test-user"></a>Skapa BambooHR-testanvändare

Om du vill göra det möjligt för Azure AD-användare att logga in på BambooHR, konfigurerar du dem manuellt i BambooHR genom att göra följande:

1. Logga in på din **BambooHR**-webbplats som administratör.

2. Välj **Inställningar** i verktygsfältet högst upp.
   
    ![Knappen Inställningar](./media/bamboo-hr-tutorial/IC796694.png "Inställning")

3. Välj **Översikt**.

4. I det vänstra fönstret väljer du **säkerhets**  >  **användare**.

5. Skriv användarnamn, lösenord och e-postadress för det giltiga Azure AD-konto som du vill konfigurera.

6. Välj **Spara**.
        
>[!NOTE]
>Om du vill konfigurera Azure AD-användarkonton kan du också använda verktyg för att skapa BambooHR-användarkonton eller API:er.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till BambooHR-inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till BambooHR-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen BambooHR i åtkomst panelen omdirigeras den till BambooHR-inloggnings-URL. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat BambooHR kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).