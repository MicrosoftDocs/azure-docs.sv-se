---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med BeyondTrust-fjärrsupport | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BeyondTrust-fjärrsupport.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: jeedes
ms.openlocfilehash: ecaeea625bc4891c95614292a44d31b616d39eb2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549306"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med BeyondTrust-fjärrsupport

I den här självstudien får du lära dig att integrera BeyondTrust-fjärrsupport med Azure Active Directory (Azure AD). När du integrerar BeyondTrust remote support med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BeyondTrust-fjärrsupport.
* Gör det möjligt för användarna att logga in automatiskt för att BeyondTrust fjärrsupport med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* BeyondTrust support för enkel inloggning (SSO) med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Fjärrsupport för BeyondTrust har stöd för **SP** -INITIERAd SSO
* BeyondTrust-fjärrsupporten stöder **just-in-Time** User-etablering

## <a name="adding-beyondtrust-remote-support-from-the-gallery"></a>Lägga till BeyondTrust remote support från galleriet

Om du vill konfigurera integrationen av BeyondTrust-fjärrsupport i Azure AD måste du lägga till BeyondTrust-fjärrsupport från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **BeyondTrust remote support** i sökrutan.
1. Välj **BeyondTrust remote support** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beyondtrust-remote-support"></a>Konfigurera och testa enkel inloggning med Azure AD för BeyondTrust-fjärrsupport

Konfigurera och testa Azure AD SSO med BeyondTrust-fjärrsupport med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i BeyondTrust remote support.

Om du vill konfigurera och testa Azure AD SSO med BeyondTrust fjärrsupport, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BeyondTrust remote support SSO](#configure-beyondtrust-remote-support-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa BeyondTrust support test User](#create-beyondtrust-remote-support-test-user)** – för att få en motsvarighet till B. Simon i BeyondTrust-fjärrsupport som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)går du till integrations sidan för **BeyondTrust-fjärrsupport** , letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<HOSTNAME>.bomgar.com/saml`

    b. I rutan **identifierare** anger du en URL med följande mönster: `https://<HOSTNAME>.bomgar.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<HOSTNAME>.bomgar.com/saml/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Du får de här värdena förklaras senare i självstudien.

1. BeyondTrust-fjärrsupportprogram förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig BeyondTrust-fjärrsupporten att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ---------------| ----------|
    | GivenName | user.givenname |
    | EmailAddress | user.mail |
    | Name | user.userprincipalname |
    | Användarnamn | user.userprincipalname |
    | Grupper | användare. grupper |
    | Unik användaridentifierare | user.userprincipalname |

    > [!NOTE]
    > När du tilldelar Azure AD-grupper för BeyondTrust-fjärrsupportprogram måste alternativet "grupper som returneras i anspråk" ändras från ingen till SecurityGroup. Grupperna importeras till programmet som objekt-ID. Du hittar objekt-ID för Azure AD-gruppen genom att kontrol lera egenskaperna i Azure Active Directory-gränssnittet. Detta krävs för att referera till och tilldela Azure AD-grupper till rätt grup principer.

1. När du anger den unika användar identifieraren måste värdet anges till NameID-format: **persistent**. Vi kräver att det är en beständig identifierare för att korrekt identifiera och koppla användaren till rätt grup principer för behörigheter. Klicka på redigerings ikonen för att öppna dialog rutan **användarattribut & Claims** för att redigera värdet för unikt användar-ID.

1. I avsnittet **Hantera anspråk** klickar du på **formatet Välj namn identifierare** och anger värdet till **beständigt** och klickar på **Spara**.

    ![Användarattribut och anspråk](./media/bomgarremotesupport-tutorial/attribute-unique-user-identifier.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera BeyondTrust-fjärrsupport** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BeyondTrust-fjärrsupport.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **BeyondTrust remote support**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurera BeyondTrust remote support SSO

1. Logga in på BeyondTrust remote support som administratör i ett annat webbläsarfönster.

1. Klicka på **status** -menyn och kopiera **ID**, **svars-URL** och **inloggnings-URL** och Använd dessa värden i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ![Konfigurera BeyondTrust remote support](./media/bomgarremotesupport-tutorial/config-url-values.png)

1. Gå till BeyondTrust remote support/login Interface på `https://support.example.com/login` där **support.example.com** är det primära värd namnet för din installation och autentisera med dina administrativa autentiseringsuppgifter.

1. Navigera till **användare & säkerhets**  >  **säkerhets leverantörer**.

1. I den nedrullningsbara menyn väljer du **SAML** och klickar på knappen **skapa Provider** .

1. I avsnittet Inställningar för identitetsprovider finns ett alternativ för att ladda upp metadata för identitetsprovider. Leta upp XML-filen med metadata som du har laddat ned från Azure Portal och klicka på knappen **överför** . **Entitets-ID: t**och certifikatet för **enkel inloggnings tjänst** laddas automatiskt upp och **protokoll bindningen** måste ändras till **http post**. Se skärm bilden nedan:

    ![Konfigurera BeyondTrust remote support](./media/bomgarremotesupport-tutorial/config-uploadfile.png)

### <a name="create-beyondtrust-remote-support-test-user"></a>Skapa BeyondTrust support test användare

Vi kommer att konfigurera inställningarna för användar etablering här. Värdena som används i det här avsnittet kommer att refereras till i avsnittet **användarattribut &-anspråk** i Azure Portal. Vi konfigurerade detta som standardvärden som redan har importer ATS vid tidpunkten för skapandet, men värdet kan anpassas om det behövs.

![Skapar användare](./media/bomgarremotesupport-tutorial/config-user1.png)

> [!NOTE]
> Attributen Groups och e-mail är inte nödvändiga för den här implementeringen. Om du använder Azure AD-grupper och tilldelar dem till BeyondTrust för fjärran sluten support, måste objekt-ID: t för gruppen refereras via dess egenskaper i Azure Portal och placeras i avsnittet "tillgängliga grupper". När detta har slutförts kommer objekt-ID/AD-gruppen nu att vara tillgänglig för tilldelning till en grup princip för behörigheter.

![Skapar användare](./media/bomgarremotesupport-tutorial/config-user2.png)

![Skapar användare](./media/bomgarremotesupport-tutorial/config-user3.png)

> [!NOTE]
> Du kan också ställa in en standard grup princip på SAML2-säkerhetsprovidern. Genom att definiera det här alternativet tilldelar detta alla användare som autentiserar via SAML de behörigheter som anges i grup principen. Den allmänna medlems principen ingår i BeyondTrust remote support/Privileged Remote Access med begränsade behörigheter som kan användas för att testa autentiseringen och tilldela användare rätt principer. Användarna kommer inte att fylla i listan SAML2 användare via/login > användare & säkerhet förrän det första lyckade autentiserings försöket. Du hittar mer information om grup principer på följande länk: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen fjärrsupport för BeyondTrust på åtkomst panelen, bör du loggas in automatiskt till det BeyondTrust-fjärrstöd som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BeyondTrust remote support med Azure AD](https://aad.portal.azure.com/)
