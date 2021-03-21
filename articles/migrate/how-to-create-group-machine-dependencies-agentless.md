---
title: Konfigurera beroende analys utan agent i Azure Migrate Server bedömning
description: Konfigurera en agent lös beroende analys i Azure Migrate Server bedömning.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: c3aa2aea764af8469152b007e60427724fea398a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045861"
---
# <a name="analyze-server-dependencies-agentless"></a>Analysera Server beroenden (utan agent)

Den här artikeln beskriver hur du konfigurerar beroende analyser utan agent med hjälp av Azure Migrate: Server bedömning. Beroende [analys](concepts-dependency-visualization.md) hjälper dig att identifiera och förstå beroenden mellan servrar för utvärdering och migrering till Azure.

> [!IMPORTANT]
> En för hands version av en beroende analys är för närvarande en för hands version för servrar som körs i din VMware-miljö, som identifieras med verktyget Azure Migrate: Server bedömning.
> Den här för hands versionen täcks av kund support och kan användas för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Aktuella begränsningar

- I vyn beroende analys kan du för närvarande inte lägga till eller ta bort en server från en grupp.
- En beroende karta för en grupp med servrar är inte tillgänglig för närvarande.
- I ett Azure Migrate projekt kan beroende data insamling aktive ras samtidigt för 1000-servrar. Du kan analysera ett större antal servrar genom att sekvensera i batchar på 1000-servrar.

## <a name="before-you-start"></a>Innan du börjar

