---
title: SWIFT-CSCF v2020 skiss exempel kontroller
description: Kontroll mappning för SWIFT-CSCF v2020 skiss-exemplet. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 466b37b287d79a891fba39b133ab5fa79dbd147e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98048801"
---
# <a name="control-mapping-of-the-swift-csp-cscf-v2020-blueprint-sample"></a>Kontroll mappning för SWIFT CSP-CSCF v2020 skiss-exempel

Följande artikel beskriver hur Azure-skisser SWIFT-CSCF v2020 skiss-exemplet mappar till SWIFT-CSP-CSCF v2020-kontroller. Mer information om kontrollerna finns i [Swift CSP-CSCF v2020](https://www.swift.com/myswift/customer-security-programme-csp).

Följande mappningar är till **Swift-CSCF v2020-** kontroller. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[ förhands granskning \] : granska Swift CSP-CSCF v2020-kontroller och distribuera särskilda VM-tillägg så att de stöder gransknings krav** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/swift-2020/control-mapping.md).

## <a name="12-and-51-account-management"></a>1,2 och 5,1 konto hantering

Den här skissen hjälper dig att granska konton som kanske inte uppfyller organisationens krav på konto hantering. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som granskar externa konton med Läs-, skriv-och ägar behörigheter för en prenumeration och föråldrade konton. Genom att granska de konton som granskas av dessa principer kan du vidta lämpliga åtgärder för att säkerställa att konto hanterings kraven är uppfyllda.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration

## <a name="26-51-64-and-65a-account-management--role-based-schemes"></a>2,6, 5,1, 6,4 och 6.5 A konto hantering | Role-Baseds scheman

[Rollbaserad åtkomst kontroll i Azure](../../../../role-based-access-control/overview.md) (Azure RBAC) som hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också [Azure policy](../../../policy/overview.md) definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster. Dessutom tilldelar den här skissen en Azure Policy-definition för att granska användningen av anpassade Azure RBAC-regler. Att förstå var anpassade Azure RBAC-regler implementeras kan hjälpa dig att kontrol lera behovet och korrekt implementering, eftersom anpassade Azure RBAC-regler är fel känsliga.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Granska virtuella datorer som inte använder hanterade diskar
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering

## <a name="29a--account-management--account-monitoring--atypical-usage"></a>2.9 a konto hantering | Konto övervakning/ovanlig-användning

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Alla JIT-begäranden för att komma åt virtuella datorer loggas i aktivitets loggen så att du kan övervaka ovanlig-användning. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men som ännu inte har kon figurer ATS.

- Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst

## <a name="13-51-and-64-separation-of-duties"></a>1,3, 5,1 och 6,4 separering av uppgifter

Att ha bara en Azure-prenumerations ägare tillåter inte administrativ redundans. Att ha för många Azure-prenumerationer kan däremot öka risken för intrång via ett komprometterat ägar konto. Den här skissen hjälper dig att underhålla ett lämpligt antal prenumerations ägare i Azure genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också Azure Policy definitioner som hjälper dig att styra medlemskap i gruppen Administratörer på virtuella Windows-datorer. Att hantera prenumerations ägare och administratörs behörighet för virtuell dator kan hjälpa dig att implementera lämplig uppdelning av uppgifter.

- Högst 3 ägare bör anges för din prenumeration
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="13-51-and-64-least-privilege--review-of-user-privileges"></a>1,3, 5,1 och 6,4 minsta behörighet | Granskning av användar behörigheter

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) hjälper dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner till gransknings konton som ska prioriteras för granskning. Att granska dessa konto indikatorer kan hjälpa dig att se till att minst behörighets kontroller implementeras.

- Högst 3 ägare bör anges för din prenumeration
- Visa gransknings resultat från virtuella Windows-datorer som inte är anslutna till den angivna domänen
- Distribuera krav för att granska virtuella Windows-datorer som inte är anslutna till den angivna domänen
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="22-and-27-security-attributes"></a>Säkerhetsattributen 2,2 och 2,7

Funktionerna för data identifiering och klassificering av avancerad data säkerhet för Azure SQL Database ger funktioner för att upptäcka, klassificera, märka och skydda känsliga data i dina databaser. Det kan användas för att ge insyn i tillståndet för din databasklassificering samt för att spåra åtkomst till känsliga data i och utanför databasen. Avancerad data säkerhet kan hjälpa dig att se till att informationen är associerad med lämpliga säkerhetsattribut för din organisation. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner för att övervaka och upprätthålla användningen av avancerad data säkerhet på SQL Server.

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar

## <a name="22-27-41-and-61-remote-access--automated-monitoring--control"></a>2,2, 2,7, 4,1 och 6,1 fjärråtkomst | Automatiserad övervakning/kontroll

