---
title: Konfigurera agentbaserade beroende analyser i Azure Migrate Server utvärdering
description: I den här artikeln beskrivs hur du konfigurerar en agent-baserad beroende analys i Azure Migrate Server bedömning.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 772602a11878276da3b81b84e7f4016997375077
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183071"
---
# <a name="set-up-dependency-visualization"></a>Konfigurera beroende visualisering

I den här artikeln beskrivs hur du konfigurerar en agent beroende analys i Azure Migrate: Server utvärdering. Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan datorer som du vill utvärdera och migrera till Azure.

## <a name="before-you-start"></a>Innan du börjar

- Granska support-och distributions kraven för agent-baserad beroende analys för:
    - [VMwares virtuella datorer](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fysiska servrar](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Hyper-V:s virtuella datorer](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Kontrol lera att du:
    - Ha ett Azure Migrate-projekt. Om du inte gör det [skapar](./create-manage-projects.md) du en nu.
    - Kontrol lera att du har [lagt](how-to-assess.md) till verktyget Azure Migrate: Server utvärderings verktyg i projektet.
    - Konfigurera en [Azure Migrate-apparat](migrate-appliance.md) för att identifiera lokala datorer. Enheten identifierar lokala datorer och skickar metadata-och prestanda data till Azure Migrate: Server utvärdering. Konfigurera en installation för:
        - [VMware](how-to-set-up-appliance-vmware.md) VMs
        - [Hyper-V](how-to-set-up-appliance-hyper-v.md) VMs
        - [Fysiska servrar](how-to-set-up-appliance-physical.md)
- Om du vill använda beroende visualisering associerar du en [Log Analytics arbets yta](../azure-monitor/logs/manage-access.md) med ett Azure Migrate-projekt:
    - Du kan bara koppla en arbets yta när du har konfigurerat Azure Migrate-installationen och identifierat datorer i Azure Migrate projektet.
    - Se till att du har en arbets yta i prenumerationen som innehåller det Azure Migrate projektet.
    - Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Det går inte att koppla arbets ytor i andra regioner till ett projekt.
    - Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Du kan associera en ny eller befintlig Log Analytics arbets yta med ett Azure Migrate-projekt.
    - Du ansluter arbets ytan första gången du ställer in beroende visualisering för en dator. Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
    - I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.

## <a name="associate-a-workspace"></a>Koppla en arbets yta

1. När du har identifierat datorer för utvärdering klickar du på Översikt i **servrar**  >  **Azure Migrate: Server utvärdering**.   
2. Klicka på **Essentials** i **Azure Migrate: Server bedömning**.
3. I **OMS-arbetsytan** klickar du på **kräver konfiguration**.

     ![Konfigurera Log Analytics-arbetsyta](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. I **Konfigurera OMS-arbetsyta** anger du om du vill skapa en ny arbets yta eller Använd en befintlig.
    - Du kan välja en befintlig arbets yta från alla arbets ytor i den migrerade projekt prenumerationen.
    - Du måste ha Läs behörighet till arbets ytan för att koppla den.
5. Om du skapar en ny arbets yta väljer du en plats för den.

    ![Lägg till en ny arbets yta](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>Hämta och installera VM-agenterna

Installera agenterna på varje dator som du vill analysera.

> [!NOTE]
> För datorer som övervakas av System Center Operations Manager 2012 R2 eller senare behöver du inte installera MMA-agenten. Tjänstkarta integreras med Operations Manager. [Följ](../azure-monitor/vm/service-map-scom.md#prerequisites) integrerings vägledningen.

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. För varje dator som du vill analysera med beroende visualiseringen klickar du på **kräver agent installation** i kolumnen **beroenden** .
3. På sidan **beroenden** laddar du ned MMA-och beroende agenten för Windows eller Linux.
4. Under **Konfigurera MMA agent**, kopierar du arbetsyte-ID och nyckel. Du behöver dessa när du installerar MMA-agenten.

    ![Installera agenterna](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installera MMA

Installera MMA på varje Windows-eller Linux-dator som du vill analysera.

### <a name="install-mma-on-a-windows-machine"></a>Installera MMA på en Windows-dator

Så här installerar du agenten på en Windows-dator:

1. Dubbelklicka på den hämtade agenten.
2. På sidan **Välkommen** klickar du på **Nästa**. På sidan **licens villkor** klickar du på **Jag accepterar** att godkänna licensen.
3. I **målmappen**, Behåll eller ändra standardmappen för installationen > **Nästa**.
4. I **installations alternativ för agent** väljer du **Azure Log Analytics**  >  **Nästa**.
5. Klicka på **Lägg** till för att lägga till en ny Log Analytics-arbetsyta. Klistra in det arbetsyte-ID och den nyckel som du kopierade från portalen. Klicka på **Nästa**.

Du kan installera agenten från kommando raden eller med en automatiserad metod som Configuration Manager eller [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Lär dig mer](../azure-monitor/agents/log-analytics-agent.md#installation-options) om att använda dessa metoder för att installera MMA-agenten.
- MMA-agenten kan också installeras med detta [skript](https://github.com/brianbar-MSFT/Install-MMA).
- [Läs mer](../azure-monitor/agents/agents-overview.md#supported-operating-systems) om de Windows-operativsystem som stöds av MMA.

### <a name="install-mma-on-a-linux-machine"></a>Installera MMA på en Linux-dator

Så här installerar du MMA på en Linux-dator:

1. Överför lämpligt paket (x86 eller x64) till Linux-datorn med hjälp av SCP/SFTP.
2. Installera paketet med hjälp av argumentet--install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Läs mer](../azure-monitor/agents/agents-overview.md#supported-operating-systems) om listan med stöd för Linux-operativsystem i MMA. 

## <a name="install-the-dependency-agent"></a>Installera beroendeagenten

1. Installera beroende agenten på en Windows-dator genom att dubbelklicka på installations filen och följa guiden.
2. Installera beroende agenten på en Linux-dator genom att installera som rot med följande kommando:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Läs mer](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) om hur du kan använda skript för att installera beroende agenten.
- [Läs mer](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) om de operativ system som stöds av beroende agenten.


## <a name="create-a-group-using-dependency-visualization"></a>Skapa en grupp med hjälp av beroende visualisering

Skapa nu en grupp för utvärdering. 


> [!NOTE]
> Grupper som du vill visualisera beroenden för får inte innehålla fler än 10 datorer. Om du har fler än 10 datorer delar du in dem i mindre grupper.

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. I kolumnen **beroenden** klickar du på **Visa beroenden** för varje dator som du vill granska.
3. På beroende kartan kan du se följande:
    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar, till och från datorn.
    - Beroende datorer som inte har beroende agenter installerade grupperas efter port nummer.
    - Beroende datorer med installerade beroende agenter visas som separata rutor.
    - Processer som körs inuti datorn. Expandera alla dator rutor om du vill visa processerna.
    - Dator egenskaper (inklusive fullständigt domän namn, operativ system, MAC-adress). Klicka på varje dator box för att visa information.

4. Du kan titta på beroenden för olika tids perioder genom att klicka på varaktigheten för tids området.
    - Som standard är intervallet en timme. 
    - Du kan ändra tidsintervallet, eller ange start-och slutdatum samt varaktighet.
    - Tidsintervallet kan vara upp till en timme. Om du behöver ett längre intervall använder du Azure Monitor för att köra frågor mot beroende data under en längre period.

5. När du har identifierat de beroende datorer som du vill gruppera tillsammans, använder du Ctrl + klicka för att markera flera datorer på kartan och klicka på **grupp datorer**.
6. Ange ett grupp namn.
7. Kontrol lera att de beroende datorerna upptäcks av Azure Migrate.

    - Om en beroende dator inte identifieras av Azure Migrate: Server utvärdering kan du inte lägga till den i gruppen.
    - Om du vill lägga till en dator kör du identifieringen igen och kontrollerar att datorn har identifierats.

8. Om du vill skapa en utvärdering för den här gruppen, markerar du kryss rutan för att skapa en ny utvärdering för gruppen.
8. Spara gruppen genom att klicka på **OK** .

När du har skapat gruppen rekommenderar vi att du installerar agenter på alla datorer i gruppen och sedan visualiserar beroenden för hela gruppen.

## <a name="query-dependency-data-in-azure-monitor"></a>Fråga beroende data i Azure Monitor

Du kan fråga beroende data som har registrerats av Tjänstkarta i arbets ytan Log Analytics som är associerad med Azure Migrate projektet. Log Analytics används för att skriva och köra Azure Monitor logg frågor.

- [Lär dig hur du](../azure-monitor/vm/service-map.md#log-analytics-records) söker efter tjänstkarta data i Log Analytics.
- [Få en översikt](../azure-monitor/logs/get-started-queries.md)  över att skriva logg frågor i [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Kör en fråga för beroende data på följande sätt:

1. När du har installerat agenterna går du till portalen och klickar på **Översikt**.
2. I **Azure Migrate: Server utvärdering** klickar du på **Översikt**. Klicka på nedpilen för att expandera **grunderna**.
3. I **OMS-arbetsytan** klickar du på arbets ytans namn.
3. På sidan Log Analytics arbets yta > **Allmänt** klickar du på **loggar**.
4. Skriv din fråga och klicka på **Kör**.

### <a name="sample-queries"></a>Exempelfrågor

Här följer några exempel frågor som du kan använda för att extrahera beroende data.

- Du kan ändra frågorna för att extrahera önskade data punkter.
- [Granska](../azure-monitor/vm/service-map.md#log-analytics-records) en fullständig lista över beroende data poster.
- [Granska](../azure-monitor/vm/service-map.md#sample-log-searches) ytterligare exempel frågor.

#### <a name="sample-review-inbound-connections"></a>Exempel: granska inkommande anslutningar

Granska inkommande anslutningar för en uppsättning virtuella datorer.

- Posterna i tabellen för anslutnings mått (VMConnection) representerar inte enskilda fysiska nätverks anslutningar.
- Flera fysiska nätverks anslutningar är grupperade i en logisk anslutning.
- [Läs mer](../azure-monitor/vm/service-map.md#connections) om hur data för fysiska nätverks anslutningar sammanställs i VMConnection.

```
// the machines of interest
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

#### <a name="sample-summarize-sent-and-received-data"></a>Exempel: sammanfatta skickade och mottagna data

Det här exemplet sammanfattar mängden data som skickas och tas emot på inkommande anslutningar mellan en uppsättning datorer.

```
// the machines of interest
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