- Se till att du har [skapat ett Azure Migrate-projekt](./create-manage-projects.md) med verktyget Azure Migrate: Server bedömning har lagts till.
- Granska [VMware-kraven](migrate-support-matrix-vmware.md#vmware-requirements) för att utföra beroende analyser.
- Granska installations [kraven](migrate-support-matrix-vmware.md#azure-migrate-appliance-requirements) innan du konfigurerar installationen.
- [Granska kraven för beroende analys](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) innan du aktiverar beroende analys på servrar.

## <a name="deploy-and-configure-the-azure-migrate-appliance"></a>Distribuera och konfigurera Azure Migrate-enheten

1. [Granska](migrate-appliance.md#appliance---vmware) kraven för att distribuera Azure Migrate-enheten.
2. Granska de Azure-URL: er som krävs för att få åtkomst till [molnet](migrate-appliance.md#government-cloud-urls) [offentliga](migrate-appliance.md#public-cloud-urls) och myndigheter.
3. [Granska data](migrate-appliance.md#collected-data---vmware) som samlas in under identifiering och bedömning.
4. [Antecknings](migrate-support-matrix-vmware.md#port-access-requirements) portens åtkomst krav för produkten.
5. [Distribuera Azure Migrate-apparaten](how-to-set-up-appliance-vmware.md) för att starta identifieringen. För att distribuera installationen kan du hämta och importera en ägg-mall till VMware för att skapa en server som körs i din vCenter Server. När du har distribuerat installationen måste du registrera den med Azure Migrate-projektet och konfigurera den för att initiera identifieringen.
6. När du konfigurerar installationen måste du ange följande i Konfigurations hanteraren för installationen:
    - Information om de vCenter Server som du vill ansluta till.
    - vCenter Server autentiseringsuppgifter som är begränsade för att identifiera servrarna i VMware-miljön.
    - Autentiseringsuppgifter för servern, som kan vara domän-/Windows-eller Linux-autentiseringsuppgifter (inte domän). [Lär dig mer](add-server-credentials.md) om hur du anger autentiseringsuppgifter och hur vi hanterar dem.

## <a name="verify-permissions"></a>Kontrollera behörigheter

- Du måste [skapa ett vCenter Server skrivskyddat konto](./tutorial-discover-vmware.md#prepare-vmware) för identifiering och utvärdering. Det skrivskyddade kontot måste ha behörighet för **Virtual Machines**  >  **gäst åtgärder** för att kunna samverka med servrarna för att samla in beroende data.
- Du behöver ett användar konto så att Server utvärderingen kan få åtkomst till servern för att samla in beroende data. [Lär dig](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) mer om konto krav för Windows-och Linux-servrar.

### <a name="add-credentials-and-initiate-discovery"></a>Lägg till autentiseringsuppgifter och initiera identifiering

1. Öppna Konfigurations hanteraren för installationen, slutför de nödvändiga kontrollerna och registreringen av enheten.
2. Gå till panelen **Hantera autentiseringsuppgifter och identifierings källor** .
1.  I **steg 1: ange vCenter Server autentiseringsuppgifter** klickar du på **Lägg till autentiseringsuppgifter** för att ange autentiseringsuppgifter för det vCenter servers konto som ska användas av enheten för att identifiera servrar som kör på vCenter Server.
1. I **steg 2: ange vCenter Server information** klickar du på **Lägg till identifierings källa** för att välja det egna namnet för autentiseringsuppgifter i list rutan. Ange **IP-adressen/FQDN** för vCenter Server instans :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="panel 3 på installations programmet för vCenter Server information i Configuration Manager":::
1. I **steg 3: ange autentiseringsuppgifter för servern för att utföra program varu inventering, hantering av agenter och identifiering av SQL Server instanser och databaser**, klickar du på **Lägg till autentiseringsuppgifter** för att tillhandahålla flera autentiseringsuppgifter för att initiera program varu inventeringen.
1. Klicka på **Starta identifiering** för att starta vCenter Server identifiering.

 När vCenter Server identifieringen är klar initierar installations programmet identifieringen av installerade program, roller och funktioner (program varu inventering). Under program varu inventeringen upprepas de tillagda servrarnas autentiseringsuppgifter mot servrar och val IDE ras för agent avsöknings analys. Du kan aktivera agent avsöknings analys för servrar från portalen. Det går bara att välja de servrar där verifieringen lyckas, för att aktivera en agent lös beroende analys.

## <a name="start-dependency-discovery"></a>Starta beroende identifiering

Välj de servrar som du vill aktivera beroende identifiering för.

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Välj **namnet** på den enhet vars identifiering du vill granska.
1. Du kan se validerings statusen för servrarna under **beroenden (utan agent)** .
1. Klicka på list rutan **beroende analys** .
1. Klicka på **Lägg till servrar**.
1. På sidan **Lägg till servrar** väljer du de servrar där du vill aktivera beroende analys. Du kan endast aktivera beroende mappning på de servrar där verifieringen lyckades. Nästa validerings cykel körs 24 timmar efter den sista tidsstämpeln för valideringen.
1. När du har valt servrar klickar du på **Lägg till servrar**.

:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png" alt-text="Starta beroende analys":::

Du kan visualisera beroenden runt sex timmar efter aktivering av beroende analys på servrar. Om du samtidigt vill aktivera flera servrar för beroende analys kan du använda [PowerShell](#start-or-stop-dependency-analysis-using-powershell) för att göra det.

## <a name="visualize-dependencies"></a>Visualisera beroenden

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
1. Välj **namnet** på den enhet vars identifiering du vill granska.
1. Sök efter den server vars beroenden som du vill granska.
1. Under kolumnen **beroenden (utan agent)** klickar du på **Visa beroenden**
1. Ändra den tids period som du vill visa kartan med i list rutan **tids längd** .
1. Expandera **klient** gruppen för att lista servrarna med ett beroende på den valda servern.
1. Expandera **port** gruppen om du vill visa en lista över de servrar som har ett beroende från den valda servern.
1. Om du vill navigera till kartan för någon av de beroende servrarna klickar du på Server namnet > **belastnings Server karta** 
 :::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png" alt-text="Expandera server port grupp och läsa in Server karta":::
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png" alt-text="Expandera klient grupp":::

8. Expandera den valda servern om du vill visa information på process nivå för varje beroende.
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png" alt-text="Expandera Server för att visa processer":::

> [!NOTE]
> Process information för ett beroende är inte alltid tillgängligt. Om det inte är tillgängligt visas beroendet med processen markerad som "okänd process".

## <a name="export-dependency-data"></a>Exportera beroende data

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
2. Klicka på list rutan **beroende analys** .
3. Klicka på **Exportera program beroenden**.
4. På sidan **Exportera program beroenden** väljer du namnet på den apparat som identifierar önskade servrar.
5. Välj start tid och slut tid. Observera att du bara kan hämta data under de senaste 30 dagarna.
6. Klicka på **Exportera beroende**.

Beroende data exporteras och hämtas i CSV-format. Den hämtade filen innehåller beroende data över alla servrar som är aktiverade för beroende analys. 
:::image type="content" source="./media/how-to-create-group-machine-dependencies-agentless/export.png" alt-text="Exportera beroenden":::

### <a name="dependency-information"></a>Beroende information

Varje rad i den exporterade CSV-filen motsvarar ett beroende som observerats inom den angivna tids perioden.

I följande tabell sammanfattas fälten i den exporterade CSV-filen. Observera att fälten Server namn, program och process endast fylls i för servrar som har en agent lös beroende analys aktive rad.

**Fältnamn** | **Information**
--- | --- 
Timeslot | Timeslot under vilken beroendet observerades. <br/> Beroende data samlas in över 6 timmars fack för närvarande.
Käll Server namn | Namn på käll servern 
Käll program | Namnet på programmet på käll servern  
Käll process | Namnet på processen på käll servern  
Mål server namn | Namnet på mål servern
Mål-IP-adress | IP-adress för mål servern
Mål program | Namnet på programmet på mål servern
Mål process | Namnet på processen på mål servern  
Målport | Port nummer på mål servern

## <a name="stop-dependency-discovery"></a>Stoppa beroende identifiering

Välj de servrar som du vill stoppa beroende identifiering för.

1. Klicka på **identifierade servrar** i **Azure Migrate: Server bedömning**.
1. Välj **namnet** på den enhet vars identifiering du vill granska.
1. Klicka på list rutan **beroende analys** .
1. Klicka på **ta bort servrar**.
1. På sidan **ta bort servrar** väljer du den server som du vill stoppa för beroende analys.
1. När du har valt servrar klickar du på **ta bort servrar**.

Om du vill stoppa beroende samtidigt på flera servrar kan du använda [PowerShell](#start-or-stop-dependency-analysis-using-powershell) för att göra det.

## <a name="start-or-stop-dependency-analysis-using-powershell"></a>Starta eller stoppa beroende analys med hjälp av PowerShell

Ladda ned PowerShell-modulen från [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) lagrings platsen på GitHub.

### <a name="log-in-to-azure"></a>Logga in på Azure

1. Logga in på Azure-prenumerationen med hjälp av Connect-AzAccount cmdlet.

    ```PowerShell
    Connect-AzAccount
    ```
    Om du använder Azure Government använder du följande kommando.
    ```PowerShell
    Connect-AzAccount -EnvironmentName AzureUSGovernment
    ```

2. Välj den prenumeration där du har skapat Azure Migrate-projektet 

    ```PowerShell
    select-azsubscription -subscription "Fabrikam Demo Subscription"
    ```

3. Importera den hämtade AzMig_Dependencies PowerShell-modulen

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

### <a name="enable-or-disable-dependency-data-collection"></a>Aktivera eller inaktivera insamling av beroende data

1. Hämta listan över identifierade servrar i ditt Azure Migrate-projekt med hjälp av följande kommandon. I exemplet nedan är projekt namnet FabrikamDemoProject och den resurs grupp som det tillhör är FabrikamDemoRG. Listan över servrar kommer att sparas i FabrikamDemo_VMs.csv

    ```PowerShell
    Get-AzMigDiscoveredVMwareVMs -ResourceGroupName "FabrikamDemoRG" -ProjectName "FabrikamDemoProject" -OutputCsvFile "FabrikamDemo_VMs.csv"
    ```

    I filen kan du se serverns visnings namn, aktuell status för beroende samling och ARM-ID för alla identifierade servrar. 

2. Om du vill aktivera eller inaktivera beroenden skapar du en CSV-fil med indata. Filen måste ha en kolumn med huvudet "ARM-ID". Eventuella ytterligare rubriker i CSV-filen ignoreras. Du kan skapa en CSV-fil med hjälp av filen som genererades i föregående steg. Skapa en kopia av filen som behåller de servrar som du vill aktivera eller inaktivera beroenden för. 

    I följande exempel aktive ras beroende analys på listan med servrar i indatafilen FabrikamDemo_VMs_Enable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Enable.csv -Enable
    ```

    I följande exempel inaktive ras beroende analys på listan med servrar i indatafilen FabrikamDemo_VMs_Disable.csv.

    ```PowerShell
    Set-AzMigDependencyMappingAgentless -InputCsvFile .\FabrikamDemo_VMs_Disable.csv -Disable
    ```

## <a name="visualize-network-connections-in-power-bi"></a>Visualisera nätverks anslutningar i Power BI

Azure Migrate erbjuder en Power BI mall som du kan använda för att visualisera nätverks anslutningar för många servrar samtidigt, och filtrera efter process och server. Du kan visualisera genom att läsa in Power BI med beroende data enligt anvisningarna nedan.

1. Ladda ned PowerShell-modulen och Power BI mal len från [Azure PowerShell samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/dependencies-at-scale) lagrings platsen på GitHub.

2. Logga in på Azure med hjälp av anvisningarna nedan: 
    - Logga in på Azure-prenumerationen med hjälp av Connect-AzAccount cmdlet.

        ```PowerShell
        Connect-AzAccount
        ```

    - Om du använder Azure Government använder du följande kommando.

        ```PowerShell
        Connect-AzAccount -EnvironmentName AzureUSGovernment
        ```

    - Välj den prenumeration där du har skapat Azure Migrate-projektet

        ```PowerShell
        select-azsubscription -subscription "Fabrikam Demo Subscription"
        ```

3. Importera den hämtade AzMig_Dependencies PowerShell-modulen

    ```PowerShell
    Import-Module .\AzMig_Dependencies.psm1
    ```

4. Kör följande kommando. Det här kommandot hämtar beroende data i en CSV och bearbetar dem för att generera en lista med unika beroenden som kan användas för visualisering i Power BI. I exemplet nedan är projekt namnet FabrikamDemoProject och den resurs grupp som det tillhör är FabrikamDemoRG. Beroendena hämtas för servrar som identifieras av FabrikamAppliance. De unika beroendena kommer att sparas i FabrikamDemo_Dependencies.csv

    ```PowerShell
    Get-AzMigDependenciesAgentless -ResourceGroup FabrikamDemoRG -Appliance FabrikamAppliance -ProjectName FabrikamDemoProject -OutputCsvFile "FabrikamDemo_Dependencies.csv"
    ```

5. Öppna mallen för hämtade Power BI

6. Läs in de nedladdade beroende data i Power BI.
    - Öppna mallen i Power BI.
    - Klicka på **Hämta data** i verktygsfältet. 
    - Välj **text/CSV** från vanliga data källor.
    - Välj beroende filen som hämtats.
    - Klicka på **Läs in**.
    - Du ser att en tabell importeras med namnet på CSV-filen. Du kan se tabellen i fält fältet till höger. Byt namn på den till AzMig_Dependencies
    - Klicka på Uppdatera från verktygsfältet.

    Diagrammet nätverks anslutningar och käll serverns namn, mål serverns namn, käll process namn, namn utsnitt för mål process, bör vara lätta med importerade data.

7. Visualisera kartan över filtrering av nätverks anslutningar efter servrar och processer. Spara filen.

## <a name="next-steps"></a>Nästa steg

[Grupp servrar](how-to-create-a-group.md) för utvärdering.