Den här skissen hjälper dig att övervaka och kontrol lera fjärråtkomst genom att tilldela [Azure policy](../../../policy/overview.md) definitioner till Övervakare som fjärrfelsökning för Azure App Service program är inaktive rad och princip definitioner som granskar virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord. Den här skissen tilldelar också en Azure Policy definition som hjälper dig att övervaka obegränsad åtkomst till lagrings konton. Genom att övervaka dessa indikatorer kan du se till att du ser till att fjärranslutna metoder överensstämmer med din säkerhets princip.

- Visa gransknings resultat från virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Distribuera förutsättningar för att granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Lagrings konton bör begränsa nätverks åtkomsten
- Fjärrfelsökning bör inaktive ras för API-appen
- Fjärrfelsökning bör inaktive ras för Funktionsapp
- Fjärrfelsökning bör inaktive ras för webb program

## <a name="13-and-64-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>1,3 och 6,4 innehåll i gransknings poster | Centraliserad hantering av innehåll för planerad gransknings post

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta som möjliggör centraliserad konfiguration och hantering. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar och tillämpar distribution av log Analyticss agenten på virtuella Azure-datorer.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Distribuera Log Analytics agent för virtuella Linux-datorer
- Distribuera Log Analytics agent för virtuella Windows-datorer

## <a name="22-27-and-64-response-to-audit-processing-failures"></a>2,2, 2,7 och 6,4 svar på gransknings bearbetnings problem

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som övervakar konfigurationer för granskning och händelse loggning. Övervakning av de här konfigurationerna kan ge en indikator för ett fel eller en felaktig konfiguration av systemet och hjälpa dig att vidta lämpliga åtgärder.

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Granska diagnostikinställning
- Granskning på SQL Server måste vara aktiverat

## <a name="13-and-64-audit-review-analysis-and-reporting--central-review-and-analysis"></a>1,3 och 6,4 gransknings granskning, analys och rapportering | Central granskning och analys

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta som möjliggör central rapportering och analys. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar och tillämpar distribution av log Analyticss agenten på virtuella Azure-datorer.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Distribuera Log Analytics agent för virtuella Linux-datorer
- Distribuera Log Analytics agent för virtuella Windows-datorer

## <a name="13-22-27-64-and-65a-audit-generation"></a>1,3, 2,2, 2,7, 6,4 och 6.5 en gransknings generation

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser.
Dessa princip definitioner granskar och tillämpar distributionen av Log Analytics agent på Azure Virtual Machines och konfigurationen av gransknings inställningar för andra Azure-resurs typer. Dessa princip definitioner granskar också konfigurationen av diagnostikloggar för att ge inblick i åtgärder som utförs i Azure-resurser. Dessutom konfigureras granskning och avancerad data säkerhet på SQL-servrar.

- Granska Log Analytics agent distribution – VM-avbildning (OS) har inte listats
- Distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- Distribuera Log Analytics agent för virtuella Linux-datorer
- Distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- Distribuera Log Analytics agent för virtuella Windows-datorer
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Granskning på SQL Server måste vara aktiverat
- Distribuera diagnostikinställningar för nätverks säkerhets grupper

## <a name="11-least-functionality--prevent-program-execution"></a>1,1 lägsta funktionalitet | Förhindra program körning

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Program kontroll kan köras i ett tvingande läge som förhindrar att icke-godkända program körs. Den här skissen tilldelar en Azure Policy-definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer

## <a name="11-least-functionality--authorized-software--whitelisting"></a>1,1 lägsta funktionalitet | Auktoriserad program vara/vit listning

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Med program kontroll kan du skapa godkända program listor för dina virtuella datorer. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer

## <a name="11-user-installed-software"></a>1,1 User-Installed program vara

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Program kontroll kan hjälpa dig att upprätthålla och övervaka efterlevnaden av principer för begränsning av program vara. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller för att definiera säkra program ska aktive ras på dina datorer
- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser

## <a name="42-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>4,2 identifiering och autentisering (organisations användare) | Nätverks åtkomst till privilegierade konton

Den här skissen hjälper dig att begränsa och kontrol lera privilegie rad åtkomst genom att tilldela [Azure policy](../../../policy/overview.md) definitioner för granskning av konton med ägare och/eller Skriv behörigheter som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med skrivbehörigheter för din prenumeration

## <a name="42-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>4,2 identifiering och autentisering (organisations användare) | Nätverks åtkomst till konton som inte är privilegierade

Den här skissen hjälper dig att begränsa och kontrol lera åtkomst genom att tilldela en [Azure policy](../../../policy/overview.md) -definition till gransknings konton med Läs behörighet som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

