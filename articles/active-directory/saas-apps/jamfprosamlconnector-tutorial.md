---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med JAMF Pro | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Jamf Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.openlocfilehash: 780421d93916c7da7897dfa15d09dc895cf56280
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552670"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Självstudie: Azure Active Directory SSO-integrering med JAMF Pro

I den här självstudien får du lära dig att integrera JAMF Pro med Azure Active Directory (Azure AD). När du integrerar JAMF Pro med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till JAMF Pro.
* Logga in användarna automatiskt till JAMF Pro med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En JAMF Pro-prenumeration som är enkel inloggning aktive rad (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. 

* JAMF Pro stöder **SP-initierad** och **IDP-initierad** SSO.
* När du har konfigurerat JAMF Pro kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Lägg till JAMF Pro från galleriet

För att konfigurera integrering av Jamf Pro med Azure AD behöver du lägga till Jamf Pro från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller din personliga Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv *JAMF Pro* i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **JAMF Pro** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurera och testa SSO i Azure AD för JAMF Pro

Konfigurera och testa Azure AD SSO med JAMF Pro genom att använda en test användare som kallas B. Simon. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i JAMF Pro.

I det här avsnittet konfigurerar och testar du Azure AD SSO med JAMF Pro.

1. [Konfigurera SSO i Azure AD](#configure-sso-in-azure-ad) så att användarna kan använda den här funktionen.
    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) för att testa Azure AD SSO med B. Simon-kontot.
    1. [Tilldela test användaren i Azure AD](#assign-the-azure-ad-test-user) så att B. Simon kan använda SSO i Azure AD.
1. Konfigurera [SSO i JAMF Pro](#configure-sso-in-jamf-pro) för att konfigurera SSO-inställningar på program sidan.
    1. [Skapa en JAMF Pro-testanvändare](#create-a-jamf-pro-test-user) för att få en motsvarighet till B. Simon i JAMF Pro som är länkad till användarens Azure AD-representation.
1. [Testa SSO-konfigurationen](#test-the-sso-configuration) för att verifiera att konfigurationen fungerar.

## <a name="configure-sso-in-azure-ad"></a>Konfigurera SSO i Azure AD

I det här avsnittet aktiverar du Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för integrering av **JAMF Pro** -program och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera sidan grundläggande SAML-konfiguration.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** kan du ange värden för följande fält om du vill konfigurera programmet i **IDP-initierat** läge:

    a. I text rutan **identifierare** anger du en URL som använder följande formel: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. I text rutan **svars-URL** anger du en URL som använder följande formel: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Välj **Ange ytterligare URL: er**. Om du vill konfigurera programmet i **SP-initierat** läge, i text rutan **inloggnings-URL** , anger du en URL som använder följande formel: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Du får det faktiska ID-värdet från avsnittet **enkel inloggning** i JAMF Pro-portalen, som beskrivs senare i självstudien. Du kan extrahera det faktiska under domän svärdet från ID-värdet och använda under domän informationen som inloggnings-URL och svars-URL. Du kan också se de formler som visas i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

1. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat** , väljer **kopierings** knappen för att kopiera **URL för appens Federations-metadata**och sparar den sedan på din dator.

    ![Länken för nedladdning av SAML-signeringscertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.
   1. I fältet **användar namn** anger du [Name] @ [companydomain]. [Extension]. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet beviljar du B. Simon-åtkomst till JAMF Pro.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Jamf Pro** i programlistan.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Välj knappen Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och väljer sedan knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i dialog rutan **Välj roll** . Välj sedan knappen **Välj** längst ned på skärmen.
1. I dialog rutan **Lägg till tilldelning** väljer du knappen **tilldela** .

## <a name="configure-sso-in-jamf-pro"></a>Konfigurera SSO i JAMF Pro

1. Om du vill automatisera konfigurationen i JAMF Pro installerar du **tillägget Mina appar säker inloggnings webbläsare** genom att välja **installera tillägget**.

    ![Sidan Mina appar säker inloggnings webb läsar tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren väljer du **Konfigurera JAMF Pro**. När JAMF Pro-programmet öppnas anger du administratörs behörighet för att logga in. Webb läsar tillägget konfigurerar programmet automatiskt och automatiserar steg 3 till 7.

    ![Konfigurera konfigurations sidan i JAMF Pro](common/setup-sso.png)

3. Om du vill konfigurera JAMF Pro manuellt öppnar du ett nytt webbläsarfönster och loggar in på din JAMF Pro-företags webbplats som administratör. Utför sedan följande steg.

4. Välj **inställnings ikonen** i det övre högra hörnet på sidan.

    ![Välj inställnings ikonen i JAMF Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Välj **enkel inloggning**.

    ![Välj enkel inloggning i JAMF Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Utför följande steg på sidan **enkel inloggning** .

    ![Sidan för enkel inloggning i JAMF Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Välj **Redigera**.

    b. Markera kryss rutan **aktivera autentisering med enkel inloggning** .

  c. Välj **Azure** som ett alternativ på den nedrullningsbara menyn **identitets leverantör** .

  d. Kopiera **entitets-ID-** värdet och klistra in det i fältet **identifierare (enhets-ID)** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

> [!NOTE]
> Använd värdet i `<SUBDOMAIN>` fältet för att slutföra inloggnings-URL: en och svars-URL: en i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

  e. Välj **URL för metadata** från List menyn **metadata källa för identitetsprovider** . I fältet som visas klistrar du in URL-värdet för **appens federationens metadata** som du har kopierat från Azure Portal.

  f. Valfritt Redigera värdet för token-förfallo tid eller välj "inaktivera SAML-token upphör att gälla".

7. Rulla ned till **användar mappnings** avsnittet på samma sida. Utför sedan följande steg.

    ![Avsnittet användar mappning på sidan för enkel inloggning i JAMF Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Välj alternativet **NameID** för **identitets leverantörens användar mappning**. Som standard är det här alternativet inställt på **NameID**, men du kan definiera ett anpassat attribut.

    b. Välj **e-post** för **användar mappning av JAMF Pro**. JAMF Pro mappar SAML-attribut som skickas av IdP först av användare och sedan efter grupper. När en användare försöker komma åt JAMF Pro hämtar JAMF Pro information om användaren från identitets leverantören och matchar den mot alla JAMF Pro-användarkonton. Om det inkommande användar kontot inte hittas försöker JAMF Pro matcha det efter grupp namn.

    c. Klistra in värdet `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` i fältet **namn på autentiseringsprovider för identitetsprovider** .

    d. Rulla ned till avsnittet **säkerhet** på samma sida och välj **Tillåt användare att kringgå autentisering med enkel inloggning**. Därför omdirigeras inte användare till inloggnings sidan för identitets leverantören för autentisering och kan logga in på JAMF Pro direkt i stället. När en användare försöker få åtkomst till Jamf Pro via identitetsprovidern sker görs IdP-initierad autentisering och auktorisering med enkel inloggning.

    e. Välj **Spara**.

### <a name="create-a-jamf-pro-test-user"></a>Skapa en JAMF Pro-test användare

För att Azure AD-användare ska kunna logga in på JAMF Pro måste de tillhandahållas i JAMF Pro. Etableringen i JAMF Pro är en manuell uppgift.

Gör så här för att etablera ett användar konto:

1. Logga in på din företags webbplats för JAMF Pro som administratör.

2. Välj **inställnings** ikonen i det övre högra hörnet på sidan.

    ![Inställnings ikonen i JAMF Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Välj **JAMF Pro-användarkonton & grupper**.

    ![JAMF Pro-användarkonton & Groups-ikonen i JAMF Pro-inställningar](./media/jamfprosamlconnector-tutorial/user1.png)

4. Välj **Nytt**.

    ![JAMF Pro-användarkonton & grupper system inställnings sida](./media/jamfprosamlconnector-tutorial/user2.png)

5. Välj **Create Standard Account** (Skapa standardkonto).

    ![Alternativet Skapa standard konto på sidan med & grupper för användar konton i JAMF Pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. I dialog rutan **nytt konto** utför du följande steg:

    ![Nya konto installations alternativ i JAMF Pro-Systeminställningar](./media/jamfprosamlconnector-tutorial/user4.png)

    a. I fältet **username (användar namn** ) anger `Britta Simon` du det fullständiga namnet på test användaren.

    b. Välj alternativ för **åtkomst nivå**, **behörighets uppsättning**och **åtkomst status** som är i enlighet med din organisation.

    c. I fältet **fullständigt namn** anger du `Britta Simon` .

    d. I fältet **e-postadress** anger du e-postadressen för Britta Simon-kontot.

    e. I fältet **lösen ord** anger du användarens lösen ord.

    f. I fältet **Verifiera lösen ord** anger du användarens lösen ord igen.

    ex. Välj **Spara**.

## <a name="test-the-sso-configuration"></a>Testa SSO-konfigurationen

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer JAMF Pro-panelen på åtkomst panelen, bör du loggas in automatiskt på det JAMF Pro-konto som du har konfigurerat SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Testa JAMF Pro med Azure AD](https://aad.portal.azure.com/)