---
title: De kontroller som är skyddade av den australiska regeringens exempel kontroller
description: Kontroll av mappning av det insamlade utkastet av myndigheter som är skyddat av myndigheter. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 04/02/2021
ms.topic: sample
ms.openlocfilehash: ecb6a912d0b1f319022f6b7deff68e1495dccbca
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106386457"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Kontroll mappning för det insamlade utkastet av myndigheter som är skyddat av myndigheter

I följande artikel beskrivs hur Azure-skisser det skyddade skiss exemplet för Azure-ritningar mappar till ISM-skyddade kontroller. Mer information om kontrollerna finns i [ISM Protected](https://www.cyber.gov.au/ism).

Följande mappningar är till de **skyddade ISM** -kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[ förhands granskningen \] : granska de skyddade kontrollerna i australisk myndigheter och distribuera särskilda VM-tillägg för att ge stöd för det inbyggda princip initiativet för gransknings krav** .

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).


## <a name="location-constraints"></a>Plats begränsningar

Den här skissen hjälper dig att begränsa platsen för distributionen av alla resurser och resurs grupper till "Australien, centrala", "Australien Central2", "Australien, östra" och "Australien, sydöstra" genom att tilldela följande Azure Policys definitioner:

- Tillåtna platser (har hårdkodats till "Australien, centrala", "Australien Central2", "Australien, öst" och "Australien, sydöstra")
- Tillåtna platser för resurs grupper (har hårdkodats till "Australien, centrala", "Australien Central2", "Australien, östra" och "Australien, sydöstra")

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Rikt linjer för personal säkerhet – åtkomst till system och deras resurser

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 personal som beviljats åtkomst till ett system och dess resurser är unikt identifierbar

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 standard åtkomst till system, program och data lager är begränsad till den som krävs för att personal ska kunna åta sig sina uppgifter

- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 privilegie rad åtkomst till system, program och data centraler verifieras när den första begärs och omverifieras på grund av en årlig eller oftare

- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 privilegie rad åtkomst till system, program och data lager är begränsad till den som krävs för att personal ska kunna åta sig sina uppgifter

- Högst 3 ägare bör anges för din prenumeration
- Det bör finnas fler än en ägare som tilldelats din prenumeration
- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 användningen av delade användar konton är strikt kontrollerad och personal som använder sådana konton är unikt identifierbara

- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 privilegierade användare tilldelas ett dedikerat privilegierat konto som endast kan användas för uppgifter som kräver privilegie rad åtkomst

- Visa gransknings resultat från virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna
- Distribuera krav för att granska virtuella Windows-datorer där gruppen administratörer innehåller någon av de angivna medlemmarna

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 åtkomst till system, program och data lager tas bort eller inaktive ras samma dag som personal inte längre har ett legitimt krav för åtkomst

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 när personalen beviljas tillfällig åtkomst till ett system, införs effektiva säkerhets kontroller för att begränsa deras åtkomst till den information som krävs för att de ska kunna utföra sina uppgifter

- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med Skriv behörighet bör tas bort från din prenumeration
- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Rikt linjer för system härdning – härdning av operativ system

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 den senaste versionen (N) eller N-1-versionen av ett operativ system används för standard operativ miljöer (SOEs)

- Systemuppdateringar ska ha installerats på dina datorer
- System uppdateringar på virtuella datorers skalnings uppsättningar bör installeras

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 onödiga operativ system konton, program vara, komponenter, tjänster och funktioner tas bort eller inaktive ras

- Föråldrade konton bör tas bort från din prenumeration
- Föråldrade konton med ägar behörigheter bör tas bort från din prenumeration

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 en Application vit listning-lösning implementeras på alla servrar för att begränsa körningen av körbara filer, program varu bibliotek, skript och installations program till en godkänd uppsättning

- Anpassningsbara program kontroller ska vara aktiverade på virtuella datorer

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 antivirus program vara implementerade på arbets stationer och servrar och konfigureras med: Signeringsalgoritm-baserad identifiering aktive rad och inställd på en hög nivå, heuristisk-baserad identifiering aktive rad och inställd på en hög nivå, identifierings-signaturer har kontroller ATS för valuta och uppdaterats minst dagligen, automatisk och vanlig sökning har kon figurer ATS för alla fasta diskar och flyttbara medier

- Microsoft IaaSAntimalware-tillägget bör distribueras på Windows-servrar
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Rikt linjer för system härdning – autentiserings härdning

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 användare autentiseras innan de beviljas åtkomst till ett system och dess resurser

- Granska obegränsad nätverks åtkomst till lagrings konton
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering
- Visa gransknings resultat från virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Distribuera förutsättningar för att granska virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord
- Visa gransknings resultat från virtuella Linux-datorer som har konton utan lösen ord
- Distribuera förutsättningar för att granska virtuella Linux-datorer som har konton utan lösen ord

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 Multi-Factor Authentication används för att autentisera standard användare

- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Multi-Factor Authentication används för att autentisera alla privilegierade användare och andra förtroende uppdrag

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 lösen fraser som används för autentisering med en viss faktor är minst 14 tecken långa, helst med 4 slumpmässiga ord

- Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets inställningar – konto principer
- Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets inställningar – konto principer

## <a name="guidelines-for-system-management---system-administration"></a>Rikt linjer för system hantering – system administration

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 Multi-Factor Authentication används för att autentisera användare varje gången de utför privilegierade åtgärder

- MFA ska vara aktiverat på konton med ägar behörigheter för din prenumeration
- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 hanterings trafik får endast komma från nätverks zoner som används för att administrera system och program

- Just-in-time-kontroller av nätverksåtkomst ska tillämpas på virtuella datorer
- Fjärrfelsökning bör inaktive ras för API Apps
- Fjärrfelsökning bör inaktive ras för Function-appar
- Fjärrfelsökning bör inaktive ras för webb program

## <a name="guidelines-for-system-management---system-patching"></a>Rikt linjer för system hantering – system uppdatering

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 säkerhets sårbarheter i program och driv rutiner som bedöms som extrema risk korrigeras, uppdateras eller begränsas inom 48 timmar efter säkerhets risker som identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning bör vara aktiverat på datorer
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 säkerhets sårbarheter i program och driv rutiner som bedöms som hög risk korrigeras, uppdateras eller minimeras inom två veckor från det att säkerhets problemet identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 säkerhets sårbarheter i program och driv rutiner som bedöms som måttlig eller låg risk korrigeras, uppdateras eller begränsas inom en månad efter det att säkerhets problemet identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 säkerhets sårbarheter i operativ system och inbyggd program vara bedöms som extrema risk, uppdateras eller minimeras inom 48 timmar efter säkerhets risker som identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 säkerhets sårbarheter i operativ system och inbyggd program vara bedöms som hög risk, uppdateras eller minimeras inom två veckor från det att säkerhets problemet identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 säkerhets sårbarheter i operativ system och inbyggd program vara som bedöms som måttlig eller låg risk korrigeras, uppdateras eller begränsas inom en månad efter det att säkerhets problemet identifieras av leverantörer, oberoende tredje parter, system chefer eller användare

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas
- Inställningar för sårbarhets bedömning för SQL Server ska innehålla en e-postadress för att ta emot Sök rapporter

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Rikt linjer för system hantering – säkerhets kopiering och återställning av data

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 säkerhets kopiering av viktig information, program vara och konfigurations inställningar utförs minst en gång per dag

- Granska virtuella datorer utan haveri beredskap har kon figurer ATS

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Rikt linjer för system övervakning – händelse loggning och granskning

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 en centraliserad loggnings funktion implementeras och system är konfigurerade för att spara händelse loggar till den centraliserade loggnings funktionen så snart som möjligt efter att varje händelse inträffar

- Azure-prenumerationer ska ha en logg profil för aktivitets loggen

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 följande händelser loggas för operativ system: åtkomst till viktiga data och processer, programkrascher och eventuella fel meddelanden, försök att använda särskilda behörigheter, ändringar av konton, ändringar av säkerhets princip, ändringar i systemkonfigurationer, Domain Name System (DNS) och Hypertext Transfer Protocol HTTP-förfrågningar, misslyckade försök att komma åt data och system resurser, tjänst fel och omstarter, system start och avstängning , överföring av data till externa medier, användar-eller grupp hantering, användning av särskilda privilegier

- \[För hands version \] : granska Log Analytics agent distribution-VM avbildning (OS) har inte listats
- Granska Log Analytics agent distribution i VMSS – VM-avbildningen (OS) har inte listats
- Granska Log Analytics arbets yta för VM-rapportera fel
- Granska diagnostikinställning

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 följande händelser loggas för databaser: åtkomst till särskilt viktig information, tillägg av nya användare, särskilt privilegierade användare, alla frågor som innehåller kommentarer, alla frågor som innehåller flera inbäddade frågor, eventuella frågor eller databas aviseringar eller haverier, försök att höja behörigheter, försök till åtkomst som har lyckats eller misslyckats, ändringar i databas strukturen, ändringar av användar roller eller databas behörigheter, databas administratörs åtgärder , databas inloggningar och utloggning, ändringar av data, användning av körbara kommandon

- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar
- Granska diagnostikinställning
- Avancerad data säkerhet ska vara aktiverat på SQL-hanterade instanser

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Rikt linjer för system övervakning – sårbarhets hantering

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 sårbarhets bedömningar och inträngande tester utförs av lämplig kunnig personal innan ett system distribueras, efter en betydande ändring i ett system, och minst en gång per år eller som anges av system ägaren

