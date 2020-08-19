---
title: Anpassade domäner i Azure AD-programproxy
description: Konfigurera och hantera anpassade domäner i Azure AD-programproxy.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 6688875385d34fcbece964d43827c6d62ae7ced4
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587777"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurera anpassade domäner med Azure AD-programproxy

När du publicerar ett program via Azure Active Directory-programproxy skapar du en extern URL för dina användare. Den här URL: en hämtar standard domänen *yourtenant.msappproxy.net*. Om du till exempel publicerar en app med namnet *utgifter* i din klient med namnet *contoso*, är den externa webb adressen *https: \/ /expenses-contoso.msappproxy.net*. Om du vill använda ditt eget domän namn i stället för *msappproxy.net*kan du konfigurera en anpassad domän för ditt program. 

## <a name="benefits-of-custom-domains"></a>Förmåner för anpassade domäner

Det är en bra idé att konfigurera anpassade domäner för dina appar närhelst det är möjligt. Några skäl till att använda anpassade domäner är:

- Länkar mellan appar fungerar även utanför företags nätverket. Utan en anpassad domän, om din app har hårdkodade interna länkar till mål utanför programproxyn, och länkarna inte kan matchas externt, kommer de att brytas. När dina interna och externa URL: er är desamma, undviker du det här problemet. Om du inte kan använda anpassade domäner, se [omdirigera hårdkodad-Länkar för appar som publicerats med Azure AD-programproxy](../application-proxy-link-translation.md) för andra sätt att åtgärda problemet. 
  
- Användarna får en enklare upplevelse eftersom de kan komma åt appen med samma URL inifrån eller utanför nätverket. De behöver inte lära sig olika interna och externa URL: er eller spåra deras nuvarande plats. 

- Du kan styra din anpassning och skapa de URL: er som du vill använda. En anpassad domän kan hjälpa till att bygga användarens förtroende, eftersom användarna ser och använder ett välbekant namn i stället för *msappproxy.net*.

- Vissa konfigurationer fungerar bara med anpassade domäner. Du behöver till exempel anpassade domäner för appar som använder Security Assertion Markup Language (SAML), till exempel när du använder Active Directory Federation Services (AD FS) (AD FS) men inte kan använda WS-Federation. Mer information finns i [arbeta med anspråks känsliga appar i Application Proxy](application-proxy-configure-for-claims-aware-applications.md). 

Om du inte kan göra de interna och externa URL: erna identiska är det inte lika viktigt att använda anpassade domäner, men du kan fortfarande dra nytta av de andra fördelarna. 

## <a name="dns-configuration-options"></a>Alternativ för DNS-konfiguration

Det finns flera alternativ för att konfigurera din DNS-konfiguration, beroende på dina krav:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Samma interna och externa URL, olika interna och externa beteenden 

Om du inte vill att dina interna användare ska dirigeras via programproxyn kan du konfigurera en *DNS-konfiguration med delad hjärna*. En delad DNS-infrastruktur dirigerar interna värdar till en intern domän namn server och externa värdar till en extern domän namn server för namn matchning. 

