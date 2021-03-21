---
title: Konfigurera automatisk inloggnings acceleration med hjälp av identifiering av start sfär
description: Lär dig hur du konfigurerar identifierings princip för start sfär för Azure Active Directory autentisering för federerade användare, inklusive automatisk acceleration och domän tips.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: kenwith
ms.custom: seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ed101282a69120162d6e3b526693c0a83df45b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607117"
---
# <a name="configure-azure-active-directory-sign-in-behavior-for-an-application-by-using-a-home-realm-discovery-policy"></a>Konfigurera Azure Active Directory inloggnings beteende för ett program med hjälp av en princip för identifiering av start sfär

Den här artikeln innehåller en introduktion till hur du konfigurerar Azure Active Directory autentisering för federerade användare med hjälp av HRD-princip (Home sfär Discovery).  Den täcker med automatisk acceleration att hoppa över skärmen användar namn och automatiskt vidarebefordra användare till slut punkter för federerade inloggningar.  Microsoft rekommenderar inte att du konfigurerar automatisk acceleration längre, eftersom det kan förhindra användning av starkare autentiseringsmetoder som FIDO och hindrar samarbete.

## <a name="home-realm-discovery"></a>Upptäckt av hemsfär

Start sfär identifiering (HRD) är den process som tillåter Azure Active Directory (Azure AD) för att avgöra vilken identitetsprovider ("IdP") som en användare behöver autentisera med vid inloggnings tillfället.  När en användare loggar in på en Azure AD-klient för att få åtkomst till en resurs, eller till den vanliga inloggnings sidan för Azure AD, skriver de ett användar namn (UPN). Azure AD använder för att identifiera var användaren behöver logga in.

Användaren kommer att tas till någon av följande identitets leverantörer som ska autentiseras:

- Användarens hem klient (kan vara samma klient som den resurs som användaren försöker komma åt).

- Microsoft-konto.  Användaren är en gäst i resurs klienten som använder ett konsument konto för autentisering.

- En lokal identitetsprovider som Active Directory Federation Services (AD FS) (AD FS).

- En annan identitetsprovider som är federerad med Azure AD-klienten.

## <a name="auto-acceleration"></a>Automatisk acceleration

Vissa organisationer konfigurerar domäner i sin Azure Active Directory-klient att federera med en annan IdP, till exempel AD FS för användarautentisering.  

När en användare loggar in på ett program visas de först med en inloggnings sida för Azure AD. När de har skrivit sitt UPN, om de finns i en federerad domän, tas de sedan till inloggnings sidan för IdP som betjänar domänen. Under vissa omständigheter kanske administratörer vill dirigera användare till inloggnings sidan när de loggar in på specifika program.

Det innebär att användare kan hoppa över den första Azure Active Directory sidan. Den här processen kallas för automatisk acceleration av inloggning.

I de fall där klienten är federerad till en annan IdP för inloggning, gör automatisk acceleration att användar inloggningen är smidigare.  Du kan konfigurera automatisk acceleration för enskilda program.

>[!NOTE]
>Om du konfigurerar ett program för automatisk acceleration kan användarna inte använda hanterade autentiseringsuppgifter (t. ex. FIDO) och gäst användare kan inte logga in. Om du tar en användare direkt till en federerad IdP för autentisering, finns det inget sätt att gå tillbaka till Azure Active Directory inloggnings sida. Gäst användare, som kan behöva dirigeras till andra klienter eller en extern IdP, till exempel en Microsoft-konto, kan inte logga in till programmet eftersom de hoppar över steget för identifiering av start sfär.  

Det finns tre sätt att styra automatisk acceleration till en federerad IdP:

