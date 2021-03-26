---
title: Stöd för VMware-utvärdering i Azure Migrate
description: Lär dig om utvärderings support för servrar som körs i VMware-miljö med Azure Migrate identifiering och utvärdering
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 4d51fc13e3587c21a7340b35db10d3cf36ab74b5
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557555"
---
# <a name="support-matrix-for-vmware-assessment"></a>Support mat ris för VMware-utvärdering 

Den här artikeln sammanfattar krav och support när du identifierar och utvärderar servrar som körs i VMware-miljön för migrering till Azure med hjälp av verktyget [Azure Migrate: identifiering och bedömning](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) . Du kan utvärdera servrar genom att skapa ett projekt som lägger till verktyget Azure Migrate: identifiering och bedömning i projektet. När du har lagt till verktyget distribuerar du Azure Migrate-enheten. Enheten identifierar kontinuerligt lokala servrar och skickar konfigurations-och prestanda-metadata till Azure. När identifieringen är klar samlar du in identifierade servrar i grupper och kör en utvärdering för en grupp.

Om du vill migrera VMware-servrar till Azure läser du [matrisen migration support](migrate-support-matrix-vmware-migration.md).



## <a name="limitations"></a>Begränsningar

**Krav** | **Information**
--- | ---
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration.<br/><br/> Du kan identifiera och utvärdera upp till 50 000 servrar från VMware-miljön i ett enda [projekt](migrate-support-matrix.md#project). Ett projekt kan också innehålla fysiska servrar och servrar från Hyper-V-miljön upp till utvärderings gränserna.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 10 000 servrar på en vCenter Server.
**Utvärdering** | Du kan lägga till upp till 35 000-servrar i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000-servrar i en enda utvärdering.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.


## <a name="vmware-requirements"></a>Krav för VMware

**VMware** | **Information**
--- | ---
**vCenter Server** | Servrar som du vill identifiera och utvärdera måste hanteras av vCenter Server version 5,5, 6,0, 6,5, 6,7 eller 7,0.<br/><br/> Det finns för närvarande inte stöd för att identifiera servrar genom att tillhandahålla ESXi värd information i installationen.
**Behörigheter** | Azure Migrate: identifiering och utvärdering behöver ett vCenter Server skrivskyddat konto för identifiering och utvärdering.<br/><br/> Om du vill utföra identifiering av program varu inventering och agent lös beroende analys, måste kontot ha behörighet som är aktiverat för **Virtual Machines**  >  **gäst åtgärder**.

## <a name="server-requirements"></a>Serverkrav
**VMware** | **Information**
--- | ---
**Operativ system som stöds** | Alla Windows-och Linux-operativsystem kan utvärderas för migrering.
**Storage** | Diskar som är anslutna till SCSI-, IDE-och SATA-baserade styrenheter stöds.


## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera installationen som en server i VMware-miljön med hjälp av en tjänstmall, som importeras till vCenter Server eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---vmware) för VMware.
- I Azure Government måste du distribuera enheten [med hjälp av skriptet](deploy-appliance-script-government.md).
- Granska de URL: er som krävs för att komma åt produkten i [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.


## <a name="port-access-requirements"></a>Port åtkomst krav

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med hjälp av URL: en: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Utgående anslutningar på port 443 (HTTPS) för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**vCenter-Server** | Inkommande anslutningar på TCP-port 443 för att tillåta att installationen samlar in konfigurations-och prestanda-metadata för utvärderingar. <br/><br/> Enheten ansluter som standard till vCenter på port 443. Om vCenter-servern lyssnar på en annan port kan du ändra porten när du konfigurerar identifiering.
**ESXi-värdar** | Om du vill göra en [identifiering av program varu inventeringen](how-to-discover-applications.md)eller en [agent lös beroende analys](concepts-dependency-visualization.md#agentless-analysis), ansluter enheten till ESXI-värdar på TCP-port 443 för att identifiera program varu inventering och beroenden på servrarna.

## <a name="application-discovery-requirements"></a>Krav för program identifiering

Förutom att identifiera servrar, Azure Migrate: identifiering och utvärdering kan identifiera program varu inventering som körs på servrar. Med program identifiering kan du identifiera och planera en sökväg för migrering som är anpassad för dina lokala arbets belastningar.

**Support** | **Information**
--- | ---
**Servrar som stöds** | Stöds för närvarande endast för servrar i VMware-miljön. Du kan utföra program identifiering på upp till 10000 servrar, från varje Azure Migrate-apparat.
**Operativsystem** | Servrar som kör alla Windows-och Linux-versioner stöds.
**Krav för virtuell dator** | För att kunna identifiera program varu inventering måste VMware-verktyg installeras och köras på servrar. <br/><br/> VMware Tools-versionen måste vara senare än 10.2.0.<br/><br/> Windows-servrar måste ha PowerShell version 2,0 eller senare installerat.
**Identifiering** | Program identifiering på servrar utförs från vCenter Server med hjälp av VMware-verktyg som är installerade på servrarna. Enheten samlar in information om program varu inventeringen från vCenter Server med hjälp av vSphere-API: er. Program identifieringen är agent utan agent. Ingen agent är installerad på servern och enheten ansluter inte direkt till servrarna. WMI och SSH bör vara aktiverat och tillgängligt på Windows respektive Linux-servrar.
**vCenter Server användar konto** | VCenter Server skrivskyddat konto som används för utvärdering måste ha behörighet som är aktiverat för **Virtual Machines**  >  **gäst åtgärder** för att kunna interagera med servrarna för program identifiering.
**Server åtkomst** | Du kan lägga till flera domän-och icke-domänautentiseringsuppgifter (Windows/Linux)-autentiseringsuppgifter i Konfigurations hanteraren för program identifiering.<br/><br/> Du behöver ett gäst användar konto för Windows-servrar och ett vanligt/vanligt användar konto (icke-sudo åtkomst) för alla Linux-servrar.
**Port åtkomst** | Azure Migrate-installationen måste kunna ansluta till TCP-port 443 på ESXi-värdar som kör servrar där du vill utföra program identifiering. VCenter Server returnerar en ESXi-värd anslutning för att ladda ned filen som innehåller information om program varu inventeringen.

## <a name="requirements-for-discovery-of-sql-server-instances-and-databases"></a>Krav för identifiering av SQL Server instanser och databaser

[Program identifieringen](how-to-discover-applications.md) identifierar SQL Server instanser. Med hjälp av den här informationen försöker installations programmet ansluta till respektive SQL Server instanser via Windows-autentisering eller SQL Server autentiseringsuppgifter som anges på enheten. När enheten är ansluten samlar den in konfigurations-och prestanda data för SQL Server instanser och databaser. SQL Server konfigurations data uppdateras en gång var 24: e timme och prestanda data samlas in var 30: e sekund.

**Support** | **Information**
--- | ---
**Servrar som stöds** | Stöds för närvarande endast för SQL-servrar i VMware-miljön. Du kan identifiera upp till 300 SQL Server instanser eller 6000 SQL-databaser, beroende på vilket som är lägre.
**Windows-servrar** | Windows Server 2008 och senare stöds.
**Linux-servrar** | Stöds inte för närvarande.
**Autentiseringsmekanism** | Både Windows-och SQL Server-autentisering stöds. Du kan ange autentiseringsuppgifter för båda typerna av autentisering i Konfigurations hanteraren för installationen.
**SQL Server-åtkomst** | Azure Migrate kräver ett Windows-användarkonto som är medlem i Server rollen sysadmin.
**SQL Server-versioner** | SQL Server 2008 och senare stöds.
**SQL Server-versioner** | Enterprise-, standard-, Developer-och Express-versioner stöds.
**SQL-konfiguration som stöds** | För närvarande går det bara att identifiera fristående SQL Server instanser och motsvarande databaser.<br/> Identifiering av redundanskluster och Always on-tillgänglighetsgrupper stöds inte.
**SQL-tjänster som stöds** | Endast SQL Server databas motor stöds. <br/> Identifiering av SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS) och SQL Server Analysis Services (SSAS) stöds inte.

> [!Note]
> Azure Migrate krypterar kommunikationen mellan Azure Migrates utrustning och källa SQL Server instanser (med egenskapen krypterad anslutning inställd på TRUE). Dessa anslutningar är krypterade med [**TrustServerCertificate**](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (anges till sant). transport lagret använder SSL för att kryptera kanalen och kringgå certifikat kedjan för att verifiera förtroende. Installations servern måste vara konfigurerad för att [**lita på certifikatets rot utfärdare**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).<br/>
Om inget certifikat har etablerats på servern när det startas, genererar SQL Server ett självsignerat certifikat som används för att kryptera inloggnings paket. [**Läs mer**](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

## <a name="dependency-analysis-requirements-agentless"></a>Krav för beroende analys (utan agent)

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala servrar som du vill utvärdera och migrera till Azure. Tabellen sammanfattar kraven för att skapa en agent utan beroende analys.

**Support** | **Information**
--- | --- 
**Servrar som stöds** | Stöds för närvarande endast för servrar i VMware-miljön.
**Windows-servrar** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2 (64-bitars).<br/>Microsoft Windows Server 2008 (32-bitars).
**Linux-servrar** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14,04, 16,04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.<br/> SUSE Linux Enterprise Server 11 och senare.
**Serverkrav** | VMware-verktyg (senare än 10.2.0) måste installeras och köras på servrar som du vill analysera.<br/><br/> Servrar måste ha PowerShell version 2,0 eller senare installerat.
**Identifierings metod** |  Beroende information mellan servrar samlas in från vCenter Server med hjälp av VMware-verktyg som är installerade på servern. Installations programmet samlar in informationen från vCenter Server med hjälp av vSphere-API: er. Ingen agent är installerad på servern och enheten ansluter inte direkt till servrar. WMI och SSH bör vara aktiverat och tillgängligt på Windows respektive Linux-servrar.
**vCenter-konto** | Det skrivskyddade kontot som används av Azure Migrate för utvärdering måste ha behörighet som Aktiver ATS för **Virtual Machines > gäst åtgärder**.
**Windows Server-behörigheter** |  Ett användar konto (lokal eller domän) med administratörs behörighet på-servrar.
**Linux-konto** | Rot användar konto eller ett konto med dessa behörigheter för/bin/netstat-och/bin/ls-filer: CAP_DAC_READ_SEARCH och CAP_SYS_PTRACE.<br/><br/> Ange dessa funktioner med följande kommandon: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP-/bin/netstat
**Port åtkomst** | Azure Migrate-installationen måste kunna ansluta till TCP-port 443 på ESXi-värdar som kör de servrar vars beroenden du vill identifiera. VCenter Server returnerar en ESXi-värd anslutning för att ladda ned filen som innehåller beroende data.


## <a name="dependency-analysis-requirements-agent-based"></a>Krav för beroende analys (agent-baserad)

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala servrar som du vill utvärdera och migrera till Azure. I tabellen sammanfattas kraven för att skapa en agent beroende analys.

**Krav** | **Information** 
--- | --- 
**Före distribution** | Du bör ha ett projekt på plats, med verktyget Azure Migrate: identifiering och bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala servrar.<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till ett identifierings-och utvärderings verktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar Azure Migrate-enheten för utvärdering av [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.
**Servrar som stöds** | Stöds för alla servrar i din lokala miljö.
**Log Analytics** | Azure Migrate använder [tjänstkarta](../azure-monitor/insights/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett projekt. Det går inte att ändra arbets ytan för ett projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln och projekt namnet.
**Agenter som krävs** | Installera följande agenter på varje server som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md).<br/> [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent).<br/><br/> Om lokala servrar inte är anslutna till Internet, måste du ladda ned och installera Log Analytics gateway på dem.<br/><br/> Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics arbets yta** | Arbets ytan måste vara i samma prenumeration som projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions).<br/><br/> Det går inte att ändra arbets ytan för ett projekt när den har lagts till.
**Kostnad** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När projektet tas bort raderas inte arbets ytan tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/platform/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.
**Internetanslutning** | Om servrarna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.
**Azure Government** | Agent-baserad beroende analys stöds inte.


## <a name="next-steps"></a>Nästa steg

- [Granska](best-practices-assessment.md) Metod tips för att skapa utvärderingar.
- [Förbered för VMware](./tutorial-discover-vmware.md) -utvärdering.