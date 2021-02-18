---
title: Azures säkerhets bas linje för Azures front dörr
description: Säkerhets bas linjen för Azures front dörr ger procedur vägledning och resurser för att implementera de säkerhets rekommendationer som anges i Azures säkerhets benchmark.
author: msmbaldwin
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 59eef7654e677ccfef79445f4a9cab26800bdfc5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589134"
---
# <a name="azure-security-baseline-for-azure-front-door"></a>Azures säkerhets bas linje för Azures front dörr

Den här säkerhets bas linjen använder vägledning från [Azures säkerhets benchmark version 2,0](../security/benchmarks/overview.md) till Azures front dörr. Azure Security Benchmark ger rekommendationer om hur du kan skydda dina molnlösningar i Azure. Innehållet grupperas efter **säkerhets kontrollerna** som definieras av Azures säkerhets benchmark och relaterade rikt linjer som gäller för Azures front dörr. **Kontroller** som inte är tillämpliga på Azures front dörr har uteslutits.

Om du vill se hur Azures front dörr är helt mappad till Azures säkerhets benchmark, se den [fullständiga Azure-filen för säkerhets bas linje mappning](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Nätverkssäkerhet

*Mer information finns i [Azure Security Benchmark: Nätverkssäkerhet](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: skydda program och tjänster från externa nätverks attacker

**Vägledning**: Använd Azure PowerShell för att skapa en princip för geo-filtrering och koppla principen till din befintliga Azure frontend-frontend-värd. Denna princip för geo-filtrering blockerar förfrågningar från externa nätverk, till exempel de från andra länder eller regioner utom USA.

- [Självstudie – så här konfigurerar du en princip för geo-filtrering WAF för din front dörr](front-door-tutorial-geo-filtering.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: förenkla nätverks säkerhets regler

**Vägledning**: Använd Virtual Network Service-taggar för att definiera nätverks åtkomst kontroller för nätverks säkerhets grupper som kon figurer ATS för dina resurser i Azures front dörr. Service märken kan användas i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (AzureFrontDoor. frontend, AzureFrontDoor. backend, AzureFrontDoor. FirstParty) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. 

Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

- [Förstå och använda service märken](../virtual-network/service-tags-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="identity-management"></a>Identitetshantering

*Mer information finns i [Azure Security Benchmark: Identitetshantering](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Begränsa åtkomsten till Azure-resurser baserat på villkor

**Vägledning**: som standard svarar Azures front dörr på alla användar förfrågningar oavsett var begäran kommer. Kunder kan också begränsa åtkomsten till webb programmet efter länder eller regioner. Brand Väggs tjänsten för webb program i Azure-frontend gör det möjligt för kunder att definiera en princip med hjälp av anpassade åtkomst regler, för en specifik sökväg på slut punkten, antingen tillåta eller blockera åtkomst från angivna länder eller regioner.

- [Självstudie – så här konfigurerar du en princip för geo-filtrering WAF för din Azure-front dörr](front-door-tutorial-geo-filtering.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="privileged-access"></a>Privilegierad åtkomst

*Mer information finns i [Azure Security Benchmark: Privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Begränsa administrativ åtkomst till affärskritiska system

**Vägledning**: Azure-frontend använder Azure-rollbaserad åtkomst kontroll (Azure RBAC) för att isolera åtkomst till verksamhets kritiska system. Använd Azure RBAC för att begränsa konton som beviljas privilegie rad åtkomst till prenumerationer och hanterings grupper, där de finns.

Säkerställ begränsad åtkomst till hanterings-, identitets-och säkerhets system som har administrativ åtkomst till affärs kritiska system, till exempel Active Directory-domän kontrollanter, säkerhets verktyg och system hanterings verktyg. Justera alla typer av åtkomst kontroller till din strategi för företags segmentering för kontinuerlig och konsekvent implementering.

- [Azure-komponenter och referens modell](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Åtkomst till hanterings grupp](../governance/management-groups/overview.md#management-group-access) 

- [Administratörer för Azure-prenumeration](../cost-management-billing/manage/add-change-subscription-administrator.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Använd arbetsstationer med privilegierad åtkomst

**Vägledning**: Det är viktigt med säkra och isolerade arbetsstationer för att skydda känsliga roller som administratörer, utvecklare och operatörer av kritiska tjänster. 

Använd mycket säkra användar arbets stationer med Azure skydds för administrativa uppgifter. Välj Azure Active Directory (Azure AD), Microsoft Defender Avancerat skydd (ATP) och Microsoft Intune för att distribuera säkra och hanterade användar arbets stationer för administrativa uppgifter. De skyddade arbets stationerna måste hanteras centralt för att upprätthålla säker konfiguration, inklusive stark autentisering, program vara och maskin varu bas linjer, begränsad logisk och nätverks åtkomst.

- [Förstå arbets stationer med privilegie rad åtkomst](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Distribuera en arbetsstation med privilegierad åtkomst](/security/compass/privileged-access-deployment)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Följ JEA (Just Enough Administration, precis tillräcklig administration) (principen om minsta behörighet) 

**Vägledning**: Azure-front dörren är integrerad med rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att hantera dess resurser. Med Azure RBAC kan du hantera åtkomst till Azure-resurser via rolltilldelningar. Du kan tilldela dessa roller till användare, grupper tjänstens huvud namn och hanterade identiteter. Det finns fördefinierade inbyggda roller för vissa resurser och dessa roller kan inventeras eller frågas via verktyg som Azure CLI, Azure PowerShell eller Azure-portalen. 

Följ den lägsta behörighets modellen för rollbaserade behörigheter som tilldelats resurser med Azure RBAC och se till att de baseras på affärs behov. Det här är ett komplement till JIT-metoden (just in time) för Azure AD Privileged Identity Management (PIM) och bör granskas regelbundet.

Använd inbyggda roller för att allokera behörighet och endast skapa anpassade roller baserat på affärs krav.

- [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) 

- [Så här konfigurerar du RBAC i Azure](../role-based-access-control/role-assignments-portal.md) 

- [Så här använder du identitets- och åtkomstgranskningar i Azure AD](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

## <a name="data-protection"></a>Dataskydd

*Mer information finns i [Azure Security Benchmark: Dataskydd](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-4-encrypt-sensitive-information-in-transit"></a>DP-4: Kryptera känslig information under överföring

**Vägledning**: för att komplettera åtkomst kontroller bör data i överföring skyddas mot "out of band"-attacker (t. ex. trafik insamling) som använder kryptering för att säkerställa att angripare inte kan läsa eller ändra data.

Frontend-dörren stöder TLS-versionerna 1,0, 1,1 och 1,2. TLS 1,3 stöds inte ännu. Alla profiler för front dörren som skapats efter september 2019 använder TLS 1,2 som standard minimum.

Även om det är valfritt för trafik i privata nätverk, är detta avgörande för trafik på externa och offentliga nätverk. För HTTP-trafik ser du till att alla klienter som ansluter till dina Azure-resurser kan förhandla TLS v 1.2 eller senare. För fjärrhantering använder du SSH (för Linux) eller RDP/TLS (för Windows) i stället för ett okrypterat protokoll. Inaktuella SSL-, TLS-och SSH-versioner och-protokoll och svaga chiffer bör inaktive ras.

Som standard tillhandahåller Azure kryptering för data i överföring mellan Azures Data Center.

- [Självstudie – så här konfigurerar du HTTPS på en anpassad domän i en front dörr](front-door-custom-domain-https.md)

- [Förstå kryptering i överföring med Azure](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) 

- [Information om TLS-säkerhet](/security/engineering/solving-tls1-problem) 

- [Double Encryption för Azure-data under överföring](../security/fundamentals/double-encryption.md#data-in-transit)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

## <a name="asset-management"></a>Tillgångshantering

*Mer information finns i [Azure Security Benchmark: Tillgångshantering](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Se till att säkerhetsteamet har insyn i risker gällande tillgångar

**Vägledning**: se till att säkerhets team beviljas behörigheter för säkerhets läsare i din Azure-klient och prenumerationer för att övervaka säkerhets risker med hjälp av Azure Security Center. 

Beroende på hur säkerhetsteamets ansvarsområden är strukturerade kan ansvaret för övervakning av säkerhetsrisker ligga hos en central säkerhetsgrupp eller ett lokalt team. Säkerhets insikter och risker måste dock alltid aggregeras centralt inom en organisation. 

Behörigheter för säkerhetsläsare kan tillämpas brett över en hel klientorganisation (rothanteringsgrupp) eller omfattas av hanteringsgrupper eller specifika prenumerationer. 

Obs! ytterligare behörigheter kan krävas för insyn i arbets belastningar och tjänster. 

- [Översikt över säkerhetsläsarrollen](../role-based-access-control/built-in-roles.md#security-reader)

- [Översikt över Azure-hanteringsgrupper](../governance/management-groups/overview.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Se till att säkerhetsteamet har åtkomst till tillgångsinventering och metadata

**Vägledning**: Använd taggar för Azure-resurser, resurs grupper och prenumerationer för att logiskt organisera dem i en taxonomi. Varje tagg består av ett namn och ett värde-par. Du kan till exempel använda namnet "miljö" och värdet "produktion" för alla resurser i produktionen.

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Azure Security Center till gångs lager hantering](../security-center/asset-inventory.md) 

- [Beslutsguide för namngivning och taggning av resurser](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Använd bara godkända Azure-tjänster

**Vägledning**: Använd Azure policy för att granska och begränsa vilka tjänster som användare kan etablera i din miljö. Fråga efter och identifiera resurser inom sina prenumerationer med Azure Resource Graph.

Använd Azure Monitor för att skapa regler för att utlösa aviseringar när en icke-godkänd tjänst upptäcks.

- [Konfigurera och hantera Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Så här nekar du en speciell resurs typ med Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Skapa frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantera säker livscykelhantering för tillgångar

**Vägledning**: det är kunden som ansvarar för att underhålla attribut och nätverkskonfigurationer för Azures front dörrs till gångar som betraktas som hög påverkan.

Vi rekommenderar att kunden skapar en process för att avbilda attributet och ändringar i nätverks konfigurationen, mäter ändrings effekt och skapa reparations uppgifter, om det är tillämpligt.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="logging-and-threat-detection"></a>Loggning och hotidentifiering

*Mer information finns i [Azure Security Benchmark: Loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Aktivera loggning av nätverksaktiviteter i Azure

**Vägledning**: Azure-front dörren är inte avsedd att distribueras till virtuella nätverk. på grund av att de här kunderna inte kan aktivera flödes loggning för nätverks säkerhets grupper, dirigera trafik via en brand vägg eller utför paket avbildningar.

Azures front dörr loggar all nätverks trafik som den bearbetar för kund åtkomst. Aktivera funktionen för nätverks flödes loggar och konfigurera loggarna så att de skickas till ett lagrings konto för långsiktig kvarhållning och granskning.

- [Självstudie – Hur du ställer in övervaknings mått och loggar i Azures front dörr](front-door-diagnostics.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Aktivera loggning för Azure-resurser

**Vägledning**: aktivitets loggar, som är automatiskt tillgängliga, innehåller alla Skriv åtgärder (placera, skicka, ta bort) för dina Azures resurser för front dörren förutom Läs åtgärder (get). Aktivitets loggar kan användas för att hitta ett fel när du felsöker eller övervakar hur en användare i organisationen ändrade en resurs.

Aktivera Azures resurs loggar för Azures frontend-dörr. Du kan använda Azure Security Center och Azure Policy för att aktivera resurs loggar och insamling av logg data. Dessa loggar kan vara kritiska för senare utredning av säkerhets incidenter och utföra kriminal tekniska övningar.

- [Samla in plattforms loggar och mått med Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Förstå loggning och olika logg typer i Azure](../azure-monitor/essentials/platform-logs-overview.md) 

- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Delad

## <a name="incident-response"></a>Incidenthantering

*Mer information finns i [Azure Security Benchmark: Incidentsvar](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Förberedelse – uppdatera processen för svar på incidenter i Azure

**Vägledning**: se till att din organisation har definierat processer för att svara på säkerhets incidenter. Bekräfta att de här processerna har uppdaterats för Azure-resurser och testa dem regelbundet för att säkerställa beredskap.

- [Implementera säkerhet i hela företagsmiljön](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Referensguide för incidentsvar](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Förberedelse – konfigurera aviseringar vid incidenter

**Vägledning**: Konfigurera kontaktinformation vid säkerhetsincidenter i Azure Security Center. Microsoft använder de här kontaktuppgifterna till att kontakta dig om Microsoft Security Response Center (MSRC) upptäcker obehörig åtkomst till dina data. Det finns även alternativ för att anpassa aviseringar och meddelanden vid incidenter i olika Azure-tjänster baserat på åtgärdsbehovet. 

- [Så här ställer du in säkerhetskontakt i Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center-övervakning**: Ja

**Ansvar**: Kund

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Identifiering och analys – skapa incidenter baserat på aviseringar med hög kvalitet

**Riktlinjer**: Se till att du har en process för att skapa aviseringar med hög kvalitet och att mäta kvaliteten på aviseringar. På så sätt kan du dra slutsatser från tidigare incidenter och prioritera aviseringar för analytiker, så att de inte slösar tid på falska positiva identifieringar. 

Aviseringar med hög kvalitet kan byggas utifrån erfarenhet från tidigare incidenter, validerade community-källor och verktyg som utformats för att generera och rensa aviseringar genom att slå samman och korrelera olika signalkällor. 

Azure Security Center tillhandahåller aviseringar med hög kvalitet för flera olika typer av Azure-tillgångar. Du kan använda anslutningsprogrammet för ASC-data för att strömma aviseringarna till Azure Sentinel. Med Azure Sentinel kan du skapa avancerade aviseringsregler för att generera incidenter automatiskt för en undersökning. 

Exportera dina Azure Security Center-aviseringar och -rekommendationer med hjälp av exportfunktionen för att identifiera risker med Azure-resurser. Exportera aviseringar och rekommendationer antingen manuellt eller löpande.

- [Så här konfigurerar du export](../security-center/continuous-export.md)

- [Så här strömmar du aviseringar till Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Identifiering och analys – undersöka en incident

**Vägledning**: se till att analytiker kan fråga och använda olika data källor när de undersöker potentiella incidenter för att bygga en fullständig bild av vad som hände. Olika logg typer bör samlas in för att spåra aktiviteter för en potentiell angripare över Kill-kedjan för att undvika eventuella blinda fläckar.  Se till att insikter och insikter samlas in för andra analytiker och för framtida historiska referenser.  

Datakällorna för undersökningen innehåller de centraliserade loggningskällor som redan har samlats in från tjänster som omfattas och system som körs, men kan även innehålla:

- Nätverksdata – Använd nätverkssäkerhetsgruppers flödesloggar, Azure Network Watcher och Azure Monitor för att avbilda nätverksflödesloggar och annan analysinformation. 

- Ögonblicksbilder av system som körs: 

    - Använd funktionen för ögonblicksbilder på den virtuella Azure-datorn för att skapa en ögonblicksbild av det aktiva systemets disk. 

    - Använd operativsystemets interna minnesdumpningsfunktion för att skapa en ögonblicksbild av det aktiva systemets minne.

    - Använd funktionen för ögonblicksbilder i Azure-tjänsterna eller din programvaras egna funktion för att skapa ögonblicksbilder av de system som körs.

Azure Sentinel tillhandahåller omfattande dataanalyser i praktiskt taget alla loggkällor och en ärendehanteringsportal för att hantera hela livscykeln för incidenter. Affärsinformation under en undersökning kan associeras med en incident för spårning och rapportering. 

- [Ögonblicksbild av en Windows-dators disk](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Ögonblicksbild av en Linux-dators disk](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Microsoft Azure har stöd för diagnostikinformation och insamling av minnesdumpar](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Undersöka incidenter med Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Azure Security Center-övervakning**: Inte tillämpligt

**Ansvar**: Kund

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Identifiering och analys – prioritera incidenter

**Riktlinjer**: Tillhandahålla en kontext till analytiker om vilka incidenter att fokusera på först baserat på aviseringens allvarlighetsgrad och tillgångens känslighet. 

Azure Security Center tilldelar en allvarlighetsgrad till varje avisering för att hjälpa dig att prioritera vilka aviseringar som bör undersökas först. Allvarlighetsgraden baseras på hur tillförlitligt Security Center är i sökandet eller på den analys som användes för att utfärda aviseringen, samt konfidensnivån att det fanns en skadlig avsikt bakom den aktivitet som ledde till aviseringen.

Markera även resurser med taggar och skapa ett namngivningssystem för att identifiera och kategorisera Azure-resurser, i synnerhet sådana som används för bearbetning av känsliga data.  Det är ditt ansvar att prioritera åtgärdandet av aviseringar baserat på allvarlighetsgraden för de Azure-resurser och den miljö där incidenten inträffade.

- [Säkerhetsaviseringar i Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Använda taggar för att organisera dina Azure-resurser](../azure-resource-manager/management/tag-resources.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Inneslutning, utrotning och återställning – automatisera hanteringen av incidenter

**Vägledning**: Automatisera manuella återkommande uppgifter för att korta ned svarstiderna och underlätta för analytikerna. Det tar längre tid att köra uppgifter manuellt, så att varje incident tar längre tid att hantera och analytikerna hinner med färre incidenter. Manuella uppgifter gör dessutom analytikern mer utmattad, vilket ökar risken för mänskliga fel som i sin tur orsakar fördröjningar, samt minskar analytikernas förmåga att fokusera på komplexa uppgifter på ett effektivt sätt. Använd automatiseringsfunktioner för arbetsflöde i Azure Security Center och Azure Sentinel för att automatiskt utlösa åtgärder eller köra en spelbok för att svara på inkommande säkerhetsaviseringar. Spelboken vidtar åtgärder som att skicka meddelanden, inaktivera konton och isolera problematiska nätverk. 

- [Konfigurera arbetsflödesautomation i Security Center](../security-center/workflow-automation.md)

- [Konfigurera automatiska svar på hot i Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Konfigurera automatiska svar på hot i Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Kund

## <a name="posture-and-vulnerability-management"></a>Hantering av säkerhetsposition och säkerhetsrisker

*Mer information finns i [Azure Security Benchmark: Hantering av säkerhetsposition och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: upprätta säkra konfigurationer för beräknings resurser

**Vägledning**: Använd Azure Security Center och Azure policy för att upprätta säkra konfigurationer på alla beräknings resurser, inklusive Virtual Machines, behållare med mera.

- [Så här övervakar du Azure Security Center rekommendationer](../security-center/security-center-recommendations.md) 

- [Säkerhetsrekommendationer – en referensguide](../security-center/recommendations-reference.md)

**Azure Security Center-övervakning**: Inte tillgänglig för tillfället

**Ansvar**: Delad

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: Åtgärda sårbarheter för programvara snabbt och automatiskt

**Riktlinjer**: Distribuera programuppdateringar snabbt för att åtgärda programvarurelaterade sårbarheter i operativsystem och program.

Prioritera Använd ett gemensamt risk bedömnings program (t. ex. vanliga sårbarhets bedömnings system) eller standard risk klassificeringar som tillhandahålls av ett skannings verktyg från tredje part som du använder. och skräddarsy till din miljö med hjälp av ett sammanhang där programmen utgör en hög säkerhets risk och som kräver hög drift tid.

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: Utför regelbundna angreppssimuleringar

**Vägledning**: Utför intrångstester och ”red team”-aktiviteter för dina Azure-resurser och åtgärda alla kritiska säkerhetsbrister som upptäcks.
Se till att följa reglerna för intrångstester i Microsoft Cloud så att dina tester inte strider mot Microsofts policyer. Använd Microsofts strategi och utförande av ”red team”-aktiviteter och intrångstester live mot molninfrastruktur, tjänster och appar som hanteras av Microsoft.

- [Intrångstester i Azure](../security/fundamentals/pen-testing.md)

- [Regler för intrångstester](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [”Red team”-aktiviteter i Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Delad

## <a name="governance-and-strategy"></a>Styrning och strategi

*Mer information finns i [Azure Security Benchmark: Styrning och strategi](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definiera en strategi för tillgångshantering och dataskydd 

**Vägledning**: Se till att dokumentera och förmedla en tydlig strategi för kontinuerlig övervakning och skydd av system och data. Prioritera identifiering, utvärdering, skydd och övervakning av affärskritiska data och system. 

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   En standard för dataklassificering i enlighet med affärsrisker

-   Säkerhetsorganisationens insyn i risker och tillgångsinventering 

-   Säkerhetsorganisationens godkännande av de Azure-tjänster som används 

-   Tillgångars säkerhet genom hela livscykeln

-   Strategi för nödvändig åtkomstkontroll i enlighet med organisationens dataklassificering

-   Användning av inbyggda säkerhetsfunktioner för Azure- och tredje partsdata

-   Krypteringskrav för användningsfall med data under transport och i vila

-   Lämpliga kryptografiska standarder

Mer information finns på de länkar som refereras till.

- [Rekommendationer för en säkerhetsarkitektur i Azure – lagring, data och kryptering](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Grundläggande Azure-säkerhet – säkerhet, kryptering och lagring av data i Azure](../security/fundamentals/encryption-overview.md)

- [Azure Security Benchmark – dataskydd](../security/benchmarks/security-controls-v2-data-protection.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definiera företagets segmenteringsstrategi 

**Vägledning**: Upprätta en strategi för hela företaget gällande att segmentera åtkomsten till tillgångar utifrån en kombination av identiteter, nätverk, program, prenumerationer, hanteringsgrupper och andra kontroller.

Du måste noga avväga behovet av separationsskyddet med behovet att underlätta den dagliga driften av de system som måste kommunicera med varandra och komma åt data.

Se till att segmenteringsstrategin implementeras konsekvent för olika kontrolltyper som nätverkssäkerhet, modeller för identiteter och åtkomst, modeller för appbehörighet och appåtkomst samt kontroller för mänskliga processer.

- [Vägledning om segmenteringsstrategi i Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Vägledning om segmenteringsstrategi i Azure (dokument)](/security/compass/governance#enterprise-segmentation-strategy)

- [Justera nätverkssegmenteringen efter företagets segmenteringsstrategi](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definiera en strategi för hantering av säkerhetspositionen

**Vägledning**: Mät och minimera risker gällande enskilda tillgångar och miljöerna där de finns regelbundet. Prioritera höga värde till gångar och högexponerade attack ytor, till exempel publicerade program, ingångs-och utgångs punkter, användar-och administratörs slut punkter och så vidare.

- [Azure Security Benchmark – hantering av säkerhetspositionen och säkerhetsrisker](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Justera organisationens roller och ansvarsområden

**Vägledning**: Dokumentera och förmedla en tydlig strategi gällande säkerhetsorganisationens roller och ansvarsområden. Prioritera att delegera ett tydligt ansvar för olika säkerhetsbeslut och utbilda alla kring modellen med gemensamt ansvar, och ge de tekniska teamen den utbildning som behövs kring tekniken för att skydda molnet.

- [Regelverk för Azure-säkerhet 1 – personal: utbilda teamen om molnsäkerhetsresan](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Regelverk för Azure-säkerhet 2 – personal: utbilda teamen om molnsäkerhetstekniken](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Regelverk för Azure-säkerhet 3 – personal: tilldela ansvar för molnsäkerhetsbeslut](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definiera en strategi för nätverkssäkerhet

**Vägledning**: Upprätta en strategi för nätverkssäkerhet i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Centraliserade ansvarsområden kring nätverkshantering och säkerhet

-   Modell för segmentering av virtuella nätverk anpassad efter företagets segmenteringsstrategi

-   Åtgärdsstrategi för olika hot- och angreppsscenarier

-   Strategi för kantenheter på internet samt in- och utgångar

-   Strategi för hybridmoln och lokala anslutningar

-   Aktuella nätverkssäkerhetsartefakter (som nätverksdiagram och referensnätverksarkitekturer)

Mer information finns på de länkar som refereras till.

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark – nätverkssäkerhet](../security/benchmarks/security-controls-v2-network-security.md)

- [Översikt över nätverkssäkerhet i Azure](../security/fundamentals/network-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definiera en strategi för identiteter och privilegierad åtkomst

**Vägledning**: Upprätta en strategi för identiteter och privilegierad åtkomst i Azure inom ramen för organisationens övergripande strategi för åtkomstkontroll.  

Strategin bör omfatta dokumenterad vägledning, policyer och standarder för följande element: 

-   Ett centraliserat system för identiteter och autentisering och dess anslutningar till andra interna och externa identitetssystem

-   Starka autentiseringsmetoder i olika användningsfall och scenarier

-   Skydda och användare med hög behörighet

-   Övervaka och hantera avvikande användaraktiviteter  

-   Process för att granska och stämma av identiteter och åtkomstbehörighet

Läs mer i följande referenser:

- [Azure Security Benchmark – hantering av identiteter](../security/benchmarks/security-controls-v2-identity-management.md)

- [Azure Security Benchmark – privilegierad åtkomst](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Regelverk för Azure-säkerhet 11 – arkitektur. En enhetlig säkerhetsstrategi](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Översikt över säker identitetshantering i Azure](../security/fundamentals/identity-management-overview.md)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definiera en strategi för loggning och hotåtgärder

**Riktlinjer**: Upprätta en strategi för loggning och hothantering för att snabbt upptäcka och åtgärda hot och samtidigt uppfylla kraven för efterlevnad. Prioritera att ge analytiker högkvalitativa varningar och sömlösa upplevelser så att de kan fokusera på hot i stället för på integrering och manuella åtgärder. 

Strategin bör omfatta dokumenterad vägledning, principer och standarder för följande element: 

-   Säkerhetsorganisationens roller och ansvarsområden 

-   En väldefinierad process för incidentsvar anpassad efter NIST eller något annat branschramverk 

-   Insamling och kvarhållning av loggar som stöd till hotidentifiering, incidentsvar och efterlevnadskrav

-   Central insyn i och korrelerande information om olika hot med hjälp av SIEM, interna Azure-funktioner och andra källor 

-   Plan för kommunikation med kunder, leverantörer och offentliga intressenter

-   Användning av plattformar för incidenthantering i Azure och från tredje part, till exempel för loggning och hotidentifiering, datautredning och motverkande åtgärder

-   Processer för hantering av incidenter och efterföljande aktiviteter som hantering av lärdomar och bevis

Mer information finns på de länkar som refereras till.

- [Azure Security Benchmark – loggning och hotidentifiering](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark – svar på incidenter](../security/benchmarks/security-controls-v2-incident-response.md)

- [Regelverk för Azure-säkerhet 4 – process: uppdatera processer kring incidentsvar för molnet](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Azure Adoption Framework, guide till beslut om loggning och rapporter](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting)

**Övervakning i Azure Security Center**: Ej tillämpligt

**Ansvar**: Kund

## <a name="next-steps"></a>Nästa steg

- Läs mer i [Översikten över Azure Security Benchmark V2](../security/benchmarks/overview.md)
- Läs mer om [säkerhetsbaslinjer för Azure](../security/benchmarks/security-baselines-overview.md)