## <a name="23-and-41-authenticator-management"></a>2,3 och 4,1 Authenticator-hantering

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som granskar virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord och/eller har felaktiga behörigheter som angetts för passwd-filen. Den här skissen tilldelar också princip definitioner som granskar konfigurationen av lösen ords krypterings typen för virtuella Windows-datorer. Genom att övervaka dessa indikatorer kan du se till att system autentiserare följer organisationens principer för identifiering och autentisering.

- Visa gransknings resultat från virtuella Linux-datorer som inte har passwd-filbehörigheterna inställt på 0644
- Distribuera krav för att granska virtuella Linux-datorer som inte har passwd-filbehörigheterna inställt på 0644
- Visa gransknings resultat från virtuella Linux-datorer som har konton utan lösen ord
- Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösen ord
- Visa gransknings resultat från virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering
- Distribuera krav för att granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering

## <a name="23-and-41-authenticator-management--password-based-authentication"></a>Hantering av 2,3 och 4,1-autentisering | Password-Based autentisering

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar virtuella Windows-datorer som inte kräver lägsta möjliga styrka och andra lösen ords krav. Medvetenheten om virtuella datorer som strider mot principen för lösen ords styrka hjälper dig att vidta åtgärder för att se till att lösen ord för alla virtuella dator användar konton följer organisationens lösen ords princip.

- Visa gransknings resultat från virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- Visa gransknings resultat från virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- Visa gransknings resultat från virtuella Windows-datorer som inte har minsta ålder på lösen ord på 1 dag
- Visa gransknings resultat från virtuella Windows-datorer där inställningen för lösen ords komplexitet är aktive rad
- Visa gransknings resultat från virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- Visa gransknings resultat från virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering
- Distribuera krav för att granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- Distribuera krav för att granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- Distribuera krav för att granska virtuella Windows-datorer som inte har en minsta ålder på lösen ord på 1 dag
- Distribuera krav för att granska virtuella Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat
- Distribuera krav för att granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- Distribuera krav för att granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering

## <a name="22-and-27-vulnerability-scanning"></a>2,2 och 2,7 sårbarhets sökning

Den här skissen hjälper dig att hantera problem med informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar sårbarheter för operativ system, sårbarheter i SQL och säkerhets risker för virtuella datorer i Azure Security Center.
Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser. Den här skissen tilldelar också princip definitioner som granskar och tillämpar avancerad data säkerhet på SQL-servrar. Avancerad data säkerhet inkluderar sårbarhets bedömning och avancerade hot skydds funktioner som hjälper dig att förstå sårbarheter i dina distribuerade resurser.

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Granskning på SQL Server måste vara aktiverat
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas 
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas

## <a name="13-denial-of-service-protection"></a>1,3 denial of service-skydd

Azures standard nivå för DDoS (distributed denial of Service) tillhandahåller ytterligare funktioner och funktioner för att minska risken för den grundläggande tjänst nivån. Bland dessa ytterligare funktioner ingår Azure Monitor-integrering och möjligheten att gå igenom rapporter efter angrepps minskning. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som revisioner om standard nivån för DDoS är aktive rad. Att förstå funktions skillnaden mellan tjänst nivåerna kan hjälpa dig att välja den bästa lösningen för att åtgärda denial of service Protection för din Azure-miljö.

- Azure DDoS Protection standard ska vara aktive rad

## <a name="11-and-61-boundary-protection"></a>1,1 och 6,1 gränser för skydd

Den här skissen hjälper dig att hantera och kontrol lera system gränser genom att tilldela en [Azure policy](../../../policy/overview.md) -definition som övervakar för nätverks säkerhets grupps härdnings rekommendationer i Azure Security Center. Azure Security Center analyserar trafik mönster för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper för att minska risken för angrepp. Dessutom tilldelar skissen princip definitioner som övervakar oskyddade slut punkter, program och lagrings konton. Slut punkter och program som inte skyddas av en brand vägg och lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet.

- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet
- Åtkomst via slut punkt mot Internet bör vara begränsad
- Granska obegränsad nätverks åtkomst till lagrings konton

## <a name="29a-boundary-protection--access-points"></a>2.9 ett gränser skydd | Åtkomst punkter

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Åtkomst till virtuella JIT-datorer hjälper dig att begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men som ännu inte har kon figurer ATS.

- Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst

## <a name="29a-boundary-protection--external-telecommunications-services"></a>2.9 ett gränser skydd | Externa telekommunikations tjänster

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Åtkomst till JIT-fjärråtkomst hjälper dig att hantera undantag till trafik flödes principen genom att under lätta åtkomst förfrågningar och godkännande processer. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men som ännu inte har kon figurer ATS.

- Hanterings portar för virtuella datorer bör skyddas med just-in-Time-kontroll för nätverks åtkomst

