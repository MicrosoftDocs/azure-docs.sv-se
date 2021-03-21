---
title: 'Självstudie: Azure Active Directory integration med Salesforce Sandbox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce-Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: c82b00b1f107dc147cbef6a0ca406f2054321216
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734889"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med begränsat läge för Salesforce

I den här självstudien får du lära dig hur du integrerar Salesforce Sandbox med Azure Active Directory (Azure AD). När du integrerar Salesforce Sandbox med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Salesforce-Sandbox.
* Gör det möjligt för användarna att logga in automatiskt till Salesforce-Sandbox med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration med enkel inloggning (SSO) för Salesforce Sandbox.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Salesforce Sandbox stöder **SP-och IDP** -INITIERAd SSO
* Salesforce Sandbox stöder **just-in-Time** User-etablering
* Salesforce Sandbox stöder [ **automatiserad** användar etablering](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Lägga till Salesforce-Sandbox från galleriet

Om du vill konfigurera integreringen av Salesforce-Sandbox i Azure AD måste du lägga till Salesforce-Sandbox från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Salesforce Sandbox** i sökrutan.
1. Välj **Salesforce-Sandbox** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Konfigurera och testa Azure AD SSO för Salesforce-Sandbox

Konfigurera och testa Azure AD SSO med Salesforce Sandbox med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i Salesforce-Sandbox.

Utför följande steg för att konfigurera och testa Azure AD SSO med begränsat läge:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Salesforce SANDBOX SSO](#configure-salesforce-sandbox-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Salesforce-testanvändare för Salesforce](#create-salesforce-sandbox-test-user)** – om du vill ha en motsvarighet till B. Simon i Salesforce-Sandbox som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integration i **Salesforce-Sandbox** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du har en **metadata-fil för Service Provider** och vill konfigurera i **IDP** initierat läge utför du följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    ![Ladda upp metadatafil](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![välj metadatafil](common/browse-upload-metadata.png)

    > [!NOTE]
    > Du hämtar metadata-filen för tjänst leverantören från Salesforce Sandbox admin-portalen som beskrivs senare i självstudien.

    c. När metadatafilen har laddats upp visas **svars-URL** -värdet automatiskt i text rutan **svars-URL** .

    ![image](common/both-replyurl.png)

    > [!Note]
    > Om **svars-URL** -värdet inte får automatiskt polulated fyller du i värdet manuellt enligt ditt krav.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera Salesforce-Sandbox** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Salesforce Sandbox.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Salesforce-Sandbox**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-salesforce-sandbox-sso"></a>Konfigurera Salesforce Sandbox SSO

1. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

2. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Skärm bild som visar ikonen "Inställningar" längst upp till höger och "Setup" som valts i list rutan.](./media/salesforce-sandbox-tutorial/configure1.png)

3. Rulla ned till **inställningarna** i det vänstra navigerings fönstret och klicka på **identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Skärm bild som visar menyn "Inställningar" i det vänstra fönstret med inställningen "Single Sign-On Settings" på "Identity"-menyn.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Skärm bild som visar sidan "inställningar för enskilda Sign-On" med knappen "redigera" markerad.](./media/salesforce-sandbox-tutorial/configure3.png)

5. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Skärm bild som visar sidan "Single Sign-On Settings" med kryss rutan "S A M L Enabled" markerad och knappen "Spara" markerad.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Skärm bild som visar sidan "enkla Sign-On inställningar" med knappen "nytt från metadatafil" markerad.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klicka på **Välj fil** för att ladda upp XML-filen med metadata som du har laddat ned från Azure-portalen och klicka på **Skapa**.

    ![Skärm bild som visar sidan "Single Sign-On Settings" med knapparna "Välj fil" och "skapa" markerade.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. På sidan med **inställningar för enkel inloggning med SAML** fylls fälten i automatiskt. Klicka på Spara.

    ![Skärm bild som visar sidan "enkla Sign-On inställningar" med fält ifyllda och knappen "Spara" markerad.](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. På sidan **Inställningar för enkel Sign-On** klickar du på knappen **Hämta metadata** för att ladda ned metadata-filen för tjänste leverantören. Använd den här filen i avsnittet **grundläggande SAML-konfiguration** i Azure Portal för att konfigurera de nödvändiga URL: erna enligt beskrivningen ovan.

    ![Skärm bild som visar sidan "enskilda Sign-On inställningar" med knappen "hämta metadata" vald.](./media/salesforce-sandbox-tutorial/configure4.png)

10. Om du vill konfigurera programmet i **SP** -initierat läge måste följande vara uppfyllda för:

    a. Du bör ha en verifierad domän.

    b. Du måste konfigurera och aktivera din domän i Salesforce-Sandbox. stegen för detta beskrivs senare i den här självstudien.

    c. I Azure Portal i avsnittet **grundläggande SAML-konfiguration** klickar du på **Ange ytterligare URL: er** och utför följande steg:
  
    ![Information om enkel inloggning för Salesforce Sandbox-domän och URL: er](common/both-signonurl.png)

    I text rutan **inloggnings-URL** skriver du värdet med följande mönster: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Det här värdet bör kopieras från Salesforce-portalen när du har aktiverat domänen.

11. I avsnittet **SAML-signeringscertifikat** klickar du på **XML för federationsmetadata** och sparar sedan XML-filen på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

12. Öppna en ny flik i webbläsaren och logga in på ditt Salesforce-administratörskonto.

13. Klicka på **Setup** (Konfiguration) under **inställningsikonen** i det övre högra hörnet på sidan.

    ![Skärm bild som visar ikonen "Inställningar" längst upp till höger och "Setup" vald på den nedrullningsbara menyn.](./media/salesforce-sandbox-tutorial/configure1.png)

14. Rulla ned till **inställningarna** i det vänstra navigerings fönstret och klicka på **identitet** för att expandera det relaterade avsnittet. Klicka sedan på **Inställningar för enkel inloggning**.

    ![Skärm bild som visar menyn "Inställningar" i det vänstra navigerings fönstret med inställningen "Single Sign-On Settings" på "Identity"-menyn.](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. På sidan med **Inställningar för enkel inloggning** klickar du på **Redigera**.

    ![Skärm bild som visar sidan "inställningar för enskilda Sign-On" med knappen "redigera" markerad.](./media/salesforce-sandbox-tutorial/configure3.png)

16. Välj **SAML-aktiverat** och klicka på **Spara**.

    ![Skärm bild som visar sidan "Single Sign-On Settings" med rutan "S A M L Enabled" markerad och knappen "Spara" markerad.](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Konfigurera inställningarna för enkel inloggning med SAML genom att klicka på **Nytt från metadatafil**.

    ![Skärm bild som visar sidan "inställningar för enskilda Sign-On" och knappen "nytt från metadatafil" vald.](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klicka på **Välj fil** för att ladda upp metadata-XML-filen och klicka på **skapa**.

    ![Skärm bild som visar sidan "enkla Sign-On inställningar" med knappen "Välj fil" och knappen "skapa" vald.](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. På sidan **Inställningar för SAML Single Sign-On** , fyller du i fält automatiskt, skriver namnet på konfigurationen (till exempel: *SPSSOWAAD_Test*), i text rutan **namn** och klickar på Spara.

    ![Skärm bild som visar sidan "enkla Sign-On inställningar" med ifyllda fält, ett exempel namn i text rutan "namn" och knappen "Spara" markerad.](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Utför följande steg för att aktivera din domän i Salesforce-Sandbox:

    > [!NOTE]
    > Innan du aktiverar domänen måste du skapa samma på Salesforce-Sandbox. Mer information finns i [definiera ditt domän namn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). När domänen har skapats kontrollerar du att den har kon figurer ATS korrekt.

21. I det vänstra navigerings fönstret i Salesforce Sandbox klickar du på **företags inställningar** för att expandera det relaterade avsnittet och klicka sedan på **min domän**.

    ![Skärm bild som visar "företags inställningar" och "min domän" som valts i det vänstra navigerings fönstret.](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. I avsnittet **konfiguration av autentisering** klickar du på **Redigera**.

    ![Skärm bild som visar avsnittet "Authentication Configuration" med knappen "redigera" markerad.](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. I avsnittet **konfiguration av autentisering** , som **Autentiseringstjänsten**, väljer du namnet på den enda SAML-Sign-On inställningen som du angav under SSO-konfigurationen i Salesforce-Sandbox och klickar på **Spara**.

    ![Konfigurera enkel inloggning](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Skapa Salesforce-test användare för Salesforce

I det här avsnittet skapas en användare som kallas Britta Simon i Salesforce-Sandbox. Salesforce-Sandbox stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Salesforce Sandbox skapas en ny när du försöker få åtkomst till Salesforce-Sandbox. Salesforce Sandbox stöder även automatisk användar etablering, du hittar mer information [här](salesforce-sandbox-provisioning-tutorial.md) om hur du konfigurerar automatisk användar etablering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till Salesforce Sandbox-inloggnings-URL där du kan initiera inloggnings flödet.  

* Gå till inloggnings-URL: en för Salesforce i begränsat läge direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så ska du loggas in automatiskt till det Salesforce-Sandbox som du konfigurerade SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen för Salesforce-Sandbox i Mina appar, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den Salesforce-Sandbox som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Salesforce-Sandbox kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)