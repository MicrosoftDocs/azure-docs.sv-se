---
title: Använd aktivitetsrapporten för att flytta AD FS till Azure Active Directory | Microsoft Docs'
description: Med Active Directory Federation Services (AD FS) (AD FS)-programaktivitetsrapporten kan du snabbt migrera program från AD FS till Azure Active Directory (Azure AD). Det här migreringsverktyget för AD FS identifierar kompatibilitet med Azure AD och ger migreringsvägledning.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 01/14/2019
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9265a61b0879078332b8ccc2d10e711b4ac8f1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377428"
---
# <a name="use-the-ad-fs-application-activity-report-to-migrate-applications-to-azure-ad"></a>Använda rapporten AD FS programaktivitet för att migrera program till Azure AD

Många organisationer använder Active Directory Federation Services (AD FS) (AD FS) för att tillhandahålla enkel inloggning till molnprogram. Det finns betydande fördelar med att flytta dina AD FS-program till Azure AD för autentisering, särskilt vad gäller kostnadshantering, riskhantering, produktivitet, efterlevnad och styrning. Men det kan vara tidskrävande att förstå vilka program som är kompatibla med Azure AD och identifiera specifika migreringssteg.

Med AD FS-programaktivitetsrapporten i Azure Portal kan du snabbt identifiera vilka av dina program som kan migreras till Azure AD. Den utvärderar alla AD FS program för kompatibilitet med Azure AD, söker efter eventuella problem och ger vägledning om hur du förbereder enskilda program för migrering. Med AD FS-programaktivitetsrapporten kan du:

* **Upptäck AD FS program och begränsa migreringen.** Rapporten AD FS programaktivitet visar alla AD FS i din organisation som har haft en aktiv användarinloggning under de senaste 30 dagarna. Rapporten visar en appberedskap för migrering till Azure AD. Rapporten visar inte Microsoft-relaterade förlitande parter i AD FS till exempel Office 365. Till exempel förlitande parter med namnet "urn:federation:MicrosoftOnline".

* **Prioritera program för migrering.** Hämta antalet unika användare som har loggat in på programmet under de senaste 1, 7 eller 30 dagarna för att avgöra hur kritiskt eller risk det är att migrera programmet.
* **Kör migreringstester och åtgärda problem.** Reporting Service kör automatiskt tester för att avgöra om ett program är redo att migreras. Resultaten visas i rapporten för AD FS som en migreringsstatus. Om AD FS konfigurationen inte är kompatibel med en Azure AD-konfiguration får du specifik vägledning om hur du tar itu med konfigurationen i Azure AD.

De AD FS programaktivitetsdata är tillgängliga för användare som har tilldelats någon av dessa administratörsroller: global administratör, rapportläsare, säkerhetsläsare, programadministratör eller molnprogramadministratör.

## <a name="prerequisites"></a>Förutsättningar

* Din organisation måste för närvarande använda AD FS för att få åtkomst till program.
* Azure AD Connect Health måste vara aktiverat i Din Azure AD-klientorganisation.
* Den Azure AD Connect Health för AD FS agenten måste vara installerad.
   * [Läs mer om Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Kom igång med att konfigurera Azure AD Connect Health installera AD FS agenten](../hybrid/how-to-connect-health-agent-install.md)

>[!IMPORTANT] 
>Det finns några orsaker till att du inte ser alla program som du förväntar dig när du har installerat Azure AD Connect Health. Rapporten AD FS programaktivitet visar endast AD FS förlitande parter med användarinloggningar under de senaste 30 dagarna. Rapporten visar inte heller Microsoft-relaterade förlitande parter, till exempel Office 365.

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Upptäck AD FS program som kan migreras 

Rapporten AD FS programaktivitet är tillgänglig i Azure Portal Azure AD **Usage & Insights** Reporting. Rapporten AD FS programaktivitet analyserar varje program AD FS för att avgöra om det kan migreras som det är eller om ytterligare granskning krävs. 