![DNS med delad hjärna](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Olika interna och externa URL: er 

Om de interna och externa URL: erna skiljer sig åt, behöver du inte konfigurera delade hjärna-funktioner, eftersom användarnas routning bestäms av URL: en. I det här fallet ändrar du bara den externa DNS-adressen och dirigerar den externa URL: en till programproxyns slut punkt. 

När du väljer en anpassad domän för en extern URL, visar ett informations fält den CNAME-post som du behöver lägga till i den externa DNS-providern. Du kan alltid se den här informationen genom att gå till appens **programproxy** -sida.

## <a name="set-up-and-use-custom-domains"></a>Konfigurera och Använd anpassade domäner

Om du vill konfigurera en lokal app för att använda en anpassad domän behöver du en verifierad Azure Active Directory anpassad domän, ett PFX-certifikat för den anpassade domänen och en lokal app att konfigurera. 

### <a name="create-and-verify-a-custom-domain"></a>Skapa och verifiera en anpassad domän

Skapa och verifiera en anpassad domän:

1. I Azure Active Directory väljer du **anpassade domän namn** i det vänstra navigerings fältet och väljer sedan **Lägg till anpassad domän**. 
1. Ange ditt anpassade domän namn och välj **Lägg till domän**. 
1. På sidan domän kopierar du TXT-postinformationen för din domän. 
1. Gå till din domän registrator och skapa en ny TXT-post för din domän, baserat på din kopierade DNS-information.
1. När du har registrerat domänen väljer du **Verifiera**på domän sidan i Azure Active Directory. När domänens status har **verifierats**kan du använda domänen över alla dina Azure AD-konfigurationer, inklusive Application Proxy. 

Mer detaljerade instruktioner finns i [lägga till ditt anpassade domän namn med hjälp av Azure Active Directory portalen](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurera en app för att använda en anpassad domän

Publicera din app via Application Proxy med en anpassad domän:

1. För en ny app väljer du **företags program** i Azure Active Directory i det vänstra navigerings fältet. Välj **Nytt program**. I avsnittet **lokala program** väljer du **Lägg till ett lokalt program**. 
   
   För en app som redan finns i **företags program**markerar du den i listan och väljer sedan **Application Proxy** i det vänstra navigerings fältet. 

2. På sidan Inställningar för programproxy anger du ett **namn** om du vill lägga till ett eget lokalt program.

3.  I fältet **intern URL** anger du den interna URL: en för din app.
   
4. I fältet **extern URL** markerar du List rutan och väljer den anpassade domän som du vill använda.
   
5. Välj **Lägg till**.
   
   ![Välj anpassad domän](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Om domänen redan har ett certifikat visas certifikat informationen i fältet **certifikat** . Annars väljer du fältet **certifikat** .
   
   ![Klicka om du vill överföra ett certifikat](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. På sidan **SSL-certifikat** bläddrar du till och väljer din PFX-certifikatfil. Ange lösen ordet för certifikatet och välj **Ladda upp certifikat**. Mer information om certifikat finns i avsnittet [certifikat för anpassade domäner](#certificates-for-custom-domains) . Om certifikatet inte är giltigt eller om det är problem med lösen ordet visas ett fel meddelande. [Vanliga frågor om programproxy](application-proxy-faq.md#application-configuration) innehåller några fel söknings steg som du kan prova.
   
   ![Ladda upp certifikat](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > En anpassad domän behöver bara ett certifikat laddas upp en gång. Därefter tillämpas det överförda certifikatet automatiskt när du använder den anpassade domänen för andra appar.
   
8. Om du har lagt till ett certifikat går du till sidan **Application Proxy** och väljer **Spara**. 
   
9. I informations fältet på sidan **Application Proxy** noterar du den CNAME-post som du behöver lägga till i din DNS-zon. 
   
   ![Lägg till CNAME DNS-post](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Följ instruktionerna i [hantera DNS-poster och post uppsättningar med hjälp av Azure Portal](../../dns/dns-operations-recordsets-portal.md) för att lägga till en DNS-post som omdirigerar den nya externa URL: en till *msappproxy.net* -domänen.
   
11. Kontrol lera att DNS-posten har kon figurer ATS korrekt genom att använda kommandot [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) för att bekräfta att din externa URL är nåbar och att *msapproxy.net* -domänen visas som ett alias.

Ditt program har nu kon figurer ATS för att använda den anpassade domänen. Se till att tilldela användare till ditt program innan du testar eller släpper det. 

Om du vill ändra domänen för en app väljer du en annan domän i list rutan i **extern URL** på appens **programproxy** -sida. Ladda upp ett certifikat för den uppdaterade domänen, om det behövs, och uppdatera DNS-posten. Om du inte ser den anpassade domän som du vill använda i list rutan i **extern URL**, kanske den inte är verifierad.

Mer detaljerad information om programproxyn finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy i Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certifikat för anpassade domäner

Ett certifikat skapar en säker TLS-anslutning för din anpassade domän. 

### <a name="certificate-formats"></a>Certifikat format

Du måste använda ett PFX-certifikat för att se till att alla nödvändiga mellanliggande certifikat ingår. Certifikatet måste innehålla den privata nyckeln.

De vanligaste metoderna för certifikat signering stöds, till exempel ett alternativt namn för certifikat mottagare (SAN). 

Du kan använda certifikat med jokertecken så länge jokertecknet matchar den externa URL: en. Du måste använda certifikat med jokertecken för [program med jokertecken](application-proxy-wildcard.md). Om du vill använda certifikatet till att även komma åt under domäner måste du lägga till jokertecken för underdomäner som alternativa namn i samma certifikat. Till exempel fungerar inte ett certifikat för * \* . Adventure-Works.com* för * \* . Apps.Adventure-Works.com* om du inte lägger till * \* . Apps.Adventure-Works.com* som alternativt namn för certifikat mottagare. 

Du kan använda certifikat som utfärdats av din egen infrastruktur för offentliga nycklar (PKI) om certifikat kedjan är installerad på klient enheterna. Intune kan distribuera dessa certifikat till hanterade enheter. För icke-hanterade enheter måste du installera dessa certifikat manuellt. 

Vi rekommenderar inte att du använder en privat rot certifikat utfärdare eftersom den privata rot certifikat utfärdaren också måste skickas till klient datorer, vilket kan medföra många utmaningar.

### <a name="certificate-management"></a>Certifikathantering

All certifikat hantering sker via enskilda program sidor. Gå till programmets **Application Proxy** -sida för att komma åt fältet **certifikat** .

När ett certifikat har överförts till ett program kommer det också att appliceras automatiskt på **nya** appar som kon figurer ATS som använder samma certifikat. Du måste ladda upp certifikatet för befintliga appar i din klient organisation igen.

När ett certifikat går ut får du en varning om att ladda upp ett annat certifikat. Om certifikatet har återkallats kan användarna se en säkerhets varning vid åtkomst till appen. Om du vill uppdatera certifikatet för en app går du till sidan **Application Proxy** för appen, väljer **certifikat**och laddar upp ett nytt certifikat. Om det gamla certifikatet inte används av andra appar tas det bort automatiskt. 

## <a name="next-steps"></a>Nästa steg

* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md) till dina publicerade appar med Azure AD-autentisering.
* [Villkorlig åtkomst](../conditional-access/concept-conditional-access-cloud-apps.md) för dina publicerade molnappar.
