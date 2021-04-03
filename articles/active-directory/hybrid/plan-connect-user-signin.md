---
title: 'Azure AD Connect: användar inloggning | Microsoft Docs'
description: Azure AD Connect användar inloggning för anpassade inställningar.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3365a58a0c667ca55b74a5120cdd7a78ad0abc79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997791"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Azure AD Connect user sign-in options (Alternativ för användarinloggning i Azure AD Connect)
Med Azure Active Directory (Azure AD) Connect kan användarna logga in i både molnet och lokala resurser med samma lösen ord. I den här artikeln beskrivs viktiga begrepp för varje identitets modell som hjälper dig att välja den identitet som du vill använda för att logga in på Azure AD.

Om du redan är bekant med identitets modellen för Azure AD och vill lära dig mer om en särskild metod, se lämplig länk:

* [Password-hash-synkronisering](#password-hash-synchronization) med [sömlös enkel inloggning (SSO)](how-to-connect-sso.md)
* [Direktautentisering](how-to-connect-pta.md) med [sömlös enkel inloggning (SSO)](how-to-connect-sso.md)
* [Federerad enkel inloggning (med Active Directory Federation Services (AD FS) (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federation med PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Det är viktigt att komma ihåg att genom att konfigurera Federation för Azure AD, etablera förtroende mellan din Azure AD-klient och de federerade domänerna. Med det här förtroendet federerade domän användare får du till gång till moln resurser i Azure AD inom klienten.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>Välja användar inloggnings metod för din organisation
Det första beslutet vid implementering av Azure AD Connect är att välja vilken autentiseringsmetod som användarna ska använda för att logga in. Det är viktigt att se till att du väljer rätt metod som uppfyller organisationens säkerhet och avancerade krav. Autentisering är kritiskt eftersom det kommer att verifiera användarens identiteter för att komma åt appar och data i molnet. Om du vill välja rätt autentiseringsmetod måste du ta hänsyn till tiden, den befintliga infrastrukturen, komplexiteten och kostnaden för att implementera ditt val. Dessa faktorer är olika för alla organisationer och kan ändras med tiden.

Azure AD stöder följande autentiseringsmetoder: 

* **Molnbaserad autentisering** – när du väljer den här autentiseringsmetoden hanterar Azure AD autentiseringsprocessen för användarens inloggning. Med molnbaserad autentisering kan du välja mellan två alternativ: 
   * **PHS (Password hash Sync)** – hash-synkronisering av lösen ord gör det möjligt för användare att använda samma användar namn och lösen ord som de använder lokalt utan att behöva distribuera ytterligare infrastruktur utöver Azure AD Connect. 
   * **Direktautentisering (PTA)** – det här alternativet liknar lösen ordets hash-synkronisering, men ger en enkel lösen ords validering med lokala program varu agenter för organisationer med principer för hög säkerhets-och efterlevnadsprinciper.
* **Federerad autentisering** – när du väljer den här autentiseringsmetoden kommer Azure AD att lämna autentiseringsprocessen till ett separat betrott autentiseringsschema, till exempel AD FS eller ett Federations system från tredje part, för att verifiera användarens inloggning. 

För de flesta organisationer som bara vill aktivera användar inloggning för att Microsoft 365, SaaS-program och andra Azure AD-baserade resurser, rekommenderar vi att du använder standard alternativet för synkronisering av lösen ords hash.
 
Detaljerad information om hur du väljer en autentiseringsmetod finns i [Välj rätt autentiseringsmetod för din Azure Active Directory hybrid identitets lösning](./choose-ad-authn.md)

### <a name="password-hash-synchronization"></a>Synkronisering av lösenordshash
Med hash-synkronisering av lösen ord synkroniseras hashar av användar lösen ord från lokala Active Directory till Azure AD. När lösen ord ändras eller återställs lokalt synkroniseras de nya hasharna för lösen ord till Azure AD omedelbart så att användarna alltid kan använda samma lösen ord för moln resurser och lokala resurser. Lösen orden skickas aldrig till Azure AD eller lagras i Azure AD i klartext. Du kan använda Password-hash-synkronisering tillsammans med tillbakaskrivning av lösen ord för att aktivera självbetjäning för återställning av lösen ord i Azure AD.

Dessutom kan du aktivera [sömlös enkel inloggning](how-to-connect-sso.md) för användare på domänanslutna datorer som finns i företags nätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användar namn för att hjälpa dem att få säker åtkomst till moln resurser.

![Synkronisering av lösenordshash](./media/plan-connect-user-signin/passwordhash.png)

Mer information finns i artikeln om [synkronisering av lösen ords hash](how-to-connect-password-hash-synchronization.md) .

### <a name="pass-through-authentication"></a>Direktautentisering
Med direktautentisering verifieras användarens lösen ord mot den lokala Active Directory kontrollanten. Lösen ordet behöver inte finnas i något annat formulär i Azure AD. Detta gör det möjligt för lokala principer, till exempel begränsningar för inloggnings timmar, att utvärderas vid autentisering till moln tjänster.

Direktautentisering använder en enkel agent på en Windows Server 2012 R2-domänansluten dator i den lokala miljön. Den här agenten lyssnar efter begär Anden om lösen ords verifiering. Inga inkommande portar måste vara öppna för Internet.

Dessutom kan du även aktivera enkel inloggning för användare på domänanslutna datorer som finns i företags nätverket. Med enkel inloggning behöver aktiverade användare bara ange ett användar namn för att hjälpa dem att få säker åtkomst till moln resurser.
![Direktautentisering](./media/plan-connect-user-signin/pta.png)

Mer information finns i:
- [Direktautentisering](how-to-connect-pta.md)
- [Enkel inloggning](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federation som använder en ny eller befintlig Server grupp med AD FS i Windows Server 2012 R2
Med federerad inloggning kan dina användare logga in på Azure AD-baserade tjänster med sina lokala lösen ord. Även om de är i företags nätverket behöver de inte ens ange sina lösen ord. Genom att använda Federations alternativet med AD FS kan du distribuera en ny eller befintlig Server grupp med AD FS i Windows Server 2012 R2. Om du väljer att ange en befintlig Server grupp konfigurerar Azure AD Connect förtroendet mellan server gruppen och Azure AD så att användarna kan logga in.

<center>

![Federation med AD FS i Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Distribuera Federation med AD FS i Windows Server 2012 R2

Om du distribuerar en ny server grupp behöver du:

* En Windows Server 2012 R2-server för Federations servern.
* En Windows Server 2012 R2-server för webbprogramproxy.
* En. pfx-fil med ett TLS/SSL-certifikat för ditt avsedda Federations tjänst namn. Till exempel: fs.contoso.com.

Om du distribuerar en ny server grupp eller använder en befintlig Server grupp behöver du:

* Lokal administratörs behörighet på dina Federations servrar.
* Lokal administratörs behörighet på arbets grupps servrar (inte domänanslutna) som du tänker distribuera rollen Webbprogramproxy på.
* Datorn som du kör guiden på för att kunna ansluta till andra datorer som du vill installera AD FS eller Webbprogramproxy på med hjälp av Windows Remote Management.

Mer information finns i [Konfigurera SSO med AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs).

### <a name="federation-with-pingfederate"></a>Federation med PingFederate
Med federerad inloggning kan dina användare logga in på Azure AD-baserade tjänster med sina lokala lösen ord. Även om de är i företags nätverket behöver de inte ens ange sina lösen ord.

Mer information om hur du konfigurerar PingFederate för användning med Azure Active Directory finns i [PingFederate-integrering med Azure Active Directory och Office 365](https://www.pingidentity.com/AzureADConnect)

Information om hur du konfigurerar Azure AD Connect med hjälp av PingFederate finns i [Azure AD Connect anpassad installation](how-to-connect-install-custom.md#configuring-federation-with-pingfederate)

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Logga in med en tidigare version av AD FS eller en lösning från tredje part
Om du redan har konfigurerat moln inloggning med en tidigare version av AD FS (till exempel AD FS 2,0) eller en tredjeparts-federationsserverproxy kan du välja att hoppa över inloggnings konfigurationen för användare via Azure AD Connect. På så sätt kan du hämta den senaste synkroniseringen och andra funktioner i Azure AD Connect medan du fortfarande använder din befintliga lösning för inloggning.

Mer information finns i kompatibilitetslistan för [Azure AD från tredje part](how-to-connect-fed-compatibility.md).


## <a name="user-sign-in-and-user-principal-name"></a>Användar inloggning och User Principal Name
### <a name="understanding-user-principal-name"></a>Förstå User Principal Name
I Active Directory är standardsuffixet för User Principal Name (UPN) DNS-namnet för den domän där användar kontot skapades. I de flesta fall är detta domän namnet som är registrerat som företags domänen på Internet. Du kan dock lägga till fler UPN-suffix genom att använda Active Directory domäner och förtroenden.

Användarens UPN har formatet username@domain . Till exempel för en Active Directory domän med namnet "contoso.com" kan en användare med namnet John ha UPN-" john@contoso.com ". Användarens UPN baseras på RFC 822. Även om UPN och e-post delar samma format, kan värdet för UPN för en användare eller inte vara detsamma som användarens e-postadress.

### <a name="user-principal-name-in-azure-ad"></a>Användarens huvud namn i Azure AD
I guiden Azure AD Connect används attributet userPrincipalName eller så kan du ange attributet (i en anpassad installation) som ska användas från lokalt som User Principal Name i Azure AD. Detta är det värde som används för att logga in på Azure AD. Om värdet för userPrincipalName-attributet inte motsvarar en verifierad domän i Azure AD, ersätter Azure AD det med ett default. onmicrosoft.com-värde.

Alla kataloger i Azure Active Directory har ett inbyggt domän namn, med formatet contoso.onmicrosoft.com, som gör att du kan komma igång med Azure eller andra Microsoft-tjänster. Du kan förbättra och förenkla inloggnings upplevelsen genom att använda anpassade domäner. Information om anpassade domän namn i Azure AD och hur du verifierar en domän finns i [lägga till ett anpassat domän namn till Azure Active Directory](../fundamentals/add-custom-domain.md).

## <a name="azure-ad-sign-in-configuration"></a>Inloggningskonfiguration för Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Inloggnings konfiguration för Azure AD med Azure AD Connect
Inloggnings upplevelsen i Azure AD beror på om Azure AD kan matcha User Principal Name suffix för en användare som synkroniseras till en av de anpassade domänerna som verifieras i Azure AD-katalogen. Azure AD Connect ger hjälp när du konfigurerar inloggnings inställningar för Azure AD, så att användarens inloggnings upplevelse i molnet liknar den lokala upplevelsen.

Azure AD Connect listar de UPN-suffix som har definierats för domänerna och försöker matcha dem med en anpassad domän i Azure AD. Sedan hjälper det dig med lämplig åtgärd som måste vidtas.
Inloggnings sidan för Azure AD visar de UPN-suffix som har definierats för lokala Active Directory och visar motsvarande status för varje suffix. Status värden kan vara något av följande:

| Stat | Beskrivning | Åtgärd krävs |
|:--- |:--- |:--- |
| Kontrol |Azure AD Connect hittade en matchande verifierad domän i Azure AD. Alla användare för den här domänen kan logga in med sina lokala autentiseringsuppgifter. |Ingen åtgärd krävs. |
| Inte verifierad |Azure AD Connect hittade en matchande anpassad domän i Azure AD, men den har inte verifierats. UPN-suffixet för användare av den här domänen kommer att ändras till default. onmicrosoft.com-suffixet efter synkronisering om domänen inte har verifierats. | [Verifiera den anpassade domänen i Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Inte tillagt |Azure AD Connect gick inte att hitta en anpassad domän som motsvarar UPN-suffixet. UPN-suffixet för användare av den här domänen kommer att ändras till default. onmicrosoft.com-suffixet om domänen inte har lagts till och verifierats i Azure. | [Lägg till och verifiera en anpassad domän som motsvarar UPN-suffixet.](../fundamentals/add-custom-domain.md) |

På inloggnings sidan för Azure AD visas de UPN-suffix som har definierats för lokala Active Directory och motsvarande anpassade domän i Azure AD med den aktuella verifierings statusen. I en anpassad installation kan du nu välja attributet för User Principal Name på inloggnings sidan för **Azure AD** .

![Inloggnings sida för Azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

Du kan klicka på knappen Uppdatera för att hämta den senaste statusen för de anpassade domänerna från Azure AD igen.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Välja attributet för User Principal Name i Azure AD
Attributet userPrincipalName är det attribut som användarna använder när de loggar in på Azure AD och Microsoft 365. Du bör kontrol lera domänerna (även kallade UPN-suffix) som används i Azure AD innan användarna synkroniseras.

Vi rekommenderar starkt att du behåller standardattributet userPrincipalName. Om det här attributet är nonroutable och inte kan verifieras, är det möjligt att välja ett annat attribut (e-post, till exempel) som attributet som innehåller inloggnings-ID: t. Detta kallas för alternativ-ID. Attributvärdet för alternativ-ID måste följa RFC 822-standarden. Du kan använda ett alternativt ID med både lösen ord för enkel inloggning och Federation SSO som inloggnings lösning.

> [!NOTE]
> Att använda ett alternativt ID är inte kompatibelt med alla Microsoft 365 arbets belastningar. Mer information finns i [Konfigurera alternativt inloggnings-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id).
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Olika anpassade domän tillstånd och deras inverkan på Azures inloggnings upplevelse
Det är mycket viktigt att förstå relationen mellan de anpassade domän tillstånden i din Azure AD-katalog och de UPN-suffix som definieras lokalt. Vi går igenom de olika möjliga Azures inloggnings upplevelser när du konfigurerar synkronisering med hjälp av Azure AD Connect.

För följande information antar vi att vi är intresserade av UPN-suffixet contoso.com, som används i den lokala katalogen som en del av UPN, till exempel user@contoso.com .

###### <a name="express-settingspassword-hash-synchronization"></a>Snabb inställningar/Password-hash-synkronisering

| Tillstånd | Inverkan på användarens inloggnings upplevelse i Azure |
|:---:|:--- |
| Inte tillagt |I det här fallet har ingen anpassad domän för contoso.com lagts till i Azure AD-katalogen. Användare som har UPN lokalt med suffixet kan @contoso.com inte använda sina lokala UPN för att logga in på Azure. De måste i stället använda ett nytt UPN som tillhandahålls av Azure AD genom att lägga till suffixet för standard Azure AD-katalogen. Om du till exempel synkroniserar användare till Azure AD-katalogen azurecontoso.onmicrosoft.com får den lokala användaren user@contoso.com ett UPN för user@azurecontoso.onmicrosoft.com . |
| Inte verifierad |I det här fallet har vi en anpassad domän-contoso.com som har lagts till i Azure AD-katalogen. Men det har inte verifierats ännu. Om du går vidare med att synkronisera användare utan att verifiera domänen, tilldelas användarna ett nytt UPN av Azure AD, precis som i scenariot "inte tillagt". |
| Kontrol |I det här fallet har vi en anpassad domän-contoso.com som redan har lagts till och verifierats i Azure AD som UPN-suffix. Användarna kan använda sina lokala User Principal Name, till exempel user@contoso.com för att logga in på Azure när de har synkroniserats till Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS Federation
Du kan inte skapa en federation med default. onmicrosoft.com-domänen i Azure AD eller en overifierad anpassad domän i Azure AD. Om du använder guiden Azure AD Connect och väljer en overifierad domän för att skapa en federation med, uppmanas Azure AD Connect att ange de nödvändiga posterna som ska skapas där din DNS är värd för domänen. Mer information finns i [Verifiera att Azure AD-domänen har valts för Federation](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Om du valde Federations alternativet för användar inloggning **med AD FS** måste du ha en anpassad domän för att kunna fortsätta skapa en federation i Azure AD. För vår diskussion innebär det att vi bör ha en anpassad domän contoso.com som lagts till i Azure AD-katalogen.

| Tillstånd | Inverkan på användarens inloggnings upplevelse i Azure |
|:---:|:--- |
| Inte tillagt |I det här fallet gick det Azure AD Connect inte att hitta någon matchande anpassad domän för UPN-suffixet contoso.com i Azure AD-katalogen. Du måste lägga till en anpassad domän contoso.com om du vill att användarna ska logga in med hjälp av AD FS med sitt lokala UPN (t user@contoso.com . ex.). |
| Inte verifierad |I det här fallet visas Azure AD Connect med lämplig information om hur du kan verifiera din domän i ett senare skede. |
| Kontrol |I det här fallet kan du fortsätta med konfigurationen utan någon ytterligare åtgärd. |

## <a name="changing-the-user-sign-in-method"></a>Ändra användarinloggningsmetod
Du kan ändra användar inloggnings metoden från Federation, Password-hash-synkronisering eller direktautentisering genom att använda de uppgifter som är tillgängliga i Azure AD Connect efter den inledande konfigurationen av Azure AD Connect med guiden. Kör guiden Azure AD Connect igen så visas en lista över uppgifter som du kan utföra. Välj **ändra användar inloggning** i listan med uppgifter.

![Ändra användar inloggning](./media/plan-connect-user-signin/changeusersignin.png)

På nästa sida uppmanas du att ange autentiseringsuppgifterna för Azure AD.

![Skärm bild som visar var du ska ange autentiseringsuppgifterna för Azure AD.](./media/plan-connect-user-signin/changeusersignin2.png)

På sidan **användar inloggning** väljer du önskad användar inloggning.

![Anslut till Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Om du bara gör en tillfällig växel till hash-synkronisering av lösen ord markerar du kryss rutan **Konvertera inte användar konton** . Om du inte markerar alternativet konverteras varje användare till federerad och det kan ta flera timmar.
>
>

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur [du integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
- Lär dig mer om att [Azure AD Connect design koncept](plan-connect-design-concepts.md).