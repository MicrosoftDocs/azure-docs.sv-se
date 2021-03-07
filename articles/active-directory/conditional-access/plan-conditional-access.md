---
title: Planera en distribution av Azure Active Directory villkorlig åtkomst
description: Lär dig hur du utformar principer för villkorlig åtkomst och hur du effektivt distribuerar i din organisation.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13460fad0ed106f5c0590df961ceca54cb04cd7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427189"
---
# <a name="plan-a-conditional-access-deployment"></a>Planera en distribution av villkorsstyrd åtkomst

Det är viktigt att planera distributionen av den villkorliga åtkomsten för att uppnå organisationens åtkomst strategi för appar och resurser.

I en mobil-och mellanliggande värld, kan dina användare komma åt din organisations resurser från var som helst med olika enheter och appar. Därför räcker det inte längre att fokusera på vem som har åtkomst till resursen. Du måste också fundera över var användaren är, vilken enhet som används, vilken resurs som används och mer. 

Azure Active Directory (Azure AD) analys av villkorlig åtkomst signalerar som användare, enhet och plats för att automatisera beslut och tvinga organisations åtkomst principer för resursen. Du kan använda principer för villkorlig åtkomst för att tillämpa åtkomst kontroller som Multi-Factor Authentication (MFA). Med principer för villkorlig åtkomst kan du uppmana användarna att använda MFA när de behöver säkerhet, och samtidigt ta bort användarnas användning när de inte behövs.

