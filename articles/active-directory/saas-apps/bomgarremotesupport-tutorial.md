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
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 1996024d163a4bf7cfa741110038bb8db5b883e8
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632754"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyondtrust-remote-support"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med BeyondTrust-fjärrsupport

I den här självstudien får du lära dig att integrera BeyondTrust-fjärrsupport med Azure Active Directory (Azure AD). När du integrerar BeyondTrust remote support med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BeyondTrust-fjärrsupport.
* Gör det möjligt för användarna att logga in automatiskt för att BeyondTrust fjärrsupport med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

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

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **BeyondTrust remote support** i sökrutan.
1. Välj **BeyondTrust remote support** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-beyondtrust-remote-support"></a>Konfigurera och testa Azure AD SSO för BeyondTrust-fjärrsupport

Konfigurera och testa Azure AD SSO med BeyondTrust-fjärrsupport med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i BeyondTrust remote support.

Utför följande steg för att konfigurera och testa Azure AD SSO med BeyondTrust-fjärrsupport:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BeyondTrust remote support SSO](#configure-beyondtrust-remote-support-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa BeyondTrust support test User](#create-beyondtrust-remote-support-test-user)** – för att få en motsvarighet till B. Simon i BeyondTrust-fjärrsupport som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan Azure Portal går du till integrations sidan för **BeyondTrust-fjärrsupport** , letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **identifierare** anger du en URL med följande mönster: `https://<HOSTNAME>.bomgar.com`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<HOSTNAME>.bomgar.com/saml/sso`
    
    c. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<HOSTNAME>.bomgar.com/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL: en och Sign-On-URL. Du får de här värdena förklaras senare i självstudien.

1. BeyondTrust-fjärrsupportprogram förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig BeyondTrust-fjärrsupporten att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Name |  Källattribut|
    | ---------------| ----------|
    | Användarnamn | user.userprincipalname |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |
    | Grupper | användare. grupper |

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

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BeyondTrust-fjärrsupport.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **BeyondTrust remote support**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-beyondtrust-remote-support-sso"></a>Konfigurera BeyondTrust remote support SSO

1. Logga in på BeyondTrust remote support som administratör i ett annat webbläsarfönster.

1. Navigera till **användare & säkerhets**  >  **säkerhets leverantörer**.

1. Klicka på **Redigera** ikon i **SAML-providern**.

    ![Redigerings ikon för SAML-Provider](./media/bomgarremotesupport-tutorial/saml-providers.png)

1. Expandera avsnittet **tjänst leverantörs inställningar** .

1. Klicka på **Hämta metadata för tjänst leverantör** eller kopiera **entitets-ID: t** och **ACS URL** -värden och Använd dessa värden i avsnittet **grundläggande SAML-konfiguration** i Azure Portal.

    ![Hämta metadata för tjänst leverantör](./media/bomgarremotesupport-tutorial/service-provider-metadata.png)


1. Under avsnittet Inställningar för identitetsprovider klickar du på **Ladda upp metadata för identitetsprovider** och letar upp XML-filen med metadata som du har laddat ned från Azure Portal.

1.  **Enhets-ID: t** för **tjänst-url för enskild Sign-On** och **Server certifikat** kommer automatiskt att överföras och **URL-protokollets bindning** måste ändras till **http post**.

    ![Skärm bild som visar avsnittet för identitets leverantörs inställningar där du utför de här åtgärderna.](./media/bomgarremotesupport-tutorial/identity-provider.png)

1. Klicka på **Spara**.

### <a name="create-beyondtrust-remote-support-test-user"></a>Skapa BeyondTrust support test användare

I det här avsnittet skapas en användare med namnet Britta Simon i BeyondTrust remote support. BeyondTrust-fjärrsupporten stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i BeyondTrust-fjärrsupporten skapas en ny efter autentiseringen.

Följ proceduren nedan, som är obligatorisk för att konfigurera BeyondTrust-fjärrsupporten.

Vi kommer att konfigurera inställningarna för användar etablering här. Värdena som används i det här avsnittet kommer att refereras till i avsnittet **användarattribut &-anspråk** i Azure Portal. Vi konfigurerade detta som standardvärden som redan har importer ATS vid tidpunkten för skapandet, men värdet kan anpassas om det behövs.

![Skärm bild som visar användar etablerings inställningar där du kan konfigurera användar värden.](./media/bomgarremotesupport-tutorial/user-attribute.png)

> [!NOTE]
> Attributen Groups och e-mail är inte nödvändiga för den här implementeringen. Om du använder Azure AD-grupper och tilldelar dem till BeyondTrust för fjärran sluten support, måste objekt-ID: t för gruppen refereras via dess egenskaper i Azure Portal och placeras i avsnittet "tillgängliga grupper". När detta har slutförts kommer objekt-ID/AD-gruppen nu att vara tillgänglig för tilldelning till en grup princip för behörigheter.

![Skärm bild som visar avsnittet I T med medlemskaps typ, källa, typ och objekt I D.](./media/bomgarremotesupport-tutorial/config-user-2.png)

![Skärm bild som visar sidan grundläggande inställningar för en grup princip.](./media/bomgarremotesupport-tutorial/group-policy.png)

> [!NOTE]
> Du kan också ställa in en standard grup princip på SAML2-säkerhetsprovidern. Genom att definiera det här alternativet tilldelar detta alla användare som autentiserar via SAML de behörigheter som anges i grup principen. Den allmänna medlems principen ingår i BeyondTrust remote support/Privileged Remote Access med begränsade behörigheter som kan användas för att testa autentiseringen och tilldela användare rätt principer. Användarna kommer inte att fylla i listan SAML2 användare via/login > användare & säkerhet förrän det första lyckade autentiserings försöket. Du hittar mer information om grup principer på följande länk: `https://www.beyondtrust.com/docs/remote-support/getting-started/admin/group-policies.htm`

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till BeyondTrust-inloggnings-URL för fjärrsupport där du kan starta inloggnings flödet. 

* Gå till BeyondTrust remote support inloggnings-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på BeyondTrust-fjärrsupport panelen i Mina appar omdirigeras det till BeyondTrust-URL för fjärrsupport. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat BeyondTrust-fjärrsupporten kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget av organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)