1. Logga in på [Azure Portal](https://portal.azure.com) med en administratörsroll som har åtkomst till AD FS-programaktivitetsdata (global administratör, rapportläsare, säkerhetsläsare, programadministratör eller molnprogramadministratör).

2. Välj **Azure Active Directory** och sedan **Företagsprogram**.

3. Under **Aktivitet** väljer du **Usage & Insights** och väljer sedan AD FS **programaktivitet** för att öppna en lista över alla AD FS i din organisation.

   ![AD FS programaktivitet](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. För varje program i AD FS programaktivitetslistan visar du **migreringsstatusen**:

   * **Redo för migrering** innebär AD FS-programkonfigurationen stöds fullt ut i Azure AD och kan migreras som den är.

   * **Behöver granskas** innebär att vissa av programmets inställningar kan migreras till Azure AD, men du måste granska de inställningar som inte kan migreras som de är.

   * **Ytterligare steg som** krävs innebär att Azure AD inte stöder vissa av programmets inställningar, så programmet kan inte migreras i dess aktuella tillstånd.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Utvärdera ett programs beredskap för migrering 

1. I listan AD FS programaktivitet klickar du på statusen i kolumnen **Migreringsstatus för** att öppna migreringsinformation. Du ser en sammanfattning av de konfigurationstester som har godkänts, tillsammans med eventuella migreringsproblem.

   ![Information om migrering](media/migrate-adfs-application-activity/migration-details.png)

2. Klicka på ett meddelande för att öppna ytterligare information om migreringsregeln. En fullständig lista över de egenskaper som har testats finns i [AD FS för programkonfigurationstester](#ad-fs-application-configuration-tests) nedan.

   ![Information om migreringsregel](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>AD FS programkonfigurationstester

I följande tabell visas alla konfigurationstester som utförs på AD FS program.

|Resultat  |Pass/Warning/Fail  |Beskrivning  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> Minst en icke-migreringsbar regel har identifierats för AdditionalAuthentication.       | Pass/Warning          | Den förlitande parten har regler för att fråga efter multifaktorautentisering (MFA). Om du vill flytta till Azure AD översätter du dessa regler till principer för villkorsstyrd åtkomst. Om du använder en lokal MFA rekommenderar vi att du flyttar till Azure AD MFA. [Läs mer om villkorlig åtkomst.](../authentication/concept-mfa-howitworks.md)        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Förlitande part har AdditionalWSFedEndpoint inställt på true.       | Godkänn/Underkänn          | Den förlitande parten i AD FS tillåter flera WS-Fed slutpunkter för försäkran.Azure AD stöder för närvarande endast en.Om du har ett scenario där det här resultatet blockerar migreringen kan [du berätta för oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Förlitande part har angett AllowedAuthenticationClassReferences.       | Godkänn/Underkänn          | Med den här AD FS kan du ange om programmet är konfigurerat för att endast tillåta vissa autentiseringstyper. Vi rekommenderar att du använder villkorlig åtkomst för att uppnå den här funktionen. Om du har ett scenario där det här resultatet blockerar migreringen kan [du meddela oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Läs mer om villkorlig åtkomst.](../authentication/concept-mfa-howitworks.md)          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Godkänn/Underkänn          | Med den här AD FS kan du ange om programmet är konfigurerat för att ignorera cookies för enkel inloggning och **Fråga alltid efter autentisering.** I Azure AD kan du hantera autentiseringssessionen med principer för villkorsstyrd åtkomst för att uppnå liknande beteende. [Läs mer om hur du konfigurerar hantering av autentiseringssessioner med villkorlig åtkomst.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Test-ADFSRPAutoUpdateEnabled <br> Förlitande part har AutoUpdateEnabled inställt på true       | Pass/Warning          | Med den här AD FS kan du ange om AD FS är konfigurerad för att automatiskt uppdatera programmet baserat på ändringar i federationsmetadata. Azure AD stöder inte detta i dag, men bör inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Förlitande part har flera ClaimsProviders aktiverat       | Godkänn/Underkänn          | Den här AD FS anropar de identitetsproviders som den förlitande parten accepterar anspråk från. I Azure AD kan du aktivera externt samarbete med hjälp av Azure AD B2B. [Läs mer om Azure AD B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Godkänn/Underkänn          | Programmet har definierade anpassade auktoriseringsregler för delegering. Det här är WS-Trust som Azure AD stöder med hjälp av moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2.0. [Läs mer om Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/Warning          | Programmet har definierade auktoriseringsregler för anpassad personifiering.Det här är WS-Trust som Azure AD stöder med hjälp av moderna autentiseringsprotokoll, till exempel OpenID Connect och OAuth 2.0. [Läs mer om Microsoft Identity Platform.](../develop/v2-protocols-oidc.md)          |
|Test-ADFSRPIssuanceAuthorizationRules <br> Minst en icke-migreringsbar regel har identifierats för IssuanceAuthorization.       | Pass/Warning          | Programmet har anpassade auktoriseringsregler för utfärdande som definierats i AD FS.Azure AD stöder den här funktionen med villkorsstyrd åtkomst i Azure AD. [Läs mer om villkorlig åtkomst.](../conditional-access/overview.md) <br> Du kan också begränsa åtkomsten till ett program efter användare eller grupper som tilldelats till programmet. [Läs mer om hur du tilldelar användare och grupper för att få åtkomst till program.](./assign-user-or-group-access-portal.md)            |
|Test-ADFSRPIssuanceTransformRules <br> Minst en icke-migreringsbar regel har identifierats för IssuanceTransform.       | Pass/Warning          | Programmet har anpassade utfärdandetransformregler som definierats i AD FS. Azure AD stöder anpassning av anspråk som utfärdats i token. Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)           |
|Test-ADFSRPMonitoringEnabled <br> Den förlitande parten har MonitoringEnabled inställt på true.       | Pass/Warning          | Med den här AD FS kan du ange om AD FS har konfigurerats för att automatiskt uppdatera programmet baserat på ändringar i federationsmetadata. Azure AD stöder inte detta i dag, men bör inte blockera migreringen av programmet till Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/Warning          | AD FS tillåter en tidsskevhet baserat på tiden NotBefore och NotOnOrAfter i SAML-token. Azure AD hanterar detta automatiskt som standard.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Förlitande part har RequestMFAFromClaimsProviders inställt på sant.       | Pass/Warning          | Den här AD FS anger beteendet för MFA när användaren kommer från en annan anspråksprovider. I Azure AD kan du aktivera externt samarbete med hjälp av Azure AD B2B. Sedan kan du tillämpa principer för villkorlig åtkomst för att skydda gäståtkomst. Läs mer om [Azure AD B2B och](../external-identities/what-is-b2b.md) [villkorsstyrd åtkomst.](../conditional-access/overview.md)          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Förlitande part har SignedSamlRequestsRequired inställt på true       | Godkänn/Underkänn          | Programmet konfigureras i en AD FS att verifiera signaturen i SAML-begäran. Azure AD accepterar en signerad SAML-begäran. Signaturen verifieras dock inte. Azure AD har olika metoder för att skydda mot skadliga anrop. Azure AD använder till exempel svars-URL:erna som konfigurerats i programmet för att verifiera SAML-begäran. Azure AD skickar bara en token till svars-URL:er som konfigurerats för programmet. Om du har ett scenario där det här resultatet blockerar migreringen kan [du berätta för oss](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/Warning         | Programmet har konfigurerats för en anpassad tokenlivslängd. Standardvärdet AD FS en timme.Azure AD stöder den här funktionen med villkorlig åtkomst. Mer information finns i Konfigurera [hantering av autentiseringssessioner med villkorlig åtkomst.](../conditional-access/howto-conditional-access-session-lifetime.md)          |
|Förlitande part är inställd på att kryptera anspråk. Detta stöds av Azure AD       | Godkänd          | Med Azure AD kan du kryptera den token som skickas till programmet. Mer information finns i Konfigurera [Azure AD SAML-tokenkryptering.](./howto-saml-token-encryption.md)          |
|EncryptedNameIdRequiredCheckResult      | Godkänn/Underkänn          | Programmet har konfigurerats för att kryptera nameID-anspråket i SAML-token.Med Azure AD kan du kryptera hela token som skickas till programmet.Kryptering av specifika anspråk stöds inte ännu. Mer information finns i Konfigurera [Azure AD SAML-tokenkryptering.](./howto-saml-token-encryption.md)         |

## <a name="check-the-results-of-claim-rule-tests"></a>Kontrollera resultatet av anspråksregeltester

Om du har konfigurerat en anspråksregel för programmet i AD FS tillhandahåller upplevelsen en detaljerad analys av alla anspråksregler. Du ser vilka anspråksregler som kan flyttas till Azure AD och vilka som behöver granskas ytterligare.

1. I listan AD FS programaktivitet klickar du på statusen i kolumnen **Migreringsstatus** för att öppna migreringsinformation. Du ser en sammanfattning av de konfigurationstester som har godkänts samt eventuella migreringsproblem.

2. På sidan **Information om migreringsregel** expanderar du resultaten för att visa information om potentiella migreringsproblem och få ytterligare vägledning. En detaljerad lista över alla anspråksregler som testats finns i [tabellen Kontrollera resultatet av anspråksregeltester](#check-the-results-of-claim-rule-tests) nedan.

   Exemplet nedan visar information om migreringsregel för regeln IssuanceTransform. Den visar de specifika delar av anspråket som måste granskas och åtgärdas innan du kan migrera programmet till Azure AD.

   ![Ytterligare vägledning om migreringsregel](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Anspråksregeltester

I följande tabell visas alla anspråksregeltester som utförs på AD FS program.

|Egenskap  |Beskrivning  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | Villkorssatsen använder reguljära uttryck för att utvärdera om anspråket matchar ett visst mönster.Om du vill uppnå en liknande funktion i Azure AD kan du använda fördefinierad transformering, till exempel IfEmpty(), StartWith(), Contains(), med mera. Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_CONDITION_CLASS      | Villkorssatsen har flera villkor som måste utvärderas innan du kör utfärdande-instruktionen.Azure AD kan stödja den här funktionen med anspråkstransformeringsfunktioner där du kan utvärdera flera anspråksvärden.Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)          |
|UNSUPPORTED_RULE_TYPE      | Det gick inte att känna igen anspråksregeln. Mer information om hur du konfigurerar anspråk i Azure AD finns i Anpassa anspråk som [utfärdats i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | Villkorssatsen använder en Utfärdare som inte stöds i Azure AD.Azure AD använder för närvarande inte källanspråk från andra lager än Active Directory eller Azure AD. Om detta hindrar dig från att migrera program till Azure AD kan [du berätta för oss.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)         |
|UNSUPPORTED_CONDITION_FUNCTION      | Villkorssatsen använder en mängdfunktion för att utfärda eller lägga till ett enskilt anspråk oavsett antalet matchningar.I Azure AD kan du utvärdera attributet för en användare för att avgöra vilket värde som ska användas för anspråket med funktioner som IfEmpty(), StartWith(), Contains(), med flera.Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)          |
|RESTRICTED_CLAIM_ISSUED      | Villkorssatsen använder ett anspråk som är begränsat i Azure AD. Du kanske kan utfärda ett begränsat anspråk, men du kan inte ändra dess källa eller tillämpa någon transformering. Mer information finns i [Anpassa anspråk som genereras i token för en specifik app i Azure AD.](../develop/active-directory-claims-mapping.md)          |
|EXTERNAL_ATTRIBUTE_STORE      | Utfärdande-instruktionen använder ett annat attributarkiv än Active Directory. Azure AD använder för närvarande inte källanspråk från andra lager än Active Directory eller Azure AD. Om det här resultatet hindrar dig från att migrera program till Azure AD kan [du berätta för oss.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)          |
|UNSUPPORTED_ISSUANCE_CLASS      | Utfärdande-uttrycket använder ADD för att lägga till anspråk i den inkommande anspråksuppsättningen. I Azure AD kan detta konfigureras som flera anspråksomvandlare.Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-claims-mapping.md)         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | Utfärdande-instruktionen använder reguljära uttryck för att transformera värdet för anspråket som ska genereras.Om du vill uppnå liknande funktioner i Azure AD kan du använda fördefinierad transformering, till exempel Extract(), Trim(), ToLower med flera. Mer information finns i Anpassa [anspråk som utfärdas i SAML-token för företagsprogram.](../develop/active-directory-saml-claims-customization.md)          |

## <a name="troubleshooting"></a>Felsökning

### <a name="cant-see-all-my-ad-fs-applications-in-the-report"></a>Jag kan inte se alla mina AD FS program i rapporten

 Om du har installerat Azure AD Connect Health men fortfarande uppmanas att installera det eller om du inte ser alla dina AD FS-program i rapporten kan det vara så att du inte har några aktiva AD FS-program eller att dina AD FS-program är Microsoft-program.
 
 Rapporten AD FS programaktivitet visar en lista över alla AD FS i din organisation med aktiva användare som loggat in under de senaste 30 dagarna. Rapporten visar inte heller Microsoft-relaterade förlitande parter i AD FS till exempel Office 365. Förlitande parter med namnet "urn:federation:MicrosoftOnline", "microsoftonline", "microsoft:winhello:cert:prov:server" visas inte i listan.





## <a name="next-steps"></a>Nästa steg

- [Video: Så här använder du AD FS för att migrera ett program](https://www.youtube.com/watch?v=OThlTA239lU)
- [Hantera program med Azure Active Directory](what-is-application-management.md)
- [Hantera åtkomst till appar](what-is-access-management.md)
- [Azure AD Connect federation](../hybrid/how-to-connect-fed-whatis.md)
