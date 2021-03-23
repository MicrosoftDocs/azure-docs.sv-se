---
title: Stöd för Hyper-V-utvärdering i Azure Migrate
description: Läs mer om stöd för Hyper-V-utvärdering med Azure Migrate identifiering och utvärdering
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: bdd8fd336da12b5549875cf53f9e221cc431c2b9
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104773223"
---
# <a name="support-matrix-for-hyper-v-assessment"></a>Stöd mat ris för Hyper-V-utvärdering

I den här artikeln sammanfattas krav och support när du identifierar och utvärderar lokala servrar som körs i en Hyper-V-miljö för migrering till Azure med hjälp av verktyget [Azure Migrate: identifiering och bedömning](migrate-services-overview.md#azure-migrate-server-assessment-tool) . Om du vill migrera servrar som körs på Hyper-V till Azure läser du [matrisen migration support](migrate-support-matrix-hyper-v-migration.md).

Om du vill konfigurera identifiering och utvärdering av servrar som körs på Hyper-V skapar du ett projekt och lägger till verktyget Azure Migrate: identifiering och bedömning i projektet. När du har lagt till verktyget distribuerar du [Azure Migrate-enheten](migrate-appliance.md). Enheten identifierar kontinuerligt lokala servrar och skickar Server-metadata och prestanda data till Azure. När identifieringen är klar samlar du in identifierade servrar i grupper och kör en utvärdering för en grupp.

## <a name="limitations"></a>Begränsningar

**Support** | **Information**
--- | ---
**Utvärderings gränser** | Du kan identifiera och utvärdera upp till 35 000 servrar i ett enda [projekt](migrate-support-matrix.md#azure-migrate-projects).
**Projekt gränser** | Du kan skapa flera projekt i en Azure-prenumeration. Förutom servrar i Hyper-V kan ett projekt innehålla servrar på VMware och fysiska servrar, upp till utvärderings gränserna för var och en.
**Identifiering** | Azure Migrates apparaten kan identifiera upp till 5000 servrar som körs på Hyper-V.<br/><br/> Enheten kan ansluta till upp till 300 Hyper-V-värdar.
**Utvärdering** | Du kan lägga till upp till 35 000-servrar i en enda grupp.<br/><br/> Du kan utvärdera upp till 35 000-servrar i en enda utvärderings miljö för en grupp.

[Läs mer](concepts-assessment-calculation.md) om utvärderingar.

## <a name="hyper-v-host-requirements"></a>Krav för Hyper-V-värd

| **Support**                | **Information**
| :-------------------       | :------------------- |
| **Hyper-V-värd**       | Hyper-V-värden kan vara fristående eller distribuerade i ett kluster.<br/><br/> Hyper-V-värden kan köra Windows Server 2019, Windows Server 2016 eller Windows Server 2012 R2. Server Core-installationer av dessa operativ system stöds också. <br/>Du kan inte utvärdera servrar som finns på Hyper-V-värdar som kör Windows Server 2012.
| **Behörigheter**           | Du behöver administratörs behörighet på Hyper-V-värden. <br/> Om du inte vill tilldela administratörs behörighet skapar du ett lokalt användar konto eller ett domän användar konto och lägger till användar kontot i dessa grupper – fjärrhanterings användare, Hyper-V-administratörer och användare av prestanda övervakning. |
| **PowerShell-fjärranvändning**   | [PowerShell-fjärrkommunikation](/powershell/module/microsoft.powershell.core/enable-psremoting) måste vara aktiverat på varje Hyper-V-värd. |
| **Hyper-V-replikering**       | Om du använder Hyper-V-replikering (eller om du har flera servrar med samma server identifierare), och du identifierar både de ursprungliga och replikerade servrarna med hjälp av Azure Migrate, kan det hända att utvärderingen som genereras av Azure Migrate inte är korrekt. |

## <a name="server-requirements"></a>Serverkrav

| **Support**                  | **Information**
| :----------------------------- | :------------------- |
| **Operativsystem** | Alla operativ system kan utvärderas för migrering.  |
| **Integration Services**       | [Integrerings tjänsterna för Hyper-V](/virtualization/hyper-v-on-windows/reference/integration-services) måste köras på servrar som du bedömer, för att kunna avbilda information om operativ systemet. |
| **Storage** | Lokal disk, DAS, JBOD, lagrings utrymmen, CSV, SMB. Dessa lagrings enheter för Hyper-V-värdar där VHD/VHDX lagras stöds. <br/> Virtuella IDE-och SCSI-styrenheter stöds|

## <a name="azure-migrate-appliance-requirements"></a>Installationskrav för Azure Migrate

Azure Migrate använder [Azure Migrates enheten](migrate-appliance.md) för identifiering och utvärdering. Du kan distribuera installationen med hjälp av en komprimerad virtuell Hyper-V-hårddisk som du laddar ned från portalen eller med ett [PowerShell-skript](deploy-appliance-script.md).

- Lär dig mer om installations [krav](migrate-appliance.md#appliance---hyper-v) för Hyper-V.
- Lär dig mer om webb adresser som behövs för att få åtkomst till [offentliga](migrate-appliance.md#public-cloud-urls) [och offentliga](migrate-appliance.md#government-cloud-urls) moln.
- I Azure Government måste du distribuera enheten [med hjälp av skriptet](deploy-appliance-script-government.md).

## <a name="port-access"></a>Port åtkomst

I följande tabell sammanfattas port kraven för utvärdering.

**Enhet** | **Anslutning**
--- | ---
**Enhet** | Inkommande anslutningar på TCP-port 3389 för att tillåta fjärr skrivbords anslutningar till enheten.<br/><br/> Inkommande anslutningar på port 44368 för fjärråtkomst till appen för program hantering med hjälp av URL: en: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Utgående anslutningar på portarna 443 (HTTPS) för att skicka identifierings-och prestanda-metadata till Azure Migrate.
**Hyper-V-värd/-kluster** | Inkommande anslutning på WinRM-port 5985 (HTTP) eller 5986 (HTTPS) för att hämta metadata och prestanda data för servrar i Hyper-V, med hjälp av en Common Information Model-session (CIM).

## <a name="agent-based-dependency-analysis-requirements"></a>Krav för agent-baserade beroende analyser

Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera beroenden mellan lokala servrar som du vill utvärdera och migrera till Azure. I tabellen sammanfattas kraven för att skapa en agent beroende analys. Hyper-V stöder för närvarande endast agentbaserade beroende visualisering.

**Krav** | **Information**
--- | --- 
**Före distribution** | Du bör ha ett projekt på plats, med verktyget Azure Migrate: identifiering och bedömning som har lagts till i projektet.<br/><br/>  Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala servrar.<br/><br/> [Lär dig hur](create-manage-projects.md) du skapar ett projekt för första gången.<br/> [Lär dig hur](how-to-assess.md) du lägger till Azure Migrate: identifierings-och utvärderings verktyg i ett befintligt projekt.<br/> Lär dig hur du konfigurerar installations programmet för identifiering och utvärdering av [servrar på Hyper-V](how-to-set-up-appliance-hyper-v.md).
**Azure Government** | Beroende visualisering är inte tillgänglig i Azure Government.
**Log Analytics** | Azure Migrate använder [tjänstkarta](../azure-monitor/vm/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md) för beroende visualisering.<br/><br/> Du associerar en ny eller befintlig Log Analytics arbets yta med ett projekt. Det går inte att ändra arbets ytan för ett projekt när den har lagts till. <br/><br/> Arbets ytan måste vara i samma prenumeration som projektet.<br/><br/> Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.<br/><br/> Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.
**Agenter som krävs** | Installera följande agenter på varje server som du vill analysera:<br/><br/> [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md).<br/> [Beroende agenten](../azure-monitor/agents/agents-overview.md#dependency-agent).<br/><br/> Om lokala servrar inte är anslutna till Internet, måste du ladda ned och installera Log Analytics gateway på dem.<br/><br/> Läs mer om att installera [beroende agenten](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) och [MMA](how-to-create-group-machine-dependencies.md#install-the-mma).
**Log Analytics arbets yta** | Arbets ytan måste vara i samma prenumeration som projektet.<br/><br/> Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra.<br/><br/>  Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).<br/><br/> Det går inte att ändra arbets ytan för ett projekt när den har lagts till.
**Kostnader** | Tjänstkarta lösningen debiteras inga avgifter för de första 180 dagarna (från dagen då du associerar arbets ytan Log Analytics med projektet)/<br/><br/> Efter 180 dagar gäller standardpriserna för Log Analytics.<br/><br/> Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standardkostnader](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics.<br/><br/> När projektet tas bort raderas inte arbets ytan tillsammans med den. När du har tagit bort projektet är Tjänstkarta användning inte kostnads fritt, och varje nod debiteras enligt den betalda nivån för Log Analytics arbets yta/<br/><br/>Om du har projekt som du har skapat innan Azure Migrate allmän tillgänglighet (GA-28 februari 2018) kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa betalning efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor innan GA fortfarande kan debiteras.
**Hantering** | När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av projektet.<br/><br/> Du kan använda Log Analytics arbets ytan utanför Azure Migrate.<br/><br/> Om du tar bort det associerade projektet tas arbets ytan inte bort automatiskt. [Ta bort den manuellt](../azure-monitor/logs/manage-access.md).<br/><br/> Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.
**Internetanslutning** | Om servrarna inte är anslutna till Internet måste du installera Log Analytics gateway på dem.
**Azure Government** | Agent-baserad beroende analys stöds inte.

## <a name="next-steps"></a>Nästa steg

[Förbered för utvärdering av servrar som körs på Hyper-V](./tutorial-discover-hyper-v.md).