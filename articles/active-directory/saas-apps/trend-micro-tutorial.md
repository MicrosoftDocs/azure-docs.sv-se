---
title: 'Självstudie: Azure AD SSO-integrering med Trend Micro Web Security (TMWS)'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Trend Micro Web Security (TMWS).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.openlocfilehash: f2a6598cc28c39719d73be333bd74c24fce9371b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551917"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Trend Micro Web Security (TMWS)

I den här självstudien får du lära dig hur du integrerar Trend Micro Web Security (TMWS) med Azure Active Directory (Azure AD). När du integrerar TMWS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till TMWS.
* Gör det möjligt för användarna att logga in automatiskt på TMWS med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Läs mer om SaaS-appintegrering med Azure AD i [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande för att komma igång:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En TMWS-prenumeration som är aktive rad för SSO.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* TMWS stöder SP-initierad SSO.
* När du har konfigurerat TMWS kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. Information om hur du tvingar fram en session med hjälp av Microsoft Cloud App Security finns i [publicera och distribuera appkontroll för villkorsstyrd åtkomst för alla appar](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>Lägg till TMWS från galleriet

Om du vill konfigurera integreringen av TMWS i Azure AD måste du lägga till TMWS från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Välj **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , anger du **Trend Micro Web Security (TMWS)** i sökrutan.
1. Välj **Trend Micro Web Security (TMWS)** i Sök resultaten och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Konfigurera och testa Azure AD SSO för TMWS

Du konfigurerar och testar Azure AD SSO med TMWS med hjälp av en test användare som heter B. Simon. För att SSO ska fungera måste du upprätta en länk mellan en Azure AD-användare och en relaterad användare i TMWS.

Du utför de här grundläggande stegen för att konfigurera och testa Azure AD SSO med TMWS:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) för att aktivera funktionen för dina användare.
    1. [Skapa en Azure AD-användare](#create-an-azure-ad-test-user) för att testa enkel inloggning med Azure AD.
    1. [Ge Azure AD-testa användar](#grant-the-azure-ad-test-user-access-to-tmws) åtkomst till TMWS.
    1. [Konfigurera inställningar för synkronisering av användare och grupper i Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [Konfigurera TMWS SSO](#configure-tmws-sso) på program sidan.
1. [Testa SSO](#test-sso) för att verifiera konfigurationen.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Slutför de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan för program integrering av **Trend Micro Web Security (TMWS)** i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Redigera de grundläggande konfigurations inställningarna för SAML](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden i följande rutor:

    a. I rutan **identifierare (enhets-ID)** anger du en URL i följande mönster:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Ange följande URL i rutan **svars-URL** :

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > Värdet för identifieraren i föregående steg är inte det värde som du ska ange. Du måste använda den faktiska identifieraren. Du kan hämta det här värdet i **Service Provider-inställningarna för Azure Admin Portal** -avsnittet på sidan **AUTENTISERINGSMETOD** för azure AD från **Administration > katalog tjänster**.

1. TMWS förväntar sig SAML-intyg i ett särskilt format, så du måste lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. Den här skärm bilden visar standardattributen:

    ![Standardattribut](common/default-attributes.png)

1. Förutom attributen i föregående skärm förväntar TMWS två fler attribut att skickas tillbaka i SAML-svaret. Dessa attribut visas i följande tabell. Attributen är färdiga, men du kan ändra dem så att de uppfyller dina krav.
    
    | Name | Källattribut|
    | --------------- | --------- |
    | sAMAccountName | User. egna namnet onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)**. Välj **nedladdnings** länken bredvid det här certifikat namnet för att ladda ned certifikatet och spara det på datorn:

    ![Länk för nedladdning av certifikat](common/certificatebase64.png)

1. I avsnittet **Konfigurera Trend Micro Web Security (TMWS)** kopierar du lämplig URL eller URL, baserat på dina krav:

    ![Kopiera URL: en för konfigurationen](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare som heter B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare**och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I rutan **namn** anger du `B.Simon` .  
   1. Ange username companydomain i rutan **användar namn** *** *@* *.* tillägg***. Till exempel `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord**och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>Ge Azure AD-testa användar åtkomst till TMWS

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TMWS.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Trend Micro Web Security (TMWS)**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**:

   ![Välj Användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare** och sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Välj Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-kontrollen, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Konfigurera inställningar för synkronisering av användare och grupper i Azure AD

1. Välj **Azure Active Directory** i den vänstra rutan.

1. Under **Hantera**väljer du **Appregistreringar**och väljer sedan ditt nya företags program under **alla program**.

1. Under **Hantera**väljer du **certifikat & hemligheter**.

1. I avsnittet **klient hemligheter** väljer du **ny klient hemlighet**.

1. På sidan **Lägg till en klient hemlighet**kan du också lägga till en beskrivning och välja en förfallo period för klient hemligheten och sedan välja **Lägg till**. Den nya klient hemligheten visas i avsnittet **klient hemligheter** .

1. Registrera klientens hemliga värde. Senare kan du ange den i TMWS.

1. Under **Hantera**, Välj **API-behörigheter**. 

1. I fönstret **API-behörigheter** väljer du **Lägg till en behörighet**.

1. På fliken **Microsoft API: er** i fönstret **begär API-behörigheter** väljer du **Microsoft Graph** och sedan **program behörigheter**.

1. Leta upp och Lägg till följande behörigheter: 

    * Group.Read.All
    * User. Read. all

1. Välj **Lägg till behörigheter**. Ett meddelande visas för att bekräfta att inställningarna har sparats. De nya behörigheterna visas i fönstret **API-behörigheter** .

1. I sektionen **beviljande medgivande** väljer du **bevilja administratörs medgivande för *ditt administratörs konto* (standard katalog)** och väljer sedan **Ja**. Ett meddelande visas för att bekräfta att administratörs medgivande för de begärda behörigheterna har beviljats.

1. Välj **Översikt**. 

1. Registrera program-ID och **katalog (** **klient** ) ID som visas i den högra rutan. Senare kan du ange den informationen i TMWS. Du kan också välja **anpassade domän namn** under **Azure Active Directory > hantera** och registrera domän namnet som visas i den högra rutan.

## <a name="configure-tmws-sso"></a>Konfigurera TMWS SSO

Slutför de här stegen för att konfigurera TMWS SSO på program sidan.

1. Logga in på TMWS-hanterings konsolen och gå till **administrations**  >  **användare &**  >  **Directory-tjänster**för autentisering.

1. Välj **här** på den övre delen av skärmen.

1. På sidan **autentiseringsmetod** väljer du **Azure AD**.

1. Välj **på** eller **av** för att konfigurera huruvida Azure AD-användare i din organisation ska kunna besöka webbplatser via TMWS om deras data inte synkroniseras med TMWS.

    > [!NOTE]
    > Användare som inte är synkroniserade från Azure AD kan bara autentiseras via kända TMWS-gatewayer eller den dedikerade porten för din organisation.

1. I avsnittet **Inställningar för identitetsprovider** slutför du följande steg:

    a. I rutan **tjänst-URL** anger du **inloggnings-URL** -värdet som du kopierade från Azure Portal.

    b. I rutan **inloggnings namn** anger du **användar anspråkets namn** med attributet **user. egna namnet onpremisessamaccountname** source från Azure Portal.

    c. I rutan **offentligt SSL-certifikat** använder du det hämtade **certifikatet (Base64)** från Azure Portal.

1. I avsnittet **synkroniseringsinställningar** utför du följande steg:

    a. I rutan **klient** anger du **katalog (klient) ID** eller **anpassat domän namn** värde från Azure Portal.

    b. I rutan **program-ID** anger du **programmets (klient) ID-** värde från Azure Portal.

    c. I rutan **klient hemlighet** anger du **klient hemligheten** från Azure Portal.

    d. Välj **synkroniseringsschema** som ska synkroniseras med Azure AD manuellt eller enligt ett schema. Om du väljer **manuellt**, när det finns ändringar i Active Directory användar information, måste du komma ihåg att gå tillbaka till sidan **katalog tjänster** och utföra manuell synkronisering så att informationen i TMWS förblir aktuell.

    e. Välj **Testa anslutning** för att kontrol lera om Azure AD-tjänsten kan anslutas.
    
    f. Välj **Spara**.
 
 > [!NOTE]
 > Mer information om hur du konfigurerar TMWS med Azure AD finns i [Konfigurera Azure AD-inställningar på TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx).

## <a name="test-sso"></a>Testa SSO 

När du har konfigurerat Azure AD-tjänsten och angett Azure AD som autentiseringsmetod för användaren, kan du logga in på TMWS-proxyservern för att verifiera installationen. När Azure AD-inloggningen har verifierat ditt konto kan du besöka Internet.

> [!NOTE]
> TMWS stöder inte testning av enkel inloggning från Azure AD-portalen, under **Översikt**  >  **enkel inloggning**  >  **Konfigurera enkel inloggning med SAML**  >  **test** av ditt nya företags program.

1. Rensa webbläsaren för alla cookies och starta sedan om webbläsaren. 

1. Peka din webbläsare till TMWS-proxyservern. Mer information finns i [vidarebefordran av trafik med PAC-filer](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B).

1. Besök en webbplats på Internet. TMWS leder dig till TMWS-portalen.

1. Ange ett Active Directory konto (format: *domän* \\ -*sAMAccountName* eller *sAMAccountName* @ -*domän*), e-postadress eller UPN och välj sedan **Logga in**. TMWS skickar dig till inloggnings fönstret för Azure AD.

1. Ange dina autentiseringsuppgifter för Azure AD-kontot i inloggnings fönstret i Azure AD. Du bör nu vara inloggad på TMWS.

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Trend Micro Web Security med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Skydda Trend Micro Web Security med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

