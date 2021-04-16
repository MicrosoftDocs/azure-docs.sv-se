---
title: Exempelkontroller för mediaskiss
description: Kontrollera mappningen av Media-skissexempel. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 04/13/2021
ms.topic: sample
ms.openlocfilehash: 4c04c661d7dd8b2b07a05c20524258d6bb887e62
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376706"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Kontrollmappning av Media-skissexempel

I följande artikel beskrivs hur Azure Blueprints Media-skissexempel mappar till mediakontrollerna. Mer information om kontrollerna finns i [Media](https://www.motionpictures.org/best-practices).

Följande mappningar är till **mediakontrollerna.** Använd navigeringen till höger för att gå direkt till en specifik kontrollmappning. Många av de mappade kontrollerna implementeras med ett [Azure Policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet **öppnar du** Princip i Azure Portal och väljer **sidan** Definitioner. Leta sedan upp och välj det **\[ inbyggda \] principinitiativ för förhandsversionen** : Granska media-kontroller.

> [!IMPORTANT]
> Varje kontroll nedan är associerad med en eller flera [Azure Policy](../../../policy/overview.md) definitioner. Dessa principer kan hjälpa dig [att utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta inte en en-till-en-matchning eller fullständig matchning mellan en kontroll och en eller flera principer. Därför **refererar Kompatibel i** Azure Policy endast till själva principerna. Detta säkerställer inte att du är helt kompatibel med alla krav för en kontroll. Dessutom innehåller efterlevnadsstandarden kontroller som inte hanteras av några Azure Policy definitioner för just nu. Därför är efterlevnad i Azure Policy endast en partiell vy över din övergripande efterlevnadsstatus. Associationerna mellan kontroller och Azure Policy för det här exemplet på efterlevnadsskiss kan ändras med tiden. Om du vill visa ändringshistoriken kan du gå [till GitHub Commit History (Genomförandehistorik för GitHub).](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md)

## <a name="access-control"></a>Åtkomstkontroll

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1 – Kontrollera att det inte finns någon rotåtkomstnyckel

- \[Förhandsversion: \] Distribuera krav för att granska virtuella Windows-datorer som inte innehåller de angivna certifikaten i betrodd rot

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2 – Lösenord, PIN-koder och token måste skyddas

- \[Förhandsversion: \] Distribuera krav för att granska virtuella Windows-datorer som inte begränsar den minsta lösenordslängden till 14 tecken

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8 – Åtkomst till delat konto är förbjuden

- Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort Service Bus från namnområdet

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9 –Systemet måste begränsa åtkomsten till behöriga användare.

- Granska obegränsad nätverksåtkomst till lagringskonton

### <a name="ac--114--system-must-enforce-access-rights"></a>AC- 1.14 –Systemet måste framtvinga åtkomstbehörigheter.

- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Tilldelning av användarrättigheter"

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC- 1.15 – Förhindra obehörig åtkomst till relevant säkerhetsinformation eller funktioner.

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – Systeminställningar"

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21 – Uppdelning av uppgifter måste framtvingas genom lämplig rolltilldelning.

- \][Förhandsversion: Role-Based Access Control (RBAC) ska användas på Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40 – Se till att systemen inte ansluter betrott nätverk och ej betrodda nätverk samtidigt.

- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – nätverksåtkomst"

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC- 1.43 – Fjärråtkomst för icke-anställda måste begränsas till att endast tillåta åtkomst till specifikt godkända informationssystem

- \[Förhandsversion: \] Visa granskningsresultat från virtuella Linux-datorer som tillåter fjärranslutningar från konton utan lösenord

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50 – Logga säkerhetsrelaterade händelser för alla informationssystemkomponenter.

- Diagnostikloggar i Logic Apps ska vara aktiverade

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54 – Se till att multifaktorautentisering (MFA) är aktiverat för alla molnkonsolanvändare.

- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- Multifaktorautentisering (MFA) ska aktiveras för alla prenumerationskonton med skrivbehörighet för att förhindra intrång i konton eller resurser.

## <a name="auditing--logging"></a>Granskning & loggning

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1 – Lyckade och misslyckade händelser måste loggas.

- Diagnostikloggar i Söktjänster ska vara aktiverade

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL -2.16 – Nätverksenheter/instanser måste logga alla händelser som klassificeras som en kritisk säkerhetshändelse av den nätverksenheten/nätverksinstansen (ELBs, brandväggar för webbaserade program osv.)

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i Säkerhetsalternativ – Konton

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17 – Servrar/instanser måste logga alla händelser som klassificeras som en kritisk säkerhetshändelse av den servern/instansen

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i Säkerhetsalternativ – Konton

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19 – Domänhändelser måste logga alla händelser som klassificeras som kritiska eller högsäkerhetshändelser av domänhanteringsprogramvaran

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i Säkerhetsalternativ – Konton
- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – Microsoft-nätverksklient"

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20 – Domänhändelser måste logga alla händelser som klassificeras som kritiska säkerhetshändelser av domänsäkerhetskontroller

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i Säkerhetsalternativ – Konton

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21 – Domänhändelser måste logga åtkomst eller ändringar i domänloggen

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – återställningskonsol"

## <a name="cryptographic-controls"></a>Kryptografiska kontroller

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2 – Program och system måste använda aktuella kryptografiska lösningar för att skydda data.

- transparent datakryptering på SQL-databaser ska vara aktiverat
- Transparent datakryptering ska aktiveras för att skydda vilodata och uppfylla efterlevnadskrav

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5 – Digitala certifikat måste signeras av en godkänd certifikatutfärdare.

- \[Förhandsversion: \] Visa granskningsresultat från virtuella Windows-datorer som innehåller certifikat som upphör att gälla inom det angivna antalet dagar

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6 – Digitala certifikat måste tilldelas unikt till en användare eller enhet.

- \[Förhandsversion: \] Distribuera krav för att granska virtuella Windows-datorer som innehåller certifikat som upphör att gälla inom det angivna antalet dagar

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7 – Kryptografiskt material måste lagras för att aktivera dekryptering av posterna under den tid posterna bevaras.

- Diskkryptering ska tillämpas på virtuella datorer
- Virtuella datorer utan aktiverad diskkryptering kommer att övervakas av Azure Security Center som rekommendationer

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8 – Hemliga och privata nycklar måste lagras på ett säkert sätt.

- transparent datakryptering på SQL-databaser ska vara aktiverat
- Transparent datakryptering ska aktiveras för att skydda vilodata och uppfylla efterlevnadskrav

## <a name="change--config-management"></a>Ändra & konfigurationshantering

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2 – Endast behöriga användare kan implementera godkända ändringar i systemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12 – Upprätthålla en uppdaterad, fullständig, korrekt och lättillgänglig baslinjekonfiguration för informationssystemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13 – Använd automatiserade verktyg för att upprätthålla en baslinjekonfiguration av informationssystemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14 – Identifiera och inaktivera onödiga och/eller icke-säkra funktioner, portar, protokoll och tjänster.

- Nätverksgränssnitt ska inaktivera IP-vidare vidarebefordran
- \[Förhandsversion: \] IP-vidarebefordran på den virtuella datorn ska vara inaktiverat

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19 – Övervaka ändringar i säkerhetskonfigurationsinställningarna.

- Distribuera diagnostikinställningar för nätverkssäkerhetsgrupper

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22- Se till att endast auktoriserad programvara och uppdateringar är installerade på företagets system.

- Systemuppdateringar ska ha installerats på dina datorer
- Uppdateringar av säkerhetssystem som saknas på dina servrar övervakas av Azure Security Center som rekommendationer

## <a name="identity--authentication"></a>Identitetsautentisering & autentisering

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1 – Användarkonton måste tilldelas unikt till enskilda användare för åtkomst till information som inte klassificeras som offentlig. Konto-ID:er måste konstrueras med ett standardiserat logiskt format.

- Externa konton med ägarbehörighet ska tas bort från din prenumeration
- Externa konton med ägarbehörighet bör tas bort från din prenumeration för att förhindra oövervakad åtkomst.

## <a name="network-security"></a>Nätverkssäkerhet

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2 – Åtkomsten till funktionen för hantering av nätverksenhet är begränsad till behöriga användare.

- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – Nätverksåtkomst"

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3 – Alla nätverksenheter måste konfigureras med sina säkraste konfigurationer.

- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Säkerhetsalternativ – Nätverksåtkomst"

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5 – Alla nätverksanslutningar till ett system via en brandvägg måste godkännas och granskas regelbundet.

- \[Förhandsversion: \] Visa granskningsresultat från konfigurationer av virtuella Windows-datorer i Egenskaper för Windows-brandväggen

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7 – Lämpliga kontroller måste finnas vid alla gränser mellan ett betrott nätverk och eventuella ej betrodda eller offentliga nätverk.

- \[Förhandsversion: \] Distribuera krav för att granska konfigurationer av virtuella Windows-datorer i "Egenskaper för Windows-brandväggen"

## <a name="security-planning"></a>Säkerhetsplanering

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3 – Hot måste identifieras som kan påverka sekretessen, integriteten eller tillgängligheten för företagets information och innehåll negativt samt sannolikheten för att de inträffar.

- Advanced Threat Protection-typer ska vara inställda på Alla i SQL Managed Instance Advanced Data Security inställningar

### <a name="security-continuity"></a>Säkerhetskontinuui

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5 – Data i långsiktig lagring måste vara tillgängliga under kvarhållningsperioden och skyddas mot medieförsämring och teknikändringar.

- SQL-servrar ska konfigureras med granskningsbevarande dagar som är längre än 90 dagar.
- Granska SQL-servrar som konfigurerats med en kvarhållningsperiod för granskning på mindre än 90 dagar.

## <a name="system-integrity"></a>Systemintegritet

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3 – Endast auktoriserad personal kan övervaka nätverks- och användaraktiviteter.

- Sårbarheter i DINA SQL-databaser bör åtgärdas
- Övervaka genomsökningsresultat för sårbarhetsbedömning och rekommendationer för hur du åtgärdar sårbarheter i databasen.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4 – Internetuppriktade system måste ha intrångsidentifiering.

- Distribuera hotidentifiering på SQL-servrar

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13 – Standardiserad centralt hanterad programvara mot skadlig kod bör implementeras i hela företaget.

- Distribuera Microsoft IaaSAntimalware-standardtillägget för Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14 – Program mot skadlig kod måste genomsöka datorer och media varje vecka som minst.

- Distribuera Microsoft IaaSAntimalware-standardtillägget för Windows Server

## <a name="vulnerability-management"></a>Sårbarhetshantering

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4 – Kontrollera att program genomsöks efter säkerhetsrisker månadsvis.

- Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas
- Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem från attacker.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5 – Se till att sårbarheter identifieras, kopplas till hot och utvärderas för risk.

- Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas
- Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem mot attacker.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6 – Se till att identifierade sårbarheter har åtgärdats inom en ömsesidigt överenskommen tidslinje.

- Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas
- Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem mot attacker.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7 – Åtkomst till och användning av sårbarhetshanteringssystem måste begränsas till auktoriserad personal.

- Sårbarheter i säkerhetskonfigurationen på dina VM-skalningsuppsättningar bör åtgärdas
- Granska säkerhetsproblemen i operativsystemet på dina VM-skalningsuppsättningar för att skydda dem mot attacker.

> [!NOTE]
> Tillgängligheten för Azure Policy definitioner kan variera Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontrollmappningen av Media-skissexempel. Gå sedan till följande artiklar för att lära dig mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [Media-skiss – översikt](./control-mapping.md) 
>  [Media-skiss – Distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