## <a name="21-24-24a-25a-and-26-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>2,1, 2,4, 2,4 a, 2.5 A, och 2,6 överförings konfidentialitet och integritet | Kryptografiskt eller alternativt fysiskt skydd

Den här skissen hjälper dig att skydda konfidentiell och integritet på överförd information genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka kryptografisk mekanism som implementerats för kommunikations protokoll. Att säkerställa att kommunikationen är korrekt krypterad kan hjälpa dig att uppfylla organisationens krav eller skydda information från obehörigt utlämnande av information och ändringar.

- API-appen bör bara vara tillgänglig via HTTPS
- Visa gransknings resultat från Windows-webbservrar som inte använder säkra kommunikations protokoll
- Distribuera förutsättningar för att granska Windows-webbservrar som inte använder säkra kommunikations protokoll
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Säker överföring till lagringskonton ska vara aktiverat
- Webb program bör endast vara tillgängliga via HTTPS

## <a name="22-23-25-41-and-27-protection-of-information-at-rest--cryptographic-protection"></a>2,2, 2,3, 2,5, 4,1 och 2,7 skydd av information i vila | Kryptografiskt skydd

Den här skissen hjälper dig att genomdriva principen om användningen av kryptografiska kontroller för att skydda informationen i vila genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som tillämpar vissa kryptografiska kontroller och granska användningen av svaga kryptografiska inställningar. Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver princip definitionerna som tilldelas av den här skissen kryptering för data Lake Storage-konton. Kräv transparent data kryptering på SQL-databaser; och granska saknad kryptering på SQL-databaser, virtuella dator diskar och variabler för Automation-konto.

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Distribuera transparent data kryptering för SQL DB
- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="13-22-and-27-flaw-remediation"></a>1,3, 2,2 och 2,7 fel reparation

Den här skissen hjälper dig att hantera fel i informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade system uppdateringar, sårbarheter för operativ system, sårbarheter i SQL och virtuella dator sårbarheter i Azure Security Center. Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser. Den här skissen tilldelar också en princip definition som garanterar korrigering av operativ systemet för skalnings uppsättningar för virtuella datorer.

- Kräv automatisk uppdatering av operativ system avbildningar på Virtual Machine Scale Sets
- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras
- System uppdateringar bör installeras på dina virtuella datorer
- Granska beroende agent distribution i skalnings uppsättningar för virtuella datorer – VM-avbildning (OS) har inte listats
- Variabler för Automation-konton ska vara krypterade
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på dina virtuella datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas

## <a name="61-malicious-code-protection"></a>6,1 skadlig kod skydd

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakas för saknade Endpoint Protection på virtuella datorer i Azure Security Center och tillämpa Microsoft Antimalware-lösningen på virtuella Windows-datorer.

- Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center
- Lagrings konton ska migreras till nya Azure Resource Manager resurser

## <a name="61-malicious-code-protection--central-management"></a>6,1 skadlig kod skydd | Central hantering

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar för saknade Endpoint Protection på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserade hanterings-och rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser.

- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="11-13-22-27-28-and-64-information-system-monitoring"></a>1,1, 1,3, 2,2, 2,7, 2,8 och 6,4 information system övervakning

Den här skissen hjälper dig att övervaka systemet genom att granska och framtvinga loggning och data säkerhet i Azure-resurser. Mer specifikt är principerna tilldelade granskning och tillämpar distribution av Log Analytics agenten och förbättrade säkerhets inställningar för SQL-databaser, lagrings konton och nätverks resurser. Dessa funktioner kan hjälpa dig att identifiera avvikande beteende och indikatorer på attacker så att du kan vidta lämpliga åtgärder.

- Visa gransknings resultat från virtuella Windows-datorer där Log Analytics-agenten inte är ansluten som förväntat
- Distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- Distribuera Log Analytics agent för virtuella Linux-datorer
- Distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- Distribuera Log Analytics agent för virtuella Windows-datorer
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad
- Distribuera avancerad data säkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera nätverks övervakare när virtuella nätverk skapas
- Distribuera hot identifiering på SQL-servrar

## <a name="22-and-28-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>2,2 och 2,8 information system övervakning | Analysera trafik/konvertera exfiltrering

Avancerat skydd för Azure Storage identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Skydds aviseringar innehåller avvikande åtkomst mönster, avvikande extrakt/uppladdningar och misstänkt lagrings aktivitet. Dessa indikatorer kan hjälpa dig att identifiera konverterad exfiltrering av information.

- Distribuera hot identifiering på SQL-servrar

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln.

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av SWIFT CSP-CSCF v2020-skissen kan du gå till följande artiklar för att lära dig om skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [Swift CSP – CSCF v2020 skiss – översikt](./index.md) 
>  [Swift CSP – CSCF v2020 skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
