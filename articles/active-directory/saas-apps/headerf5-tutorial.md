---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med F5 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 28040a92d713707204f0651773f7edce9f4daf6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651781"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Självstudie: Konfigurera enkel inloggning (SSO) mellan Azure Active Directory och F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör det möjligt för användarna att logga in automatiskt på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

> [!NOTE]
> F5 BIG-IP APM [Purchase nu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

* F5-aktiverad prenumeration med enkel inloggning (SSO).

* Distribution av den gemensamma lösningen kräver följande licens:

    * F5 BIG-IP® bästa paket (eller) 

    * F5-fristående licens för BIG-IP Access Policy Manager™ (APM) 

    * F5-tilläggsprogram för BIG-IP Access Policy Manager™ (APM) för en befintlig BIG IP F5 BIG-IP® lokala Traffic Manager™ (LTM).

    * Förutom ovanstående licens kan F5-systemet också vara licensierat med: 

        * En URL-filtrerings prenumeration som använder URL-kategorins databas

        * En F5-prenumeration för IP-information för att identifiera och blockera kända angripare och skadlig trafik
     
        * En nätverks-och maskin varu säkerhetsmodul (HSM) som skyddar och hanterar digitala nycklar för stark autentisering

* F5 BIG-IP-system är etablerad med APM-moduler (LTM är valfritt)

* Även om det är valfritt, rekommenderar vi starkt att du distribuerar F5-systemen i en [enhets grupp för synkronisering/redundans](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), som innehåller det aktiva standby-paret med en flytande IP-adress för hög tillgänglighet (ha). Ytterligare gränssnitts redundans kan uppnås med hjälp av LACP (Link Aggregation Control Protocol). LACP hanterar de anslutna fysiska gränssnitten som ett enda virtuellt gränssnitt (samlings grupp) och identifierar eventuella gränssnitts problem i gruppen.

* För Kerberos-program, ett lokalt AD-tjänstkontot för begränsad delegering.  Se [F5-dokumentationen](https://support.f5.com/csp/article/K43063049) för att skapa ett AD Delegerings konto.

## <a name="access-guided-configuration"></a>Åtkomst till guidad konfiguration

* Åtkomst till guidad konfiguration stöds på F5 TMOS version 13.1.0.8 och senare. Om ditt BIG-IP-system kör en version nedan 13.1.0.8, se avsnittet **Avancerad konfiguration** .

* Åtkomst till interaktiv konfiguration visar en helt ny och strömlinjeformad användar upplevelse. Den här arbets flödes-baserade arkitekturen ger intuitiva konfigurations steg som är anpassade till den valda topologin.

* Innan du fortsätter med konfigurationen ska du uppgradera den guidade konfigurationen genom att ladda ned det senaste användnings fall paketet från [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). Följ proceduren nedan om du vill uppgradera.

    >[!NOTE]
    >Skärm bilderna nedan är för den senaste utgivna versionen (BIG-IP 15,0 med AGC version 5,0). Konfigurations stegen nedan är giltiga för det här användnings fallet över från 13.1.0.8 till den senaste versionen av BIG-IP.

1. I webb gränssnittet F5 BIG-IP klickar du på **åtkomst >> guide konfiguration**.

1. På sidan **guidad konfiguration** klickar du på **Uppgradera guidad konfiguration** i det övre vänstra hörnet.

    ![Skärm bild som visar sidan guidad konfiguration med länken Uppdatera guidad konfiguration.](./media/headerf5-tutorial/configure14.png) 

1. På popup-skärmen uppgraderings guide konfiguration väljer du **Välj fil** för att ladda upp det nedladdade användnings fallet och klickar på knappen **överför och installera** .

    ![Skärm bild som visar dialog rutan uppgradera guidad konfiguration med Välj fil valt.](./media/headerf5-tutorial/configure15.png) 

1. När uppgraderingen är klar klickar du på knappen **Fortsätt** .

    ![Skärm bild som visar dialog rutan uppgradera guidad konfiguration med ett slut för ande meddelande.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenarier för nyckel autentisering

* Förutom Azure Active Directory inbyggt integrerings stöd för moderna autentiseringsprotokoll som öppen ID Connect, SAML och WS-utfodras, F5 utökar säker åtkomst för äldre-baserade autentiseringscertifikat för både intern och extern åtkomst med Azure AD, vilket möjliggör moderna scenarier (t. ex. lösen ords mindre åtkomst) till dessa program. Detta inkluderar:

* Huvudbaserade autentiseringsmekanismer

* Kerberos-autentisering av appar

* Anonym autentisering eller inga inbyggta autentiseringscertifikat

* NTLM-autentisering av appar (skydd med dubbla prompter för användaren)

* Formulär baserat program (skydd med dubbla prompter för användaren)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integrering av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **F5** i sökrutan.
1. Välj **F5** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-f5"></a>Konfigurera och testa Azure AD SSO för F5

Konfigurera och testa Azure AD SSO med F5 med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i F5.

Utför följande steg för att konfigurera och testa Azure AD SSO med F5:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera F5 SSO](#configure-f5-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa F5 test användare](#create-f5-test-user)** – om du vill ha en motsvarighet till B. Simon i F5 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **F5** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<YourCustomFQDN>.f5.com/`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5-klientens support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera F5** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till F5.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **F5**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurera F5 enkel inloggning för sidhuvud baserat program

### <a name="guided-configuration"></a>Guidad konfiguration

1. Öppna ett nytt webbläsarfönster och logga in på din "F5"-företags webbplats som administratör och utför följande steg:

1. Gå till **System > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet. Ange ett **certifikat namn** (kommer att refereras till senare i konfigurationen). I **certifikat källan** väljer du överför fil ange det certifikat som hämtats från Azure när du konfigurerar SAML enkel inloggning. Klicka på **Importera**.

    ![Skärm bild som visar en lista med ett s L-certifikat där du väljer certifikatets namn och certifikat källa.](./media/headerf5-tutorial/configure12.png)
 
1. Dessutom krävs **SSL-certifikat för program värd namnet. Gå till system > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet. **Import typen** är **PKCS 12 (IIS)**. Ange ett **nyckel namn** (kommer att refereras till senare i konfigurationen) och ange PFX-filen. Ange **lösen ordet** för PFX-filen. Klicka på **Importera**.

    >[!NOTE]
    >I exempel namnet på appen `Headerapp.superdemo.live` använder vi ett jokertecken med vårt namn `WildCard-SuperDemo.live` .

    ![Skärm bild som visar sidan S L-certifikat/nyckel-källa.](./media/headerf5-tutorial/configure13.png)

1. Vi kommer att använda den guidade upplevelsen för att konfigurera Azure AD Federation och program åtkomst. Gå till – F5 BIG-IP- **huvud** och välj **åtkomst > guidad konfiguration > Federation > SAML-tjänstprovider**. Klicka på **Nästa** och påbörja konfigurationen genom att klicka på **Nästa** .

    ![Skärm bild som visar sidan guidad konfiguration med den valda federationen.](./media/headerf5-tutorial/configure01.png)

    ![Skärm bild som visar sidan för SAML-tjänstprovidern.](./media/headerf5-tutorial/configure02.png)
 
1. Ange ett **konfigurations namn**. Ange **entitets-ID: t** (samma som det som du konfigurerade i Azure AD-programkonfigurationen). Ange **värd namnet**. Lägg till en **Beskrivning** av referensen. Godkänn återstående standard poster och välj och klicka sedan på **spara & nästa**.

    ![Skärm bild som visar sidan Egenskaper för tjänst leverantör.](./media/headerf5-tutorial/configure03.png) 

1. I det här exemplet skapar vi en ny virtuell server som 192.168.30.20 med port 443. Ange IP-adressen för den virtuella servern i **mål adressen**. Välj klienten **SSL-profil**, Välj Skapa ny. Ange tidigare överförda program certifikat (jokertecken i det här exemplet) och tillhör ande nyckel och klicka sedan på **spara & nästa**.

    >[!NOTE]
    >i det här exemplet körs vår interna webserver på port 888 och vi vill publicera den med 443.

    ![Skärm bild som visar sidan Egenskaper för virtuell server.](./media/headerf5-tutorial/configure04.png) 

1. Under **Välj metod för att konfigurera din IDP-anslutning** anger du metadata, klickar på Välj fil och överför metadata-XML-filen som hämtades tidigare från Azure AD. Ange ett unikt **namn** för SAML IDP Connector. Välj det **signerings certifikat för metadata** som överfördes tidigare. Klicka på **spara & nästa**.

    ![Skärm bild som visar sidan anslutnings inställningar för extern identitetsprovider.](./media/headerf5-tutorial/configure05.png)
 
1. Under **Välj en pool** anger du **Skapa ny** (du kan också välja en pool som den redan finns). Låt andra värde vara standard. Under pooler anger du IP-adressen under **IP-adress/nodnamn**. Ange **porten**. Klicka på **spara & nästa**.

    ![Skärm bild som visar sidan Egenskaper för poolen.](./media/headerf5-tutorial/configure06.png)

1. På skärmen Inställningar för enkel Sign-On väljer du **aktivera enkel inloggning**. Välj **http-huvudbaserad** under markerad enkel Sign-On typ. Ersätt **session. SAML. senaste. identitet** med **session. SAML. Last. attr. Name. identitet** under användar namn källa (den här variabeln anges med anspråks mappning i Azure AD). Under SSO-rubriker.

    * **Huvud: behörighet för autentisering**

    * **Huvud värde:% {session. SAML. senaste. attr. Name. Identity}**

    * Klicka på **spara & nästa**

    Se bilaga för en fullständig lista över variabler och värden. Du kan lägga till fler rubriker efter behov.

    >[!NOTE]
    >Konto namn är F5-Delegerings kontot som skapats (se F5-dokumentation).

    ![Skärm bild som visar sidan för enskilda Sign-On inställningar.](./media/headerf5-tutorial/configure07.png) 

1. I den här vägledningen kommer vi att hoppa över slut punkts kontroller.  Mer information finns i F5-dokumentationen. Välj **spara & nästa**.

    ![Skärm bild som visar sidan Egenskaper för slut punkts kontroll.](./media/headerf5-tutorial/configure08.png)

1. Acceptera standardinställningarna och klicka på **spara & nästa**. Mer information om inställningar för hantering av SAML-sessioner finns i F5-dokumentationen.

    ![Skärm bild som visar sidan timeout-inställningar.](./media/headerf5-tutorial/configure09.png)

1. Granska sammanfattnings skärmen och välj **distribuera** för att konfigurera Big-IP. Klicka på **Slutför**.

    ![Skärm bild som visar att ditt program är klart att distribueras.](./media/headerf5-tutorial/configure10.png)

    ![Skärm bild som visar att ditt program är distribuerat.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Avancerad konfiguration

Det här avsnittet är avsett att användas om du inte kan använda den guidade konfigurationen eller om du vill lägga till eller ändra ytterligare parametrar. Du behöver ett TLS/SSL-certifikat för program-värdnamnet.

1. Gå till **System > certifikat hantering > hantering av trafik certifikat > SSL-certifikat lista**. Välj **Importera** från det högra hörnet. **Import typen** är **PKCS 12 (IIS)**. Ange ett **nyckel namn** (kommer att refereras till senare i konfigurationen) och ange PFX-filen. Ange **lösen ordet** för PFX-filen. Klicka på **Importera**.

    >[!NOTE]
    >I exempel namnet på appen `Headerapp.superdemo.live` använder vi ett jokertecken med vårt namn `WildCard-SuperDemo.live` .
  
    ![Skärm bild som visar sidan S L-certifikat/nyckel-källa för Avancerad konfiguration.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Lägga till en ny webb server i BigIP-F5

1. Klicka på **Main > IApps > Programtjänster > program > skapa**.

1. Ange **namnet** och under **mall** väljer du **F5. http**.
 
    ![Skärm bild som visar Programtjänster sidan med val av mall.](./media/headerf5-tutorial/configure18.png)

1. Vi kommer att publicera vår HeaderApp2 externt som HTTPS i detta fall, **hur ska Big-IP-systemet hantera SSL-trafik**? vi anger **att avsluta SSL från klienten, klartext till servrar (SSL-avlastning)**. Ange ditt certifikat och nyckel under **vilket SSL-certifikat vill du använda?** och **vilken SSL-privat nyckel vill du använda?**. Ange IP-adressen för den virtuella servern under den **IP-adress som du vill använda för den virtuella servern?**. 

    * **Ange övrig information**

        * FQDN  

        * Ange att den avslutar programpoolen eller skapa en ny.

        * Om du skapar en ny app server anger du en **intern IP-adress** och **port nummer**.

        ![Skärm bilden visar fönstret där du kan ange den här informationen.](./media/headerf5-tutorial/configure19.png) 

1. Klicka på **klart**.

    ![Skärm bilden visar sidan efter slut för ande.](./media/headerf5-tutorial/configure20.png) 

1. Se till att appens egenskaper kan ändras. Klicka på **Main > IApps > programtjänster: program >> HeaderApp2**. Avmarkera **strikta uppdateringar** (vi kommer att ändra vissa inställningar utanför det grafiska användar gränssnittet). Klicka på knappen **Uppdatera** .

    ![Skärm bild som visar Programtjänster sidan med fliken Egenskaper markerad.](./media/headerf5-tutorial/configure21.png) 

1. Nu bör du kunna bläddra i den virtuella servern.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurera F5 som SP och Azure som IDP

1.  Klicka på **åtkomst > Federation> SAML Service Provider > lokal SP-tjänst > Klicka på Skapa eller + signera**.

    ![Skärm bild som visar sidan om den här stora I s-sidan. ](./media/headerf5-tutorial/configure22.png)

1. Ange information om tjänst leverantörs tjänsten. Ange **namn** som representerar F5 SP-konfiguration. Ange **entitets-ID** (vanligt vis samma som program-URL).

    ![Skärm bild som visar sidan SAML-tjänstprovider med dialog rutan skapa ny SAML S P-tjänst.](./media/headerf5-tutorial/configure23.png)

    ![Skärm bild som visar dialog rutan skapa ny SAML S P-tjänst med de valda slut punkts inställningarna.](./media/headerf5-tutorial/configure24.png)

    ![Skärm bild som visar dialog rutan skapa ny SAML S P-tjänst med valda säkerhets inställningar.](./media/headerf5-tutorial/configure25.png)

    ![Skärm bild som visar dialog rutan skapa ny SAML S P-tjänst med autentiserings kontext vald.](./media/headerf5-tutorial/configure26.png)

    ![Skärm bild som visar dialog rutan skapa ny SAML S P-tjänst med begärda attribut markerade.](./media/headerf5-tutorial/configure27.png)

    ![Skärm bild som visar dialog rutan Redigera SAML S P-tjänst med avancerade inställningar valda.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Skapa IDP-koppling

1. Klicka på **BIND/Unbind IDP Connectors** , Välj **Skapa ny IDP-koppling** och välj från alternativet **metadata** och utför sedan följande steg:
 
    ![Skärm bild som visar den Redigera SAML I d PS som använder den här S P-dialog rutan med skapa ny I/P-koppling valt.](./media/headerf5-tutorial/configure29.png)

    a. Bläddra till metadata.xml fil som hämtats från Azure AD och ange ett namn på en **identitetsprovider**.

    b. Klicka på **OK**.

    c. Kopplingen skapas och certifikatet kan hämtas automatiskt från XML-filen med metadata.
    
    ![Skärm bild som visar dialog rutan skapa en ny SAML I d P-koppling.](./media/headerf5-tutorial/configure30.png)

    d. Konfigurera F5BIG-IP för att skicka alla förfrågningar till Azure AD.

    e. Klicka på **Lägg till ny rad**, Välj **AzureIDP** (som du skapade i föregående steg, ange 

    f. **Matchande källa =% {session. Server. landinguri}** 

    ex. **Matchande värde =/***

    h. Klicka på **Uppdatera**

    i. Klicka på **OK**

    j. **SAML-IDP har installerats**
    
    ![Skärm bild som visar dialog rutan Redigera SAML I d PS som användaren har.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurera F5-princip för att omdirigera användare till Azure SAML-IDP

1. Utför följande steg för att konfigurera F5-principen för att omdirigera användare till Azure SAML-IDP:

    a. Klicka på **Main > åtkomst > profil/principer > åtkomst profiler**.

    b. Klicka på knappen **skapa** .

    ![Skärm bild som visar sidan åtkomst profiler.](./media/headerf5-tutorial/configure32.png)
 
    c. Ange **namn** (HeaderAppAzureSAMLPolicy i exemplet).

    d. Du kan anpassa andra inställningar mer information finns i F5-dokumentationen.

    ![Skärm bild som visar sidan allmänna egenskaper.](./media/headerf5-tutorial/configure33.png)

    ![Skärm bild som visar sidan allmänna egenskaper fortsatte.](./media/headerf5-tutorial/configure34.png) 

    e. Klicka på **klart**.

    f. När principen har skapats klickar du på principen och går till fliken **åtkomst princip** .

    ![Skärm bild som visar fliken åtkomst princip med allmänna egenskaper.](./media/headerf5-tutorial/configure35.png)
 
    ex. Klicka på redigeraren för **visuella principer**, redigera **åtkomst princip för profil** länk.

    h. Klicka på +-tecknet i redigeraren för visuella principer och välj **SAML-autentisering**.

    ![Skärm bild som visar en åtkomst princip.](./media/headerf5-tutorial/configure36.png)

    ![Skärm bild som visar en Sök dialog ruta med SAML-autentisering vald.](./media/headerf5-tutorial/configure37.png)
 
    i. Klicka på **Lägg till objekt**.

    j. Under **Egenskaper** anger **namn** och under **AAA server** väljer du den tidigare konfigurerade SP-filen klickar du på **Spara**.
 
    ![Skärm bild som visar egenskaperna för objektet, inklusive dess A-Server.](./media/headerf5-tutorial/configure38.png)

    k. Den grundläggande principen är klar kan du anpassa principen för att lägga till ytterligare källor/attributarkiv.

    ![Skärm bild som visar den anpassade principen.](./media/headerf5-tutorial/configure39.png)
 
    l. Se till att du klickar på länken **tillämpa åtkomst princip** överst.

### <a name="apply-access-profile-to-the-virtual-server"></a>Använd åtkomst profil på den virtuella servern

1. Tilldela åtkomst profilen till den virtuella servern för att använda F5 BIG-IP APM för att tillämpa profil inställningarna på inkommande trafik och köra den tidigare definierade åtkomst principen.

    a. Klicka på **huvud**  >  servrar för **lokal trafik**  >  .

    ![Skärm bild som visar sidan Virtual servers List.](./media/headerf5-tutorial/configure40.png)
 
    b. Klicka på den virtuella servern, bläddra till avsnittet **åtkomst princip** i list rutan **åtkomst profil** och välj den SAML-princip som skapades (i exemplet HeaderAppAzureSAMLPolicy)

    c. Klicka på **Uppdatera**
 
    ![Skärm bild som visar fönstret åtkomst princip.](./media/headerf5-tutorial/configure41.png)

    d. skapa en F5 BIG-IP-iRule® för att extrahera anpassade SAML-attribut från inkommande kontroll och skicka dem som HTTP-huvuden till Server dels test programmet. Klicka på **huvud > lokal trafik > iRules > IRule List > Klicka på Skapa**

    ![Skärm bild som visar iRule lista över lokala trafik.](./media/headerf5-tutorial/configure42.png)
 
    e. Klistra in texten F5 BIG-IP-iRule nedan i definitions fönstret.

    ![Skärm bilden visar den nya iRule-sidan.](./media/headerf5-tutorial/configure43.png)
 
    När RULE_INIT {set static::d ebug 0} när ACCESS_ACL_ALLOWED {

    Ange AZUREAD_USERNAME [ACCESS:: sessionsdata Hämta "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name "] om {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} om {! ( [HTTP:: header finns "AZUREAD_USERNAME"]) } {HTTP:: rubrik infoga AZUREAD_USERNAME $AZUREAD _USERNAME}

    Ange AZUREAD_DISPLAYNAME [ACCESS:: sessionsdata Hämta "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname "] om {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} om {! ( [HTTP:: header finns "AZUREAD_DISPLAYNAME"]) } {HTTP:: rubrik infoga AZUREAD_DISPLAYNAME $AZUREAD _DISPLAYNAME}

    Ange AZUREAD_EMAILADDRESS [ACCESS:: sessionsdata Hämta "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress "] om {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} om {! ( [HTTP:: header finns "AZUREAD_EMAILADDRESS"]) } {HTTP:: rubrik infoga AZUREAD_EMAILADDRESS $AZUREAD _EMAILADDRESS}}

    **Exempel på utdata nedan**

    ![Skärm bild som visar exempel på utdata.](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>Skapa F5-test användare

I det här avsnittet skapar du en användare som heter B. Simon i F5. Arbeta med [F5-klient support teamet](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna på F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till F5-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till F5 inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på F5 som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på F5 i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på F5 som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

> [!NOTE]
> F5 BIG-IP APM [Purchase nu](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/f5-networks.f5-big-ip-best?tab=Overview).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat F5 kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).