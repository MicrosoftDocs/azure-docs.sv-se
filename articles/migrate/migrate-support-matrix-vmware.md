---
title: Stöd för VMware-utvärdering i Azure Migrate
description: Läs mer om stöd för utvärdering av virtuella VMware-datorer med Azure Migrate Server-utvärdering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: ce8a1d77ae74a3946174ef58abf9add2e81eb90b
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763004"
---
# <a name="support-matrix-for-vmware-assessment"></a>Support mat ris för VMware-utvärdering 

I den här artikeln sammanfattas krav och support vid identifiering och utvärdering av virtuella VMware-datorer för migrering till Azure med hjälp av verktyget [Azure Migrate: Server bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) . För att utvärdera virtuella VMware-datorer skapar du ett Azure Migrate-projekt och lägger till Server utvärderings verktyget i projektet. När du har lagt till verktyget distribuerar du Azure Migrate-enheten. Enheten identifierar kontinuerligt lokala datorer och skickar metadata och prestanda data till Azure. När identifieringen är klar samlar du in identifierade datorer i grupper och kör en utvärdering för en grupp. 

Om du vill migrera virtuella VMware-datorer till Azure läser du [matrisen migration support](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Begränsningar

**Krav** | **Information**
--- | ---
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration.<br/><br/> Du kan identifiera och utvärdera upp till 35 000 virtuella VMware-datorer i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects). Ett projekt kan även innehålla fysiska servrar och virtuella Hyper-V-datorer, upp till utvärderings gränserna för var och en.
**Identifikation** | Azure Migrates apparaten kan identifiera upp till 10 000 virtuella VMware-datorer på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000 datorer i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000 virtuella datorer i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="vmware-requirements"></a>Krav för VMware

**VMware** | **Information**
--- | ---
**vCenter Server** | Datorer som du vill identifiera och utvärdera måste hanteras av vCenter Server version 5,5, 6,0, 6,5, 6,7 eller 7,0.<br/><br/> Det finns för närvarande inte stöd för att identifiera virtuella VMware-datorer genom att tillhandahålla ESXi-värd information i installationen.
**Behörigheter** | Server utvärderingen behöver ett vCenter Server skrivskyddat konto för identifiering och utvärdering.<br/><br/> Om du vill göra en program identifiering eller beroende visualisering måste kontot ha behörighet som är aktiverat för **Virtual Machines**  >  **gäst åtgärder**.

