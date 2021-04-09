---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cisco WebEx | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
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
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592531"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Cisco WebEx

I den här självstudien får du lära dig hur du integrerar Cisco WebEx med Azure Active Directory (Azure AD). När du integrerar Cisco WebEx med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco WebEx.
* Gör det möjligt för användarna att logga in automatiskt till Cisco WebEx med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cisco WebEx-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Cisco WebEx stöder **SP** -initierad SSO.
* Cisco WebEx har stöd för [**Automatisk användar etablering**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Cisco WebEx** i sökrutan.
1. Välj **Cisco WebEx** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Konfigurera och testa Azure AD SSO för Cisco WebEx

Konfigurera och testa Azure AD SSO med Cisco WebEx med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco WebEx.

Utför följande steg för att konfigurera och testa Azure AD SSO med Cisco WebEx:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
2. **[Konfigurera Cisco-WebEx](#configure-cisco-webex)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa Cisco WebEx-test](#create-cisco-webex-test-user)** för att få en motsvarighet till B. Simon i Cisco WebEx som är länkat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Cisco WebEx** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du hämtar metadata-filen för tjänst leverantören från avsnittet **Konfigurera Cisco-WebEx** , som beskrivs senare i självstudien. 

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    I text rutan för **inloggnings-URL** klistrar du in värdet för **svars-URL**, som automatiskt fylls med fil uppladdning av SP-metadatafil.

1. Cisco Webex-programmet förväntar sig att hitta SAML-försäkringar i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattributen. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar Cisco WebEx-programmet fler attribut som ska skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
  
    | Name |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    > [!NOTE]
    >  Värdet för källattribut är som standard mappat till userpricipalname. Detta kan ändras till User. mail eller user. onpremiseuserprincipalname eller något annat värde som enligt inställningen i WebEx.


1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco WebEx.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. Välj **Cisco Webex** i listan över program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cisco-webex"></a>Konfigurera Cisco-WebEx

1. Logga in på Cisco WebEx med dina administratörsautentiseringsuppgifter.

1. Välj **organisations inställningar** och under avsnittet **autentisering** klickar du på **ändra**.

    ![Skärm bild som visar autentiseringsinställningar där du kan välja ändra.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Välj **integrera en identitets leverantör från tredje part. (Avancerat)** och klicka på **Nästa**.

    ![Skärm bild som visar integrera en identitets leverantör från tredje part.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Klicka på **Hämta metadatafil** för att ladda ned **metadata-filen för tjänste leverantören** och spara den på din dator. Klicka på **Nästa**.

    ![Skärm bild som visar filen med tjänst leverantörens metadata.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Klicka på alternativet **fil läsare** för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Skärm bild som visar sidan för att importera I d P-metadata.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

1. Gå tillbaka till fliken för **Cisco Cloud Collaboration Management** browser. Om testet lyckades väljer du **det här testet lyckades. Aktivera alternativ för enkel Sign-On** och klicka på **Nästa**.

1. Klicka på **Spara**.

> [!NOTE]
> Om du vill veta mer om hur du konfigurerar Cisco-WebEx, se [den här](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) sidan.

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet skapas en användare som heter B. Simon i Cisco WebEx. det här programmet har stöd för automatisk användar etablering, vilket möjliggör automatisk etablering och avetablering baserat på dina affärs regler.  Microsoft rekommenderar att du använder automatisk etablering närhelst det är möjligt. Se Aktivera automatisk etablering för [Cisco WebEx](./cisco-webex-provisioning-tutorial.md).

Utför följande steg om du behöver skapa en användare manuellt:

1. Logga in på Cisco WebEx med dina administratörsautentiseringsuppgifter.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Skärm bild som visar sidan användare där du kan hantera användare.](./media/cisco-spark-tutorial/user-1.png) 

3. I fönstret **Hantera användare** väljer du **Lägg till eller ändra användare manuellt**.

    ![Skärm bild som visar sidan användare där du kan hantera användare och välja Lägg till eller ändra användare manuellt.](./media/cisco-spark-tutorial/user-2.png)

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Skärm bild som visar dialog rutan hantera-användare där du kan lägga till eller ändra användare manuellt.](./media/cisco-spark-tutorial/user-3.png) 

    a. I text rutan **förnamn** skriver du första namnet på användaren, t. ex. **B**.

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress: b.simon@contoso.com.

5. Klicka på plus tecknet för att lägga till B. Simon. Klicka sedan på **Nästa.**

6. I fönstret **Lägg till tjänster för användare** klickar du på **Lägg till användare** och **Slutför** sedan.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Cisco WebEx-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Cisco WebEx-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Cisco WebEx i Mina appar omdirigeras det till Cisco WebEx-inloggnings-URL. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Cisco WebEx kan du framtvinga sessionshantering, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)