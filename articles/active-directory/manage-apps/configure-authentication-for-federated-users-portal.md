---
title: Konfigurera automatisk acceleration för inloggning med identifiering av hemsfär
description: Lär dig hur du konfigurerar identifieringsprincipen för Azure Active Directory för federerade användare, inklusive automatisk acceleration och domäntips.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1af80979a4712f6d25d994835128f9d5d2205f42
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534732"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera Azure Active Directory för ett program med hjälp av en identifieringsprincip för hemsfär

Den här artikeln innehåller en introduktion till att konfigurera Azure Active Directory för federerade användare med en HRD-princip (Home Realm Discovery).  Den beskriver hur du använder automatisk acceleration för att hoppa över användarnamnsinmatningsskärmen och automatiskt vidarebefordra användare till federerade inloggningsslutpunkter.  Microsoft rekommenderar inte att du konfigurerar automatisk acceleration längre, eftersom det kan förhindra användning av starkare autentiseringsmetoder som FIDO och hindrar samarbete.

## <a name="home-realm-discovery"></a>Upptäckt av hemsfär

Identifiering av hemsfär (HRD) är den process som gör att Azure Active Directory (Azure AD) kan avgöra vilken identitetsprovider ("IdP") som en användare behöver autentisera med vid inloggningen.  När en användare loggar in på en Azure AD-klientorganisation för att få åtkomst till en resurs, eller till den vanliga inloggningssidan för Azure AD, skriver de ett användarnamn (UPN). Azure AD använder det för att identifiera var användaren behöver logga in.

Användaren kommer att tas till någon av följande identitetsproviders som ska autentiseras:

- Användarens hemklientorganisation (kan vara samma klientorganisation som den resurs som användaren försöker komma åt).

- Microsoft-konto.  Användaren är en gäst i resursklientorganisationen som använder ett konsumentkonto för autentisering.

- En lokal identitetsprovider, till exempel Active Directory Federation Services (AD FS) (AD FS).

- En annan identitetsprovider som är federerad med Azure AD-klientorganisationen.

## <a name="auto-acceleration"></a>Automatisk acceleration

Vissa organisationer konfigurerar domäner i Azure Active Directory klientorganisation för att federera med en annan IdP, till exempel AD FS för användarautentisering.  

När en användare loggar in i ett program visas först en Inloggningssida för Azure AD. När de har skrivit sitt UPN och om de finns i en federerad domän, kommer de sedan till inloggningssidan för IdP:n som betjänar domänen. Under vissa omständigheter kan administratörer vilja dirigera användare till inloggningssidan när de loggar in till specifika program.

Därför kan användarna hoppa över den första Azure Active Directory sidan. Den här processen kallas "automatisk acceleration vid inloggning".

I de fall där klientorganisationen är federerad till en annan IdP för inloggning, gör automatisk acceleration användar inloggning mer effektiviserad.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration kan användarna inte använda hanterade autentiseringsuppgifter (som FIDO) och gästanvändare kan inte logga in. Om du tar en användare direkt till en federerad IdP för autentisering går det inte att gå tillbaka till Azure Active Directory inloggningssidan. Gästanvändare som kan behöva dirigeras till andra klienter eller en extern IdP, till exempel en Microsoft-konto, kan inte logga in i programmet eftersom de hoppar över identifieringssteget för hemsfär.  

Det finns tre sätt att styra automatisk acceleration till en federerad IdP:

- Använd en [domäntips vid](#domain-hints) autentiseringsbegäranden för ett program.
- Konfigurera en identifieringsprincip för hemsfär [för att tvinga fram automatisk acceleration.](#home-realm-discovery-policy-for-auto-acceleration)
- Konfigurera en identifieringsprincip för hemsfär [för att ignorera domäntips](prevent-domain-hints-with-home-realm-discovery.md) från specifika program eller för vissa domäner.

### <a name="domain-hints"></a>Domäntips

Domäntips är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att påskynda användarens federerade IdP-inloggningssida. Eller så kan de användas av ett program för flera innehavare för att snabba upp användaren direkt till den märkta Azure AD-inloggningssidan för sin klientorganisation.  

Till exempel kan programmet "largeapp.com" göra det möjligt för kunderna att komma åt programmet via en anpassad URL contoso.largeapp.com. Appen kan också innehålla ett domäntips för contoso.com i autentiseringsbegäran.

Syntaxen för domäntips varierar beroende på vilket protokoll som används och den konfigureras vanligtvis i programmet.

**WS-Federation:** whr=contoso.com i frågesträngen.

**SAML:** Antingen en SAML-autentiseringsbegäran som innehåller en domäntips eller en frågesträng som är =contoso.com.

**Öppna ID Connect:** En frågesträng domain_hint=contoso.com.

Som standard försöker Azure AD omdirigera inloggningen till IdP:n som  har konfigurerats för en domän om båda följande stämmer:

- Ett domäntips ingår i autentiseringsbegäran från programmet **och**
- Klientorganisationen är federerad med den domänen.

Om domäntipset inte refererar till en verifierad federerad domän ignoreras det.

Mer information om automatisk acceleration med hjälp av domäntips som stöds av Azure Active Directory finns i [Enterprise Mobility + Security blogg](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Om ett domäntips ingår i en autentiseringsbegäran och ska respekteras [åsidosätter](#home-realm-discovery-policy-to-prevent-auto-acceleration)dess närvaro automatisk acceleration som har angetts för programmet i HRD-principen.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Identifieringsprincip för hemsfär för automatisk acceleration

Vissa program är inte ett sätt att konfigurera autentiseringsbegäran som de skickar. I dessa fall går det inte att använda domäntips för att styra automatisk acceleration. Automatisk acceleration kan konfigureras via identifieringsprincipen för hemsfär för att uppnå samma beteende.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Identifieringsprincip för hemsfär för att förhindra automatisk acceleration

Vissa Microsoft- och SaaS-program inkluderar domain_hints automatiskt (till exempel resulterar i en inloggningsbegäran med tillagt), vilket kan störa utrullningen av hanterade autentiseringsuppgifter `https://outlook.com/contoso.com` `&domain_hint=contoso.com` som FIDO.  Du kan använda [Identifieringsprincip för hemsfär](/graph/api/resources/homeRealmDiscoveryPolicy) för att ignorera domäntips från vissa appar eller för vissa domäner under utrullningen av hanterade autentiseringsuppgifter.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Aktivera direkt ROPC-autentisering för federerade användare för äldre program

Bästa praxis är att program använder AAD-bibliotek och interaktiv inloggning för att autentisera användare. Biblioteken tar hand om de federerade användarflödena.  Ibland skrivs inte äldre program, särskilt de som använder ROPC-bidrag, användarnamn och lösenord direkt till Azure AD för att förstå federation. De utför inte identifiering av hemsfären och interagerar inte med rätt federerade slutpunkt för att autentisera en användare. Om du väljer att använda HRD-principen kan du aktivera specifika äldre program som skickar autentiseringsuppgifter för användarnamn/lösenord med hjälp av ROPC-beviljandet för att autentisera direkt med Azure Active Directory. Synkronisering av lösenordshashar måste vara aktiverat.

> [!IMPORTANT]
> Aktivera endast direktautentisering om du har aktiverat synkronisering av lösenordshashar och du vet att det är okej att autentisera det här programmet utan några principer som implementeras av din lokala IdP. Om du inaktiverar synkronisering av lösenordshashar eller inaktiverar katalogsynkronisering med AD Connect av någon anledning bör du ta bort den här principen för att förhindra direktautentisering med hjälp av en inaktuell lösenordshashar.

## <a name="set-hrd-policy"></a>Ange HRD-princip

Det finns tre steg för att ange HRD-princip för ett program för automatisk acceleration av federerad inloggning eller direkt molnbaserade program:

1. Skapa en HRD-princip.

2. Leta upp tjänstens huvudnamn som principen ska bifogas till.

3. Koppla principen till tjänstens huvudnamn.

Principer gäller endast för ett specifikt program när de är kopplade till ett huvudnamn för tjänsten.

Endast en HRD-princip kan vara aktiv på ett huvudnamn för tjänsten åt gången.  

Du kan använda de Azure Active Directory PowerShell-cmdletarna för att skapa och hantera HRD-principen.

Följande är ett exempel på en PRINCIPdefinition för HRD:

 ```JSON
   {  
    "HomeRealmDiscoveryPolicy":
    {  
    "AccelerateToFederatedDomain":true,
    "PreferredDomain":"federated.example.edu",
    "AllowCloudPasswordValidation":false,    
    }
   }
```

Principtypen är "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

**AccelerateToFederatedDomain är** valfritt. Om **AccelerateToFederatedDomain** är falskt har principen ingen effekt på automatisk acceleration. Om **AccelerateToFederatedDomain** är sant och det bara finns en verifierad och federerad domän i klientorganisationen, kommer användarna direkt till den federerade IdP:n för inloggning. Om det är sant och det finns fler än en verifierad domän i klientorganisationen måste **PreferredDomain** anges.

**PreferredDomain** är valfritt. **PreferredDomain** bör ange en domän som ska påskyndas. Den kan utelämnas om klientorganisationen bara har en federerad domän.  Om den utelämnas och det finns fler än en verifierad federerad domän har principen ingen effekt.

 Om **PreferredDomain** har angetts måste den matcha en verifierad, federerad domän för klientorganisationen. Alla användare av programmet måste kunna logga in på den domänen – användare som inte kan logga in på den federerade domänen kommer att vara fångad och kan inte slutföra inloggningen.

**AllowCloudPasswordValidation** är valfritt. Om **AllowCloudPasswordValidation** är sant kan programmet autentisera en federerad användare genom att visa autentiseringsuppgifter för användarnamn/lösenord direkt till Azure Active Directory tokenslutpunkt. Detta fungerar bara om synkronisering av lösenordshashar är aktiverat.

Dessutom finns det två HRD-alternativ på klientnivå som inte visas ovan:

- **AlternateIdLogin är** valfritt.  Om det här alternativet är [aktiverat kan användarna logga in med sina e-postadresser](../authentication/howto-authentication-use-email-signin.md) i stället för sina UPN på Inloggningssidan för Azure AD.  Alternativa ID:er förlitar sig på att användaren inte accelereras automatiskt till en federerad IDP.

- **DomainHintPolicy** är ett valfritt komplext objekt som förhindrar domäntips från [  att automatiskt accelerera användare till federerade domäner.](prevent-domain-hints-with-home-realm-discovery.md) Den här inställningen för hela klientorganisationen används för att säkerställa att program som skickar domäntips inte hindrar användare från att logga in med moln hanterade autentiseringsuppgifter.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärdering av HRD-principer

HRD-principer kan skapas och sedan tilldelas till specifika organisationer och tjänstens huvudnamn. Det innebär att det är möjligt för flera principer att tillämpas på ett visst program, så Azure AD måste bestämma vilken som har företräde. En uppsättning regler avgör vilken HRD-princip (många som tillämpas):

- Om det finns en domäntips i autentiseringsbegäran kontrolleras HRD-principen för klienten (principen som angetts som klientorganisationsstandard) för att se om domäntips ska [ignoreras.](prevent-domain-hints-with-home-realm-discovery.md) Om domäntips tillåts används det beteende som anges av domäntipset.

- Om en princip uttryckligen tilldelas till tjänstens huvudnamn tillämpas den annars.

- Om det inte finns någon domäntips och ingen princip uttryckligen tilldelas till tjänstens huvudnamn, tillämpas en princip som uttryckligen tilldelas till den överordnade organisationen för tjänstens huvudnamn.

- Om det inte finns någon domäntips och ingen princip har tilldelats till tjänstens huvudnamn eller organisationen används standardbeteendet för HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Självstudie för att ställa in HRD-princip för ett program

Vi använder Azure AD PowerShell-cmdlets för att gå igenom några scenarier, inklusive:

- Konfigurera EN HRD-princip för automatisk acceleration för ett program i en klientorganisation med en enda federerad domän.

- Konfigurera EN HRD-princip för automatisk acceleration av ett program till en av flera domäner som har verifierats för din klientorganisation.

- Konfigurera HRD-principen så att ett äldre program kan autentiseras direkt med användarnamn/lösenord till Azure Active Directory en federerad användare.

- Lista de program som en princip har konfigurerats för.

### <a name="prerequisites"></a>Förutsättningar

I följande exempel skapar, uppdaterar, länkar och tar du bort principer för programtjänsthuvudnamn i Azure AD.

1. Börja genom att ladda ned den senaste förhandsversionen av Azure AD PowerShell-cmdleten.

2. När du har hämtat Azure AD PowerShell-cmdletarna kör du kommandot Connect för att logga in på Azure AD med ditt administratörskonto:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Kör följande kommando för att se alla principer i din organisation:

    ``` powershell
    Get-AzureADPolicy
    ```

Om inget returneras innebär det att du inte har skapat några principer i din klientorganisation.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Exempel: Ange en HRD-princip för ett program

I det här exemplet skapar du en princip som när den tilldelas till ett program antingen:

- Påskyndar automatiskt användare till en AD FS inloggningsskärm när de loggar in på ett program när det finns en enda domän i din klientorganisation.
- Påskyndar automatiskt användare till en AD FS inloggningsskärm där det finns mer än en federerad domän i din klientorganisation.
- Aktiverar icke-interaktiv inloggning med användarnamn/lösenord direkt Azure Active Directory för federerade användare för de program som principen är tilldelad till.

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: Skapa en HRD-princip

Följande princip påskyndar automatiskt användare till en AD FS inloggningsskärm när de loggar in på ett program när det finns en enda domän i din klientorganisation.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Följande princip påskyndar automatiskt användare till en AD FS inloggningsskärm där det finns fler än en federerad domän i din klientorganisation. Om du har fler än en federerad domän som autentiserar användare för program måste du ange domänen som ska accelereras automatiskt.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Om du vill skapa en princip för att aktivera autentisering med användarnamn/lösenord för federerade användare direkt med Azure Active Directory specifika program kör du följande kommando:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Om du vill se den nya principen och hämta **dess ObjectID** kör du följande kommando:

``` powershell
Get-AzureADPolicy
```

Om du vill tillämpa HRD-principen när du har skapat den kan du tilldela den till flera programtjänsthuvudnamn.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: Leta upp tjänstens huvudnamn som principen ska tilldelas till

Du behöver **ObjectID för** de tjänsthuvudnamn som du vill tilldela principen till. Det finns flera sätt att hitta **ObjectID för** tjänstens huvudnamn.

Du kan använda portalen eller köra frågor mot [Microsoft Graph](/graph/api/resources/serviceprincipal). Du kan också gå till [Graph Explorer-verktyget](https://developer.microsoft.com/graph/graph-explorer) och logga in på ditt Azure AD-konto för att se alla tjänstens huvudnamn för din organisation.

Eftersom du använder PowerShell kan du använda följande cmdlet för att visa en lista över tjänstens huvudnamn och deras ID:n.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: Tilldela principen till tjänstens huvudnamn

När du har **ObjectID** för tjänstens huvudnamn för programmet som du vill konfigurera automatisk acceleration för kör du följande kommando. Det här kommandot associerar HRD-principen som du skapade i steg 1 med tjänstens huvudnamn som du placerade i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje tjänsthuvudnamn som du vill lägga till principen i.

Om ett program redan har tilldelats en HomeRealmDiscovery-princip kan du inte lägga till en andra princip.  I så fall ändrar du definitionen för identifieringsprincipen för hemsfär som har tilldelats programmet för att lägga till ytterligare parametrar.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Steg 4: Kontrollera vilka programtjänsthuvudnamn som din HRD-princip är tilldelad till

Om du vill kontrollera vilka program som har KONFIGURERAT HRD-principen använder du cmdleten **Get-AzureADPolicyAppliedObject.** Skicka **ObjectID för** den princip som du vill kontrollera.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Steg 5: Du är klar!

Testa programmet för att kontrollera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exempel: Lista de program som HRD-principen är konfigurerad för

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Lista alla principer som har skapats i din organisation

``` powershell
Get-AzureADPolicy
```

Observera **ObjectID för** den princip som du vill visa en lista över tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Ange de tjänsthuvudnamn som principen är tilldelad till  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Exempel: Ta bort en HRD-princip från ett program

#### <a name="step-1-get-the-objectid"></a>Steg 1: Hämta ObjectID

Använd föregående exempel för att hämta **ObjectID** för principen och det programtjänsthuvudnamn som du vill ta bort den från.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: Ta bort principtilldelningen från programmets tjänsthuvudnamn  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 3: Kontrollera borttagningen genom att visa en lista över de tjänsthuvudnamn som principen är tilldelad till

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur autentisering fungerar i Azure AD finns i [Autentiseringsscenarier för Azure AD.](../develop/authentication-vs-authorization.md)
- Mer information om enkel inloggning för användare finns i [Enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).
- Besök [Microsoft Identity-plattformen](../develop/v2-overview.md) för en översikt över allt utvecklarrelaterat innehåll.