![Översikt över villkorsstyrd åtkomst](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

Microsoft tillhandahåller standard villkors principer som kallas [säkerhets inställningar](../fundamentals/concept-fundamentals-security-defaults.md) som garanterar en grundläggande säkerhets nivå. Din organisation kan dock behöva större flexibilitet än erbjudandet om säkerhets standard. Du kan använda villkorlig åtkomst för att anpassa säkerhets standardvärden med mer detaljerad information och konfigurera nya principer som uppfyller dina krav.

## <a name="learn"></a>Learn

Innan du börjar ska du kontrol lera att du förstår hur [villkorlig åtkomst](overview.md) fungerar och när du ska använda det.

### <a name="benefits"></a>Fördelar

Fördelarna med att distribuera villkorlig åtkomst är:

* Öka produktiviteten. Du behöver bara avbryta användare med ett inloggnings villkor som MFA när en eller flera signaler garanterar det. Med principer för villkorlig åtkomst kan du styra när användare tillfrågas om MFA, när åtkomst är blockerad och när de måste använda en betrodd enhet.

* Hantera risker. Att automatisera riskbedömning med princip villkor innebär att riskfyllda inloggningar identifieras och åtgärdas eller blockeras. Koppling villkorlig åtkomst med [identitets skydd](../identity-protection/overview-identity-protection.md), som identifierar avvikelser och misstänkta händelser, gör att du kan rikta in dig på åtkomst till resurser som blockeras eller gated. 

* Hantera efterlevnad och styrning. Med villkorlig åtkomst kan du granska åtkomst till program, Visa användnings villkoren för medgivande och begränsa åtkomsten baserat på efterlevnadsprinciper.

* Hantera kostnad. Att flytta åtkomst principer till Azure AD minskar beroendet av anpassade eller lokala lösningar för villkorlig åtkomst och deras infrastruktur kostnader.

### <a name="license-requirements"></a>Licenskrav

Se [licens krav för villkorlig åtkomst](overview.md).

Om ytterligare funktioner krävs kan du också behöva relaterade licenser. Mer information finns i [Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Förutsättningar

* En fungerande Azure AD-klient med Azure AD Premium-eller utvärderings licens aktive rad. Om det behövs kan du [skapa ett kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ett konto med administratörs behörighet för villkorlig åtkomst.

* En icke-administratörs användare med ett lösen ord som du känner till, till exempel testuser. Om du behöver skapa en användare, se [snabb start: Lägg till nya användare i Azure Active Directory](../fundamentals/add-users-azure-active-directory.md).

* En grupp som användaren som inte är administratör är medlem i. Om du behöver skapa en grupp kan du läsa [skapa en grupp och lägga till medlemmar i Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Utbildnings resurser

Följande resurser kan vara användbara när du lär dig mer om villkorlig åtkomst:


#### <a name="videos"></a>Video

* [Vad är villkorsstyrd åtkomst?](https://youtu.be/ffMAw2IVO7A)
* [Hur distribuerar jag villkorlig åtkomst?](https://youtu.be/c_izIRNJNuk)
* [Hur gör jag för att distribuera principer för villkorlig åtkomst till slutanvändarna?](https://youtu.be/0_Fze7Zpyvc)
* [Ta med eller undanta användare från principer för villkorlig åtkomst](https://youtu.be/5DsW1hB3Jqs)
* [Villkorsstyrd åtkomst med enhetskontroller](https://youtu.be/NcONUf-jeS4)
* [Villkorlig åtkomst med Azure AD MFA](https://youtu.be/Tbc-SU97G-w)
* [Villkorlig åtkomst i Enterprise Mobility + Security](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Onlinekurser på PluralSight

* [Utforma identitets hantering i Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Designa autentisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Design auktorisering för Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Planera distributions projektet

Överväg organisationens behov medan du fastställer strategin för den här distributionen i din miljö.

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör [du se till att du är engagerande rätt intressenter](../fundamentals/active-directory-deployment-plans.md) och att projekt rollerna är tydliga.

### <a name="plan-communications"></a>Planera kommunikation

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare hur deras upplevelse kommer att ändras, när den kommer att ändras och hur du får support om de drabbas av problem.

### <a name="plan-a-pilot"></a>Planera en pilot

När nya principer är klara för din miljö kan du distribuera dem i faser i produktions miljön. Använd först en princip för en liten uppsättning användare i en test miljö och kontrol lera om principen fungerar som förväntat. Se [metod tips för en pilot](../fundamentals/active-directory-deployment-plans.md).

> [!NOTE]
> Undanta alla administratörer för att distribuera nya principer som inte är speciella för administratörer. Detta säkerställer att administratörer fortfarande kan komma åt principen och göra ändringar eller återkalla den om det uppstår en betydande inverkan. Verifiera alltid principen med mindre användar grupper innan du gäller alla användare.

## <a name="understand-conditional-access-policy-components"></a>Förstå komponenter för villkorlig åtkomst princip
Principer för villkorlig åtkomst är if-then-satser: om en tilldelning är uppfyllt ska du tillämpa dessa åtkomst kontroller.

När du konfigurerar principer för villkorlig åtkomst kallas de för *tilldelningar*. Med principer för villkorlig åtkomst kan du tillämpa åtkomst kontroller i din organisations appar baserat på vissa tilldelningar.


Mer information finns i [skapa en princip för villkorlig åtkomst](concept-conditional-access-policies.md).

![skärmen skapa princip](media/plan-conditional-access/create-policy.png)

[Tilldelningar](concept-conditional-access-policies.md#assignments) definierar

* [Användare och grupper](concept-conditional-access-users-groups.md) som ska påverkas av principen

* [Molnappar eller åtgärder](concept-conditional-access-cloud-apps.md) som principen ska gälla för 

* [Villkor](concept-conditional-access-conditions.md) enligt vilka principen ska gälla.

Inställningarna för [åtkomst kontroll](concept-conditional-access-policies.md) bestämmer hur du ska tillämpa en princip:

* [Bevilja eller blockera](concept-conditional-access-grant.md) åtkomst till molnappar.

* [Sessions kontroller](concept-conditional-access-session.md) möjliggör begränsade upplevelser inom vissa molnappar.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Ställ rätt frågor för att bygga dina principer

Principer svarar på frågor om vem som ska ha åtkomst till dina resurser, vilka resurser de ska ha åtkomst till och under vilka omständigheter. Principer kan utformas för att bevilja åtkomst eller blockera åtkomst. Se till att ställa rätt frågor om vad din policy försöker uppnå. 

Dokumentera svaren på frågorna för varje princip innan du skapar den. 

#### <a name="common-questions-about-assignments"></a>Vanliga frågor om tilldelningar

[Användare och grupper](concept-conditional-access-users-groups.md)

* Vilka användare och grupper som ska inkluderas i eller exkluderas från principen?

* Omfattar den här principen alla användare, en speciell grupp av användare, katalog roller eller externa användare?

[Molnappar eller åtgärder](concept-conditional-access-cloud-apps.md)

* Vilka program kommer principen att gälla för?

* Vilka användar åtgärder kommer att omfattas av den här principen?

[Villkor](concept-conditional-access-conditions.md)

* Vilka plattformar kommer att inkluderas i eller exkluderas från principen?

* Vilka är organisationens betrodda platser?

* Vilka platser kommer att inkluderas i eller exkluderas från principen?

* Vilka typer av klient program (webbläsare, mobil, Station ära datorer, appar med äldre autentiseringsmetoder) kommer att ingå i eller uteslutas från principen?

* Har du principer som skulle leda till undantag för Azure AD-anslutna enheter eller hybrid Azure AD-anslutna enheter från principer? 

* Vill du införliva inloggnings risk skyddet om du använder [identitets skydd](../identity-protection/concept-identity-protection-risks.md)?

#### <a name="common-questions-about-access-controls"></a>Vanliga frågor om åtkomst kontroller

[Bevilja eller blockera ](concept-conditional-access-grant.md) 

Vill du bevilja åtkomst till resurser genom att kräva ett eller flera av följande?

* Krav på MFA

* Kräv att enheten ska markeras som kompatibel

* Kräv hybrid Azure AD-ansluten enhet

* Kräv godkänd klientapp

* Kräva appskyddsprincip

[Kontrol lera session](concept-conditional-access-session.md)

Vill du använda någon av följande åtkomst kontroller i molnappar?

* Använd app-framtvingade behörigheter

* Använd Appkontroll för villkorsstyrd åtkomst

* Framtvinga inloggnings frekvens

* Använda beständiga webbläsarbaserade sessioner

### <a name="access-token-issuance"></a>Utfärdande av åtkomsttoken

Det är viktigt att förstå hur åtkomst-token utfärdas. 

![Diagram över utfärdande av åtkomsttoken](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Om ingen tilldelning krävs och ingen princip för villkorlig åtkomst är aktive rad, är standard beteendet att utfärda en åtkomsttoken. 

Anta till exempel en princip där:

Om användaren är i grupp 1 tvingar du MFA att komma åt app 1.

Om en användare som inte finns i grupp 1 försöker komma åt appen "If" är uppfyllt och en token utfärdas. För att utesluta användare utanför grupp 1 kräver en separat princip för att blockera alla andra användare.

## <a name="follow-best-practices"></a>Följ bästa praxis

Med ramverket för villkorlig åtkomst får du en bra flexibel konfiguration. Men stor flexibilitet innebär också att du noga bör granska varje konfigurations princip innan du släpper den för att undvika oönskade resultat.

### <a name="apply-conditional-access-policies-to-every-app"></a>Tillämpa principer för villkorlig åtkomst för alla appar

Åtkomsttoken utfärdas som standard om ett villkor för villkorlig åtkomst inte utlöser en åtkomst kontroll. Se till att alla appar har minst en tillämpad princip för villkorlig åtkomst

> [!IMPORTANT]
> Var försiktig med att använda block och alla appar i en enda princip. Detta kan låsa administratörer från Azure-administrationskonsolen, och undantag kan inte konfigureras för viktiga slut punkter som Microsoft Graph.

### <a name="minimize-the-number-of-conditional-access-policies"></a>Minimera antalet principer för villkorlig åtkomst

Att skapa en princip för varje app är inte effektivt och leder till svårt administration. Villkorlig åtkomst tillämpar bara de första 195 principerna per användare. Vi rekommenderar att du analyserar dina appar och grupperar dem till program som har samma resurs krav för samma användare. Om till exempel alla Microsoft 365 appar eller alla HR-appar har samma krav för samma användare, skapar du en enskild princip och inkluderar alla appar som den gäller för. 

### <a name="set-up-emergency-access-accounts"></a>Konfigurera åtkomst konton för nöd situationer

Om du konfigurerar en princip felaktigt kan den låsa organisationerna från Azure Portal. Minska effekten av oavsiktligt administratörs lås genom att skapa två eller fler [konton för nöd åtkomst](../roles/security-emergency-access.md) i din organisation.

* Skapa ett användar konto dedikerat för princip administration och exkluderas från alla dina principer.

### <a name="set-up-report-only-mode"></a>Konfigurera endast rapport läge

Det kan vara svårt att förutsäga antalet och namnen på de användare som påverkas av vanliga distributions initiativ, till exempel:

* blockerar äldre autentisering
* kräver MFA
* implementera inloggnings risk principer

Med [endast rapport läge](concept-conditional-access-report-only.md) kan administratörer utvärdera effekten av principer för villkorlig åtkomst innan de aktive ras i deras miljö.

Lär dig hur du [konfigurerar läge för endast rapporter i en princip för villkorlig åtkomst](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Planera för avbrott

Om du förlitar dig på en enskild åtkomst kontroll, till exempel MFA eller en nätverks plats, för att skydda dina IT-system, är du utsatt för åtkomst till problem om den enskilda åtkomst kontrollen blir otillgänglig eller felkonfigurerad. För att minska risken för utelåsning under oförutsedda avbrott, [Planera strategier](../authentication/concept-resilient-controls.md) för att införa för din organisation.

### <a name="set-naming-standards-for-your-policies"></a>Ange namngivnings standarder för dina principer

Namngivnings standarden hjälper dig att hitta principer och förstå deras syfte utan att öppna dem i Azure admin-portalen. Vi rekommenderar att du namnger principen för att visa:

* Ett sekvensnummer

* De moln program som den gäller för

* Svaret

* Vem det gäller för

* När det gäller (om tillämpligt)

![Skärm bild som visar namngivnings standarder för principer.](media/plan-conditional-access/11.png)

**Exempel**; En princip för att kräva MFA för marknadsförings användare som har åtkomst till Dynamics CRP-appen från externa nätverk kan vara:

![Namngivnings standard](media/plan-conditional-access/naming-example.png)

Ett beskrivande namn hjälper dig att hålla en översikt över implementeringen av villkorlig åtkomst. Sekvensnumret är användbart om du behöver referera till en princip i en konversation. När du till exempel pratar med en administratör på telefonen kan du be dem att öppna princip CA01 för att lösa ett problem.

#### <a name="naming-standards-for-emergency-access-controls"></a>Namngivnings standarder för kontroller för nöd åtkomst

Förutom dina aktiva principer implementerar du inaktiverade principer som fungerar som sekundära [elastiska åtkomst kontroller i avbrott eller nöd situationer](../authentication/concept-resilient-controls.md). Din namngivnings standard för katastrof principerna bör innehålla:
* Aktivera i nödfall i början för att få namnet att stå ut bland de andra principerna.

* Namnet på det avbrott som det ska gälla för.

* Ett ordnings ordnings nummer som hjälper administratören att veta i vilka ordnings principer ska aktive ras.

**Exempel**

Följande namn anger att den här principen är den första av fyra principer som ska aktive ras om det uppstår ett MFA-avbrott:

EM01 – aktivera i nödfall: MFA-avbrott [1/4]-Exchange SharePoint: Kräv hybrid Azure AD-anslutning för VIP-användare.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Uteslut länder från vilka du aldrig förväntar dig en inloggning.

Med Azure Active Directory kan du skapa [namngivna platser](location-condition.md). Skapa en namngiven plats som innehåller alla länder från vilka du aldrig förväntar dig att en inloggning ska ske. Skapa sedan en princip för alla appar som blockerar inloggning från den namngivna platsen. **Se till att undanta dina administratörer från den här principen**.

### <a name="plan-your-policy-deployment"></a>Planera princip distributionen

När nya principer är klara för din miljö, se till att du granskar varje princip innan du släpper den för att undvika oönskade resultat.

## <a name="common-policies"></a>Vanliga principer

När du planerar din lösning för princip för villkorlig åtkomst ska du utvärdera om du behöver skapa principer för att uppnå följande resultat.

* [Krav på MFA](#require-mfa)
* [Svara på potentiellt komprometterade konton](#respond-to-potentially-compromised-accounts)
* [Kräv hanterade enheter](#require-managed-devices)
* [Kräv godkända klient program](#require-approved-client-apps)
* [Blockera åtkomst](#block-access)

### <a name="require-mfa"></a>Krav på MFA

Vanliga användnings fall för att kräva MFA-åtkomst:

* [Av administratörer](howto-conditional-access-policy-admin-mfa.md)

* [Till vissa appar](../authentication/tutorial-enable-azure-mfa.md)

* [För alla användare](howto-conditional-access-policy-all-users-mfa.md)

* [Från nätverks platser är du inte betrodd](untrusted-networks.md)

* [För Azure-hantering](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Svara på potentiellt komprometterade konton

Med principer för villkorlig åtkomst kan du implementera automatiserade svar på inloggningar av potentiellt komprometterade identiteter. Sannolikheten för att ett konto har komprometterats uttrycks i form av risk nivåer. Det finns två risk nivåer som beräknas med identitets skydd: inloggnings risker och användar risker. Följande tre standard principer som kan aktive ras.

* [Kräv att alla användare registrerar sig för MFA](howto-conditional-access-policy-risk.md)

* [Kräv en lösen ords ändring för användare med hög risk](howto-conditional-access-policy-risk.md)

* [Kräv MFA för användare med medelhög eller hög inloggnings risk](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Kräv hanterade enheter

Spridningen av enheter som stöds för att komma åt moln resurser hjälper till att förbättra användarnas produktivitet. Du vill förmodligen inte att vissa resurser i din miljö ska kunna nås av enheter med en okänd skydds nivå. För dessa resurser [kräver att användarna bara kan komma åt dem med hjälp av en hanterad enhet](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Kräva godkända klientappar

Anställda använder sina mobila enheter för både personliga och arbetsrelaterade uppgifter. För BYOD-scenarier måste du bestämma om du vill hantera hela enheten eller bara data på den. Om du bara hanterar data och åtkomst kan du [kräva godkända molnappar](app-based-conditional-access.md) som kan skydda företagets data. Du kan till exempel kräva att e-post bara kan nås via Outlook Mobile och inte via ett allmänt e-postprogram.

### <a name="block-access"></a>Blockera åtkomst

Alternativet att [Blockera all åtkomst](howto-conditional-access-policy-block-access.md) är kraftfullt. Den kan användas, till exempel när du migrerar en app till Azure AD, men är inte redo för att någon ska kunna logga in till den ännu. Blockera åtkomst: 

* Åsidosätter alla andra tilldelningar för en användare

* Har kraften att blockera hela organisationen från att logga in på din klient organisation

> [!IMPORTANT]
> Om du skapar en princip för att blockera åtkomst för alla användare, se till att undanta åtkomst konton för nöd situationer och Överväg att undanta alla administratörer från principen.

Andra vanliga scenarier där du kan blockera åtkomst för dina användare är:

* [Blockera vissa nätverks platser](howto-conditional-access-policy-location.md) för att komma åt dina molnappar. Du kan använda den här principen för att blockera vissa länder där du vet att trafiken inte kommer från.

* Azure AD stöder äldre autentisering. Äldre autentisering stöder dock inte MFA och många miljöer kräver det för att hantera identitets säkerhet. I det här fallet kan du [blockera appar med hjälp av äldre autentisering ](block-legacy-authentication.md) från att komma åt dina klient resurser.

## <a name="build-and-test-policies"></a>Bygga och testa principer

I varje steg i distributionen ser du till att du bedömer att resultaten är som förväntat. 

När nya principer är klara kan du distribuera dem i faser i produktions miljön:

* Tillhandahålla intern ändrings kommunikation till slutanvändare.

* Börja med en liten uppsättning användare och kontrol lera att principen fungerar som förväntat.

* När du expanderar en princip för att inkludera fler användare fortsätter du att undanta alla administratörer. Om du undantar administratörer ser det till att någon fortfarande har åtkomst till en princip om en ändring krävs.

* Tillämpa bara en princip för alla användare när den har testats grundligt. Se till att du har minst ett administratörs konto som en princip inte tillämpas på.

### <a name="create-test-users"></a>Skapa test användare

Skapa en uppsättning test användare som återspeglar användarna i produktions miljön. Genom att skapa test användare kan du kontrol lera att principerna fungerar som förväntat innan du påverkar riktiga användare och kan störa deras åtkomst till appar och resurser.

Vissa organisationer har test klienter för detta ändamål. Det kan dock vara svårt att återskapa alla villkor och appar i en test klient för att fullständigt testa resultatet av en princip.

### <a name="create-a-test-plan"></a>Skapa en test plan

Test planen är viktig för att få en jämförelse mellan de förväntade resultaten och de faktiska resultaten. Du bör alltid ha en förväntad händelse innan du testar något. I följande tabell visas exempel test fall. Justera scenarier och förväntade resultat baserat på hur dina principer för villkorlig åtkomst konfigureras.

| Policy| Scenario| Förväntat resultat |
| - | - | - |
| [Kräv MFA när det inte är på arbetet](untrusted-networks.md)| Auktoriserad användare loggar in på appen på en betrodd plats/ett arbete| Användaren uppmanas inte att MFA |
| [Kräv MFA när det inte är på arbetet](untrusted-networks.md)| Auktoriserad användare loggar in på appen men inte på en betrodd plats/arbete| Användaren uppmanas att MFA och kan logga in |
| [Kräv MFA (för admin)](../fundamentals/concept-fundamentals-security-defaults.md)| Global administratör loggar in på appen| Administratör uppmanas att MFA |
| [Riskfyllda inloggningar](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Användaren loggar in på appen med en webbläsare som inte har godkänts| Administratör uppmanas att MFA |
| [Enhetshantering](require-managed-devices.md)| Behörig användare försöker logga in från en auktoriserad enhet| Åtkomst beviljad |
| [Enhetshantering](require-managed-devices.md)| Behörig användare försöker logga in från en oauktoriserad enhet| Åtkomst blockerad |
| [Lösen ords ändring för riskfyllda användare](../identity-protection/howto-identity-protection-configure-risk-policies.md)| Behörig användare försöker logga in med komprometterade autentiseringsuppgifter (hög risk inloggning)| Användaren uppmanas att ändra lösen ordet eller åtkomsten blockeras baserat på principen |


### <a name="configure-the-test-policy"></a>Konfigurera test principen

I [Azure Portal](https://portal.azure.com/)konfigurerar du principer för villkorlig åtkomst under Azure Active Directory > säkerhet > villkorlig åtkomst.

Om du vill veta mer om hur du skapar principer för villkorlig åtkomst kan du se det här exemplet: [princip för villkorlig åtkomst för att begära MFA när en användare loggar in till Azure Portal](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Den här snabb starten hjälper dig att:

* Bekanta dig med användar gränssnittet

* Få en första överblick över hur villkorlig åtkomst fungerar

### <a name="enable-the-policy-in-report-only-mode"></a>Aktivera principen i endast rapport läge

Om du vill utvärdera effekten av principen börjar du med att aktivera principen i [endast rapport läge](concept-conditional-access-report-only.md). Endast rapport principer utvärderas under inloggningen, men tilldelnings-och session kontroller tillämpas inte. När du har sparat principen i endast rapport läge kan du se effekten på inloggnings loggarna i real tid i inloggnings loggarna. I inloggnings loggarna väljer du en händelse och navigerar till fliken endast rapporter för att se resultatet av varje rapport princip.


![endast rapport läge ](media/plan-conditional-access/report-only-mode.png)

Genom att välja principen kan du också se hur tilldelnings-och åtkomst kontrollerna för principen utvärderades med hjälp av skärmen princip information. För att en princip ska kunna tillämpas på en inloggning måste var och en av de konfigurerade tilldelningarna vara uppfyllda. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Förstå konsekvenserna av dina principer med hjälp av arbets boken insikter och rapportering

Du kan visa den sammanlagda effekten av dina principer för villkorlig åtkomst i arbets boken insikter och rapportering. För att få åtkomst till arbets boken behöver du en Azure Monitor prenumeration och du måste [strömma dina inloggnings loggar till en Log Analytics arbets yta](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simulera inloggningar med hjälp av verktyget verktyg

Ett annat sätt att verifiera din princip för villkorlig åtkomst är att använda [verktyget verktyg](troubleshoot-conditional-access-what-if.md), som simulerar vilka principer som gäller för en användare som loggar in under hypotetiska omständigheter. Välj de inloggnings attribut som du vill testa (till exempel användare, program, enhets plattform och plats) och se vilka principer som gäller.

> [!NOTE] 
> Medan en simulerad körning ger dig en bra uppfattning om hur en princip för villkorlig åtkomst har, ersätter den inte en faktisk test körning.

### <a name="test-your-policy"></a>Testa principen

Utför varje test i test planen med test användare.

**Se till att du testar undantags villkoren för en princip**. Du kan till exempel utesluta en användare eller grupp från en princip som kräver MFA. Testa om de exkluderade användarna uppmanas att använda MFA, eftersom kombinationen av andra principer kan kräva MFA för dessa användare.

### <a name="roll-back-policies"></a>Återställa principer

Om du behöver återställa dina nyligen implementerade principer kan du använda ett eller flera av följande alternativ:

* **Inaktivera principen.** Genom att inaktivera en princip ser du till att den inte gäller när en användare försöker logga in. Du kan alltid komma tillbaka och aktivera principen när du vill använda den.

![Aktivera princip avbildning](media/plan-conditional-access/enable-policy.png)

* **Undanta en användare eller grupp från en princip.** Om en användare inte kan komma åt appen kan du välja att undanta användaren från principen.

![Exkludera användare och grupper](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Det här alternativet bör användas sparsamt, endast i situationer där användaren är betrodd. Användaren ska läggas till i principen eller gruppen igen så snart som möjligt.

* **Ta bort principen.** Om principen inte längre behövs [tar du bort](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) den.

## <a name="manage-access-to-cloud-apps"></a>Hantera åtkomst till molnappar

Använd följande hanterings alternativ för att kontrol lera och hantera principer för villkorlig åtkomst:

![Skärm bild som visar HANTERINGs alternativen för C A-principer, inklusive namngivna platser, anpassade kontroller, Användningsvillkor, V P N-anslutning och de valda klassiska principerna.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Namngivna platser

Plats villkoret för en princip för villkorlig åtkomst gör att du kan använda inställningar för åtkomst kontroll till användarnas nätverks platser. Med [namngivna platser](location-condition.md)kan du skapa logiska grupperingar av IP-adressintervall eller länder och regioner.

### <a name="custom-controls"></a>Anpassade kontroller

[Anpassade kontroller](controls.md) omdirigera dina användare till en kompatibel tjänst för att uppfylla autentiseringskrav utanför Azure AD. För att tillfredsställa den här kontrollen omdirigeras användarens webbläsare till den externa tjänsten, utför alla nödvändiga autentiseringar och omdirigeras sedan tillbaka till Azure AD. Azure AD verifierar svaret och, om användaren har autentiserats eller verifierats, fortsätter användaren i flödet för villkorlig åtkomst.

### <a name="terms-of-use"></a>Villkor för användning

Innan du får åtkomst till vissa molnappar i din miljö kan du få tillåtelse från användarna genom att acceptera din Användningsvillkor (ToU). Följ den här [snabb starten för att skapa](require-tou.md)användnings villkoren.

## <a name="troubleshoot-conditional-access"></a>Felsöka villkorlig åtkomst

När en användare har problem med en princip för villkorlig åtkomst samlar du in följande information för att under lätta fel sökningen.

* Användar princip namn

* Användarens visnings namn

* Operativ systemets namn

* Tidstämpel (ungefärlig är OK)

* Mål program

* Klient program typ (webbläsare vs-klient)

* Korrelations-ID (detta är unikt för inloggningen)

Om användaren fick ett meddelande med en mer informations länk kan de samla in merparten av den här informationen åt dig.

![Fel meddelandet "det går inte att komma till appen"](media/plan-conditional-access/cant-get-to-app.png)

När du har samlat in informationen, se följande resurser:

* [Inloggnings problem med villkorlig åtkomst](troubleshoot-conditional-access.md) – förstå oväntade inloggnings resultat relaterade till villkorlig åtkomst med hjälp av fel meddelanden och inloggnings loggen för Azure AD.

* [Använd What-If-verktyget för att](troubleshoot-conditional-access-what-if.md) förstå varför en princip har eller inte tillämpats på en användare under en viss omständighet eller om en princip skulle gälla i ett känt tillstånd.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Lär dig mer om identitets skydd](../identity-protection/overview-identity-protection.md)

[Hantera principer för villkorlig åtkomst med Microsoft Graph-API](/graph/api/resources/conditionalaccesspolicy)
