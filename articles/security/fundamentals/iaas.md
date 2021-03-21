---
title: Rekommenderade säkerhets metoder för IaaS-arbetsbelastningar i Azure | Microsoft Docs
description: " Migreringen av arbets belastningar till Azure IaaS ger möjligheter att utvärdera om våra design "
services: security
documentationcenter: na
author: terrylanfear
manager: rkarlin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: 6f073777930b4d026d826d2c3586e0886f906206
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102503087"
---
# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Säkerhetsmetodtips för IaaS-arbetsbelastningar i Azure
Den här artikeln beskriver rekommenderade säkerhets metoder för virtuella datorer och operativ system.

Bästa praxis bygger på en uppfattning om yttrandet och de fungerar med aktuella funktioner i Azure-plattformen och-funktions uppsättningar. Eftersom åsikter och tekniker kan ändras med tiden, kommer den här artikeln att uppdateras för att avspegla ändringarna.

I de flesta IaaS-scenarier (Infrastructure as a Service) är [Azure Virtual Machines (VM)](../../virtual-machines/index.yml) den huvudsakliga arbets belastningen för organisationer som använder molnbaserad data behandling. Detta faktum är uppenbart i [hybrid scenarier](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) där organisationer vill migrera arbets belastningar långsamt till molnet. I sådana scenarier följer du de [allmänna säkerhets aspekterna för IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)och tillämpar rekommenderade säkerhets metoder på alla dina virtuella datorer.

## <a name="protect-vms-by-using-authentication-and-access-control"></a>Skydda virtuella datorer med hjälp av autentiserings-och åtkomst kontroll
Det första steget i att skydda dina virtuella datorer är att se till att endast behöriga användare kan skapa nya virtuella datorer och komma åt virtuella datorer.

> [!NOTE]
> Du kan förbättra säkerheten för virtuella Linux-datorer i Azure genom att integrera med Azure AD-autentisering. När du använder [Azure AD-autentisering för virtuella Linux-datorer](../../virtual-machines/linux/login-using-aad.md)kan du centralt styra och tillämpa principer som tillåter eller nekar åtkomst till de virtuella datorerna.
>
>

**Bästa praxis**: kontrol lera VM-åtkomst.   
**Information**: Använd [Azure-principer](../../governance/policy/overview.md) för att upprätta konventioner för resurser i din organisation och skapa anpassade principer. Tillämpa dessa principer på resurser, t. ex. [resurs grupper](../../azure-resource-manager/management/overview.md). Virtuella datorer som tillhör en resurs grupp ärver sina principer.

Om din organisation har många prenumerationer kanske du behöver ett sätt att effektivt hantera åtkomst, principer och efterlevnad för dessa prenumerationer. [Azures hanterings grupper](../../governance/management-groups/overview.md) tillhandahåller en omfattnings nivå som omfattar prenumerationer. Du ordnar prenumerationer i hanterings grupper (behållare) och tillämpar dina styrnings villkor för dessa grupper. Alla prenumerationer i en hanterings grupp ärver automatiskt de villkor som tillämpas på gruppen. Hanteringsgrupper tillhandahåller hantering i företagsklass i stor skala oavsett vilken typ av prenumeration du har.

**Bästa praxis**: minska variationen i installationen och distributionen av virtuella datorer.   
**Information**: Använd [Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) mallar för att förstärka dina distributions alternativ och göra det enklare att förstå och inventera de virtuella datorerna i din miljö.

**Bästa praxis**: skydda privilegie rad åtkomst.   
**Information**: Använd en [metod med minsta behörighet](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) och inbyggda Azure-roller för att ge användare åtkomst till och konfigurera virtuella datorer:

