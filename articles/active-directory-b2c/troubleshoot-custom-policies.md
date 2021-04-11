---
title: Felsöka anpassade principer i Azure Active Directory B2C
description: Lär dig mer om metoder för att lösa fel när du arbetar med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103973"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Felsöka Azure AD B2C anpassade principer

Om du använder Azure Active Directory B2C (Azure AD B2C) [anpassade principer](custom-policy-overview.md)kan det uppstå utmaningar med XML-format eller körnings problem i princip språket. I den här artikeln beskrivs några verktyg och tips som kan hjälpa dig att identifiera och lösa problem. 

Den här artikeln fokuserar på fel sökning av din Azure AD B2C anpassade princip konfiguration. Den tar inte itu med det förlitande parts programmet eller dess identitets bibliotek.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Översikt över Azure AD B2C korrelations-ID

Azure AD B2C korrelations-ID är ett unikt ID-värde som är kopplat till autentiseringsbegäranden. Den går igenom alla steg i dirigeringen som en användare tas genom. Med korrelations-ID: t kan du:

- Identifiera inloggnings aktivitet i ditt program och spåra principens prestanda.
- Hitta inloggnings förfrågans Azure Application Insights-loggar.
- Skicka korrelations-ID: t till din REST API och Använd det för att identifiera inloggnings flödet. 

Korrelations-ID: t ändras varje gång en ny session upprättas. När du felsöker dina principer ser du till att Stäng befintliga webbläsare. Eller öppna en ny webbläsare i privat läge.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Hämta Azure AD B2C korrelations-ID

Du hittar korrelations-ID: t i Azure AD B2C registrerings-eller inloggnings sida. Välj **Visa källa** i webbläsaren. Korrelationen visas som en kommentar överst på sidan.