- Säkerhets risker i SQL-databaser bör åtgärdas
- Sårbarhets bedömning bör vara aktiverat på dina SQL-servrar
- Sårbarhets bedömning ska vara aktiverat på SQL-hanterade instanser
- Sårbarhets bedömning ska vara aktiverat på Virtual Machines
- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning
- Säkerhets problem i säkerhets konfiguration på dina datorer bör åtgärdas
- Säkerhets risker i behållar säkerhetskonfigurationer bör åtgärdas

## <a name="guidelines-for-database-systems-management---database-servers"></a>Rikt linjer för databas system hantering – databas servrar

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 hård diskar med databas servrar krypteras med fullständig disk kryptering

- Disk kryptering bör tillämpas på virtuella datorer
- transparent datakryptering på SQL-databaser ska aktive ras

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 information som förmedlas mellan databas servrar och webb program är krypterad

- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Säker överföring till lagringskonton ska vara aktiverat
- Visa gransknings resultat från Windows-webbservrar som inte använder säkra kommunikations protokoll
- Distribuera förutsättningar för att granska Windows-webbservrar som inte använder säkra kommunikations protokoll

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Rikt linjer för hantering av databas system – system program vara för databas hantering

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 standard administratörs konton för databas har inaktiverats, fått ett nytt namn eller fått sina lösen fraser ändrade

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 databas administratörer har unika och identifierbara konton

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 databas administratörs konton delas inte mellan olika databaser

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 databas administratörs konton används enbart för administrativa uppgifter, med standard databas konton som används för generell användning av interaktion med databas

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 databas administratörs åtkomst är begränsad till definierade roller i stället för konton med administratörs behörighet som standard, eller alla behörigheter

- En Azure Active Directory administratör bör tillhandahållas för SQL-servrar

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Rikt linjer för att använda grundläggande kryptografi kryptering

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 krypterings program vara som används för data i vila implementerar fullständig disk kryptering eller delvis kryptering där åtkomst kontroller bara tillåter skrivning till den krypterade partitionen

- Disk kryptering bör tillämpas på virtuella datorer

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Rikt linjer för att använda kryptografi-Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 endast den senaste versionen av TLS används

- Den senaste TLS-versionen ska användas i din API-app
- Den senaste TLS-versionen ska användas i din webbapp
- Den senaste TLS-versionen ska användas i Funktionsapp
- Distribuera förutsättningar för att granska Windows-webbservrar som inte använder säkra kommunikations protokoll
- Visa gransknings resultat från Windows-webbservrar som inte använder säkra kommunikations protokoll

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Rikt linjer för data överföringar och innehålls filtrering – innehålls filtrering

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 virus genomsökning, med flera olika sökmotorer, utförs på allt innehåll

- Microsoft IaaSAntimalware-tillägget bör distribueras på Windows-servrar
- Endpoint Protection-lösningen bör installeras på virtuella datorers skalnings uppsättningar
- Övervaka saknade Endpoint Protection i Azure Security Center

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Rikt linjer för data överföringar och innehålls filtrering – utveckling av webb program

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 allt webb programs innehåll erbjuds exklusivt med HTTPS

- Funktionsapp bör endast vara tillgängligt via HTTPS
- API-appen bör bara vara tillgänglig via HTTPS
- Webb program bör endast vara tillgängliga via HTTPS
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 säkerhets kontroller implementeras för webb program för att hjälpa till att skydda både webb program och deras användare

- CORS bör inte tillåta alla resurser åtkomst till dina webb program

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Rikt linjer för nätverks hantering – nätverks design och konfiguration

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 nätverks åtkomst kontroller implementeras i nätverk för att förhindra anslutning av icke auktoriserade nätverks enheter

- Granska obegränsad nätverks åtkomst till lagrings konton

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 nätverks åtkomst kontroller implementeras för att begränsa trafik inom och mellan nätverks segment till enbart de som krävs för affärs syfte

- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper
- Granska obegränsad nätverks åtkomst till lagrings konton
- Rekommendationer för anpassningsbar nätverks härdning bör tillämpas på virtuella datorer som är riktade mot Internet

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Rikt linjer för nätverks hantering – tjänste kontinuitet för onlinetjänster

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 åtgärder för att förebygga och minska risken för attacker och tjänster diskuteras med tjänst leverantörer, särskilt: deras kapacitet att motstå DOS-attacker (Denial-of-Service). eventuella kostnader som kan uppstå av kunder som härrör från denial-of-Service-attacker, tröskelvärden för att meddela kunder eller stänga av sina onlinetjänster under DOS-attacker (Denial-of-Service), förberedande åtgärder som kan utföras under DOS-attacker (Denial-of-Service), förebyggande åtgärder som kan vidtas för att förhindra att skadlig trafik blockeras så mycket som möjligt

- DDoS Protection standard ska vara aktive rad


> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

> [!div class="nextstepaction"]
> [ISM-skyddad skiss – översikt](./index.md) 
>  [ISM, skyddad skiss – distribuera steg](./deploy.md)

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