- [Virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor): kan hantera virtuella datorer, men inte det virtuella nätverk eller lagrings konto som de är anslutna till.
- [Klassisk virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#classic-virtual-machine-contributor): kan hantera virtuella datorer som skapats med hjälp av den klassiska distributions modellen, men inte det virtuella nätverk eller lagrings konto som de virtuella datorerna är anslutna till.
- [Säkerhets administratör](../../role-based-access-control/built-in-roles.md#security-admin): endast i Security Center: kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer, ignorera aviseringar och rekommendationer.
- [DevTest Labs-användare](../../role-based-access-control/built-in-roles.md#devtest-labs-user): kan visa allt och ansluta, starta, starta om och stänga av virtuella datorer.

Dina prenumerations administratörer och medadministratörer kan ändra den här inställningen, vilket gör dem till administratörer för alla virtuella datorer i en prenumeration. Se till att du litar på alla prenumerations administratörer och medadministratörer för att logga in på någon av dina datorer.

> [!NOTE]
> Vi rekommenderar att du konsoliderar virtuella datorer med samma livs cykel i samma resurs grupp. Med hjälp av resurs grupper kan du distribuera, övervaka och summera fakturerings kostnader för dina resurser.
>
>

Organisationer som styr VM-åtkomsten och installations programmet förbättrar sin övergripande VM-säkerhet.

## <a name="use-multiple-vms-for-better-availability"></a>Använd flera virtuella datorer för bättre tillgänglighet
Om den virtuella datorn kör kritiska program som behöver hög tillgänglighet rekommenderar vi starkt att du använder flera virtuella datorer. Använd en [tillgänglighets uppsättning](../../virtual-machines/availability-set-overview.md) eller tillgänglighets [zoner](../../availability-zones/az-overview.md)för bättre tillgänglighet.

En tillgänglighets uppsättning är en logisk gruppering som du kan använda i Azure för att se till att de VM-resurser som du placerar i den är isolerade från varandra när de distribueras i ett Azure-datacenter. Azure säkerställer att de virtuella datorerna som du placerar i en tillgänglighets uppsättning körs över flera fysiska servrar, beräknings rack, lagrings enheter och nätverks växlar. Om ett maskinvaru-eller Azure-program fel uppstår påverkas endast en del av de virtuella datorerna och det övergripande programmet fortsätter att vara tillgängligt för dina kunder. Tillgänglighets uppsättningar är en viktig funktion när du vill bygga pålitliga moln lösningar.

## <a name="protect-against-malware"></a>Skydd mot skadlig kod
Du bör installera skydd mot skadlig kod för att hjälpa till att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Du kan installera [Microsoft Antimalware](antimalware.md) eller en Microsoft partners slut punkts skydds lösning ([Trend Micro](https://help.deepsecurity.trendmicro.com/Welcome.html), [Broadcom](https://www.broadcom.com/products), [McAfee](https://www.mcafee.com/us/products.aspx), [Windows Defender](https://www.microsoft.com/windows/comprehensive-security)och [System Center Endpoint Protection](/configmgr/protect/deploy-use/endpoint-protection)).

Microsoft Antimalware innehåller funktioner som real tids skydd, schemalagd genomsökning, reparation av skadlig kod, signaturkrav, uppdatering av motor, exempel rapportering och insamling av undantags händelser. För miljöer som är separat från produktions miljön kan du använda ett tillägg för program mot skadlig kod för att skydda dina virtuella datorer och moln tjänster.

Du kan integrera Microsofts lösningar för program mot skadlig kod och partner med [Azure Security Center](../../security-center/index.yml) för enkel distribution och inbyggd identifiering (aviseringar och incidenter).

**Bästa praxis**: installera en lösning för program mot skadlig kod för att skydda mot skadlig kod.   
**Information**: [installera en lösning för Microsoft-partner eller Microsoft Antimalware](../../security-center/security-center-services.md#supported-endpoint-protection-solutions-)

**Bästa praxis**: integrera din lösning för program mot skadlig kod med Security Center för att övervaka skyddets status.   
**Information**: [hantera problem med slut punkts skydd med Security Center](../../security-center/security-center-partner-integration.md)

## <a name="manage-your-vm-updates"></a>Hantera dina VM-uppdateringar
Virtuella Azure-datorer, precis som alla lokala virtuella datorer, är avsedda att hanteras av användaren. Azure skickar inte Windows-uppdateringar till dem. Du måste hantera dina VM-uppdateringar.

**Bästa praxis**: Håll dina virtuella datorer aktuella.   
**Information**: Använd [uppdateringshantering](../../automation/update-management/overview.md) -lösningen i Azure Automation för att hantera operativ system uppdateringar för dina Windows-och Linux-datorer som distribueras i Azure, i lokala miljöer eller i andra moln leverantörer. Du kan snabbt bedöma status för tillgängliga uppdateringar på alla agentdatorer och hantera installationsprocessen för nödvändiga uppdateringar för servrar.

Datorer som hanteras med Uppdateringshantering använder följande konfigurationer för att utföra utvärdering och uppdateringsdistributioner:

- Microsoft Monitoring Agent (MMA) för Windows eller Linux
- Önskad PowerShell-tillståndskonfiguration (DSC) för Linux
- Automation Hybrid Runbook Worker
- Microsoft Update eller Windows Server Update Services (WSUS) för Windows-datorer

Om du använder Windows Update låter du inställningen automatisk Windows Update vara aktive rad.

**Bästa praxis**: se till att avbildningar som du har skapat innehåller den senaste avrundning av Windows-uppdateringar.   
**Information**: Sök efter och installera alla Windows-uppdateringar som ett första steg i varje distribution. Det här måttet är särskilt viktigt att gälla när du distribuerar avbildningar som kommer från antingen dig eller ditt eget bibliotek. Även om bilder från Azure Marketplace uppdateras automatiskt som standard, kan det finnas en fördröjning (upp till några veckor) efter en offentlig version.

**Bästa praxis**: distribuera de virtuella datorerna med jämna mellanrum för att framtvinga en ny version av operativ systemet.   
**Information**: definiera den virtuella datorn med en [Azure Resource Manager-mall](../../azure-resource-manager/templates/template-syntax.md) så att du enkelt kan distribuera om den. Med hjälp av en mall får du en korrigerad och säker VM när du behöver den.

**Bästa praxis**: installera snabbt säkerhets uppdateringar för virtuella datorer.   
**Information**: Aktivera Azure Security Center (kostnads fri nivå eller standard nivå) för att [identifiera säkerhets uppdateringar som saknas och tillämpa dem](../../security-center/asset-inventory.md).

**Bästa praxis**: installera de senaste säkerhets uppdateringarna.   
**Information**: några av de första arbets belastningarna som kunderna flyttar till Azure är labb och externa system. Om dina virtuella Azure-datorer är värd för program eller tjänster som måste vara tillgängliga för Internet, måste du vara vigilanta om korrigeringar. Korrigeringen utöver operativ systemet. Säkerhets problem som inte har korrigerats i partner program kan också leda till problem som kan undvikas om en bra uppdaterings hantering är på plats.

**Bästa praxis**: Distribuera och testa en säkerhets kopierings lösning.   
**Information**: en säkerhets kopia måste hanteras på samma sätt som du hanterar andra åtgärder. Detta gäller system som är en del av din produktions miljö som utökas till molnet.

Test-och utvecklings system måste följa säkerhets kopierings strategier som tillhandahåller återställnings funktioner som liknar de som användarna har vuxit till, baserat på deras erfarenhet av lokala miljöer. Produktions arbets belastningar som flyttas till Azure bör integreras med befintliga säkerhets kopierings lösningar när det är möjligt. Du kan också använda [Azure Backup](../../backup/backup-azure-vms-first-look-arm.md) för att hjälpa dig att lösa dina säkerhets kopierings krav.

Organisationer som inte tillämpar program uppdaterings principer är mer utsatta för hot som utnyttjar kända, tidigare fasta sårbarheter. För att följa bransch bestämmelser måste företag bevisa att de är noggranna och använder rätt säkerhets kontroller för att säkerställa säkerheten för deras arbets belastningar i molnet.

Program vara – uppdatera metod tips för ett traditionellt Data Center och Azure IaaS har många likheter. Vi rekommenderar att du utvärderar dina aktuella principer för program uppdatering så att de innehåller virtuella datorer som finns i Azure.

## <a name="manage-your-vm-security-posture"></a>Hantera säkerhets position för virtuella datorer
Cyberhot utvecklas. Att skydda dina virtuella datorer kräver en övervaknings funktion som snabbt kan identifiera hot, förhindra obehörig åtkomst till resurser, utlösa aviseringar och minska antalet falska positiva identifieringar.

Använd [Azure Security Center](../../security-center/security-center-introduction.md)för att övervaka säkerhets position för dina [virtuella](../../security-center/security-center-introduction.md) [Windows](../../security-center/security-center-introduction.md) -och Linux-datorer. I Security Center kan du skydda dina virtuella datorer genom att dra nytta av följande funktioner:

- Använd säkerhets inställningar för operativ system med rekommenderade konfigurations regler.
- Identifiera och hämta system säkerhet och viktiga uppdateringar som kanske saknas.
- Distribuera rekommendationer för Endpoint Antimalware-skydd.
- Verifiera disk kryptering.
- Utvärdera och åtgärda sårbarheter.
- Identifiera hot.

Security Center kan aktivt övervaka för hot och potentiella hot exponeras i säkerhets aviseringar. Korrelerade hot sammanställs i en enda vy som kallas för en säkerhets incident.

Security Center lagrar data i [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md). Azure Monitor loggar innehåller ett frågespråk och analys motor som ger dig insikter om hur dina program och resurser fungerar. Data samlas också in från [Azure Monitor](../../batch/monitoring-overview.md), hanterings lösningar och agenter som installerats på virtuella datorer i molnet eller lokalt. Denna delade funktion hjälper dig att få en komplett bild av din miljö.

Organisationer som inte tillämpar stark säkerhet för sina virtuella datorer förblir medvetna om potentiella försök av obehöriga användare att kringgå säkerhets kontroller.

## <a name="monitor-vm-performance"></a>Övervaka VM-prestanda
Resurs missbruk kan vara ett problem när de virtuella dator processerna förbrukar fler resurser än de borde. Prestanda problem med en virtuell dator kan leda till avbrott i tjänsten, vilket strider mot tillgänglighetens säkerhets princip. Detta är särskilt viktigt för virtuella datorer som är värdar för IIS eller andra webb servrar, eftersom hög processor-eller minnes användning kan tyda på en DOS-attack (Denial of Service). Det är absolut nödvändigt att övervaka VM-åtkomsten inte bara återaktivt under ett problem, men också proaktivt mot bas linje prestanda som mäts under normal drift.

Vi rekommenderar att du använder [Azure Monitor](../../azure-monitor/data-platform.md) för att få insyn i din resurs hälsa. Azure Monitor funktioner:

- [Loggfiler för resurs diagnostik](../../azure-monitor/essentials/platform-logs-overview.md): övervakar dina VM-resurser och identifierar potentiella problem som kan påverka prestanda och tillgänglighet.
- [Azure-diagnostik-tillägg](../../azure-monitor/agents/diagnostics-extension-overview.md): tillhandahåller funktioner för övervakning och diagnostik på virtuella Windows-datorer. Du kan aktivera dessa funktioner genom att inkludera tillägget som en del av [Azure Resource Manager-mallen](../../virtual-machines/extensions/diagnostics-template.md).

Organisationer som inte övervakar prestanda för virtuella datorer kan inte avgöra om vissa ändringar i prestanda mönster är normala eller onormala. En virtuell dator som använder fler resurser än normal kan tyda på ett angrepp från en extern resurs eller en komprometterad process som körs på den virtuella datorn.

## <a name="encrypt-your-virtual-hard-disk-files"></a>Kryptera dina filer för virtuella hård diskar
Vi rekommenderar att du krypterar dina virtuella hård diskar (VHD: er) för att skydda din start volym och dina data volymer i vila i lagring, tillsammans med dina krypterings nycklar och hemligheter.

[Azure Disk Encryption](./azure-disk-encryption-vms-vmss.md) hjälper dig att kryptera dina virtuella Windows-och Linux IaaS-datorer. Azure Disk Encryption använder den branschstandardiserade [BitLocker](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc732774(v=ws.11)) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volym kryptering för operativ systemet och data diskarna. Lösningen är integrerad med [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. Lösningen ser också till att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage.

Följande är metod tips för att använda Azure Disk Encryption:

**Bästa praxis**: Aktivera kryptering på virtuella datorer.   
**Information**: Azure Disk Encryption genererar och skriver krypterings nycklarna till nyckel valvet. Hantering av krypterings nycklar i ditt nyckel valv kräver Azure AD-autentisering. Skapa ett Azure AD-program för det här ändamålet. För autentisering kan du använda antingen autentisering baserad på klientens hemliga eller [klientbaserade Azure AD-autentisering](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

**Bästa praxis**: Använd en nyckel krypterings nyckel (KEK) för ytterligare ett säkerhets lager för krypterings nycklar. Lägg till en KEK i ditt nyckel valv.   
**Information**: Använd cmdleten [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) för att skapa en nyckel krypterings nyckel i nyckel valvet. Du kan också importera en KEK från din lokala maskin varu säkerhetsmodul (HSM) för nyckel hantering. Mer information finns i Key Vault- [dokumentationen](../../key-vault/keys/hsm-protected-keys.md). När en nyckel krypterings nyckel anges använder Azure Disk Encryption den nyckeln för att omsluta krypterings hemligheter innan du skriver till Key Vault. Att hålla en depositions kopia av den här nyckeln i en lokal nyckel hantering HSM ger ytterligare skydd mot oavsiktlig borttagning av nycklar.

**Bästa praxis**: ta en [ögonblicks bild](../../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhets kopia innan diskarna krypteras. Säkerhets kopieringar ger ett återställnings alternativ om ett oväntat fel uppstår under krypteringen.   
**Information**: virtuella datorer med hanterade diskar behöver en säkerhets kopia innan krypteringen utförs. När du har skapat en säkerhets kopia kan du använda cmdleten **set-AzVMDiskEncryptionExtension** för att kryptera hanterade diskar genom att ange parametern *-skipVmBackup* . Mer information om hur du säkerhetskopierar och återställer krypterade virtuella datorer finns i [Azure Backup](../../backup/backup-azure-vms-encryption.md) artikeln.

**Bästa praxis**: för att se till att krypterings hemligheterna inte korsar regionala gränser, Azure Disk Encryption behöver nyckel valvet och de virtuella datorerna finns i samma region.   
**Information**: skapa och Använd ett nyckel valv i samma region som den virtuella dator som ska krypteras.

När du använder Azure Disk Encryption kan du uppfylla följande affärs behov:

- Virtuella IaaS-datorer skyddas i vila med hjälp av krypteringsteknik som är branschstandard i syfte att uppfylla organisationens krav på säkerhet och efterlevnad.
- Virtuella IaaS-datorer börjar under kundstyrda nycklar och principer och du kan granska deras användning i ditt nyckel valv.

## <a name="restrict-direct-internet-connectivity"></a>Begränsa direkt Internet anslutning
Övervaka och begränsa den virtuella datorns direkta Internet anslutning. Angripare genomsöker kontinuerligt offentliga moln-IP-intervall för öppna hanterings portar och försöker med "Easy" attacker som vanliga lösen ord och kända säkerhets problem. I följande tabell visas metod tips som hjälper dig att skydda mot angrepp:

**Bästa praxis**: förhindra oavsiktlig exponering för nätverks dirigering och säkerhet.   
**Information**: Använd Azure RBAC för att se till att endast den centrala nätverks gruppen har behörighet till nätverks resurser.

**Bästa praxis**: identifiera och åtgärda exponerade virtuella datorer som tillåter åtkomst från alla käll-IP-adresser.   
**Information**: Använd Azure Security Center. Security Center rekommenderar att du begränsar åtkomst via Internet-riktade slut punkter om någon av dina nätverks säkerhets grupper har en eller flera regler för inkommande trafik som tillåter åtkomst från alla käll-IP-adresser. Security Center rekommenderar att du redigerar dessa inkommande regler för att [begränsa åtkomsten](../../security-center/security-center-network-recommendations.md) till käll-IP-adresser som faktiskt behöver åtkomst.

**Bästa praxis**: begränsa hanterings portar (RDP, SSH).   
**Information**: [just-in-Time (JIT) VM-åtkomst](../../security-center/security-center-just-in-time.md) kan användas för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. När JIT är aktiverat kan Security Center låsa inkommande trafik till dina virtuella Azure-datorer genom att skapa en regel för nätverks säkerhets grupper. Du väljer portarna på den virtuella datorn till vilken inkommande trafik ska låsas. Dessa portar styrs av JIT-lösningen.

## <a name="next-steps"></a>Nästa steg
Se [metod tips och mönster för Azure-säkerhet](best-practices-and-patterns.md) för att få bättre säkerhets metoder att använda när du utformar, distribuerar och hanterar dina moln lösningar med hjälp av Azure.

Följande resurser är tillgängliga för att ge mer allmän information om Azure-säkerhet och relaterade Microsoft-tjänster:
* [Azure Security Team-bloggen](/archive/blogs/azuresecurity/) – för uppdaterad information om det senaste i Azure-säkerhet
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – där säkerhets problem i Microsoft, inklusive problem med Azure, kan rapporteras eller via e-post till secure@microsoft.com