## <a name="vm-requirements"></a>Krav för virtuell dator
**VMware** | **Information**
--- | ---
**VMwares virtuella datorer** | Alla operativ system kan utvärderas för migrering. 
**Storage** | Diskar som är anslutna till SCSI-, IDE-och SATA-baserade styrenheter stöds.


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera installationen som en virtuell VMware-dator med hjälp av en ägg-mall, som importeras till vCenter Server eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- I Azure Government måste du distribuera enheten [med hjälp av skriptet](deploy-appliance-script-government.md).
- Granska de URL: er som krävs för att komma åt produkten i [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.


## <a name="port-access-requirements"></a>Port åtkomst krav

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med hjälp av URL: en: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443 (HTTPS) för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**vCenter-Server** | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.
**ESXi-värdar** | Om du vill göra en [app-identifiering](how-to-discover-applications.md)eller en [agent lös analys av beroenden](concepts-dependency-visualization.md#agentless-analysis)ansluter-enheten till ESXI-värdar på TCP-port 443, för att identifiera program, till och köra en agent lös beroende visualisering på virtuella datorer.

## <a name="application-discovery-requirements"></a>Krav för program identifiering

Förutom att identifiera datorer kan Server utvärderingen identifiera appar, roller och funktioner som körs på datorer. Genom att identifiera din program inventering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar. 

**Support** | **Information**
--- | ---
**Datorer som stöds** | Stöds för närvarande endast för virtuella VMware-datorer. Du kan identifiera appar som är installerade på upp till 10000 virtuella VMware-datorer, från varje Azure Migrate-apparat.
**Operativsystem** | Stöd för virtuella datorer som kör alla Windows-och Linux-versioner.
**Krav för virtuell dator** | VMware-verktyg måste installeras och köras på de virtuella datorer där du vill identifiera appar. <br/><br/> VMware Tools-versionen måste vara senare än 10.2.0.<br/><br/> Virtuella datorer måste ha PowerShell version 2,0 eller senare installerat.
**Identifikation** | Information om appar som är installerade på en virtuell dator samlas in från vCenter Server med hjälp av VMware-verktyg som är installerade på den virtuella datorn. Programinformationen samlas in från vCenter Server med hjälp av vSphere-API: er. Identifiering av appar är agenten. Ingenting installeras på virtuella datorer och installationen ansluter inte direkt till virtuella datorer. WMI/SSH bör vara aktiverat och tillgängligt på virtuella datorer.
**vCenter** | VCenter Server skrivskyddat konto som används för utvärdering måste ha behörighet som är aktiverat för **Virtual Machines**  >  **gäst åtgärder** för att kunna interagera med den virtuella datorn för program identifiering.
**VM-åtkomst** | Identifiering av appar behöver ett lokalt användar konto på den virtuella datorn för program identifiering.<br/><br/> Azure Migrate har för närvarande stöd för att använda en autentiseringsuppgift för alla Windows-servrar och en autentiseringsuppgift för alla Linux-servrar.<br/><br/> Du skapar ett gäst användar konto för virtuella Windows-datorer och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla virtuella Linux-datorer.
**Port åtkomst** | Azure Migrate-installationen måste kunna ansluta till TCP-port 443 på ESXi-värdar som kör virtuella datorer där du vill identifiera appar. VCenter Server returnerar en ESXI-värd anslutning för att ladda ned den fil som innehåller information om appen.



## <a name="dependency-analysis-requirements-agentless"></a>Krav för beroende analys (utan agent)

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. Tabellen sammanfattar kraven för att skapa en agent utan beroende analys. 

**Support** | **Information**
--- | --- 
**Datorer som stöds** | Stöds för närvarande endast för virtuella VMware-datorer.
**Virtuella Windows-datorer** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars).<br/>Microsoft Windows Server 2008 (32-bitars). 
**Virtuella Linux-datorer** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 och senare.
**Krav för virtuell dator** | VMware-verktyg (senare än 10.2.0) måste installeras och köras på de virtuella datorer som du vill analysera.<br/><br/> Virtuella datorer måste ha PowerShell version 2,0 eller senare installerat.
**Identifierings metod** |  Beroende information mellan virtuella datorer samlas in från vCenter Server med hjälp av VMware-verktyg som är installerade på den virtuella datorn. Installations programmet samlar in informationen från vCenter Server med hjälp av vSphere-API: er. Identifieringen är agent utan agent. Ingenting installeras på den virtuella datorn och installationen ansluter inte direkt till de virtuella datorerna. WMI/SSH bör vara aktiverat och tillgängligt på virtuella datorer.
**vCenter-konto** | Det skrivskyddade kontot som används av Azure Migrate för utvärdering måste ha behörighet som Aktiver ATS för **Virtual Machines > gäst åtgärder**.
**VM-behörigheter för Windows** |  Ett konto (lokal administratör eller domän) med lokal administratörs behörighet på virtuella datorer.
**Linux-konto** | Rot användar konto eller ett konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.<br/><br/> Ange dessa funktioner med följande kommandon: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/netstat
**Port åtkomst** | Azure Migrate-installationen måste kunna ansluta till TCP-port 443 på ESXI-värdar som kör de virtuella datorerna vars beroenden du vill identifiera. VCenter Server returnerar en ESXI-värd anslutning för att ladda ned filen som innehåller beroende informationen.


## <a name="dependency-analysis-requirements-agent-based"></a>Krav för beroende analys (agent-baserad)

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala datorer som du vill utvärdera och migrera till Azure. I tabellen sammanfattas kraven för att skapa en agent beroende analys. 

**Krav** | **Information** 
--- | --- 
**Före distribution** | Du bör ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till ett utvärderings verktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för utvärdering av [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Datorer som stöds** | Stöds för alla datorer.
**Azure Government** | Beroende visualisering är inte tillgänglig i Azure Government.
**Log Analytics** | Azure Migrate använder [tjänstkarta](../azure-monitor/insights/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate-projekt. Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.
**Agenter som krävs** | Installera följande agenter på varje dator som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala datorer inte är anslutna till Internet måste du ladda ned och installera Log Analytics gateway på dem.<br/><br/> Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics arbets yta** | Arbets ytan måste vara i samma prenumeration som Azure Migrate-projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
**Kostnader** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med Azure Migrate projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av Azure Migrate-projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade Azure Migrate-projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.
**Internetanslutning** | Om datorerna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.
**Azure Government** | Agent-baserad beroende analys stöds inte.


## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar.
- [Förbered för VMware](./tutorial-discover-vmware.md) -utvärdering.