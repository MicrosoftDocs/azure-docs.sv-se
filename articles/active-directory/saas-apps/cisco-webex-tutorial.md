---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx-möten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco WebEx-möten.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: bb8ea637d0353e4efa0cb946f486d68639fc699d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592499"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx-möten

I den här självstudien får du lära dig hur du integrerar Cisco WebEx-möten med Azure Active Directory (Azure AD). När du integrerar Cisco WebEx-möten med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco WebEx-möten.
* Gör det möjligt för användarna att logga in automatiskt till Cisco WebEx-möten med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.


## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cisco WebEx-möten enkel inloggning (SSO) aktive rad prenumeration.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cisco WebEx-möten stöder **SP-och IDP** -initierad SSO.

* Cisco WebEx-möten stöder **just-in-Time** User-etablering.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Lägga till Cisco WebEx-möten från galleriet

Om du vill konfigurera integreringen av Cisco WebEx-möten i Azure AD måste du lägga till Cisco WebEx-möten från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **Cisco WebEx-möten** i sökrutan.
1. Välj **Cisco WebEx-möten** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Konfigurera och testa Azure AD SSO för Cisco WebEx-möten

Konfigurera och testa Azure AD SSO med Cisco WebEx-möten med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco WebEx-möten.

Utför följande steg för att konfigurera och testa Azure AD SSO med Cisco WebEx-möten:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
   1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
   
1. **[Konfigurera Cisco WebEx-möten SSO](#configure-cisco-webex-meetings-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
   * **[Skapa Cisco WebEx-möten testa användare](#create-cisco-webex-meetings-test-user)** – för att få en motsvarighet till B. Simon i Cisco WebEx-möten som är länkade till Azure AD-representation av användare.
    
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Cisco WebEx-möten** program integration, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** kan du konfigurera programmet i **IDP** initierat läge genom att ladda upp **tjänstprovidern för tjänste leverantören** enligt följande:
   1. Klicka på **Ladda upp metadatafil**.
   1. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.
   1. När du har slutfört den överförda tjänst leverantörens metadatafil, fylls URL: **n** och **svars-URL** -värden automatiskt i avsnittet **grundläggande SAML-konfiguration** .
   
      > [!Note]
      > Du kommer att hämta metadata-filen för service providern från **Konfigurera Cisco WebEx-möten SSO** , som beskrivs senare i självstudien. 

1. Utför följande steg om du vill konfigurera programmet i **SP** initierat läge:  
   1. I avsnittet **grundläggande SAML-konfiguration** klickar du på ikonen Redigera/penna.

      ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

   1. I text rutan för **inloggnings-URL** skriver du URL: en med följande mönster: `https://<customername>.my.webex.com`

1. Cisco WebEx-Mötes program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

   ![image](common/edit-attribute.png)

1. Förutom över, förväntar Cisco WebEx-möten att fler attribut skickas tillbaka i SAML-svar. I avsnittet Användaranspråk i dialogrutan Användarattribut utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

   | Name | Källattribut|
   | ---------------|  --------- |
   |   förnamn    | user.givenname |
   |   efternamn    | user.surname |
   |   e-post       | user.mail |
   |   uid    | user.mail |

   1. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.
   1. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
   1. Lämna **Namnrymd** tom.
   1. Välj Källa som **Attribut**.
   1. Välj det attributvärde som visas för den raden i list rutan i listan **källattribut** .
   1. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera Cisco WebEx-möten** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco WebEx-möten.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Cisco WebEx-möten**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurera Cisco WebEx-möten SSO

1. Logga in på Cisco WebEx-möten med dina administratörsautentiseringsuppgifter.
1. Gå till **vanliga plats inställningar** och navigera till **SSO-konfiguration**.

   ![Skärm bild som visar Cisco WebEx-administration med gemensamma plats inställningar och S O-konfiguration valt.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. På sidan **Administration av WebEx** utför du följande steg:

   ![Skärm bild som visar sidan WebEx administration med den information som beskrivs i det här steget.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Välj **SAML 2,0** som **Federations protokoll**.
   1. Klicka på **Importera SAML-metadata** för att ladda upp metadatafilen, som du har laddat ned från Azure Portal.
   1. Välj **SSO-profil** som **IDP initierad**  och klicka på knappen **Exportera** för att ladda ned metadata-filen för tjänste leverantören och ladda upp den i avsnittet **grundläggande SAML-konfiguration** på Azure Portal.
   1. I text rutan **AuthContextClassRef** skriver du något av följande värden:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Om du vill aktivera MFA genom att använda Azure AD anger du de två värdena så här: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Välj **Skapa automatiskt konto**.
   
      > [!NOTE]
      > Om du vill aktivera användar etablering **just-in-Time** måste du kontrol lera att det **automatiska kontot har skapats**. Förutom att attribut för SAML-token måste skickas i SAML-svaret.

   1. Klicka på **Spara**.

      > [!NOTE]
      > Den här konfigurationen gäller endast för kunder som använder WebEx UserID i e-postformat.
      > 
      > Mer information om hur du konfigurerar Cisco WebEx-möten finns på WebEx- [dokumentations](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) sidan.

### <a name="create-cisco-webex-meetings-test-user"></a>Skapa Cisco WebEx-möten test användare

Syftet med det här avsnittet är att skapa en användare som kallas B. Simon i Cisco WebEx-möten. Cisco WebEx-möten stöder **just-in-Time-** etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Cisco WebEx-möten, skapas en ny när du försöker komma åt Cisco WebEx-möten.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Cisco WebEx-möten för att logga in på URL där du kan starta inloggnings flödet.  

* Gå till Cisco WebEx-möten inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på de Cisco WebEx-möten som du ställer in SSO för.

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen Cisco WebEx-möten i Mina appar, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på de Cisco WebEx-möten som du har konfigurerat SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cisco WebEx-möten kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)