---
title: IRS 1075-skiss exempel kontroller
description: Kontroll mappning för IRS 1075-skiss exemplet. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 08ce69d13f201e6f0bd1d649ebfe3bbf5c38f927
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98045231"
---
# <a name="control-mapping-of-the-irs-1075-blueprint-sample"></a>Kontroll mappning för IRS 1075-skiss exemplet

I följande artikel beskrivs hur Azure Skisss IRS 1075 skiss-exemplet mappar till IRS 1075-kontrollerna. Mer information om kontrollerna finns i [IRS 1075](https://www.irs.gov/pub/irs-pdf/p1075.pdf).

Följande mappningar är till **IRS 1075** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj för **\[ hands versionen \] : granska IRS 1075-kontroller och distribuera särskilda VM-tillägg för att ge stöd för gransknings krav** inbyggda princip initiativ.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/irs-1075/control-mapping.md).

## <a name="9321-ac-2-account-management"></a>9.3.2.1 AC-2-konto hantering

Den här skissen hjälper dig att granska konton som kanske inte uppfyller organisationens krav på konto hantering. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som granskar externa konton med Läs-, skriv-och ägar behörigheter för en prenumeration och föråldrade konton. Genom att granska de konton som granskas av dessa principer kan du vidta lämpliga åtgärder för att säkerställa att konto hanterings kraven är uppfyllda.

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Läs behörighet bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration

## <a name="9321-ac-2-7-account-management--role-based-schemes"></a>9.3.2.1 AC-2 (7) konto hantering | Role-Baseds scheman

Azure implementerar [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../../../role-based-access-control/overview.md) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar också [Azure policy](../../../policy/overview.md) definitioner för att granska användningen av Azure Active Directory autentisering för SQL-servrar och Service Fabric. Med hjälp av Azure Active Directory-autentisering möjliggörs förenklad behörighets hantering och centraliserad identitets hantering för databas användare och andra Microsoft-tjänster. Dessutom tilldelar den här skissen en Azure Policy-definition för att granska användningen av anpassade Azure RBAC-regler. Att förstå var anpassade Azure RBAC-regler implementeras kan hjälpa dig att kontrol lera behovet och korrekt implementering, eftersom anpassade Azure RBAC-regler är fel känsliga.

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar
- Granska användningen av anpassade RBAC-regler
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering

## <a name="9321-ac-2-12-account-management--account-monitoring--atypical-usage"></a>9.3.2.1 AC-2 (12) konto hantering | Konto övervakning/ovanlig-användning

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Alla JIT-begäranden för att komma åt virtuella datorer loggas i aktivitets loggen så att du kan övervaka ovanlig-användning. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men ännu inte har kon figurer ATS.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="9314-ac-4-information-flow-enforcement"></a>9.3.1.4 AC – 4 informations flöde-tvång

Med resurs delning mellan ursprung (CORS) kan App Services resurser begäras från en extern domän. Microsoft rekommenderar att du endast tillåter att domäner interagerar med API-, funktions-och webb program. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka åtkomst begränsningar för CORS-resurser i Azure Security Center. Att förstå CORS-implementeringar kan hjälpa dig att kontrol lera att informations flödes kontrollerna implementeras.

- CORS bör inte tillåta alla resurser åtkomst till ditt webb program

## <a name="9315-ac-5-separation-of-duties"></a>9.3.1.5 AC-5-separering av uppgifter

Att ha bara en Azure-prenumerations ägare tillåter inte administrativ redundans. Att ha för många Azure-prenumerationer kan däremot öka risken för intrång via ett komprometterat ägar konto. Den här skissen hjälper dig att underhålla ett lämpligt antal prenumerations ägare i Azure genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar antalet ägare för Azure-prenumerationer. Den här skissen tilldelar också Azure Policy definitioner som hjälper dig att styra medlemskap i gruppen Administratörer på virtuella Windows-datorer. Att hantera prenumerations ägare och administratörs behörighet för virtuell dator kan hjälpa dig att implementera lämplig uppdelning av uppgifter.

- Högst 3 ägare bör anges för din prenumeration
- Granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="9316-ac-6-7-least-privilege--review-of-user-privileges"></a>9.3.1.6 AC-6 (7) minsta behörighet | Granskning av användar behörigheter

Azure implementerar [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../../../role-based-access-control/overview.md) för att hjälpa dig att hantera vem som har åtkomst till resurser i Azure. Med hjälp av Azure Portal kan du granska vem som har åtkomst till Azure-resurser och deras behörigheter. Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner till gransknings konton som ska prioriteras för granskning. Att granska dessa konto indikatorer kan hjälpa dig att se till att minst behörighets kontroller implementeras.

