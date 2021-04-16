---
title: Vägledning och metodtips
description: Upptäck metodtips och vägledning för att arbetsbelastningen i molnet och lokalt till molnet
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 14476533cf896434182e1d63f89c6a1279b36362
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519071"
---
# <a name="backup-cloud-and-on-premises-workloads-to-cloud"></a>Säkerhetskopiera arbetsbelastningar i molnet och lokala arbetsbelastningar till molnet

## <a name="introduction"></a>Introduktion

Azure Backup skyddar dina datatillgångar i Azure på ett omfattande sätt med en enkel, säker och kostnadseffektiv lösning som kräver noll infrastruktur. Det är Azures inbyggda dataskyddslösning för en mängd olika arbetsbelastningar. Det hjälper till att skydda dina verksamhetskritiska arbetsbelastningar som körs i molnet och säkerställer att dina säkerhetskopior alltid är tillgängliga och hanteras i stor skala i hela din säkerhetskopia.

### <a name="intended-audience"></a>Målgrupp

Den primära målgruppen för den här artikeln är IT- och programadministratörer och implementerare av stora och medelstora organisationer som vill lära sig mer om funktionerna i Azures inbyggda dataskyddsteknik, Azure Backup och hur du effektivt implementerar lösningar som bättre skyddar dina distributioner. Artikeln förutsätter att du är bekant med grundläggande Azure-tekniker, dataskyddsbegrepp och har erfarenhet av att arbeta med en säkerhetskopieringslösning. De riktlinjer som beskrivs i den här artikeln kan göra det enklare att utforma din säkerhetskopieringslösning i Azure med hjälp av etablerade mönster och undvika kända fallgropar.

### <a name="how-this-article-is-organized"></a>Så här organiseras den här artikeln

Det är enkelt att börja skydda infrastruktur och program i Azure, men när du ser till att de underliggande Azure-resurserna är korrekt konfigurerade och används optimalt kan du påskynda tiden till värde. Den här artikeln innehåller en kort översikt över designöverväganden och vägledning för optimal konfiguration av Azure Backup distribution. Den undersöker huvudkomponenterna (till exempel Recovery Services-valv, säkerhetskopieringspolicy) och begrepp (till exempel styrning) och hur du tänker på dem och deras funktioner med länkar till detaljerad produktdokumentation.

## <a name="architecture"></a>Arkitektur

![Azure Backup-arkitektur](./media/guidance-best-practices/azure-backup-architecture.png)

### <a name="workloads"></a>Arbetsbelastningar

Azure Backup dataskydd för olika arbetsbelastningar (lokalt och i molnet). Det är en säker och tillförlitlig inbyggd dataskyddsmekanism i Azure. Den kan sömlöst skala sitt skydd över flera arbetsbelastningar utan hanteringskostnader för dig. Det finns även flera automatiseringskanaler för att aktivera detta (via PowerShell, CLI, Azure Resource Manager-mallar och REST-API:er.)

* **Skalbar, beständig och säker lagring –** Azure Backup använder tillförlitlig Blob Storage med inbyggda funktioner för säkerhet och hög tillgänglighet. Du kan välja LRS-, GRS- eller RA-GRS-lagring för dina säkerhetskopierade data.  

* **Intern arbetsbelastningsintegrering –** Azure Backup ger intern integrering med Azure-arbetsbelastningar (virtuella datorer, SAP HANA, SQL på virtuella Azure-datorer och även Azure Files) utan att du behöver hantera automatisering eller infrastruktur för att distribuera agenter, skriva nya skript eller etablera lagring.

### <a name="data-plane"></a>Dataplanet

* **Automatiserad lagringshantering** – Azure Backup etablerar och hanterar lagringskonton för säkerhetskopierade data så att de skalas när säkerhetskopierade data växer.

* **Skydd mot skadlig borttagning –** Skydda mot oavsiktliga och skadliga försök att ta bort säkerhetskopior via mjuk borttagning av säkerhetskopior. Borttagna säkerhetskopierade data lagras kostnadsfritt i 14 dagar och gör att de kan återställas från det här tillståndet.

* **Skydda krypterade säkerhetskopior–** Azure Backup ser till att dina säkerhetskopierade data lagras på ett säkert sätt, med hjälp av inbyggda säkerhetsfunktioner på Azure-plattformen som Azure RBAC och Encryption.

* **Livscykelhantering för säkerhetskopieringsdata –** Azure Backup rensar automatiskt äldre säkerhetskopierade data för att uppfylla kvarhållningsprinciperna. Du kan också nivåindelad data från driftlagring till valvlagring.