- Använd ett [domän tips](#domain-hints) för autentiseringsbegäranden för ett program.
- Konfigurera en princip för identifiering av start sfär för att [Framtvinga automatisk acceleration](#home-realm-discovery-policy-for-auto-acceleration).
- Konfigurera en princip för identifiering av start sfär för att [Ignorera domän tips](prevent-domain-hints-with-home-realm-discovery.md) från specifika program eller för vissa domäner.

### <a name="domain-hints"></a>Domän tips

Domän tips är direktiv som ingår i autentiseringsbegäran från ett program. De kan användas för att påskynda användaren till deras federerade IdP-inloggnings sida. Eller så kan de användas av ett program med flera klienter för att påskynda användaren direkt till den anpassade inloggnings sidan för Azure AD för klient organisationen.  

Till exempel kan programmet "largeapp.com" göra det möjligt för sina kunder att komma åt programmet på en anpassad URL "contoso.largeapp.com". Appen kan också innehålla ett domän tips till contoso.com i autentiseringsbegäran.

Domän tipsets syntax varierar beroende på vilket protokoll som används, och det är vanligt vis konfigurerat i programmet.

**WS-Federation**: wattimmar = contoso. com i frågesträngen.

**SAML**: en SAML-autentiseringsbegäran som innehåller ett domän tips eller frågesträngen wattimmar = contoso. com.

**Öppna ID Connect**: en frågesträng domain_hint = contoso. com.

Som standard försöker Azure AD omdirigera inloggningen till IdP som har kon figurer ATS för en domän om **båda** följande stämmer:

- Ett domän tips ingår i autentiseringsbegäran från programmet **och**
- Klienten är federerad med domänen.

Om domän tipset inte refererar till en verifierad federerad domän, ignoreras det.

Mer information om automatisk acceleration med hjälp av de domän tips som stöds av Azure Active Directory finns i [Enterprise Mobility + Security Blogg](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/11/using-azure-ad-to-land-users-on-their-custom-login-page-from-within-your-app/).

>[!NOTE]
>Om ett domän tips ingår i en autentiseringsbegäran och [bör respekteras](#home-realm-discovery-policy-to-prevent-auto-acceleration), åsidosätter dess närvaro den automatiska acceleration som har angetts för programmet i HRD-principen.

### <a name="home-realm-discovery-policy-for-auto-acceleration"></a>Identifierings princip för start sfär för automatisk acceleration

Vissa program har inget sätt att konfigurera autentiseringsbegäranden som de genererar. I dessa fall går det inte att använda domän tips för att kontrol lera automatisk acceleration. Automatisk acceleration kan konfigureras via Start sfär identifierings princip för att uppnå samma beteende.  

### <a name="home-realm-discovery-policy-to-prevent-auto-acceleration"></a>Identifierings princip för start sfär för att förhindra automatisk acceleration

Vissa Microsoft-och SaaS-program inkluderar automatiskt domain_hints (till exempel `https://outlook.com/contoso.com` resulterar i en inloggningsbegäran med `&domain_hint=contoso.com` tillägg), vilket kan avbryta distributionen av hanterade AUTENTISERINGSUPPGIFTER som Fido.  Du kan använda [princip för identifiering av start sfär](/graph/api/resources/homeRealmDiscoveryPolicy) för att ignorera domän tips från vissa appar eller för vissa domäner under distributionen av hanterade autentiseringsuppgifter.  

## <a name="enable-direct-ropc-authentication-of-federated-users-for-legacy-applications"></a>Aktivera direkt ROPC-autentisering av federerade användare för äldre program

Bästa praxis är att programmen använder AAD-bibliotek och interaktiv inloggning för att autentisera användare. Biblioteken tar hand om de federerade användar flödena.  Ibland är äldre program, särskilt sådana som använder ROPC-bidrag, skickar användar namn och lösen ord direkt till Azure AD och är inte skrivna för att förstå federationen. De utför inte identifiering av start sfär och interagerar inte med rätt federerade slut punkt för att autentisera en användare. Om du väljer till kan du använda HRD-principen för att aktivera vissa äldre program som skickar autentiseringsuppgifter för användar namn/lösen ord med hjälp av ROPC-tilldelningen för att autentisera direkt med Azure Active Directory. Hash-synkronisering av lösen ord måste vara aktive rad.

> [!IMPORTANT]
> Aktivera endast direkt autentisering om du har aktiverat Lösenordssynkronisering och du vet att det går att autentisera programmet utan några principer som implementeras av din lokala IdP. Om du inaktiverar hash-synkronisering av lösen ord eller inaktiverar katalogens synkronisering med AD Connect bör du ta bort den här principen för att förhindra direkt autentisering med en föråldrad lösen ords-hash.

## <a name="set-hrd-policy"></a>Ange HRD-princip

Det finns tre steg för att ställa in HRD-principen på ett program för federerad inloggning för automatisk acceleration eller direkt molnbaserade program:

1. Skapa en HRD-princip.

2. Leta reda på tjänstens huvud namn som du vill koppla principen till.

3. Koppla principen till tjänstens huvud namn.

Principer börjar gälla endast för ett bestämt program när de är kopplade till ett huvud namn för tjänsten.

Endast en HRD-princip kan vara aktiv i ett tjänst huvud namn vid en och samma tidpunkt.  

Du kan använda Azure Active Directory PowerShell-cmdletar för att skapa och hantera HRD-principer.

Följande är ett exempel på en HRD princip definition:

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

Princip typen är "[HomeRealmDiscoveryPolicy](/graph/api/resources/homeRealmDiscoveryPolicy)".

**AccelerateToFederatedDomain** är valfritt. Om **AccelerateToFederatedDomain** är false har principen ingen påverkan på automatisk acceleration. Om **AccelerateToFederatedDomain** är true och det bara finns en verifierad och federerad domän i klienten, kommer användarna att tas direkt till den federerade IDP för att logga in. Om det är sant och det finns fler än en verifierad domän i klient organisationen måste **PreferredDomain** anges.

**PreferredDomain** är valfritt. **PreferredDomain** bör ange en domän som ska påskyndas. Den kan utelämnas om klienten bara har en federerad domän.  Om den utelämnas och det finns fler än en verifierad federerad domän, har principen ingen påverkan.

 Om **PreferredDomain** anges måste den matcha en verifierad, federerad domän för klient organisationen. Alla användare av programmet måste kunna logga in på domänen – användare som inte kan logga in på den federerade domänen kommer att svällas och kan inte slutföra inloggningen.

**AllowCloudPasswordValidation** är valfritt. Om **AllowCloudPasswordValidation** är true kan programmet autentisera en federerad användare genom att Visa autentiseringsuppgifter för användar namn/lösen ord direkt till Azure Active Directory token-slutpunkt. Detta fungerar bara om du har aktiverat hash-synkronisering av lösen ord.

Dessutom finns två HRD-alternativ för klient organisations nivå, som inte visas ovan:

- **AlternateIdLogin** är valfritt.  Om aktive rad kan [användare logga in med sina e-postadresser i stället för deras UPN](../authentication/howto-authentication-use-email-signin.md) på inloggnings sidan för Azure AD.  Alternativa ID är beroende av att användaren inte accelererar automatiskt till en federerad IDP.

- **DomainHintPolicy** är ett valfritt komplext objekt som [ *förhindrar* domän tips från att automatiskt påskynda användare till federerade domäner](prevent-domain-hints-with-home-realm-discovery.md). Den här inställningen för hela klienten används för att säkerställa att program som skickar domän tips inte hindrar användare från att logga in med molnbaserad autentiseringsuppgifter.

### <a name="priority-and-evaluation-of-hrd-policies"></a>Prioritet och utvärdering av HRD-principer

HRD-principer kan skapas och tilldelas till särskilda organisationer och tjänst huvud namn. Det innebär att det är möjligt att flera principer gäller för ett enskilt program, så Azure AD måste bestämma vilken som prioriteras. En regel uppsättning bestämmer vilken HRD-princip (av många som tillämpas) som börjar gälla:

- Om det finns ett domän tips i autentiseringsbegäran, kontrol leras HRD-principen för klienten (principen anges som klient organisationens standardvärde) för att se om [domän tips ska ignoreras](prevent-domain-hints-with-home-realm-discovery.md). Om domän tips tillåts används det beteende som anges av domän tipset.

- Annars, om en princip uttryckligen tilldelas till tjänstens huvud namn, tillämpas.

- Om det inte finns något domän tips, och ingen princip uttryckligen tilldelas till tjänstens huvud namn, tvingas en princip som uttryckligen tilldelas till den överordnade organisationen av tjänstens huvud namn.

- Om det inte finns något domän tips, och ingen princip har tilldelats till tjänstens huvud namn eller organisationen, används standard beteendet för HRD.

## <a name="tutorial-for-setting-hrd-policy-on-an-application"></a>Självstudie för att ställa in HRD-principer på ett program

Vi använder Azure AD PowerShell-cmdlets för att gå igenom några scenarier, bland annat:

- Konfigurera HRD-principen för automatisk acceleration för ett program i en klient organisation med en enda federerad domän.

- Konfigurera HRD-principen för automatisk acceleration för ett program till en av flera domäner som verifieras för din klient.

- Konfigurera HRD-principen för att aktivera ett äldre program för direkt användar namn/lösenordsautentisering till Azure Active Directory för en federerad användare.

- Visar en lista över de program för vilka en princip har kon figurer ATS.

### <a name="prerequisites"></a>Förutsättningar

I följande exempel kan du skapa, uppdatera, länka och ta bort principer för program tjänstens huvud namn i Azure AD.

1. Börja med att hämta den senaste för hands versionen av Azure AD PowerShell-cmdleten.

2. När du har hämtat Azure AD PowerShell-cmdletarna kör du kommandot Connect för att logga in på Azure AD med ditt administratörs konto:

    ``` powershell
    Connect-AzureAD -Confirm
    ```

3. Kör följande kommando för att se alla principer i din organisation:

    ``` powershell
    Get-AzureADPolicy
    ```

Om inget returneras innebär det att du inte har några principer som skapats i din klient organisation.

### <a name="example-set-an-hrd-policy-for-an-application"></a>Exempel: Ange en HRD-princip för ett program

I det här exemplet skapar du en princip som när den tilldelas till ett program antingen:

- Automatiskt påskyndar användare till en AD FS inloggnings skärm när de loggar in på ett program när det finns en enda domän i din klient organisation.
- Automatiskt accelererar användare till en AD FS inloggnings skärm det finns fler än en federerad domän i din klient organisation.
- Möjliggör inloggning med icke-interaktivt användar namn/lösen ord direkt till Azure Active Directory för federerade användare för de program som principen är tilldelad till.

#### <a name="step-1-create-an-hrd-policy"></a>Steg 1: skapa en HRD-princip

Följande princip påskyndar automatiskt användare till en AD FS inloggnings skärm när de loggar in på ett program när det finns en enda domän i din klient organisation.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true}}") -DisplayName BasicAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Följande princip påskyndar automatiskt användare till en AD FS inloggnings skärm där det finns fler än en federerad domän i din klient organisation. Om du har mer än en federerad domän som autentiserar användare av program måste du ange domänen för att påskynda snabb start.

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AccelerateToFederatedDomain`":true, `"PreferredDomain`":`"federated.example.edu`"}}") -DisplayName MultiDomainAutoAccelerationPolicy -Type HomeRealmDiscoveryPolicy
```

Kör följande kommando för att skapa en princip för att aktivera autentisering av användar namn/lösen ord för federerade användare direkt med Azure Active Directory för vissa program:

``` powershell
New-AzureADPolicy -Definition @("{`"HomeRealmDiscoveryPolicy`":{`"AllowCloudPasswordValidation`":true}}") -DisplayName EnableDirectAuthPolicy -Type HomeRealmDiscoveryPolicy
```

Kör följande kommando för att se den nya principen och hämta dess **ObjectID**:

``` powershell
Get-AzureADPolicy
```

Om du vill tillämpa HRD-principen när du har skapat den kan du tilldela den till flera program tjänstens huvud namn.

#### <a name="step-2-locate-the-service-principal-to-which-to-assign-the-policy"></a>Steg 2: hitta det tjänst huvud namn som principen ska tilldelas till

Du behöver **ObjectID** för de tjänst huvud namn som du vill tilldela principen till. Det finns flera sätt att hitta **ObjectID** för tjänstens huvud namn.

Du kan använda portalen, eller så kan du fråga [Microsoft Graph](/graph/api/resources/serviceprincipal?view=graph-rest-beta). Du kan också gå till [Graph Explorer-verktyget](https://developer.microsoft.com/graph/graph-explorer) och logga in på ditt Azure AD-konto för att se alla företagets tjänst huvud namn.

Eftersom du använder PowerShell kan du använda följande cmdlet för att visa en lista över tjänstens huvud namn och deras ID.

``` powershell
Get-AzureADServicePrincipal
```

#### <a name="step-3-assign-the-policy-to-your-service-principal"></a>Steg 3: tilldela principen till tjänstens huvud namn

När du har **ObjectID** för tjänstens huvud namn för det program som du vill konfigurera automatisk acceleration för, kör du följande kommando. Det här kommandot kopplar HRD-principen som du skapade i steg 1 med tjänstens huvud namn som du letade i steg 2.

``` powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
```

Du kan upprepa det här kommandot för varje huvud namn för tjänsten som du vill lägga till principen för.

Om ett program redan har tilldelats en HomeRealmDiscovery-princip kan du inte lägga till ytterligare en.  I så fall kan du ändra definitionen av identifierings principen för start sfär som är tilldelad till programmet för att lägga till ytterligare parametrar.

#### <a name="step-4-check-which-application-service-principals-your-hrd-policy-is-assigned-to"></a>Steg 4: kontrol lera vilka program tjänst objekt som din HRD-princip har tilldelats

Använd cmdleten **Get-AzureADPolicyAppliedObject** för att kontrol lera vilka program som har HRD-principen konfigurerad. Skicka det till **ObjectID** för den princip som du vill checka in.

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

#### <a name="step-5-youre-done"></a>Steg 5: du är klar!

Prova programmet för att kontrol lera att den nya principen fungerar.

### <a name="example-list-the-applications-for-which-hrd-policy-is-configured"></a>Exempel: Visa en lista över de program som HRD-principen har kon figurer ATS för

#### <a name="step-1-list-all-policies-that-were-created-in-your-organization"></a>Steg 1: Visa en lista med alla principer som har skapats i din organisation

``` powershell
Get-AzureADPolicy
```

Observera **ObjectID** för den princip som du vill visa tilldelningar för.

#### <a name="step-2-list-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 2: Visa en lista över de tjänst huvud namn som principen tilldelas till  

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

### <a name="example-remove-an-hrd-policy-from-an-application"></a>Exempel: ta bort en HRD-princip från ett program

#### <a name="step-1-get-the-objectid"></a>Steg 1: Hämta ObjectID

Använd föregående exempel för att hämta **ObjectID** för principen och det som är det program tjänst objekt som du vill ta bort den från.

#### <a name="step-2-remove-the-policy-assignment-from-the-application-service-principal"></a>Steg 2: ta bort princip tilldelningen från program tjänstens huvud namn  

``` powershell
Remove-AzureADServicePrincipalPolicy -id <ObjectId of the Service Principal>  -PolicyId <ObjectId of the policy>
```

#### <a name="step-3-check-removal-by-listing-the-service-principals-to-which-the-policy-is-assigned"></a>Steg 3: kontrol lera borttagningen genom att lista de tjänst huvud namn som principen är tilldelad till

``` powershell
Get-AzureADPolicyAppliedObject -id <ObjectId of the Policy>
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur autentisering fungerar i Azure AD finns i [autentiserings scenarier för Azure AD](../develop/authentication-vs-authorization.md).
- Mer information om enkel inloggning med användare finns i [enkel inloggning till program i Azure Active Directory](what-is-single-sign-on.md).
- Besök [Microsofts identitets plattform](../develop/v2-overview.md) och se om det finns en översikt över allt innehåll i utvecklare.