- Högst 3 ägare bör anges för din prenumeration
- Granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer inte innehåller alla angivna medlemmar
- Det bör finnas fler än en ägare som tilldelats din prenumeration

## <a name="93112-ac-17-1-remote-access--automated-monitoring--control"></a>9.3.1.12 AC-17 (1) fjärråtkomst | Automatiserad övervakning/kontroll

Den här skissen hjälper dig att övervaka och kontrol lera fjärråtkomst genom att tilldela [Azure policy](../../../policy/overview.md) definitioner för att övervaka att fjärrfelsökning för Azure App Service program är inaktive rad. Skissen tilldelar också princip definitioner som granskar virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord. Dessutom tilldelar skissen en Azure Policy definition som hjälper dig att övervaka obegränsad åtkomst till lagrings konton. Genom att övervaka dessa indikatorer kan du se till att du ser till att fjärranslutna metoder överensstämmer med din säkerhets princip.

- \[För hands version \] : granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- \[För hands version \] : Distribuera krav för att granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Granska obegränsad nätverks åtkomst till lagrings konton
- Fjärrfelsökning bör inaktive ras för API-appen
- Fjärrfelsökning bör inaktive ras för Funktionsapp
- Fjärrfelsökning bör inaktive ras för webb program

## <a name="9313-au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>9.3.1.3 AU-3 (2) innehåll i gransknings poster | Centraliserad hantering av innehåll för planerad gransknings post

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta som möjliggör centraliserad konfiguration och hantering. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar och tillämpar distribution av log Analyticss agenten på virtuella Azure-datorer.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[För hands version \] : granska Log Analytics agent distribution i VMSS – VM-avbildningen (OS) har inte listats
- \[För hands version \] : granska Log Analytics arbets yta för VM-rapportera fel
- \[För hands version \] : distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Linux-datorer
- \[För hands version \] : distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Windows-datorer

## <a name="9335-au-5-response-to-audit-processing-failures"></a>9.3.3.5 AU-5 svar på gransknings bearbetnings problem

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som övervakar konfigurationer för granskning och händelse loggning. Övervakning av de här konfigurationerna kan ge en indikator för ett fel eller en felaktig konfiguration av systemet och hjälpa dig att vidta lämpliga åtgärder.

- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar

## <a name="9336-au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>9.3.3.6 AU-6 (4) gransknings granskning, analys och rapportering | Central granskning och analys

Loggdata som samlas in av Azure Monitor lagras i en Log Analytics arbets yta som möjliggör central rapportering och analys. Den här skissen hjälper dig att se till att händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar och tillämpar distribution av log Analyticss agenten på virtuella Azure-datorer.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[För hands version \] : granska Log Analytics agent distribution i VMSS – VM-avbildningen (OS) har inte listats
- \[För hands version \] : granska Log Analytics arbets yta för VM-rapportera fel
- \[För hands version \] : distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Linux-datorer
- \[För hands version \] : distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Windows-datorer

## <a name="93311-au-12-audit-generation"></a>9.3.3.11 AU-12 audit generation

Den här skissen hjälper dig att se till att system händelser loggas genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar logg inställningar på Azure-resurser.
Dessa princip definitioner granskar och tillämpar distributionen av Log Analytics agent på Azure Virtual Machines och konfigurationen av gransknings inställningar för andra Azure-resurs typer. Dessa princip definitioner granskar också konfigurationen av diagnostikloggar för att ge inblick i åtgärder som utförs i Azure-resurser. Dessutom konfigureras granskning och avancerad data säkerhet på SQL-servrar.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[För hands version \] : granska Log Analytics agent distribution i VMSS – VM-avbildningen (OS) har inte listats
- \[För hands version \] : granska Log Analytics arbets yta för VM-rapportera fel
- \[För hands version \] : distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Linux-datorer
- \[För hands version \] : distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Windows-datorer
- Granska diagnostikinställning
- Granska gransknings inställningar för SQL Server-nivå
- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Distribuera granskning på SQL-servrar
- Distribuera diagnostikinställningar för nätverks säkerhets grupper

## <a name="9357-cm-7-2-least-functionality--prevent-program-execution"></a>9.3.5.7 CM – 7 (2) minsta funktioner | Förhindra program körning

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Program kontroll kan köras i ett tvingande läge som förhindrar att icke-godkända program körs. Den här skissen tilldelar en Azure Policy-definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="9357-cm-7-5-least-functionality--authorized-software--whitelisting"></a>9.3.5.7 CM – 7 (5) de minst funktionerna | Auktoriserad program vara/vit listning

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Med program kontroll kan du skapa godkända program listor för dina virtuella datorer. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="93511-cm-11-user-installed-software"></a>9.3.5.11 CM – 11 User-Installed program vara