### <a name="management-plane"></a>Hanteringsplanet

* **Åtkomstkontroll** – Valv (Recovery Services och säkerhetskopieringsvalv) ger hanteringsfunktioner och är tillgängliga via Azure Portal, Backup Center, valvinstrumentpaneler, SDK, CLI och till och med REST API:er. Det är också en Azure RBAC-gräns, vilket ger dig möjlighet att begränsa åtkomsten till säkerhetskopior till endast auktoriserade säkerhetskopieringsadministratörer.

* **Principhantering** – Azure Backup principer i varje valv definierar när säkerhetskopiorna ska utlösas och hur länge de måste behållas. Du kan också hantera dessa principer och tillämpa dem på flera objekt.

* **Övervakning och rapportering** – Azure Backup integreras med Log Analytics och ger möjlighet att se rapporter via arbetsböcker också.

* **Hantering av** ögonblicksbilder – Azure Backup tar ögonblicksbilder för vissa inbyggda Azure-arbetsbelastningar (virtuella datorer och Azure Files), hanterar dessa ögonblicksbilder och tillåter snabba återställningar från dem. Det här alternativet minskar drastiskt tiden för att återställa dina data till den ursprungliga lagringen.

## <a name="vault-considerations"></a>Valvöverväganden

Azure Backup använder valv (Recovery Services och Backup-valv) för att orkestrera och hantera säkerhetskopior. Den använder också valv för att lagra säkerhetskopierade data. Effektiv valvdesign hjälper organisationer att upprätta en struktur för att organisera och hantera säkerhetskopieringstillgångar i Azure för att stödja dina affärsprioriteringar. Tänk på följande när du skapar ett valv:  

### <a name="align-to-subscription-design-strategy"></a>Anpassa till prenumerationens designstrategi

Eftersom valvet är omfånget för en prenumeration passar valvdesignen för att uppfylla prenumerationens designstrategi, till exempel programkategoristrategi, där prenumerationer avgränsas baserat på specifika program eller tjänster eller längs rader med programarketyper.  Mer information finns i den här [artikeln](/azure/cloud-adoption-framework/decision-guides/subscriptions/).

### <a name="single-or-multiple-vault"></a>Ett eller flera valv

Du kan använda ett enda valv eller flera valv för att organisera och hantera säkerhetskopieringen. Läs igenom följande riktlinjer:

* Om alla dina arbetsbelastningar hanteras av en enda prenumeration och en enskild resurs kan du använda ett enda valv för att övervaka och hantera din säkerhetskopieringse egendom.