![Skärm bild av visnings källa för Azure AD B2C inloggnings sidan.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Kopiera korrelations-ID: t och fortsätt sedan inloggnings flödet. Använd korrelations-ID: t för att iaktta inloggnings beteendet. Mer information finns i [fel sökning med Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>ECHO Azure AD B2C korrelations-ID

Du kan inkludera korrelations-ID: t i Azure AD B2C tokens. Så här tar du med korrelations-ID:

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till Orts anspråk i **ClaimsSchema** -elementet.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Öppna den förlitande part filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> fil. Utgående anspråk läggs till i token efter en lyckad användar resa och skickas till programmet. Ändra det tekniska profil elementet i avsnittet förlitande part för att lägga till staden som ett utgående anspråk.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Fel sökning med Application Insights

Använd [Application Insights](troubleshoot-with-application-insights.md)för att diagnosticera problem med dina anpassade principer. Application Insights spårar aktiviteten i din användar resa för anpassad policy. Det är ett sätt att diagnostisera undantag och observera utbytet av anspråk mellan Azure AD B2C och de olika anspråks leverantörerna som definieras av tekniska profiler, till exempel identitets leverantörer, API-baserade tjänster, Azure AD B2C användar katalog och andra tjänster.  

Vi rekommenderar att du installerar [Azure AD B2C tillägget](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) för [vs Code](https://code.visualstudio.com/). Med Azure AD B2C-tillägget ordnas loggarna efter princip namn, korrelations-ID (Application Insights visar den första siffran i korrelations-ID: t) och loggens tidsstämpel. Den här funktionen hjälper dig att hitta relevant logg baserat på den lokala tids stämplingen och se användar resan som utförd av Azure AD B2C. 

> [!NOTE]
> - Det finns en kort fördröjning, vanligt vis mindre än fem minuter, innan du kan se nya loggar i Application Insights.
> - Communityn har utvecklat Visual Studio Code-tillägget för Azure AD B2C för att hjälpa identitets utvecklare. Tillägget stöds inte av Microsoft och görs tillgängligt enbart i befintligt skick.

Ett enkelt inloggnings flöde kan utfärda mer än en Azure Application Insights-spårning. I följande skärm bild har *B2C_1A_signup_signin* principen tre loggar. Varje logg representerar en del av inloggnings flödet.

Följande skärm bild visar Azure AD B2C tillägget för VS Code med Azure Application Insights trace Explorer.

![Skärm bild av Azure AD B2C tillägg för VS Code med Azure Application Insights-spårning.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrera spårnings loggen

Med fokus i Azure AD B2C spårnings Utforskaren börjar du skriva den första siffran i korrelations-ID: t eller en tid som du vill hitta. En filter ruta visas längst upp till höger i Azure AD B2C spårnings Utforskaren som visar vad du har skrivit hittills och matchande spårnings loggar markeras.  

![Skärm bild av Azure AD B2C tillägg Azure AD B2C spårnings Utforskaren filter markering.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Om du hovrar över filter rutan och väljer **aktivera filter för typ** visas endast matchande spårnings loggar. Använd **Rensa-knappen "X"** för att ta bort filtret.

![Skärm bild av Azure AD B2C tillägg Azure AD B2C spårnings Utforskaren filter.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Application Insights spårnings logg information

När du väljer en Azure Application Insights-spårning, öppnar tillägget fönstret **Application Insights information** med följande information:

- **Application Insights** – allmän information om spårnings loggen, inklusive princip namnet, KORRELATIONS-id, Azure Application Insights SPÅRNINGS-ID och tidsstämpel för spårning.
- **Tekniska profiler** – lista över tekniska profiler som visas i spårnings loggen.
- **Anspråk** – alfabetisk lista över anspråk som visas i spårnings loggen och deras värden. Om ett anspråk visas i spårnings loggen flera gånger med olika värden, `=>` anger ett tecken det nyaste värdet. Du kan granska dessa anspråk för att avgöra om förväntade anspråks värden är korrekt inställda. Om du till exempel har ett villkor som kontrollerar ett anspråks värde kan avsnittet anspråk hjälpa dig att avgöra varför ett förväntat flöde beter sig annorlunda.
- **Anspråks omvandling** – lista över anspråk omvandlingar som visas i spårnings loggen. Varje anspråks omvandling innehåller indata-anspråk, indataparametrar och utgående anspråk. I avsnittet anspråk omvandling får du inblick i de data som skickas och resultatet av anspråks omvandlingen.
- **Tokens** – lista över tokens som visas i spårnings loggen. Token inkluderar de underliggande token för den underliggande federerade OAuth-och OpenId Connect-providern. Den federerade identitets leverantörens token ger information om hur identitets leverantören returnerar anspråk till Azure AD B2C så att du kan mappa identitets leverantörens tekniska profil utgående anspråk. 
- **Undantag** – lista över undantag eller allvarliga fel som visas i spårnings loggen.
- **Application Insights JSON** – rå data som returneras från Application Insights.

Följande skärm bild visar ett exempel på fönstret Application Insights spårnings logg information.  

![Skärm bild av rapporten Azure AD B2C tillägg Azure AD B2C spårning.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Felsöka JWT-token

För validering av JWT-token och fel sökning kan du avkoda JWTs med en-plats som [https://jwt.ms](https://jwt.ms) . Skapa ett testprogram som kan omdirigeras till `https://jwt.ms` för kontroll av token. Om du inte redan gjort det, [registrerar du ett webb program](tutorial-register-applications.md)och [aktiverar implicit beviljande av ID-token](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Skärm bild av för hands versionen av JWT-token.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Använd **Kör nu** och `https://jwt.ms` för att testa dina principer oberoende av ditt webb-eller mobil program. Den här webbplatsen fungerar som ett förlitande parts program. Den visar innehållet i JSON-webbtoken (JWT) som genereras av din Azure AD B2Cs princip.

## <a name="troubleshoot-saml-protocol"></a>Felsöka SAML-protokoll

För att hjälpa till att konfigurera och felsöka integrationen med din tjänst leverantör kan du använda ett webb läsar tillägg för SAML-protokollet, till exempel [SAML DevTools-tillägg](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) för Chrome, [SAML-spårningspil](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) för Firefox eller [Edge eller IE Developer Tools](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Följande skärm bild visar hur SAML DevTools-tillägget visar SAML-begäran Azure AD B2C skickar till identitets leverantören och SAML-svaret.

![Skärm bild av spårnings logg för SAML-protokoll.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Med dessa verktyg kan du kontrol lera integrationen mellan ditt program och Azure AD B2C. Exempel:

- Kontrol lera om SAML-begäran innehåller en signatur och avgör vilken algoritm som används för att logga in auktoriseringsbegäran.
- Kontrol lera om Azure AD B2C returnerar ett fel meddelande.
- Kontrol lera om kontroll avsnittet är krypterat.
- Hämta namnet på anspråken returnera identitets leverantören.

Du kan också spåra utbytet av meddelanden mellan klientens webbläsare och Azure AD B2C, med [Fiddler](https://www.telerik.com/fiddler). Det kan hjälpa dig att få en indikation på var din användar resa inte fungerar i ditt Dirigerings steg.

## <a name="troubleshoot-policy-validity"></a>Felsöka giltighet av principer

När du är klar med att utveckla principen laddar du upp principen till Azure AD B2C. Det kan finnas problem med principen. Använd följande metoder för att säkerställa att principen är integritet/giltig.

Det vanligaste felet vid inställning av anpassade principer är felaktigt formaterat XML. En bra XML-redigerare är nästan viktig. Den visar XML-kod i ursprungligt format, färg kods innehåll, för att fylla i vanliga termer, håller XML-elementen indexerade och kan val IDE ras mot ett XML-schema.

Vi rekommenderar att du använder [Visual Studio Code](https://code.visualstudio.com/). Installera sedan ett XML-tillägg, till exempel [XML-språkstöd för Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). XML-tillägget gör det möjligt att verifiera XML-schemat innan du överför XML-filen med hjälp av en anpassad princip för [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) -schema.

Du kan använda strategin för XML-filassociationen för att binda XML-filen till XSD genom att lägga till följande inställningar i VS-koden `settings.json` . Så här gör du:

1. Från VS Code klickar du på **inställningarna**. Mer information finns i [Inställningar för användare och arbets ytor](https://code.visualstudio.com/docs/getstarted/settings).
1. Sök efter **fileAssociations** och välj sedan **XML** under **tillägget**.
1. Välj **Redigera i settings.jspå**.

    ![Skärm bild av validering av XML-schema för VS-kod.](./media/troubleshoot-custom-policies/xml-validation.png)
1. Lägg till följande JSON-kod i settings.jspå:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

I följande exempel visas ett XML-validerings fel. När du flyttar musen över element namnet visar tillägget de förväntade elementen.

![Skärm bild av data validerings fel indikator för VS Code XML-schema.](./media/troubleshoot-custom-policies/xml-validation-error.png)

I följande fall `DisplayName` är elementet korrekt. Men i fel ordning. `DisplayName`Ska vara före `Protocol` elementet. Åtgärda problemet genom att flytta musen över `DisplayName` elementet till rätt ordning för elementen.

![Skärm bild av kod fel för XML-schema i VS Code.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Ladda upp principer och princip validering

Valideringen av XML-principagenten utförs automatiskt vid uppladdning. De flesta fel gör att överföringen inte fungerar. Verifiering innehåller princip filen som du överför. Den innehåller också fil kedjan som uppladdnings filen refererar till (den förlitande partens princip fil, tilläggs filen och bas filen).

> [!TIP]
> Azure AD B2C kör ytterligare verifiering för principen för förlitande part. När du har problem med principen, även om du bara redigerar tilläggs principen, är det en bra idé att ladda upp den förlitande part principen. 

Det här avsnittet innehåller vanliga verifierings fel och sannolika lösningar.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Schema validerings fel hittades... har ett ogiltigt underordnat element {name}

Din princip innehåller ett ogiltigt XML-element, eller så är XML-elementet giltigt, men verkar vara i fel ordning. Om du vill åtgärda den här typen av fel kan du läsa avsnittet [Felsök princip giltighet](#troubleshoot-policy-validity) .

### <a name="there-is-a-duplicate-key-sequence-number"></a>Det finns en dubblett av nyckel serien {Number} 

En användar [resa](userjourneys.md) eller [under resa](subjourneys.md) består av en ordnad lista över Orchestration-steg som körs i följd. När du har ändrat resan, numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.

> [!TIP]
> Du kan använda kommandot [Azure AD B2C tillägg](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) för [vs Code](https://code.visualstudio.com/) `(Shift+Ctrl+r)` för att numrera om alla användar resor och stegen för att dirigera om under resan i principen.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... förväntades ha ett steg med ordningen {Number}, men det gick inte att hitta...

Kontrol lera föregående fel.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Ordning för Orchestration-steg {Number} i användar resan {Name}... följs av ett urvals steg för anspråksprovidern och måste vara ett anspråk utbyte, men är av typen...

Steg-för-steg-typ för `ClaimsProviderSelection` , och `CombinedSignInAndSignUp` innehåller en lista med alternativ som en användare kan välja mellan. Det måste följas av en typ av `ClaimsExchange` ett eller flera anspråk utbyte.

Följande Orchestration-steg orsakar den här typen eller felet. Det andra Orchestration-steget måste vara av typen `ClaimsExchange` , inte `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... steg {Number} med 2 anspråk utbyten. Det måste föregås av ett val för anspråks leverantör för att avgöra vilka anspråk som Exchange kan användas

En typ av Orchestration-steg för `ClaimsExchange` måste ha en enda `ClaimsExchange` , om inte föregående steg är av typen `ClaimsProviderSelection` eller `CombinedSignInAndSignUp` . Följande Orchestration-steg orsakar den här typen av fel. Det sjätte steget innehåller två anspråk utbyte. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Använd två Dirigerings steg för att åtgärda den här typen av fel. Varje Dirigerings steg med ett anspråk utbyte.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Det finns en dubblett av tangentsekvensen {Name}

En resa har flera `ClaimsExchange` med samma `Id` . Följande steg orsakar den här typen av fel. ID- *AADUserWrite* visas två gånger i användar resan.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Om du vill åtgärda den här typen av fel ändrar du det åttonde Orchestration-steget "anspråk Exchange till ett unikt namn, till exempel *Call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... refererar till ClaimType med ID "{Claim Name}", men varken principen eller någon av dess bas principer innehåller ett sådant element

Den här typen av fel inträffar när principen gör en referens till ett anspråk som inte har deklarerats i [anspråks schemat](claimsschema.md). Anspråk måste definieras i minst en av filerna i principen. 

Till exempel en teknisk profil med *schoolId* utgående anspråk. Men utgående anspråks *schoolId* deklareras aldrig i principen eller i en överordnad princip.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Du kan åtgärda den här typen av fel genom att kontrol lera om `ClaimTypeReferenceId` värdet är felstavat eller inte finns i schemat. Om anspråket definieras i tilläggs principen, men det används även i bas principen. Kontrol lera att anspråket har definierats i den princip som används, eller i en princip på den högsta nivån.

Om du lägger till anspråket i anspråks schemat löses den här typen av fel.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... skapar en referens till en ClaimsTransformation med ID...

Orsaken till felet liknar den som gäller för anspråks felet. Kontrol lera föregående fel.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>Användaren är för närvarande inloggad som användare av yourtenant.onmicrosoft.com-klienten...

Du loggar in med ett konto från en klient som skiljer sig från den princip som du försöker ladda upp. Du kan till exempel Logga in med admin@contoso.onmicrosoft.com , medan principen `TenantId` är inställd på `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Kontrol lera att `TenantId` värdet i `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementen och stämmer överens med din mål Azure AD B2C klient.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Anspråks typen {Name} är utgående anspråk för den förlitande partens tekniska profil, men det är inte ett utgående anspråk i någon av stegen för användar resa...

I en princip för förlitande part lade du till ett utgående anspråk, men utgående anspråk är inte ett utgående anspråk i någon av stegen i användar resan. Azure AD B2C kan inte läsa anspråks värdet från anspråks säcken.

I följande exempel är *schoolId* -anspråket ett utgående anspråk för den förlitande partens tekniska profil, men det är inte ett utgående anspråk i någon av stegen för *SignUpOrSignIn* användar resa.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Om du vill åtgärda den här typen av fel, se till att de utgående anspråken visas i minst ett Dirigerings stegs samling med teknisk profil utgående anspråk. Ange ett standardvärde, till exempel tom sträng, i den förlitande partens tekniska profil om din användar resa inte kan mata ut anspråket.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Indatasträngen hade ett felaktigt format

Du ställer in fel värdetyp till ett anspråk från en annan typ. Du kan till exempel definiera ett heltals anspråk.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Sedan försöker du ange ett sträng värde:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

För att åtgärda den här typen av fel, se till att du anger rätt värde, till exempel `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Klienten {Name} har redan en princip med ID {Name}. Det går inte att lagra en annan princip med samma ID

Du försöker överföra en princip till klienten, men en princip med samma namn har redan överförts till din klient. 

Om du vill åtgärda den här typen av fel, klickar du på kryss rutan **Skriv över den anpassade principen om den redan finns** .

![Skärm bild som visar hur du skriver över den anpassade principen om den redan finns.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [samlar in Azure Active Directory B2C loggar med Application Insights](troubleshoot-with-application-insights.md).