Adaptiva program kontroller i Azure Security Center är en intelligent, automatiserad program filtrerings lösning från slut punkt till slut punkt som kan blockera eller förhindra att specifika program körs på dina virtuella datorer. Program kontroll kan hjälpa dig att upprätthålla och övervaka efterlevnaden av principer för begränsning av program vara. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer där en lista över tillåtna program rekommenderas men inte har kon figurer ATS än.

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

## <a name="9366-cp-7-alternate-processing-site"></a>9.3.6.6 CP-7 alternativ bearbetnings plats

Azure Site Recovery replikerar arbets belastningar som körs på virtuella datorer från en primär plats till en sekundär plats. Om ett avbrott uppstår på den primära platsen växlar arbets belastningen över den sekundära platsen. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som granskar virtuella datorer utan haveri beredskap konfigurerad. Övervakning av den här indikatorn kan hjälpa dig att se till att de nödvändiga kontrollerna är på plats.

- Granska virtuella datorer utan haveri beredskap har kon figurer ATS

## <a name="9372-ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>9.3.7.2 IA-2 (1) identifiering och autentisering (företags användare) | Nätverks åtkomst till privilegierade konton

Den här skissen hjälper dig att begränsa och kontrol lera privilegie rad åtkomst genom att tilldela [Azure policy](../../../policy/overview.md) definitioner för granskning av konton med ägare och/eller Skriv behörigheter som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- MFA ska vara aktiverat på konton med skrivbehörigheter för din prenumeration

## <a name="9372-ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>9.3.7.2 IA-2 (2) identifiering och autentisering (företags användare) | Nätverks åtkomst till konton som inte är privilegierade

Den här skissen hjälper dig att begränsa och kontrol lera åtkomst genom att tilldela en [Azure policy](../../../policy/overview.md) -definition till gransknings konton med Läs behörighet som inte har Multi-Factor Authentication aktiverat. Multi-Factor Authentication hjälper till att skydda konton även om en viss autentiseringsinformation komprometteras. Genom att övervaka konton utan att Multi-Factor Authentication har Aktiver ATS kan du identifiera konton som kan vara sannolikare.

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

## <a name="9375-ia-5-authenticator-management"></a>9.3.7.5 IA-5 Authenticator-hantering

Den här skissen tilldelar [Azure policy](../../../policy/overview.md) definitioner som granskar virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord och/eller har felaktiga behörigheter som angetts för passwd-filen. Den här skissen tilldelar också princip definitioner som granskar konfigurationen av lösen ords krypterings typen för virtuella Windows-datorer. Genom att övervaka dessa indikatorer kan du se till att system autentiserare följer organisationens principer för identifiering och autentisering.

- \[För hands version \] : granska virtuella Linux-datorer som inte har passwd-filbehörigheterna inställt på 0644
- \[För hands version \] : granska virtuella Linux-datorer som har konton utan lösen ord
- \[För hands version \] : granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering
- \[För hands version \] : Distribuera krav för att granska virtuella Linux-datorer som inte har behörigheterna passwd-fil inställd på 0644
- \[För hands version \] : Distribuera krav för att granska virtuella Linux-datorer som har konton utan lösen ord
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering

## <a name="9375-ia-5-1-authenticator-management--password-based-authentication"></a>9.3.7.5 IA-5 (1) verifierings hantering | Password-Based autentisering

Den här skissen hjälper dig att använda starka lösen ord genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som granskar virtuella Windows-datorer som inte kräver lägsta möjliga styrka och andra lösen ords krav. Medvetenheten om virtuella datorer som strider mot principen för lösen ords styrka hjälper dig att vidta åtgärder för att se till att lösen ord för alla virtuella dator användar konton följer organisationens lösen ords princip.

- \[För hands version \] : granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- \[För hands version \] : granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För hands version \] : granska virtuella Windows-datorer som inte har minsta ålder på lösen ord på 1 dag
- \[För hands version \] : granska virtuella Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat
- \[För hands version \] : granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För hands version \] : granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som tillåter åter användning av de tidigare 24 lösen orden
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte har en högsta ålder för lösen ord på 70 dagar
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte har minsta ålder på lösen ord på 1 dag
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte har inställningen för lösen ords komplexitet aktiverat
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken
- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte lagrar lösen ord med omvänd kryptering