* Om dina arbetsbelastningar är fördelade mellan prenumerationer kan du skapa flera valv, ett eller flera per prenumeration.
  * Med Backup Center kan du ha en enda ruta för att hantera alla uppgifter som rör säkerhetskopiering. [Mer information finns här]().
  * Du kan anpassa dina vyer med arbetsboksmallar. Backup-utforskaren är en sådan mall för virtuella Azure-datorer. [Mer information finns här](monitor-azure-backup-with-backup-explorer.md).
  * Om du behöver konsekventa policyer mellan valv kan du använda Azure Policy för att sprida säkerhetskopieringspolicyer över flera valv. Du kan skriva en [anpassad Azure Policy definition](../governance/policy/concepts/definition-structure.md) som använder effekten ["deployifnotexists"](../governance/policy/concepts/effects.md#deployifnotexists) för att sprida en säkerhetskopieringspolicy över flera valv. Du kan [också](../governance/policy/assign-policy-portal.md) tilldela den här Azure Policy-definitionen till ett visst omfång (prenumeration eller RG), så att den distribuerar en "säkerhetskopieringspolicy"-resurs till alla Recovery Services-valv i omfånget för Azure Policy tilldelning. Inställningarna för säkerhetskopieringsprincipen (till exempel säkerhetskopieringsfrekvens, kvarhållning och så vidare) ska anges av användaren som parametrar i Azure Policy tilldelning.

* När organisationens fotavtryck växer kanske du vill flytta arbetsbelastningar mellan prenumerationer av följande skäl: justera efter säkerhetskopieringspolicy, konsolidera valv, minska kostnaderna genom att minska redundansen (gå från GRS till LRS).  Azure Backup har stöd för att flytta ett Recovery Services-valv mellan Azure-prenumerationer eller till en annan resursgrupp inom samma prenumeration. [Mer information finns här](backup-azure-move-recovery-services-vault.md).

### <a name="review-default-settings"></a>Granska standardinställningarna

Granska standardinställningarna för Lagringsreplikeringstyp och Säkerhetsinställningar för att uppfylla dina krav innan du konfigurerar säkerhetskopieringar i valvet.

* *Lagringsreplikeringstypen* är som standard inställd på Geo-redundant (GRS). När du har konfigurerat säkerhetskopieringen inaktiveras alternativet att ändra. Följ [de](backup-create-rs-vault.md#set-storage-redundancy) här stegen för att granska och ändra inställningarna.

* *Mjuk borttagning* är som standard Aktiverat för nyligen skapade valv för att skydda säkerhetskopierade data mot oavsiktliga eller skadliga borttagningar. Följ [de](backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete) här stegen för att granska och ändra inställningarna.

* *Med återställning mellan* regioner kan du återställa virtuella Azure-datorer i en sekundär region, som är en länkad Azure-region. Med det här alternativet kan du utföra övningar för att uppfylla gransknings- eller efterlevnadskrav och återställa den virtuella datorn eller dess disk vid ett haveri i den primära regionen. CRR är en funktion för val av GRS-valv. [Mer information finns här](backup-create-rs-vault.md#set-cross-region-restore).

* Innan du slutför valvdesignen granskar du [valvsupportmatriserna](backup-support-matrix.md#vault-support) för att förstå de faktorer som kan påverka eller begränsa dina designval.

## <a name="backup-policy-considerations"></a>Överväganden för säkerhetskopieringspolicy

Azure Backup princip har två komponenter: *Schemalägg* (när säkerhetskopiering ska ske) och *Kvarhållning* (hur lång tid det tar att behålla säkerhetskopian). Du kan definiera principen baserat på vilken typ av data som säkerhetskopieras, RTO/RPO-krav, drift- eller regelefterlevnadsbehov och arbetsbelastningstyp (till exempel virtuell dator, databas, filer). [Mer information finns här](backup-architecture.md#backup-policy-essentials).

Tänk på följande när du skapar säkerhetskopieringspolicy:

### <a name="schedule-considerations"></a>Att tänka på när du schemalägger

* Överväg att gruppera virtuella datorer som kräver samma schemastarttid, frekvens och kvarhållningsinställningar inom en enda princip.

* Se till att den schemalagda starttiden för säkerhetskopieringen är under programtiden för icke-högsta produktion.

* Om du vill distribuera säkerhetskopieringstrafik bör du överväga att säkerhetskopiera olika virtuella datorer vid olika tidpunkter på dagen och se till att tiderna inte överlappar varandra.

### <a name="retention-considerations"></a>Överväganden för kvarhållning

* Kortsiktig kvarhållning kan vara "minuter" eller "dagligen". Kvarhållning för "veckovisa", "månatliga" eller "årliga" säkerhetskopieringspunkter kallas långsiktig kvarhållning.

* Långsiktig kvarhållning:
  * Planerad (efterlevnadskrav) – om du i förväg vet att data krävs år från den aktuella tiden använder du långsiktig kvarhållning.
  * Oplanerat (krav på begäran) – om du inte vet i förväg kan du använda på begäran med specifika anpassade kvarhållningsinställningar (dessa anpassade kvarhållningsinställningar påverkas inte av principinställningarna).

* Säkerhetskopiering på begäran med anpassad kvarhållning – om du behöver göra en säkerhetskopiering som inte har schemalagts via en säkerhetskopieringspolicy kan du använda en säkerhetskopiering på begäran. Detta kan vara användbart för säkerhetskopieringar som inte passar din schemalagda säkerhetskopiering eller för att göra detaljerad säkerhetskopiering (till exempel flera IaaS VM-säkerhetskopieringar per dag eftersom schemalagd säkerhetskopiering endast tillåter en säkerhetskopiering per dag). Det är viktigt att observera att kvarhållningsprincipen som definierats i den schemalagda principen inte gäller för säkerhetskopieringar på begäran.

### <a name="optimize-backup-policy"></a>Optimera säkerhetskopieringspolicy

* När dina affärsbehov ändras kan du behöva utöka eller minska kvarhållningstiden. När du gör det kan du förvänta dig följande:  
  * Om kvarhållningen utökas markeras och behålls befintliga återställningspunkter i enlighet med den nya principen.
  * Om kvarhållningen minskar markeras återställningspunkter för rensning vid nästa rensningsjobb och tas sedan bort.
  * De senaste kvarhållningsreglerna gäller för alla kvarhållningspunkter (exklusive kvarhållningspunkter på begäran). Så om kvarhållningsperioden utökas (till exempel till 100 dagar) bevaras alla säkerhetskopierade data enligt den senaste angivna kvarhållningsperioden (det vill säga 7 dagar) när säkerhetskopian tas, följt av en kvarhållningsminskning (till exempel från 100 dagar till sju dagar).

* Azure Backup ger dig flexibiliteten att *sluta skydda och hantera dina säkerhetskopior:*
  * *Stoppa skyddet och behåll säkerhetskopierade data*. Om du tar ur bruk eller inaktiverar din datakälla (VM, program), men behöver behålla data i gransknings- eller efterlevnadssyfte, kan du använda det här alternativet för att hindra alla framtida säkerhetskopieringsjobb från att skydda datakällan och behålla återställningspunkterna som har säkerhetskopierats. Du kan sedan återställa eller återuppta skyddet av virtuella datorer.
  * *Stoppa skyddet och ta bort säkerhetskopierade data*. Det här alternativet stoppar alla framtida säkerhetskopieringsjobb från att skydda den virtuella datorn och tar bort alla återställningspunkter. Du kommer inte att kunna återställa den virtuella datorn eller använda alternativet Återuppta säkerhetskopiering.

  * Om du återupptar skyddet (för en datakälla som har stoppats med kvarhållningsdata) gäller kvarhållningsreglerna. Alla utgångna återställningspunkter tas bort (vid den schemalagda tidpunkten).

* Innan du slutför principdesignen är det viktigt att du är medveten om följande faktorer som kan påverka dina designval.
  * En säkerhetskopieringspolicy är begränsad till ett valv.
  * Det finns en gräns för antalet objekt per princip (till exempel 100 virtuella datorer). Om du vill skala kan du skapa duplicerade principer med samma eller olika scheman.
  * Du kan inte ta bort specifika återställningspunkter selektivt.
  * Du kan inte helt inaktivera den schemalagda säkerhetskopieringen och behålla datakällan i ett skyddat tillstånd. Den minst frekventa säkerhetskopiering som du kan konfigurera med principen är att ha en veckovis schemalagd säkerhetskopiering. Ett alternativ är att stoppa skyddet med kvarhållna data och aktivera skydd varje gång du vill göra en säkerhetskopiering, göra en säkerhetskopiering på begäran och sedan stänga av skyddet men behålla säkerhetskopieringsdata. [Mer information finns här](backup-azure-manage-vms.md#stop-protecting-a-vm).

## <a name="security-considerations"></a>Säkerhetsöverväganden

För att hjälpa dig att skydda dina säkerhetskopierade data och uppfylla företagets säkerhetsbehov ger Azure Backup sekretess-, integritets- och tillgänglighetsgaranti mot avsiktliga attacker och missbruk av dina värdefulla data och system. Tänk på följande säkerhetsriktlinjer för din Azure Backup lösning:

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

* Rollbaserad åtkomstkontroll i Azure (Azure RBAC) möjliggör mer information om åtkomsthantering, ansvarsfördelning i ditt team och beviljar endast den mängd åtkomst till användare som behövs för att utföra sitt arbete. [Mer information finns här](backup-rbac-rs-vault.md).

* Azure Backup har tre inbyggda roller för att styra säkerhetskopieringshanteringsåtgärder: säkerhetskopieringsdeltagare, operatörer och läsare. [Mer information finns här](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions).

* Azure Backup har flera inbyggda säkerhetskontroller i tjänsten för att förhindra, identifiera och åtgärda säkerhetsproblem (Läs mer)

* Lagringskonton som används av Recovery Services-valv är isolerade och kan inte användas av användare i skadliga syften. Åtkomsten tillåts endast via Azure Backup hanteringsåtgärder, till exempel återställning.

### <a name="encryption-of-data-in-transit-and-at-rest"></a>Kryptering av data under överföring och i vila

Kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets- och efterlevnadsåtaganden.

* I Azure skyddas data under överföring mellan Azure Storage och valvet av HTTPS. Dessa data finns kvar i Azure-nätverket.

* Säkerhetskopierade data krypteras automatiskt med microsoft-hanterade nycklar. Du kan också använda dina egna nycklar, även kallade kund [hanterade nycklar.](encryption-at-rest-with-cmk.md)

* Azure Backup stöder säkerhetskopiering och återställning av virtuella Azure-datorer som har sina OS-/datadiskar krypterade med Azure Disk Encryption (ADE). [Mer information finns här](backup-azure-vms-encryption.md).

### <a name="protection-of-backup-data-from-unintentional-deletes"></a>Skydd av säkerhetskopierade data från oavsiktliga borttagningar

Azure Backup säkerhetsfunktioner som skyddar säkerhetskopierade data även efter borttagning. Om en användare tar bort säkerhetskopian med mjuk borttagning (för en virtuell dator, SQL Server-databas, Azure-filresurs, SAP HANA-databas) bevaras säkerhetskopierade data i ytterligare 14 dagar, vilket gör att säkerhetskopieringsobjektet kan återställas utan dataförlust. De ytterligare 14 dagarnas kvarhållning av säkerhetskopierade data i läget "mjuk borttagning" medför inte någon kostnad för dig. [Mer information finns här](backup-azure-security-feature-cloud.md).

### <a name="monitoring-and-alerts-of-suspicious-activity"></a>Övervakning och aviseringar om misstänkt aktivitet

Azure Backup har inbyggda övervaknings- och aviseringsfunktioner för att visa och konfigurera åtgärder för händelser relaterade till Azure Backup. [Mer information finns här](security-overview.md#monitoring-and-alerts-of-suspicious-activity).

### <a name="security-features-to-help-protect-hybrid-backups"></a>Säkerhetsfunktioner för att skydda hybridsäkerhetskopior

Azure Backup-tjänsten använder MARS-agenten (Microsoft Azure Recovery Services) för att backa upp och återställa filer, mappar och volym- eller systemtillståndet från en lokal dator till Azure. MARS innehåller nu säkerhetsfunktioner: en lösenfras som ska krypteras före överföring och dekryptering efter nedladdning från Azure Backup, borttagna säkerhetskopierade data bevaras i ytterligare 14 dagar från borttagningsdatumet och kritisk åtgärd (t.ex. ändra en lösenfras) kan endast utföras av användare som har giltiga Azure-autentiseringsuppgifter. [Mer information finns här](backup-azure-security-feature.md).

## <a name="network-considerations"></a>Nätverksöverväganden

Azure Backup kräver förflyttning av data från din arbetsbelastning till Recovery Services-valvet. Azure Backup innehåller flera funktioner för att skydda säkerhetskopierade data från att exponeras oavsiktligt (till exempel en man-in-the-middle-attack i nätverket). Läs igenom följande riktlinjer:

### <a name="internet-connectivity"></a>Internetanslutning

* *Säkerhetskopiering av virtuella Azure-datorer* – all nödvändig kommunikation och dataöverföring mellan lagring och Azure Backup-tjänsten sker i Azure-nätverket utan att du behöver komma åt ditt virtuella nätverk. Säkerhetskopiering av virtuella Azure-datorer i skyddade nätverk kräver därför inte att du tillåter åtkomst till IP-adresser eller FQDN.

* *SAP HANA databaser på virtuella Azure SQL Server databaser* på virtuella Azure-datorer – kräver anslutning till Azure Backup-tjänsten, Azure Storage och Azure Active Directory. Detta kan uppnås med hjälp av privata slutpunkter eller genom att tillåta åtkomst till de offentliga IP-adresser eller FQDN som krävs. Om du inte tillåter korrekt anslutning till de Azure-tjänster som krävs kan det leda till fel i åtgärder som databasidentifiering, konfiguration av säkerhetskopiering, säkerhetskopiering och återställning av data. Fullständig nätverksvägledning när du använder NSG-taggar, Azure Firewall och HTTP Proxy finns i dessa [SQL-](backup-sql-server-database-azure-vms.md#establish-network-connectivity) och [SAP HANA artiklar.](./backup-azure-sap-hana-database.md#establish-network-connectivity)

* *Hybrid* – MARS-agenten (Microsoft Azure Recovery Services) kräver nätverksåtkomst för alla kritiska åtgärder – installera, konfigurera, säkerhetskopiera och återställa. MARS-agenten kan ansluta till Azure Backup-tjänsten via [Azure ExpressRoute](install-mars-agent.md#use-azure-expressroute) med hjälp av offentlig peering (tillgänglig för [](install-mars-agent.md#private-endpoints) gamla kretsar) och Microsoft-peering, med hjälp av privata slutpunkter eller via [proxy/brandvägg](install-mars-agent.md#verify-internet-access)med lämpliga åtkomstkontroller .

### <a name="private-endpoints-for-azure-backup"></a>Privata slutpunkter för Azure Backup

Azure [Private Endpoint](../private-link/private-endpoint-overview.md) är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Azure Backup kan du på ett säkert sätt backa upp och återställa data från Recovery Services-valv med hjälp av privata slutpunkter.

* När du aktiverar privata slutpunkter för valvet används de bara för säkerhetskopiering och återställning av SQL- och SAP HANA-arbetsbelastningar i en virtuell Azure-dator och mars-agentsäkerhetskopior.  Du kan även använda valvet för säkerhetskopiering av andra arbetsbelastningar (de kräver dock inte privata slutpunkter). Förutom säkerhetskopiering av SQL- och SAP HANA-arbetsbelastningar och säkerhetskopiering med HJÄLP av MARS-agenten används även privata slutpunkter för att utföra filåterställning vid säkerhetskopiering av virtuella Azure-datorer. [Mer information finns här](private-endpoints.md#recommended-and-supported-scenarios).

* Azure Active Directory stöder för närvarande inte privata slutpunkter. Ip-adresser och FQDN som krävs för Azure Active Directory måste därför tillåtas utgående åtkomst från det skyddade nätverket när du säkerhetskopierar databaser på virtuella Azure-datorer och säkerhetskopierar med HJÄLP av MARS-agenten. Du kan också använda NSG-taggar Azure Firewall taggar för att tillåta åtkomst till Azure AD, efter vad som är tillämpligt. Läs mer om [förutsättningarna här.](./private-endpoints.md#before-you-start)

## <a name="governance-considerations"></a>Saker att tänka på i samband med styrning

Styrning i Azure implementeras främst med [Azure Policy](../governance/policy/overview.md) och [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md). [Azure Policy](../governance/policy/overview.md) kan du skapa, tilldela och hantera principdefinitioner för att tillämpa regler för dina resurser. Den här funktionen ser till att resurserna efterlever företagets standarder. [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) kan du spåra molnanvändning och utgifter för dina Azure-resurser och andra molnleverantörer. Dessutom spelar följande verktyg som [Azure Price Calculator](https://azure.microsoft.com/pricing/calculator/) och [Azure Advisor](../advisor/advisor-overview.md)  en viktig roll i kostnadshanteringsprocessen.

### <a name="azure-backup-support-two-key-scenarios-via-built-in-azure-policy"></a>Azure Backup två viktiga scenarier via inbyggda Azure Policy

* Se till att nyligen skapade affärskritiska datorer säkerhetskopieras automatiskt med rätt kvarhållningsinställningar. Azure Backup en inbyggd princip (med Azure Policy) som kan tilldelas till alla virtuella Azure-datorer på en angiven plats i en prenumeration eller resursgrupp. När den här principen tilldelas till ett visst omfång konfigureras alla nya virtuella datorer som skapas i det omfånget automatiskt för säkerhetskopiering till ett befintligt valv på samma plats och prenumeration. Användaren kan ange valvet och kvarhållningsprincipen som de säkerhetskopierade virtuella datorerna ska associeras med. [Mer information finns här](backup-azure-auto-enable-backup.md).

* Se till att nyligen skapade valv har diagnostik aktiverat för att stödja rapporter. Ofta kan det vara besvärligt att lägga till en diagnostikinställning manuellt per valv. Dessutom måste alla nya valv som skapas ha diagnostikinställningar aktiverade så att du kan visa rapporter för det här valvet. För att förenkla skapandet av diagnostikinställningar i stor skala (med Log Analytics som mål) tillhandahåller Azure Backup en inbyggd Azure Policy. Den här principen lägger till en LA-diagnostikinställning till alla valv i varje prenumeration eller resursgrupp. Följande avsnitt innehåller anvisningar om hur du använder den här principen. [Mer information finns här](azure-policy-configure-diagnostics.md).

### <a name="azure-backup-cost-considerations"></a>Azure Backup kostnadsöverväganden

Med Azure Backup-tjänstens funktioner får du flexibiliteten att effektivt hantera dina kostnader och ändå uppfylla affärsbehoven för BCDR (affärskontinuier och haveriberedskap). Läs igenom följande riktlinjer:

* Använd priskalkylatorn för att utvärdera och optimera kostnader genom att justera olika snedtryck. [Läs mer här](azure-backup-pricing.md)

* Backup-utforskaren: Använd Backup-utforskaren eller Backup-rapporter för att förstå och optimera tillväxten av lagring av säkerhetskopior, stoppa säkerhetskopiering för icke-kritiska arbetsbelastningar eller borttagna virtuella datorer. Du kan få en aggregerad vy över hela egendomen ur ett säkerhetskopieringsperspektiv. Detta omfattar inte bara information om dina säkerhetskopieringsobjekt, utan även resurser som inte har konfigurerats för säkerhetskopiering. Detta säkerställer att du aldrig missar att skydda viktiga data i din växande egendom och dina säkerhetskopior är optimerade för icke-kritiska arbetsbelastningar eller borttagna arbetsbelastningar.

* Optimera säkerhetskopieringspolicy
  * Optimera inställningar för schema och kvarhållning baserat på arbetsbelastningsarketyper (till exempel verksamhetskritiska, icke-kritiska)
  * Optimera kvarhållningsinställningar för omedelbar återställning
  * Välj rätt typ av säkerhetskopiering för att uppfylla kraven, samtidigt som du tar säkerhetskopieringstyper som stöds (fullständig, inkrementell, logg, differentiell) av arbetsbelastningen i Azure Backup beaktas.

* Selektiv säkerhetskopiering av diskar: Undanta disk (förhandsgranskningsfunktion) är ett effektivt och kostnadseffektivt alternativ för selektiv säkerhetskopiering av kritiska data. Du kan till exempel bara missa en disk när du inte vill backa upp resten av diskarna som är anslutna till en virtuell dator. Detta är också användbart när du har flera säkerhetskopieringslösningar. När du till exempel säkerhetskopierar dina databaser eller data med en säkerhetskopieringslösning för arbetsbelastningar (SQL Server-databas i säkerhetskopiering av virtuella Azure-datorer) och du vill använda säkerhetskopiering på azure-nivå för valda diskar.

* Azure Backup tar ögonblicksbilder av virtuella Azure-datorer och lagrar dem tillsammans med diskarna för att öka skapandet av återställningspunkt och för att påskynda återställningsåtgärder. Detta kallas omedelbar återställning. Ögonblicksbilder av omedelbar återställning sparas som standard i två dagar. Med den här funktionen kan du återställa från dessa ögonblicksbilder genom att minska återställningstiderna. Det minskar den tid som krävs för att transformera och kopiera data tillbaka från valvet. Därför ser du lagringskostnader som motsvarar ögonblicksbilder som tagits under den här perioden. [Mer information finns här](backup-instant-restore-capability.md#configure-snapshot-retention).

* Azure Backup lagringsreplikeringstypen är som standard inställd på Geo-redundant (GRS). Det här alternativet kan inte ändras efter att objekt har skyddats. Geo-redundant lagring (GRS) ger en högre nivå av datatillförlitlighet än Lokalt redundant lagring (LRS), vilket gör att du kan välja att använda återställning mellan regioner och kostar mer. Granska avvägningarna mellan lägre kostnader och högre datatillförlitlighet och bestäm vad som är bäst för ditt scenario. [Läs mer här](backup-create-rs-vault.md#set-storage-redundancy)

* Om du skyddar både arbetsbelastningen som körs på en virtuell dator och själva den virtuella datorn kontrollerar du om det dubbla skyddet behövs.

## <a name="monitoring-and-alerting-considerations"></a>Överväganden för övervakning och avisering

Som säkerhetskopieringsanvändare eller administratör bör du kunna övervaka alla säkerhetskopieringslösningar och få meddelanden om viktiga scenarier. Det här avsnittet beskriver övervaknings- och meddelandefunktionerna som tillhandahålls av Azure Backup tjänsten.

### <a name="monitoring"></a>Övervakning

* Azure Backup tillhandahåller **inbyggd jobbövervakning för** åtgärder som att konfigurera säkerhetskopiering, säkerhetskopiering, återställning, borttagning av säkerhetskopiering och så vidare. Det här är omfånget för valvet och idealiskt för övervakning av ett enda valv. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault).

* Om du behöver övervaka driftaktiviteter i stor skala **ger Backup-utforskaren** en sammanställd vy över hela din säkerhetskopieringse egendom, vilket möjliggör detaljerad analys och felsökning av detaljerad detaljnivå. Det är en inbyggd Azure Monitor-arbetsbok som ger dig en enda central plats som hjälper dig att övervaka driftaktiviteter i hela säkerhetskopieringse egendomen i Azure, som omfattar klienter, platser, prenumerationer, resursgrupper och valv. [Mer information finns här](monitor-azure-backup-with-backup-explorer.md).
  * Använd den för att identifiera resurser som inte har konfigurerats för säkerhetskopiering och se till att du aldrig missar att skydda viktiga data i din växande egendom.
  * Instrumentpanelen innehåller driftaktiviteter för de senaste sju dagarna (max). Om du behöver behålla dessa data kan du exportera som en Excel-fil och behålla dem.
  * Om du är Azure Lighthouse användare kan du visa information över flera klientorganisationsklienter, vilket möjliggör gräns-mindre övervakning.

* Om du behöver behålla och visa driftaktiviteter för långsiktighet använder du **Rapporter**. Ett vanligt krav för säkerhetskopieringsadministratörer är att få insikter om säkerhetskopior baserat på data som sträcker sig över en längre tidsperiod. Användningsfall för en sådan lösning är:
  * Allokera och göra prognoser för förbrukad molnlagring.
  * Granskning av säkerhetskopieringar och återställningar.
  * Identifiera viktiga trender med olika kornighetsnivåer.

* Dessutom ger
  * Du kan skicka data (till exempel jobb, principer och så vidare) till **Log Analytics-arbetsytan.** Detta aktiverar funktionerna i Azure Monitor-loggar för att möjliggöra korrelation av data med andra övervakningsdata som samlas in av Azure Monitor, konsolidera loggposter från flera Azure-prenumerationer och klienter till en plats för analys tillsammans, använda loggfrågor för att utföra komplex analys och få djupa insikter om loggposter. [Mer information finns här](../azure-monitor/essentials/activity-log.md#send-to-log-analytics-workspace).
  * Du kan skicka data till händelsehubben för att skicka poster utanför Azure, till exempel till en SIEM från tredje part (säkerhetsinformation och händelsehantering) eller någon annan lösning för logganalys. [Mer information finns här](../azure-monitor/essentials/activity-log.md#send-to-azure-event-hubs).
  * Du kan skicka data till ett Azure Storage-konto om du vill behålla dina loggdata längre än 90 dagar för granskning, statisk analys eller säkerhetskopiering. Om du bara behöver behålla dina händelser i 90 dagar eller mindre behöver du inte konfigurera arkiv till ett lagringskonto eftersom aktivitetslogghändelser sparas på Azure-plattformen i 90 dagar. [Läs mer](../azure-monitor/essentials/activity-log.md#send-to--azure-storage).

### <a name="alerting"></a>Aviseringar

* Aviseringar är främst ett sätt att få ett meddelande om att vidta relevanta åtgärder. Avsnittet Säkerhetskopieringsaviseringar visar aviseringar som genererats av Azure Backup tjänsten.

* Azure Backup en inbyggd **mekanism för aviseringsmeddelanden** via e-post för fel, varningar och kritiska åtgärder. Du kan ange enskilda e-postadresser eller distributionslistor som ska meddelas när en avisering genereras. Du kan också välja om du vill bli meddelad för varje enskild avisering eller gruppera dem i en sammanfattning per timme och sedan få ett meddelande.
  * De här aviseringarna definieras av tjänsten och ger stöd för begränsade scenarier – säkerhetskopierings-/återställningsfel, Stoppa skydd med kvarhållna data/Stoppa skydd med borttagningsdata och så vidare. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#alert-scenarios).
  * Om en destruktiv åtgärd som att stoppa skyddet med borttagningsdata utförs, utlöses en avisering och ett e-postmeddelande  skickas till prenumerationsägare, administratörer och medadministratörer även om meddelanden inte har konfigurerats för Recovery Services-valvet.
  * Vissa arbetsbelastningar kan generera hög frekvens av fel (till exempel SQL Server var 15:e minut). Aviseringarna konsolideras för att förhindra att aviseringar överbelastas med aviseringar som utlöses för varje felhändelse. [Mer information finns här](backup-azure-monitoring-built-in-monitor.md#consolidated-alerts).
  * De inbyggda aviseringarna kan inte anpassas och är begränsade till e-postmeddelanden som definierats i Azure Portal.

* Om du behöver skapa **anpassade aviseringar (till** exempel aviseringar om lyckade jobb) använder du Log Analytics. I Azure Monitor kan du skapa egna aviseringar på en Log Analytics-arbetsyta. Hybridarbetsbelastningar (DPM/MABS) kan också skicka data till LA och använda LA för att tillhandahålla vanliga aviseringar för arbetsbelastningar som stöds av Azure Backup.

* Du kan också få meddelanden via inbyggda Aktivitetsloggar för Recovery **Services-valv.** Den har dock stöd för begränsade scenarier och är inte lämplig för åtgärder som schemalagd säkerhetskopiering, som passar bättre med resursloggar än med aktivitetsloggar. Mer information om dessa begränsningar och hur du kan använda Log Analytics-arbetsytan för att övervaka och varna i stor skala för alla arbetsbelastningar som skyddas av Azure Backup finns i den här [artikeln](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-to-monitor-at-scale).

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar att du läser följande artiklar som utgångspunkt för att använda Azure Backup:

* [Azure Backup översikt](backup-overview.md)
* [Vanliga frågor och svar](backup-azure-backup-faq.yml)
