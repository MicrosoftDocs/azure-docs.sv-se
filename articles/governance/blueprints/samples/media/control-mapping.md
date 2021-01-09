---
title: Exempel kontroller för medie skisser
description: Styr mappningen av medie skiss exemplen. Varje kontroll mappas till en eller flera Azure Policy definitioner som hjälper till med utvärderingen.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: e1356acfd7b6b9693322000077b221781b58681f
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98035044"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Kontroll mappning av medie skiss exemplet

I följande artikel beskrivs hur medie skiss exemplet i Azure skisser mappar till medie kontrollerna. Mer information om kontrollerna finns i [Media](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

Följande mappningar är till **medie** kontrollerna. Använd navigeringen till höger om du vill gå direkt till en bestämd kontroll mappning. Många av de mappade kontrollerna implementeras med ett [Azure policy](../../../policy/overview.md) initiativ. Om du vill granska hela initiativet öppnar du **princip** i Azure Portal och väljer sidan **definitioner** . Leta sedan reda på och välj **\[ förhands granskningen \] : granska medie kontroller** i det inbyggda princip initiativet.

> [!IMPORTANT]
> Varje kontroll nedan är kopplad till en eller flera [Azure policy](../../../policy/overview.md) -definitioner. Dessa principer kan hjälpa dig att [utvärdera efterlevnaden](../../../policy/how-to/get-compliance-data.md) av kontrollen. Det finns dock ofta ingen en-till-en-eller fullständig matchning mellan en kontroll och en eller flera principer. Som sådan är **kompatibel** i Azure policy endast som avser själva principerna. Detta garanterar inte att du är helt kompatibel med alla krav för en kontroll. Standarden för efterlevnad innehåller dessutom kontroller som inte åtgärdas av några Azure Policy definitioner för tillfället. Därför är regelefterlevnad i Azure Policy bara en partiell vy av din övergripande kompatibilitetsstatus. Kopplingarna mellan kontroller och Azure Policy definitioner för det här skiss exemplet för efterlevnad kan ändras med tiden. Om du vill visa ändrings historiken läser du [inchecknings historiken för GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Åtkomstkontroll

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1,1 – kontrol lera att det inte finns någon rot åtkomst nyckel

- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte innehåller de angivna certifikaten i den betrodda roten

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1,2-lösen ord, PIN-koder och token måste skyddas

- \[För hands version \] : Distribuera krav för att granska virtuella Windows-datorer som inte begränsar minsta längd på lösen ord till 14 tecken

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1,8 – åtkomst till delat konto är förbjuden

- Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort från Service Bus namnrymd

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1,9-systemet måste begränsa åtkomsten till behöriga användare.

- Granska obegränsad nätverks åtkomst till lagrings konton

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1,14-systemet måste framtvinga åtkomst behörighet.

- \[För hands version \] : Distribuera förutsättningar för att granska Windows-konfigurationer för virtuella datorer i tilldelning av användar rättigheter

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1,15 – förhindra obehörig åtkomst till säkerhetsrelaterad information eller funktioner.

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ-Systeminställningar

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1-21-separering av uppgifter måste verkställas genom lämplig tilldelning av rollen.

- [För hands version \] : Role-Based Access Control (RBAC) ska användas på Kubernetes Services

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1,40 – Se till att systemen inte ansluter till betrodda nätverk och ej betrodda nätverk på samma tid.

- \[För hands version \] : Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ-nätverks åtkomst

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1,42 & AC-1,43 – fjärråtkomst för icke-anställda måste begränsas för att tillåta åtkomst enbart till särskilt godkända informations system

- \[För hands version \] : Visa gransknings resultat från virtuella Linux-datorer som tillåter fjärr anslutningar från konton utan lösen ord

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1,50-logg säkerhets relaterade händelser för alla informations system komponenter.

- Diagnostikloggar i Logic Apps ska vara aktive rad

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1,54 – Se till att Multi-Factor Authentication (MFA) är aktiverat för alla användare i moln konsolen.

- Multifaktorautentisering bör aktiveras på konton med skrivbehörighet för prenumerationen
- Multi-Factor Authentication (MFA) måste vara aktiverat för alla prenumerations konton med Skriv behörighet för att förhindra att konton eller resurser överlappar varandra.

## <a name="auditing--logging"></a>Granska & loggning

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2,1-lyckade och misslyckade händelser måste loggas.

- Diagnostikloggar i search Services ska vara aktive rad

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2,16-nätverks enheter/instanser måste logga varje händelse som klassificeras som en kritisk säkerhets händelse av nätverks enheten/instansen (ELBs, WebApplication-brandväggar osv.)

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ – konton

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2,17-servrar/instanser måste logga alla händelser som klassificeras som en kritisk säkerhets händelse av den servern/instansen

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ – konton

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2,19 – domän händelser måste logga varje händelse som klassificeras som en kritisk eller hög säkerhets händelse av program varan för domän hantering

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ – konton
- \[För hands version \] : Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ – Microsoft Network Client

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2,20-domän händelser måste logga varje händelse som klassificeras som en kritisk säkerhets händelse av domän säkerhets kontroller

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ – konton

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2,21 – domän händelser måste logga all åtkomst eller ändringar i domän loggen

- \[För hands version \] : Visa gransknings resultat från konfigurationer för virtuella Windows-datorer i säkerhets alternativ-återställnings konsolen

## <a name="cryptographic-controls"></a>Kryptografiska kontroller

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4,2-program och system måste använda aktuella kryptografiska lösningar för att skydda data.

- transparent datakryptering på SQL-databaser ska aktive ras
- Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4,5-digitala certifikat måste signeras av en godkänd certifikat utfärdare.

- \[För hands version \] : Visa gransknings resultat från virtuella Windows-datorer som innehåller certifikat som upphör att gälla inom det angivna antalet dagar

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4,6-digitala certifikat måste tilldelas unikt till en användare eller enhet.

- \[För hands version \] : Distribuera förutsättningar för att granska virtuella Windows-datorer som innehåller certifikat som upphör att gälla inom det angivna antalet dagar

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4,7 – kryptografiskt material måste lagras för att möjliggöra dekryptering av posterna under den tid som posterna bevaras.

- Disk kryptering bör tillämpas på virtuella datorer
- Virtuella datorer utan en aktive rad disk kryptering övervakas av Azure Security Center som rekommendationer

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4,8 – hemliga och privata nycklar måste lagras på ett säkert sätt.

- transparent datakryptering på SQL-databaser ska aktive ras
- Transparent data kryptering ska vara aktive rad för att skydda data i vila och uppfylla kraven för efterlevnad

## <a name="change--config-management"></a>Ändra & konfigurations hantering

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM – 5,2 – endast auktoriserade användare kan implementera godkända ändringar i systemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5,12 – upprätthålla en uppdaterad, fullständig, korrekt och tillgänglig bas linje konfiguration av informations systemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM – 5,13 – Använd automatiserade verktyg för att upprätthålla en grundläggande konfiguration av informations systemet.

- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5,14 – identifiera och inaktivera onödiga och/eller icke-säkra funktioner, portar, protokoll och tjänster.

- Nätverks gränssnitt bör inaktivera IP-vidarebefordring
- \[För hands version \] : IP-vidarebefordran på den virtuella datorn bör inaktive ras

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5,19 – övervaka ändringar i inställningarna för säkerhets konfiguration.

- Distribuera diagnostikinställningar för nätverks säkerhets grupper

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM – 5,22 – Se till att endast auktoriserad program vara och uppdateringar är installerade på företags system.

- Systemuppdateringar ska ha installerats på dina datorer
- Säkerhets system uppdateringar som saknas på dina servrar kommer att övervakas av Azure Security Center som rekommendationer

## <a name="identity--authentication"></a>Identitets &-autentisering

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7,1 – användar konton måste tilldelas unikt till enskilda användare för åtkomst till information som inte klassificeras som offentlig. Konto-ID: n måste konstrueras med ett standardiserat logiskt format.

- Externa konton med ägar behörigheter bör tas bort från din prenumeration
- Externa konton med ägar behörigheter bör tas bort från din prenumeration för att förhindra oövervakad åtkomst.

## <a name="network-security"></a>Nätverkssäkerhet

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9,2 – åtkomst till funktioner för hantering av nätverks enheter är begränsad till behöriga användare.

- \[För hands version \] : Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ-nätverks åtkomst

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9,3 – alla nätverks enheter måste konfigureras med de mest säkra konfigurationerna.

- \[För hands version \] : Distribuera krav för att granska Windows-konfigurationer för virtuella datorer i säkerhets alternativ-nätverks åtkomst

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9,5 – alla nätverks anslutningar till ett system via en brand vägg måste godkännas och granskas regelbundet.

- \[För hands version \] : Visa gransknings resultat från virtuella Windows-konfigurationer i egenskaper för Windows-brandväggen

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9,7 – lämpliga kontroller måste finnas i alla gränser mellan ett betrott nätverk och ett icke-betrott eller offentligt nätverk.

- \[För hands version \] : Distribuera förutsättningar för att granska Windows-konfigurationer för virtuella datorer i egenskaper för Windows-brandväggen

## <a name="security-planning"></a>Säkerhetsplanering

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11,3 – hot måste identifieras som kan påverka konfidentialitet, integritet eller tillgänglighet för företags information och innehåll, tillsammans med sannolikheten för deras förekomst.

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans

### <a name="security-continuity"></a>Säkerhets kontinuitet

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12,5-data i långtids lagring måste vara tillgängliga under hela kvarhållningsperioden och skyddas mot media försämring och teknik ändringar.

- SQL-servrar bör konfigureras med granskning av antalet dagar som är större än 90 dagar.
- Granska SQL-servrar som kon figurer ATS med en gransknings kvarhållningsperiod på mindre än 90 dagar.

## <a name="system-integrity"></a>System integritet

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14,3 – endast auktoriserad personal kan övervaka nätverks-och användar aktiviteter.

- Säkerhets risker i SQL-databaser bör åtgärdas
- Övervaka genomsöknings resultat och rekommendationer för sårbarhets bedömning för att åtgärda databas sårbarheter.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14,4-Internet-riktade system måste ha intrångs identifiering.

- Distribuera hot identifiering på SQL-servrar

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14,13 – standardiserad Central program vara för program mot skadlig kod bör implementeras i hela företaget.

- Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14,14 – program mot skadlig kod måste genomsöka datorer och media varje vecka med minst.

- Distribuera standard Microsoft IaaSAntimalware-tillägget för Windows Server

## <a name="vulnerability-management"></a>Sårbarhetshantering

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15,4 – se till att program genomsöks efter sårbarheter varje månad.

- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15,5 – Se till att sårbarheter identifieras, kopplas till hot och utvärderas för risk.

- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15,6 – Se till att identifierade sårbarheter har åtgärd ATS inom ett ömsesidigt överenskommet avtal på tids linjen.

- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15,7 – åtkomst till och användning av sårbarhets hanterings system måste begränsas till behörig personal.

- Säkerhets problem i säkerhets konfiguration på den virtuella datorns skalnings uppsättningar bör åtgärdas
- Granska OS-sårbarheterna på dina virtuella datorers skalnings uppsättningar för att skydda dem mot angrepp.

> [!NOTE]
> Tillgängligheten för vissa Azure Policy definitioner kan variera i Azure Government och andra nationella moln. 

## <a name="next-steps"></a>Nästa steg

Du har granskat kontroll mappningen för medie skiss exemplet. Gå sedan till följande artiklar för att lära dig mer om översikten och hur du distribuerar det här exemplet:

> [!div class="next step action"]
> [Medie skiss – översikt](./control-mapping.md) 
>  [Medie skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