## <a name="93143-ra-5-vulnerability-scanning"></a>9.3.14.3 RA-5 sårbarhets-genomsökning

Den här skissen hjälper dig att hantera problem med informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar sårbarheter för operativ system, sårbarheter i SQL och säkerhets risker för virtuella datorer i Azure Security Center.
Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser. Den här skissen tilldelar också princip definitioner som granskar och tillämpar avancerad data säkerhet på SQL-servrar. Avancerad data säkerhet inkluderar sårbarhets bedömning och avancerade hot skydds funktioner som hjälper dig att förstå sårbarheter i dina distribuerade resurser.

- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på dina virtuella datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="93164-sc-5-denial-of-service-protection"></a>9.3.16.4 SC-5 denial of service Protection

Azures standard nivå för DDoS (distributed denial of Service) tillhandahåller ytterligare funktioner och funktioner för att minska risken för den grundläggande tjänst nivån. Bland dessa ytterligare funktioner ingår Azure Monitor-integrering och möjligheten att gå igenom rapporter efter angrepps minskning. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) definition som revisioner om standard nivån för DDoS är aktive rad. Att förstå funktions skillnaden mellan tjänst nivåerna kan hjälpa dig att välja den bästa lösningen för att åtgärda denial of service Protection för din Azure-miljö.

- DDoS Protection standard ska vara aktive rad

## <a name="93165-sc-7-boundary-protection"></a>9.3.16.5 SC-7 gränser skydd

Den här skissen hjälper dig att hantera och kontrol lera system gränser genom att tilldela en [Azure policy](../../../policy/overview.md) -definition som övervakar för nätverks säkerhets grupps härdnings rekommendationer i Azure Security Center. Azure Security Center analyserar trafik mönster för virtuella datorer som är riktade mot Internet och ger regel rekommendationer för nätverks säkerhets grupper för att minska risken för angrepp. Dessutom tilldelar skissen princip definitioner som övervakar oskyddade slut punkter, program och lagrings konton. Slut punkter och program som inte skyddas av en brand vägg och lagrings konton med obegränsad åtkomst kan ge oavsiktlig åtkomst till information som finns i informations systemet.

- Regler för nätverks säkerhets grupper för virtuella datorer som riktas mot Internet bör vara skärpta
- Åtkomst via slut punkt mot Internet bör vara begränsad
- NSG: er-reglerna för webb program på IaaS bör vara härdade
- Granska obegränsad nätverks åtkomst till lagrings konton

## <a name="93165-sc-7-3-boundary-protection--access-points"></a>9.3.16.5 SC-7 (3) gränser skydd | Åtkomst punkter

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Åtkomst till virtuella JIT-datorer hjälper dig att begränsa antalet externa anslutningar till dina resurser i Azure. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men ännu inte har kon figurer ATS.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="93165-sc-7-4-boundary-protection--external-telecommunications-services"></a>9.3.16.5 SC-7 (4) gränser skydd | Externa telekommunikations tjänster

Just-in-Time (JIT)-åtkomst till virtuella datorer låser inkommande trafik till virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. Åtkomst till JIT-fjärråtkomst hjälper dig att hantera undantag till trafik flödes principen genom att under lätta åtkomst förfrågningar och godkännande processer. Den här skissen tilldelar en [Azure policy](../../../policy/overview.md) -definition som hjälper dig att övervaka virtuella datorer som har stöd för just-in-Time-åtkomst men ännu inte har kon figurer ATS.

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

## <a name="96163-sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>9.6.16.3 SC-8 (1) överförings konfidentialitet och integritet | Kryptografiskt eller alternativt fysiskt skydd

Den här skissen hjälper dig att skydda konfidentiell och integritet på överförd information genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som hjälper dig att övervaka kryptografisk mekanism som implementerats för kommunikations protokoll. Att säkerställa att kommunikationen är korrekt krypterad kan hjälpa dig att uppfylla organisationens krav eller skydda information från obehörigt utlämnande av information och ändringar.

- API-appen bör bara vara tillgänglig via HTTPS
- Granska Windows-webbservrar som inte använder säkra kommunikations protokoll
- Distribuera krav för att granska Windows-webbservrar som inte använder säkra kommunikations protokoll
- Funktionsapp bör endast vara tillgängligt via HTTPS
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Säker överföring till lagringskonton ska vara aktiverat
- Webb program bör endast vara tillgängliga via HTTPS

## <a name="93166-sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>9.3.16.6 SC-28 (1) skydd av information i vila | Kryptografiskt skydd

