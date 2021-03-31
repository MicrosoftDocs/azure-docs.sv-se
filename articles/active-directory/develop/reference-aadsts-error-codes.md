---
title: Fel koder för Azure AD-autentisering &
description: Lär dig mer om de AADSTS-felkoder som returneras från Azure AD Security Token Service (STS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: reference
ms.date: 03/17/2021
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 56ffa8c33c409e274318407a70f33edaabb5073d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578506"
---
# <a name="azure-ad-authentication-and-authorization-error-codes"></a>Felkoder för Azure AD-autentisering och -auktorisering

Letar du efter information om de AADSTS-felkoder som returneras från Azure Active Directory (Azure AD) Security Token Service (STS)? Läs det här dokumentet för att hitta AADSTS fel beskrivningar, korrigeringar och vissa föreslagna lösningar.

> [!NOTE]
> Den här informationen är preliminär och kan komma att ändras. Har du en fråga eller kan du inte hitta det du söker? Skapa ett GitHub-ärende eller se [support och hjälp alternativ för utvecklare](./developer-support-help-options.md) och lär dig mer om andra sätt att få hjälp och support.
>
> Den här dokumentationen tillhandahålls för vägledning för utvecklare och administratörer, men ska aldrig användas av själva klienten. Felkoder kan ändras när som helst för att ge mer detaljerade fel meddelanden som är avsedda att hjälpa utvecklaren när programmet skapas. Appar som tar ett beroende av text-eller fel kods nummer bryts över tid.

## <a name="handling-error-codes-in-your-application"></a>Hantera felkoder i ditt program

[OAuth 2.0-specifikationen](https://tools.ietf.org/html/rfc6749#section-5.2) ger vägledning om hur du hanterar fel under autentisering med hjälp av `error` delen av fel svaret. 

Här är ett exempel på fel svar:

```json
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://example.contoso.com/activity.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7", 
  "error_uri":"https://login.microsoftonline.com/error?code=70011"
}
```

| Parameter         | Beskrivning    |
|-------------------|----------------|
| `error`       | En fel kods sträng som kan användas för att klassificera typer av fel som inträffar och som ska användas för att reagera på fel. |
| `error_description` | Ett fel meddelande som kan hjälpa en utvecklare att identifiera rotor saken vid ett autentiseringsfel. Använd aldrig det här fältet för att reagera på ett fel i koden. |
| `error_codes` | En lista med STS-specificerade felkoder som kan hjälpa dig i diagnostik.  |
| `timestamp`   | Tiden då felet inträffade. |
| `trace_id`    | En unik identifierare för begäran som kan hjälpa i diagnostik. |
| `correlation_id` | En unik identifierare för begäran som kan hjälpa dig i diagnostiken mellan komponenter. |
| `error_uri` |  En länk till fel söknings sidan med ytterligare information om felet.  Detta är endast för utvecklarens användning, visar den inte för användare.  Endast om fel söknings systemet innehåller ytterligare information om felet – alla fel har angett ytterligare information.|

`error`Fältet har flera möjliga värden – granska protokoll dokumentations länkarna och OAuth 2,0-specifikationerna för att lära dig mer om vissa fel (till exempel `authorization_pending` i [enhets kod flödet](v2-oauth2-device-code.md)) och hur du kan reagera på dem.  Några vanliga visas här:

| Felkod         | Beskrivning        | Klient åtgärd    |
|--------------------|--------------------|------------------|
| `invalid_request`  | Protokoll fel, till exempel en obligatorisk parameter som saknas. | Åtgärda och skicka begäran på nytt.|
| `invalid_grant`    | En del av autentiserings materialet (auth Code, Refresh token, Access token, PKCE Challenge) var ogiltigt, kunde inte parsas, saknas eller på annat sätt oanvändbar | Testa en ny begäran till `/authorize` slut punkten för att få en ny auktoriseringskod.  Överväg att granska och verifiera appens användning av protokollen. |
| `unauthorized_client` | Den autentiserade klienten har inte behörighet att använda den här typen av auktoriserings beviljande. | Detta inträffar vanligt vis när klient programmet inte är registrerat i Azure AD eller inte har lagts till i användarens Azure AD-klient. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD. |
| `invalid_client` | Klientautentisering misslyckades.  | Klientens autentiseringsuppgifter är inte giltiga. För att åtgärda detta uppdaterar program administratören autentiseringsuppgifterna.   |
| `unsupported_grant_type` | Auktoriseringsservern stöder inte typen för auktoriserings beviljande. | Ändra beviljad typ i begäran. Den här typen av fel bör bara inträffa under utvecklingen och identifieras under den första testningen. |
| `invalid_resource` | Mål resursen är ogiltig eftersom den inte finns, det går inte att hitta den i Azure AD, eller så är den inte korrekt konfigurerad. | Detta anger att resursen, om den finns, inte har kon figurer ATS i klient organisationen. Programmet kan uppmana användaren att ange instruktioner för att installera programmet och lägga till det i Azure AD.  Under utvecklingen indikerar detta vanligt vis en felaktigt konfigurerad test klient eller ett skrivfel i namnet på det begärda omfånget. |
| `interaction_required` | Begäran kräver användar interaktion. Till exempel krävs ytterligare ett autentiserings steg. | Gör om begäran med samma resurs, interaktivt, så att användaren kan slutföra eventuella utmaningar som krävs.  |
| `temporarily_unavailable` | Servern är tillfälligt upptagen och kan inte hantera begäran. | Gör om begäran. Klient programmet kan förklara för användaren att dess svar är fördröjt på grund av ett tillfälligt tillstånd. |

## <a name="lookup-current-error-code-information"></a>Sök efter aktuell fel kod information
Felkoder och meddelanden kan komma att ändras.  För den senaste informationen tar du en titt på [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) sidan för att hitta AADSTS fel beskrivningar, korrigeringar och vissa föreslagna lösningar.  

Om du till exempel har fått felkoden "AADSTS50058" gör du en sökning i [https://login.microsoftonline.com/error](https://login.microsoftonline.com/error) för "50058".  Du kan också länka direkt till ett viss fel genom att lägga till fel kod numret till URL: en: [https://login.microsoftonline.com/error?code=50058](https://login.microsoftonline.com/error?code=50058) .

## <a name="aadsts-error-codes"></a>Felkoder för AADSTS

| Fel | Beskrivning |
|---|---|
| AADSTS16000 | SelectUserAccount – detta är ett avbrott som orsakas av Azure AD, vilket resulterar i ett användar gränssnitt som gör att användaren kan välja mellan flera giltiga SSO-sessioner. Det här felet är ganska vanligt och kan returneras till programmet om `prompt=none` har angetts. |
| AADSTS16001 | UserAccountSelectionInvalid – du ser det här felet om användaren klickar på en panel att sessionen väljer logik har avvisats. När det här felet utlöses kan användaren återställa genom att välja från en uppdaterad lista över paneler/sessioner, eller genom att välja ett annat konto. Det här felet kan inträffa på grund av ett kod fel eller ett konkurrens villkor. |
| AADSTS16002 | AppSessionSelectionInvalid-det angivna SID-kravet för appen har inte uppfyllts.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant – anger att användaren inte uttryckligen har lagts till i klienten. |
| AADSTS17003 | CredentialKeyProvisioningFailed – Azure AD kan inte etablera användar nyckeln. |
| AADSTS20001 | WsFedSignInResponseError – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS20012 | WsFedMessageInvalid – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS20033 | FedMetadataInvalidTenantName – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS40008 | OAuth2IdPUnretryableServerError – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS40010 | OAuth2IdPRetryableServerError – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError – det är problem med din federerade identitets leverantör. Kontakta din IDP för att lösa problemet. |
| AADSTS50000 | TokenIssuanceError – det finns ett problem med inloggnings tjänsten. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att lösa problemet. |
| AADSTS50001 | InvalidResource-resursen är inaktive rad eller finns inte. Kontrol lera appens kod för att se till att du har angett den exakta resurs-URL: en för resursen som du försöker få åtkomst till.  |
| AADSTS50002 | NotAllowedTenant-inloggningen misslyckades på grund av en begränsad proxy-åtkomst på klienten. Om det är din egen klientprincip kan du åtgärda problemet genom att ändra inställningarna för den begränsade klientorganisationen. |
| AADSTS500021 | Åtkomst till klienten ' {Tenant} ' nekas. AADSTS500021 anger att funktionen för klient begränsning har kon figurer ATS och att användaren försöker få åtkomst till en klient som inte finns i listan över tillåtna innehavare som anges i huvudet `Restrict-Access-To-Tenant` . Mer information finns i [använda klient begränsningar för att hantera åtkomst till SaaS Cloud-program](../manage-apps/tenant-restrictions.md).|
| AADSTS50003 | MissingSigningKey-inloggningen misslyckades på grund av en saknad signerings nyckel eller certifikat. Det kan bero på att ingen signerings nyckel har kon figurer ATS i appen. Mer information finns i fel söknings artikeln om fel [AADSTS50003](/troubleshoot/azure/active-directory/error-code-aadsts50003-cert-or-key-not-configured). Kontakta appens ägare eller en app-administratör om problemen fortfarande visas. |
| AADSTS50005 | DevicePolicyError – användaren försökte logga in på en enhet från en plattform som för närvarande inte stöds via principen för villkorlig åtkomst. |
| AADSTS50006 | InvalidSignature-signaturverifieringen misslyckades på grund av en ogiltig signatur. |
| AADSTS50007 | PartnerEncryptionCertificateMissing – det gick inte att hitta partner krypterings certifikatet för den här appen. [Öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med Microsoft för att få detta åtgärdat. |
| AADSTS50008 | InvalidSamlToken-SAML-kontroll saknas eller är felkonfigurerat i token. Kontakta den federerade providern. |
| AADSTS50010 | AudienceUriValidationFailed-URI-validering av appen misslyckades eftersom ingen token för token hade kon figurer ATS. |
| AADSTS50011 | InvalidReplyTo-svars adressen saknas, är felkonfigurerad eller matchar inte svars adresser som har kon figurer ATS för appen.  Som lösning ser du till att lägga till den saknade svars adressen i Azure Active Directory programmet eller låta någon med behörighet att hantera ditt program i Active Directory göra detta åt dig. Mer information finns i fel söknings artikeln om fel [AADSTS50011](/troubleshoot/azure/active-directory/error-code-aadsts50011-reply-url-mismatch).|
| AADSTS50012 | AuthenticationFailed-Autentiseringen misslyckades av någon av följande orsaker:<ul><li>Ämnes namnet för signerings certifikatet är inte auktoriserat</li><li>Det gick inte att hitta en matchande betrodd auktoritets princip för det godkända ämnes namnet</li><li>Certifikat kedjan är ogiltig</li><li>Signerings certifikatet är inte giltigt</li><li>Principen har inte kon figurer ATS för klienten</li><li>Signerings certifikatets tumavtryck har inte behörighet</li><li>Klientens kontroll innehåller en ogiltig signatur</li></ul> |
| AADSTS50013 | InvalidAssertion-Assertion är ogiltig på grund av olika orsaker – token Issuer inte matchar API-versionen inom sitt giltiga tidsintervall-upphört att gälla-en felaktig-uppdateringstoken i kontrollen är inte en primär uppdateringstoken. |
| AADSTS50014 | GuestUserInPendingState – användarens inlösen är i ett väntande tillstånd. Gäst användar kontot har inte skapats helt ännu. |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState – användaren måste ha en juridisk ålders grupps medgivande. |
| AADSTS50017 | CertificateValidationFailed-certifierings verifieringen misslyckades, orsaker av följande orsaker:<ul><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Det gick inte att hitta förväntat CrlSegment</li><li>Det går inte att hitta utfärdarcertifikatet i listan med betrodda certifikat</li><li>Delta CRL-distributionspunkten har konfigurerats utan motsvarande CRL-distributionspunkt</li><li>Det gick inte att hämta giltiga CRL-segment på grund av ett timeout-problem</li><li>Det gick inte att ladda ned CRL</li></ul>Kontakta administratören för klientorganisationen. |
| AADSTS50020 | UserUnauthorized – användare har inte behörighet att anropa den här slut punkten. |
| AADSTS50027 | InvalidJwtToken-ogiltig JWT-token på grund av följande orsaker:<ul><li>innehåller inte temporärt nonce-anspråk, sub-anspråk</li><li>felaktig matchning av ämnesidentifierare</li><li>duplicerade anspråk i idToken-anspråk</li><li>oväntad utfärdare</li><li>oväntad målgrupp</li><li>inte inom giltigt tidsintervall </li><li>fel tokenformat</li><li>Signaturverifieringen för externa ID-token från utfärdare misslyckades.</li></ul> |
| AADSTS50029 | Ogiltig URI – domännamnet innehåller ogiltiga tecken. Kontakta administratören för klientorganisationen. |
| AADSTS50032 | WeakRsaKey – anger att den felaktiga användaren försöker använda en svag RSA-nyckel. |
| AADSTS50033 | RetryableError-anger ett tillfälligt fel som inte är relaterat till databas åtgärderna. |
| AADSTS50034 | UserAccountNotFound-om du vill logga in på det här programmet måste kontot läggas till i katalogen. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt-det salt som krävs för att generera en bibetjänings identifierare saknas i principen. Kontakta administratören för klientorganisationen. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer-subject matchar inte Issuer-anspråk i klientens kontroll. Kontakta administratören för klientorganisationen. |
| AADSTS50049 | NoSuchInstanceForDiscovery – okänd eller ogiltig instans. |
| AADSTS50050 | MalformedDiscoveryRequest-begäran är felaktig. |
| AADSTS50053 | IdsLocked – kontot är låst eftersom användaren försökte logga in för många gånger med ett felaktigt användar-ID eller lösen ord. |
| AADSTS50055 | InvalidPasswordExpiredPassword – lösen ordet har upphört att gälla. |
| AADSTS50056 | Ogiltigt eller null lösen ord – lösen ordet finns inte i arkivet för den här användaren. |
| AADSTS50057 | UserDisabled – användar kontot är inaktiverat. Kontot har inaktiverats av en administratör. |
| AADSTS50058 | UserInformationNotProvided – det innebär att en användare inte är inloggad. Detta är ett vanligt fel som förväntas när en användare är oautentiserad och ännu inte har loggat in.</br>Om det här felet uppmuntras i en SSO-kontext där användaren tidigare har loggat in, innebär det att SSO-sessionen inte hittades eller är ogiltig.</br>Det här felet kan returneras till programmet om prompt = none har angetts. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided-klient identifierings information hittades inte i begäran eller underförstådda av de angivna autentiseringsuppgifterna. Användaren kan kontakta klient organisationens administratör för att hjälpa till att lösa problemet. |
| AADSTS50061 | SignoutInvalidRequest-begäran om utloggning är ogiltig. |
| AADSTS50064 | CredentialAuthenticationError-verifiering av autentiseringsuppgifter för användar namn eller lösen ord misslyckades. |
| AADSTS50068 | SignoutInitiatorNotParticipant-det gick inte att logga ut. Den app som initierade utloggning är inte en deltagare i den aktuella sessionen. |
| AADSTS50070 | SignoutUnknownSessionIdentifier-det gick inte att logga ut. En inloggningsbegäran angav en namn identifierare som inte matchar de befintliga sessionerna. |
| AADSTS50071 | SignoutMessageExpired – utloggnings förfrågan har upphört att gälla. |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt – användaren måste registrera sig för den andra Factor Authentication (interaktiv). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt – stark autentisering krävs och användaren godkände inte MFA-utmaningen. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired – på grund av en konfigurations ändring som gjorts av administratören eller eftersom du har flyttat till en ny plats måste användaren använda Multi-Factor Authentication för att få åtkomst till resursen. Försök igen med en ny auktoriserad begäran för resursen. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired – på grund av en konfigurations ändring som gjorts av administratören, eller om användaren har flyttat till en ny plats, krävs Multi-Factor Authentication för att använda Multi-Factor Authentication. |
| AADSTS50085 | Det krävs social IDP-inloggning för att token ska kunna uppdateras. Låt användaren försöka logga in igen med användarnamn och lösenord |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError – tjänsten är inte tillgänglig för tillfället. Försök igen. |
| AADSTS50089 | Flödestoken har upphört att gälla – autentiseringen misslyckades. Låt användaren försöka logga in igen med username-Password. |
| AADSTS50097 | DeviceAuthenticationRequired-enhets autentisering krävs. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized-JWT-signaturen är ogiltig. |
| AADSTS50105 | EntitlementGrantsNotFound – den inloggade användaren har inte tilldelats någon roll för den inloggade appen. Tilldela användaren till appen. Mer information finns i fel söknings artikeln om fel [AADSTS50105](/troubleshoot/azure/active-directory/error-code-aadsts50105-user-not-assigned-role). |
| AADSTS50107 | InvalidRealmUri-det begärda Federations sfär objektet finns inte. Kontakta administratören för klientorganisationen. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat – problem med JWT-huvudet. Kontakta administratören för klientorganisationen. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter-anspråks omvandlingen innehåller ogiltig indataparameter. Kontakta administratören för klientorganisationen för att uppdatera principen. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt-inloggningen avbröts på grund av en lösen ords återställning eller post för lösen ords registrering. |
| AADSTS50126 | InvalidUserNameOrPassword – fel vid verifiering av autentiseringsuppgifter på grund av ogiltigt användar namn eller lösen ord. |
| AADSTS50127 | BrokerAppNotInstalled – användaren måste installera en Broker-app för att få åtkomst till det här innehållet. |
| AADSTS50128 | Ogiltigt domän namn – ingen klient identifierings information hittades i begäran eller underförstådd av angivna autentiseringsuppgifter. |
| AADSTS50129 | DeviceIsNotWorkplaceJoined-Workplace-anslutning krävs för att registrera enheten. |
| AADSTS50131 | ConditionalAccessFailed – indikerar olika fel för villkorlig åtkomst, till exempel dåligt Windows-enhets tillstånd, begäran blockeras på grund av misstänkt aktivitet, åtkomst princip eller säkerhets princip beslut. |
| AADSTS50132 | SsoArtifactInvalidOrExpired-sessionen är inte giltig på grund av lösen ordets giltighets tid eller senaste lösen ords ändring. |
| AADSTS50133 | SsoArtifactRevoked-sessionen är inte giltig på grund av lösen ordets giltighets tid eller senaste lösen ords ändring. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter – fel Data Center. För att auktorisera en begäran som initierades av en app i enhets flödet OAuth 2,0 måste den auktoriserade parten finnas i samma data Center där den ursprungliga begäran finns. |
| AADSTS50135 | PasswordChangeCompromisedPassword-lösen ords ändring krävs på grund av konto risk. |
| AADSTS50136 | RedirectMsaSessionToApp – en MSA-session har identifierats. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken-sessionen är ogiltig på grund av att en extern uppdateringstoken saknas. |
| AADSTS50140 | KmsiInterrupt – det här felet inträffade på grund av "Håll mig inloggad" Avbryt när användaren loggade in. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
| AADSTS50143 | Felaktig matchning av session – sessionen är ogiltig eftersom användarens klientorganisation inte matchar domäntipset på grund av en annan resurs. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) med korrelations-ID, ID för begäran och felkod för mer information. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword-användarens Active Directory lösen ord har upphört att gälla. Skapa ett nytt lösen ord för användaren eller låt användaren använda verktyget för självbetjänings återställning för att återställa lösen ordet. |
| AADSTS50146 | MissingCustomSigningKey – den här appen måste konfigureras med en app-Specific Sign Key. Det har antingen inte konfigurerats med en sådan eller så har nyckeln upphört att gälla/är inte giltig än. |
| AADSTS50147 | MissingCodeChallenge-kodnings parameterns storlek är ogiltig. |
| AADSTS50155 | DeviceAuthenticationFailed-enhetsautentisering misslyckades för den här användaren. |
| AADSTS50158 | ExternalSecurityChallenge-extern säkerhets utmaning uppfylldes inte. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration-anspråk som skickats av extern provider är inte tillräckligt eller saknar anspråk begärd till extern provider. |
| AADSTS50166 | ExternalClaimsProviderThrottled – det gick inte att skicka begäran till anspråks leverantören. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired – klienten kan hämta en SSO-token via tillägg för Windows 10-konton, men det gick inte att hitta token i begäran eller så har den angivna token upphört att gälla. |
| AADSTS50169 | InvalidRequestBadRealm-sfären är inte en konfigurerad sfär för den aktuella tjänstens namnrymd. |
| AADSTS50170 | MissingExternalClaimsProviderMapping-mappningen av externa kontroller saknas. |
| AADSTS50173 | FreshTokenNeeded – den tillhandahållna tilldelningen har upphört att gälla på grund av att den har återkallats och en ny autentiseringstoken krävs. Antingen en administratör eller en användare återkallade tokens för den här användaren, vilket gör att efterföljande token uppdateras för att fungera och kräver omautentisering. Be användaren att logga in igen. |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers-extern utmaning stöds inte för passthrough-användare. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers-session stöds inte för passthrough-användare. |
| AADSTS50180 | WindowsIntegratedAuthMissing-integrerad Windows-autentisering krävs. Aktivera klientorganisationen för sömlös SSO. |
| AADSTS50187 | DeviceInformationNotProvided-tjänsten kunde inte utföra enhetsautentisering. |
| AADSTS50196 | LoopDetected-en klient slinga har upptäckts. Kontrol lera appens logik för att säkerställa att cachelagring av token är implementerat och att fel villkoren hanteras korrekt.  Appen har gjort för många av samma begäran i en för kort period, vilket indikerar att den är i ett felaktigt tillstånd eller att abusively begär token. |
| AADSTS50197 | ConflictingIdentities – det gick inte att hitta användaren. Försök att logga in igen. |
| AADSTS50199 | CmsiInterrupt – av säkerhets skäl krävs användar bekräftelse för denna begäran.  Eftersom det här är ett "interaction_required"-fel ska klienten utföra interaktiv autentisering.  Detta beror på att en system WebView har använts för att begära en token för ett internt program – användaren måste uppmanas att fråga om detta var faktiskt appen de avsåg att logga in på. För att undvika den här varningen ska omdirigerings-URI: n vara en del av följande säkra lista: <br />http://<br />https://<br />msauth://(endast iOS)<br />msauthv2://(endast iOS)<br />Chrome-extension://(endast Skriv bords Chrome-webbläsare) |
| AADSTS51000 | RequiredFeatureNotEnabled – funktionen är inaktive rad. |
| AADSTS51001 | DomainHintMustbePresent – domän tips måste finnas med lokal säkerhets identifierare eller lokalt UPN. |
| AADSTS51004 | UserAccountNotInDirectory – användar kontot finns inte i katalogen. |
| AADSTS51005 | TemporaryRedirect motsvarar HTTP-status 307, vilket anger att den begärda informationen finns på den URI som anges i plats rubriken. När du får den här statusen följer du plats rubriken som är kopplad till svaret. När den ursprungliga förfrågnings metoden har PUBLICERAts kommer den omdirigerade begäran också att använda POST-metoden. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth-integrerad Windows-autentisering krävs. Användaren loggade in med en sessionstoken som saknar det integrerade Windows-autentiserings-anspråket. Be användaren att logga in igen. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn – användaren har inte gett sitt medgivande för åtkomst till LinkedIn-resurser. |
| AADSTS53000 | DeviceNotCompliant-principen för villkorlig åtkomst kräver en kompatibel enhet och enheten är inte kompatibel. Användaren måste registrera sin enhet med en godkänd MDM-provider som Intune. |
| AADSTS53001 | DeviceNotDomainJoined-principen för villkorlig åtkomst kräver en domänansluten enhet och enheten är inte domänansluten. Låt användaren använda en domänansluten enhet. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp – appen som används är inte en godkänd app för villkorlig åtkomst. Användaren måste använda en av apparna från listan över godkända appar som ska användas för att få åtkomst. |
| AADSTS53003 | BlockedByConditionalAccess – åtkomst har blockerats av principer för villkorlig åtkomst. Åtkomst principen tillåter inte utfärdande av token. |
| AADSTS53004 | ProofUpBlockedDueToRisk-användaren måste slutföra registreringen av Multi-Factor Authentication innan det går att komma åt det här innehållet. Användaren bör registrera sig för multifaktorautentisering. |
| AADSTS53011 | Användaren blockerades på grund av risk på hem klient organisationen. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist – användaren eller administratören har inte samtyckt till att använda programmet med ID X. skicka en interaktiv auktoriseringsbegäran för den här användaren och resursen. |
| AADSTS65004 | UserDeclinedConsent – användaren avböjde ett medgivande till att få åtkomst till appen. Låt användaren logga in igen och ge samtycke till appen|
| AADSTS65005 | MisconfiguredApplication – appens obligatoriska resurs åtkomst lista innehåller inte appar som kan upptäckas av resursen eller klient programmet har begärt åtkomst till resursen, som inte angavs i den nödvändiga resurs åtkomst listan eller diagram tjänsten returnerade en felaktig begäran eller så gick det inte att hitta resursen. Om appen har stöd för SAML kan du ha konfigurerat appen med fel identifierare (entitet). Mer information finns i fel söknings artikeln om fel [AADSTS650056](/troubleshoot/azure/active-directory/error-code-aadsts650056-misconfigured-app). |
| AADSTS650052 | Appen behöver åtkomst till en tjänst `(\"{name}\")` som din organisation `\"{organization}\"` inte har prenumererat på eller aktiverat. Kontakta IT-administratören om du vill granska konfigurationen av dina tjänst prenumerationer. |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant – Autentiseringen misslyckades. Uppdateringstoken är ogiltig. Felet kan bero på följande orsaker:<ul><li>Token binding-huvud är tomt</li><li>Token binding hash matchar inte</li></ul> |
| AADSTS70001 | UnauthorizedClient – programmet är inaktiverat. Mer information finns i fel söknings artikeln om fel [AADSTS70001](/troubleshoot/azure/active-directory/error-code-aadsts70001-app-not-found-in-directory). |
| AADSTS70002 | InvalidClient – det gick inte att verifiera autentiseringsuppgifterna. Den angivna client_secret matchar inte det förväntade värdet för den här klienten. Korrigera client_secret och försök igen. Mer information finns i [använda auktoriseringskod för att begära en åtkomsttoken](v2-oauth2-auth-code-flow.md#request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType – appen returnerade en beviljande typ som inte stöds. |
| AADSTS70004 | InvalidRedirectUri – appen returnerade en ogiltig omdirigerings-URI. Adressen för omdirigering som angetts av klienten matchar inte några konfigurerade adresser eller någon adress på godkännandelistan för OIDC. |
| AADSTS70005 | UnsupportedResponseType – appen returnerade en svars typ som inte stöds på grund av följande orsaker:<ul><li>svars typen token har inte Aktiver ATS för appen</li><li>svarstypen "id_token" kräver OpenID-omfång – innehåller ett OAuth-parametervärde som inte stöds i den kodade wctx</li></ul> |
| AADSTS70007 | UnsupportedResponseMode – appen returnerade ett värde som inte stöds `response_mode` när en token begärdes.  |
| AADSTS70008 | ExpiredOrRevokedGrant-uppdateringstoken har gått ut på grund av inaktivitet. Token utfärdades på XXX och var inaktiv under en viss tid. |
| AADSTS70011 | InvalidScope-definitions området som begärdes av appen är ogiltigt. |
| AADSTS70012 | MsaServerError-ett Server fel uppstod vid autentisering av en MSA-användare (Consumer). Försök igen. Om det fortfarande misslyckas [skapar du ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) |
| AADSTS70016 | AuthorizationPending – fel i OAuth 2,0-enhets flöde. Auktorisering väntar. Enheten försöker att söka efter begäran igen. |
| AADSTS70018 | BadVerificationCode-ogiltig verifierings kod på grund av att användaren skriver in fel användar kod för enhets kod flödet. Auktorisering är inte godkänd. |
| AADSTS70019 | CodeExpired-verifierings koden har gått ut. Låt användaren försöka logga in igen. |
| AADSTS75001 | BindingSerializationError-ett fel uppstod under SAML-meddelande bindningen. |
| AADSTS75003 | UnsupportedBindingError – appen returnerade ett fel som är relaterat till en bindning som inte stöds (SAML-protokollets svar kan inte skickas via andra bindningar än HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid – Azure AD har inte stöd för SAML-begäran som skickats av appen för SSO. Mer information finns i fel söknings artikeln om fel [AADSTS75005](/troubleshoot/azure/active-directory/error-code-aadsts75005-not-a-valid-saml-request). |
| AADSTS7500514 | Det gick inte att hitta en typ av SAML-svar som stöds. Svars typerna som stöds är "svar" (i XML-namnområdet urn: Oasis: Names: TC: SAML: 2.0: Protocol) eller "Assertion" (i XML-namnområdet urn: Oasis: Names: TC: SAML: 2.0: Assertion). Program fel-utvecklaren kommer att hantera det här felet.|
| AADSTS750054 | SAMLRequest eller SAMLResponse måste finnas som frågeparametrar i HTTP-begäran för SAML-omdirigerings bindning. Mer information finns i fel söknings artikeln om fel [AADSTS750054](/troubleshoot/azure/active-directory/error-code-aadsts750054-saml-request-not-present). |
| AADSTS75008 | RequestDeniedError-begäran från appen nekades eftersom SAML-begäran hade ett oväntat mål. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims – autentiseringsmetoden som användaren autentiseras med tjänsten inte matchar den begärda autentiseringsmetoden. Mer information finns i fel söknings artikeln om fel [AADSTS75011](/troubleshoot/azure/active-directory/error-code-aadsts75011-auth-method-mismatch). |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy-SAML2-autentiseringsbegäran har ogiltig NameIdPolicy. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable – Autentiseringstjänsten kan inte ansluta till Active Directory. Se till att agent servrarna är medlemmar i samma AD-skog som de användare vars lösen ord måste verifieras och att de kan ansluta till Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout – tids gränsen nåddes för begäran om lösen ords validering. Se till att Active Directory är tillgänglig och svarar på begär Anden från agenterna. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException – ett okänt fel inträffade när svaret från Autentiseringstjänsten bearbetades. Gör om begäran. Om det fortfarande inte fungerar kan du [öppna ett support ärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem – det gick inte att verifiera användarens lösen ord. Mer information finns i agent loggarna och kontrol lera att Active Directory fungerar som förväntat. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException-det går inte att dekryptera lösen ordet. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours – användarna försökte logga in utanför de tillåtna timmarna (det anges i AD). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew-det gick inte att utföra autentiseringsbegäran på grund av en tids gräns mellan den dator som kör Autentiseringstjänsten och AD. Åtgärda problem med tids synkronisering. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated-Kerberos-autentiseringsförsök misslyckades. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported – autentiseringspaketet stöds inte. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader-det gick inte att hitta något Authorization-huvud. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn – klienten är inte aktive rad för sömlös SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat – det gick inte att verifiera användarens Kerberos-biljett. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid-sömlös SSO misslyckades eftersom användarens Kerberos-biljett har upphört att gälla eller är ogiltig. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed – det gick inte att hitta användar objekt baserat på information i användarens Kerberos-biljett. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn – användaren som försöker logga in på Azure AD skiljer sig från den användare som är inloggad på enheten. |
| AADSTS90002 | InvalidTenantName-klient namnet hittades inte i data lagret. Kontrol lera att du har rätt klient-ID. |
| AADSTS90004 | InvalidRequestFormat-begäran har inte formaterats korrekt. |
| AADSTS90005 | InvalidRequestWithMultipleRequirements – det gick inte att slutföra begäran. Begäran är inte giltig eftersom tipset identifierare och inloggnings tips inte kan användas tillsammans.  |
| AADSTS90006 | ExternalServerRetryableError – tjänsten är inte tillgänglig för tillfället.|
| AADSTS90007 | InvalidSessionId – felaktig begäran. Det gick inte att parsa det skickade sessions-ID: t. |
| AADSTS90008 | TokenForItselfRequiresGraphPermission – användaren eller administratören har inte samtyckt till att använda programmet. Som minimum kräver programmet åtkomst till Azure AD genom att ange behörigheten Logga in och läsa användar profil. |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier – programmet begär en token för sig själv. Det här scenariot stöds bara om den angivna resursen använder det GUID-baserade program-ID: t. |
| AADSTS90010 | NotSupported – det gick inte att skapa algoritmen. |
| AADSTS90012 | RequestTimeout-den begärda tids gränsen nåddes. |
| AADSTS90013 | InvalidUserInput – indata från användaren är inte giltiga. |
| AADSTS90014 | MissingRequiredField – den här felkoden kan visas i olika fall när ett förväntat fält inte finns i autentiseringsuppgiften. |
| AADSTS90015 | QueryStringTooLong-frågesträngen är för lång. |
| AADSTS90016 | MissingRequiredClaim-åtkomsttoken är ogiltig. Det begärda kravet saknas. |
| AADSTS90019 | MissingTenantRealm-Azure AD kunde inte fastställa klient-ID: n från begäran. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat – huvud namnets format är inte giltigt eller uppfyller inte det förväntade `name[/host][@realm]` formatet. Huvud namnet är obligatoriskt, värden och sfär är valfria och kan anges till null. |
| AADSTS90023 | InvalidRequest-begäran om autentiseringsfel är ogiltig. |
| AADSTS9002313 | InvalidRequest-begäran är felaktig eller ogiltig. – Problemet här beror på att det uppstod något fel med begäran till en viss slut punkt. Förslaget till det här problemet är att få en Fiddler spårning av felet som inträffar och se om begäran faktiskt har formaterats eller inte. |
| AADSTS90024 | RequestBudgetExceededError – ett tillfälligt fel har inträffat. Försök igen. |
| AADSTS90033 | MsodsServiceUnavailable – Microsoft Online Directory Service (MSODS) är inte tillgängligt. |
| AADSTS90036 | MsodsServiceUnretryableFailure-ett oväntat fel som inte går att försöka igen från den WCF-tjänst som är värd för MSODS har inträffat. [Skapa ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md) för att få mer information om felet. |
| AADSTS90038 | NationalCloudTenantRedirection-den angivna klient organisationen ' Y ' tillhör det nationella molnet ' X '. Den aktuella moln instansen ' Z ' är inte Federerad med X. Ett omdirigerings fel för molnet returneras. |
| AADSTS90043 | NationalCloudAuthCodeRedirection – funktionen är inaktive rad. |
| AADSTS90051 | InvalidNationalCloudId – den nationella moln identifieraren innehåller ett ogiltigt moln-ID. |
| AADSTS90055 | TenantThrottlingError – det finns för många inkommande begär Anden. Detta undantag genereras för blockerade klienter. |
| AADSTS90056 | BadResourceRequest-för att lösa in koden för en åtkomsttoken ska appen skicka en POST-begäran till `/token` slut punkten. Innan du gör det måste du också ange en auktoriseringskod och skicka den i POST-begäran till `/token` slut punkten. Läs den här artikeln om du vill ha en översikt över OAuth 2,0 Authorization Code Flow: [.. /azuread-dev/v1-Protocols-OAuth-Code.MD](../azuread-dev/v1-protocols-oauth-code.md). Dirigera användaren till `/authorize` slut punkten, vilket kommer att returnera en authorization_code. Genom att skicka en begäran till `/token` slut punkten får användaren åtkomst-token. Logga in på Azure Portal och kontrol lera **Appregistreringar > slut punkter** för att bekräfta att de två slut punkterna har kon figurer ATS korrekt. |
| AADSTS90072 | PassThroughUserMfaError – det externa kontot som användaren loggar in med inte finns på den klient som de har loggat in på. Det innebär att användaren inte kan uppfylla MFA-kraven för klient organisationen. Det här felet kan också inträffa om användarna synkroniseras, men det finns ett matchnings fel i attributet ImmutableID (sourceAnchor) mellan Active Directory och Azure AD. Kontot måste läggas till som en extern användare i klient organisationen först. Logga ut och logga in med ett annat användar konto för Azure AD. |
| AADSTS90081 | OrgIdWsFederationMessageInvalid – ett fel uppstod när tjänsten försökte bearbeta ett WS-Federation meddelande. Meddelandet är inte giltigt. |
| AADSTS90082 | OrgIdWsFederationNotSupported-den valda autentiseringsmetoden för begäran stöds inte för närvarande. |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed – gäst konton är inte tillåtna för den här platsen. |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed-tjänsten kan inte utfärda en token eftersom företags objektet inte har etablerats ännu. |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired – användarens DA-token har upphört att gälla. |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed – ett fel uppstod när WS-Federation meddelandet skulle skapas från URI: n. |
| AADSTS90090 | GraphRetryableError – tjänsten är inte tillgänglig för tillfället. |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized-Graph returnerade en otillåten felkod för begäran. |
| AADSTS90094 | AdminConsentRequired-administratörs medgivande krävs. |
| AADSTS900382 | Konfidentiell klient stöds inte i en begäran mellan moln. |
| AADSTS90099 | Programmet {appId} ({appName}) har inte auktoriserats i klient organisationen {Tenant}. Program måste ha behörighet att få åtkomst till kund innehavaren innan administratörer kan använda dem. Ge ett för hands godkännande eller kör lämpligt Partner Center-API för att auktorisera programmet. |
| AADSTS900971| Ingen svars adress har angetts.|
| AADSTS90100 | InvalidRequestParameter-parametern är tom eller ogiltig. |
| AADSTS901002 | AADSTS901002: parametern resurs begär ande stöds inte. |
| AADSTS90101 | InvalidEmailAddress – de angivna data är inte en giltig e-postadress. E-postadressen måste anges i formatet `someone@example.com` . |
| AADSTS90102 | InvalidUriParameter-värdet måste vara en giltig absolut URI. |
| AADSTS90107 | InvalidXml-begäran är inte giltig. Se till att dina data inte innehåller ogiltiga tecken.|
| AADSTS90114 | InvalidExpiryDate – tids stämplingen för Mass-token upphör att gälla. |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode-användar koden är null eller tom.|
| AADSTS90120 | InvalidDeviceFlowRequest-begäran har redan auktoriserats eller nekats. |
| AADSTS90121 | InvalidEmptyRequest-ogiltig tom begäran.|
| AADSTS90123 | IdentityProviderAccessDenied-token kan inte utfärdas eftersom identiteten eller anspråks utfärdaren nekade begäran. |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported – resursen stöds inte över-eller- `/common` `/consumers` slut punkterna. Använd den `/organizations` klient-/regionsspecifika slut punkten i stället. |
| AADSTS90125 | DebugModeEnrollTenantNotFound – användaren är inte i systemet. Kontrol lera att du har angett användar namnet korrekt. |
| AADSTS90126 | DebugModeEnrollTenantNotInferred-användar typen stöds inte för den här slut punkten. Systemet kan inte härleda användarens klient organisation från användar namnet. |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported – programmet stöds inte över-eller- `/common` `/consumers` slut punkterna. Använd den `/organizations` klient-/regionsspecifika slut punkten i stället. |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated-ett fel som inte går att försöka igen har inträffat.|
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound – användarens huvud namn har inte en NGC-ID-nyckel konfigurerad. |
| AADSTS130005 | NgcInvalidSignature-NGC-nyckelns signatur har inte verifierats.|
| AADSTS130006 | NgcTransportKeyNotFound – den NGC transport nyckeln har inte kon figurer ATS på enheten. |
| AADSTS130007 | NgcDeviceIsDisabled – enheten är inaktive rad. |
| AADSTS130008 | NgcDeviceIsNotFound-enheten som refereras av NGC-nyckeln hittades inte. |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce-fråge-ID har inte angetts. |
| AADSTS140001 | InvalidSessionKey-sessionsnyckeln är ogiltig.|
| AADSTS165900 | InvalidApiRequest – ogiltig begäran. |
| AADSTS220450 | UnsupportedAndroidWebViewVersion – Chrome-webbview-versionen stöds inte. |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource – resursen har inte kon figurer ATS för att acceptera endast enhets-token. |
| AADSTS240001 | BulkAADJTokenUnauthorized – användaren har inte behörighet att registrera enheter i Azure AD. |
| AADSTS240002 | RequiredClaimIsMissing – id_token kan inte användas som `urn:ietf:params:oauth:grant-type:jwt-bearer` beviljande.|
| AADSTS530032 | BlockedByConditionalAccessOnSecurityPolicy – klient organisationens administratör har konfigurerat en säkerhets princip som blockerar denna begäran. Kontrollera säkerhetsprinciperna som har definierats på klientnivå för att avgöra om din begäran uppfyller principkraven. |
| AADSTS700016 | UnauthorizedClient_DoesNotMatchRequest – det gick inte att hitta programmet i katalogen/klient organisationen. Detta kan inträffa om programmet inte har installerats av administratören för klientorganisationen, eller om det har godkänts av någon användare i klientorganisationen. Du kanske har konfigurerat fel identifierarvärde för programmet eller skickat din autentiseringsförfrågan till fel klientorganisation. |
| AADSTS700020 | InteractionRequired – åtkomst beviljande kräver interaktion. |
| AADSTS700022 | InvalidMultipleResourcesScope-det tillhandahållna värdet för parameter området för indata är inte giltigt eftersom det innehåller fler än en resurs. |
| AADSTS700023 | InvalidResourcelessScope-det angivna värdet för Indataparametern för indata-parametern är inte giltigt när en åtkomsttoken begärs. |
| AADSTS7000215 | Ogiltig klient hemlighet har angetts. Utvecklarens fel-appen försöker logga in utan nödvändiga eller korrekta autentiseringsmetoder.|
| AADSTS7000222 | InvalidClientSecretExpiredKeysProvided-de angivna klientens hemliga nycklar har upphört att gälla. Gå till Azure Portal om du vill skapa nya nycklar för appen eller Överväg att använda autentiseringsuppgifter för ytterligare säkerhet: [https://aka.ms/certCreds](./active-directory-certificate-credentials.md) |
| AADSTS700005 | InvalidGrantRedeemAgainstWrongTenant-auktoriseringskod är avsedd att användas gentemot andra klienter, vilket avvisas. OAuth2-auktoriseringskod måste lösas in mot samma klient som den förvärvades för (/vanliga eller/{tenant-ID} efter behov) |
| AADSTS1000000 | UserNotBoundError – bindnings-API: t kräver att Azure AD-användaren också autentiseras med en extern IDP, vilket inte har hänt än. |
| AADSTS1000002 | BindCompleteInterruptError – bindningen har slutförts, men användaren måste vara informerad. |
| AADSTS7000112 | UnauthorizedClientApplicationDisabled – programmet är inaktiverat. |
| AADSTS7000114| Programmet ' appIdentifier ' får inte göra program på uppdrag av anrop.|
| AADSTS7500529 | Värdet "SAMLId-GUID" är inte ett giltigt SAML-ID – Azure AD använder det här attributet för att fylla i InResponseTo-attributet för det returnerade svaret. ID får inte börja med en siffra, så en gemensam strategi är att lägga en sträng som "ID" till sträng representationen av ett GUID. Till exempel är id6c1c178c166d486687be4aaf5e482730 ett giltigt ID. |

## <a name="next-steps"></a>Nästa steg

* Har du en fråga eller kan du inte hitta det du söker? Skapa ett GitHub-ärende eller se [support och hjälp alternativ för utvecklare](./developer-support-help-options.md) och lär dig mer om andra sätt att få hjälp och support.
