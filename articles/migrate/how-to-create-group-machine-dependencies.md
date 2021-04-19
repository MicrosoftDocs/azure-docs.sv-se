---
title: Konfigurera agentbaserad beroendeanalys i Azure Migrate
description: Den här artikeln beskriver hur du ställer in agentbaserad beroendeanalys i Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714784"
---
# <a name="set-up-dependency-visualization"></a>Konfigurera beroendevisualisering

Den här artikeln beskriver hur du ställer in agentbaserad beroendeanalys i Azure Migrate: Identifiering och utvärdering. [Beroendeanalys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan servrar som du vill utvärdera och migrera till Azure.

## <a name="before-you-start"></a>Innan du börjar

- Granska kraven för support och distribution för agentbaserad beroendeanalys för:
    - [Servrar i VMware-miljö](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fysiska servrar](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Servrar i Hyper-V-miljö](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Kontrollera att du:
    - Ha ett Azure Migrate projekt. Om du inte gör det skapar [du](./create-manage-projects.md) ett nu.
    - Kontrollera att du har [lagt till](how-to-assess.md) Azure Migrate: Identifierings- och utvärderingsverktyget i projektet.
    - Konfigurera en [Azure Migrate för](migrate-appliance.md) att identifiera lokala servrar. Installationen identifierar lokala servrar och skickar metadata och prestandadata till Azure Migrate: Identifiering och utvärdering. Konfigurera en apparat för:
        - [Servrar i VMware-miljö](how-to-set-up-appliance-vmware.md)
        - [Servrar i Hyper-V-miljö](how-to-set-up-appliance-hyper-v.md)
        - [Fysiska servrar](how-to-set-up-appliance-physical.md)
- Om du vill använda beroendevisualisering associerar du [en Log Analytics-arbetsyta](../azure-monitor/logs/manage-access.md) med Azure Migrate projekt:
    - Du kan ansluta en arbetsyta först när du har Azure Migrate installationen och upptäckt servrar i Azure Migrate projekt.
    - Kontrollera att du har en arbetsyta i prenumerationen som innehåller Azure Migrate projekt.
    - Arbetsytan måste finnas i regionerna USA, östra, Sydostasien eller Europa, västra. Arbetsytor i andra regioner kan inte associeras med ett projekt.
    - Arbetsytan måste finnas i en region där [Tjänstkarta stöds.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
    - Du kan associera en ny eller befintlig Log Analytics-arbetsyta med Azure Migrate projekt.
    - Du kopplar arbetsytan första gången du ställer in beroendevisualisering för en server. Arbetsytan för ett Azure Migrate-projekt kan inte ändras när det har lagts till.
    - I Log Analytics är arbetsytan som är associerad Azure Migrate taggad med nyckeln för migreringsprojekt och projektnamnet.

## <a name="associate-a-workspace"></a>Associera en arbetsyta

1. När du har identifierat servrar för utvärdering går du till **Servrar**  >  **Azure Migrate: Identifiering och utvärdering och klickar** på **Översikt.**  
2. I **Azure Migrate: Identifiering och utvärdering klickar** du på **Essentials**.
3. I **OMS-arbetsyta** klickar du **på Kräver konfiguration.**

     ![Konfigurera Log Analytics-arbetsyta](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. I **Konfigurera OMS-arbetsyta** anger du om du vill skapa en ny arbetsyta eller använda en befintlig.
    - Du kan välja en befintlig arbetsyta från alla arbetsytor i projektprenumerationen.
    - Du behöver läsaråtkomst till arbetsytan för att associera den.
5. Om du skapar en ny arbetsyta väljer du en plats för den.

    ![Lägga till en ny arbetsyta](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Lär dig hur](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) du konfigurerar OMS-arbetsytan för privat slutpunktsanslutning.  

## <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

Installera agenterna på varje server som du vill analysera.

> [!NOTE]
> För servrar som övervakas System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Tjänstkarta integreras med Operations Manager. [Följ](../azure-monitor/vm/service-map-scom.md#prerequisites) integreringsvägledningen.

1. I **Azure Migrate: Identifiering och utvärdering klickar du** på Identifierade **servrar**.
2. För varje server som du vill analysera med beroendevisualisering går du till **kolumnen Beroenden och** klickar på **Kräver agentinstallation.**
3. På sidan **Beroenden laddar** du ned MMA och beroendeagenten för Windows eller Linux.
4. Under **Konfigurera MMA-agenten** kopierar du arbetsytans ID och nyckel. Du behöver dem när du installerar MMA-agenten.

    ![Installera agenterna](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installera MMA

Installera MMA på varje Windows- eller Linux-server som du vill analysera.

### <a name="install-mma-on-a-windows-server"></a>Installera MMA på en Windows-server

Så här installerar du agenten på en Windows-server:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **Licensvillkor** klickar du på **Jag accepterar för** att godkänna licensen.
3. I **Målmapp** behåller eller ändrar du standardinstallationsmappen > **Nästa.**
4. I **Installationsalternativ för agent** väljer du Azure Log **Analytics**  >  **Nästa.**
5. Klicka på **Lägg till** för att lägga till en ny Log Analytics-arbetsyta. Klistra in arbetsytans ID och nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommandoraden eller med hjälp av en automatiserad metod som Konfigurationshanteraren eller [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Läs mer om](../azure-monitor/agents/log-analytics-agent.md#installation-options) hur du använder dessa metoder för att installera MMA-agenten.
- MMA-agenten kan också installeras med detta [skript](https://github.com/brianbar-MSFT/Install-MMA).
- [Läs mer](../azure-monitor/agents/agents-overview.md#supported-operating-systems) om Windows-operativsystem som stöds av MMA.

### <a name="install-mma-on-a-linux-server"></a>Installera MMA på en Linux-server

Så här installerar du MMA på en Linux-server:

1. Överför lämpligt paket (x86 eller x64) till Linux-datorn med hjälp av scp/sftp.
2. Installera paketet med argumentet --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](../azure-monitor/agents/agents-overview.md#supported-operating-systems) om listan över stöd för Linux-operativsystem av MMA. 

## <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1. Om du vill installera beroendeagenten på en Windows-server dubbelklickar du på installationsfilen och följer guiden.
2. Installera beroendeagenten på en Linux-server genom att installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Läs mer](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) om hur du kan använda skript för att installera beroendeagenten.
- [Läs mer](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) om de operativsystem som stöds av beroendeagenten.


## <a name="create-a-group-using-dependency-visualization"></a>Skapa en grupp med hjälp av beroendevisualisering

Skapa nu en grupp för utvärdering. 


> [!NOTE]
> Grupper som du vill visualisera beroenden för får inte innehålla fler än 10 servrar. Om du har fler än 10 servrar delar du upp dem i mindre grupper.

1. I **Azure Migrate: Identifiering och utvärdering klickar du** på Identifierade **servrar.**
2. I kolumnen **Beroenden klickar** du på **Visa beroenden för** varje server som du vill granska.
3. På beroendekartan kan du se följande:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till och från servern.
    - Beroende servrar som inte har beroendeagenterna installerade grupperas efter portnummer.
    - Beroende servrar med beroendeagenter installerade visas som separata rutor.
    - Processer som körs på servern. Expandera varje serverruta för att visa processerna.
    - Serveregenskaper (inklusive FQDN, operativsystem, MAC-adress). Klicka på varje serverruta för att visa information.

4. Du kan titta på beroenden för olika tidsvaraktigheter genom att klicka på tidslängden i etiketten för tidsintervall.
    - Intervallet är som standard en timme. 
    - Du kan ändra intervallet eller ange start- och slutdatum och varaktighet.
    - Ett tidsintervall kan vara upp till en timme. Om du behöver ett längre intervall använder du Azure Monitor för att köra frågor mot beroende data under en längre period.

5. När du har identifierat de beroende servrar som du vill gruppera använder du Ctrl+ Klicka för att välja flera servrar på kartan och klickar på **Gruppera datorer.**
6. Ange ett gruppnamn.
7. Kontrollera att de beroende servrarna identifieras av Azure Migrate.

    - Om en beroende server inte identifieras av Azure Migrate: Identifiering och utvärdering kan du inte lägga till den i gruppen.
    - Om du vill lägga till en server kör du identifieringen igen och kontrollerar att servern har identifierats.

8. Om du vill skapa en utvärdering för den här gruppen markerar du kryssrutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK.**

När du har skapat gruppen rekommenderar vi att du installerar agenter på alla servrar i gruppen och sedan visualiserar beroenden för hela gruppen.

## <a name="query-dependency-data-in-azure-monitor"></a>Fråga beroendedata i Azure Monitor

Du kan fråga beroendedata som samlas in av Tjänstkarta log Analytics-arbetsytan som är associerad med Azure Migrate projekt. Log Analytics används för att skriva Azure Monitor köra loggfrågor.

- [Lär dig hur](../azure-monitor/vm/service-map.md#log-analytics-records) du söker Tjänstkarta data i Log Analytics.
- [Få en översikt](../azure-monitor/logs/get-started-queries.md)  över hur du skriver loggfrågor i [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Kör en fråga för beroendedata på följande sätt:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt.**
2. I **Azure Migrate: Identifiering och utvärdering klickar** du på **Översikt.** Klicka på nedåtpilen för att expandera **Essentials**.
3. I **OMS-arbetsyta** klickar du på arbetsytans namn.
3. På sidan Log Analytics-arbetsyta > **Allmänt klickar** du på **Loggar**.
4. Skriv frågan och klicka på **Kör.**

### <a name="sample-queries"></a>Exempelfrågor

Här är några exempelfrågor som du kan använda för att extrahera beroendedata.

- Du kan ändra frågorna för att extrahera dina önskade datapunkter.
- [Granska](../azure-monitor/vm/service-map.md#log-analytics-records) en fullständig lista över beroendedataposter.
- [Granska](../azure-monitor/vm/service-map.md#sample-log-searches) ytterligare exempelfrågor.

#### <a name="sample-review-inbound-connections"></a>Exempel: Granska inkommande anslutningar

Granska inkommande anslutningar för en uppsättning servrar.

- Posterna i tabellen för anslutningsmått (VMConnection) representerar inte enskilda fysiska nätverksanslutningar.
- Flera fysiska nätverksanslutningar är grupperade i en logisk anslutning.
- [Läs mer](../azure-monitor/vm/service-map.md#connections) om hur data för fysisk nätverksanslutning aggregeras i VMConnection.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Exempel: Sammanfatta skickade och mottagna data

I det här exemplet sammanfattas mängden data som skickas och tas emot på inkommande anslutningar mellan en uppsättning servrar.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Nästa steg

[Skapa en utvärdering](how-to-create-assessment.md) för en grupp.