Den här skissen hjälper dig att genomdriva principen om användningen av kryptografiska kontroller för att skydda informationen i vila genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som tillämpar vissa kryptografiska kontroller och granska användningen av svaga kryptografiska inställningar. Att förstå var dina Azure-resurser kan ha icke-optimala kryptografiska konfigurationer kan hjälpa dig att vidta korrigerande åtgärder för att säkerställa att resurserna konfigureras i enlighet med din informations säkerhets princip. Mer specifikt kräver princip definitionerna som tilldelas av den här skissen kryptering för data Lake Storage-konton. Kräv transparent data kryptering på SQL-databaser; och granska saknad kryptering på SQL-databaser, virtuella dator diskar och variabler för Automation-konto.

- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Distribuera transparent data kryptering för SQL DB
- Disk kryptering bör tillämpas på virtuella datorer
- Kräv kryptering för Data Lake Store-konton
- transparent datakryptering på SQL-databaser ska aktive ras

## <a name="93172-si-2-flaw-remediation"></a>9.3.17.2 SI – 2 fel reparation

Den här skissen hjälper dig att hantera fel i informations systemet genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar saknade system uppdateringar, sårbarheter för operativ system, sårbarheter i SQL och virtuella dator sårbarheter i Azure Security Center. Azure Security Center tillhandahåller rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser. Den här skissen tilldelar också en princip definition som garanterar korrigering av operativ systemet för skalnings uppsättningar för virtuella datorer.

- Kräv automatisk uppdatering av operativ system avbildningar på Virtual Machine Scale Sets
- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras
- System uppdateringar bör installeras på dina virtuella datorer
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets problem i säkerhets konfiguration på dina virtuella datorer bör åtgärdas
- Säkerhets risker i SQL-databaser bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning

## <a name="93173-si-3-malicious-code-protection"></a>9.3.17.3 SI-3 skadlig kod skydd

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakas för saknade Endpoint Protection på virtuella datorer i Azure Security Center och tillämpa Microsoft Antimalware-lösningen på virtuella Windows-datorer.

- Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="93173-si-3-1-malicious-code-protection--central-management"></a>9.3.17.3 SI-3 (1) skadlig kod skydd | Central hantering

Den här skissen hjälper dig att hantera Endpoint Protection, inklusive skadlig kod skydd, genom att tilldela [Azure policy](../../../policy/overview.md) definitioner som övervakar för saknade Endpoint Protection på virtuella datorer i Azure Security Center. Azure Security Center tillhandahåller centraliserade hanterings-och rapporterings funktioner som gör att du kan få inblick i real tid i säkerhets läget för distribuerade Azure-resurser.

- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="93174-si-4-information-system-monitoring"></a>9.3.17.4 SI – 4 information system övervakning

Den här skissen hjälper dig att övervaka systemet genom att granska och framtvinga loggning och data säkerhet i Azure-resurser. Mer specifikt är principerna tilldelade granskning och tillämpar distribution av Log Analytics agenten och förbättrade säkerhets inställningar för SQL-databaser, lagrings konton och nätverks resurser. Dessa funktioner kan hjälpa dig att identifiera avvikande beteende och indikatorer på attacker så att du kan vidta lämpliga åtgärder.

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- \[För hands version \] : granska Log Analytics agent distribution i VMSS – VM-avbildningen (OS) har inte listats
- \[För hands version \] : granska Log Analytics arbets yta för VM-rapportera fel
- \[För hands version \] : distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Linux-datorer
- \[För hands version \] : distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)
- \[För hands version \] : distribuera Log Analytics agent för virtuella Windows-datorer
- Avancerad data säkerhet ska vara aktiverat på dina hanterade instanser
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Distribuera avancerad data säkerhet på SQL-servrar
- Distribuera Avancerat skydd på lagrings konton
- Distribuera granskning på SQL-servrar
- Distribuera nätverks övervakare när virtuella nätverk skapas
- Distribuera hot identifiering på SQL-servrar

## <a name="93174-si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>9.3.17.4 SI-4 (18) information system övervakning | Analysera trafik/konvertera exfiltrering

Avancerat skydd för Azure Storage identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja lagrings konton. Skydds aviseringar innehåller avvikande åtkomst mönster, avvikande extrakt/uppladdningar och misstänkt lagrings aktivitet. Dessa indikatorer kan hjälpa dig att identifiera konverterad exfiltrering av information.

- Distribuera Avancerat skydd på lagrings konton

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat kontroll mappningen av IRS 1075-skissen kan du gå till följande artiklar för att lära dig om skissen och hur du distribuerar det här exemplet:

> [!div class="nextstepaction"]
> [IRS 1075-skiss – översikt](./index.md) 
>  [IRS 1075-skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).