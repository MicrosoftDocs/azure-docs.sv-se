---
title: Konfigurera övervakning med Azure Monitor loggar
description: Lär dig hur du konfigurerar Azure Monitor loggar för visualisering och analys av händelser för att övervaka dina Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570345"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurera Azure Monitor loggar för ett kluster

Azure Monitor loggar är vår rekommendation att övervaka händelser på kluster nivå. Du kan konfigurera Log Analytics arbets yta via Azure Resource Manager, PowerShell eller Azure Marketplace. Om du har en uppdaterad Resource Manager-mall för distributionen för framtida bruk använder du samma mall för att konfigurera din Azure Monitor loggar miljö. Distribution via Marketplace är enklare om du redan har ett kluster som har distribuerats med diagnostik aktiverat. Om du inte har åtkomst på prenumerations nivå på det konto som du distribuerar till, distribuerar du med hjälp av PowerShell eller Resource Manager-mallen.

> [!NOTE]
> Om du vill konfigurera Azure Monitor loggar för att övervaka klustret måste du ha aktiverat diagnostik för att visa händelser på kluster-eller plattforms nivå. Läs om [hur du konfigurerar diagnostik i Windows-kluster](service-fabric-diagnostics-event-aggregation-wad.md) och [hur du konfigurerar diagnostik i Linux-kluster](service-fabric-diagnostics-oms-syslog.md) för mer

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Distribuera en Log Analytics-arbetsyta med hjälp av Azure Marketplace

Om du vill lägga till en Log Analytics-arbetsyta när du har distribuerat ett kluster går du till Azure Marketplace i portalen och letar efter **Service Fabric-analys**. Detta är en anpassad lösning för Service Fabric distributioner som har data som är speciella för Service Fabric. I den här processen kommer du att skapa båda lösningarna (instrument panelen för att Visa insikter) och arbets ytan (agg regeringen av underliggande kluster data).

1. Välj **nytt** i den vänstra navigerings menyn. 

2. Sök efter **Service Fabric-analys**. Välj den resurs som visas.

3. Välj **Skapa**.

    ![Service Fabric-analys i Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. I fönstret Skapa Service Fabric-analys väljer du **Välj en arbets yta** för fältet **OMS-arbetsyta** och skapar sedan **en ny arbets yta**. Fyll i de obligatoriska posterna. Det enda kravet här är att prenumerationen för Service Fabric klustret och arbets ytan är densamma. När dina poster har verifierats börjar din arbets yta distribueras. Distributionen tar bara några minuter.

5. När du är färdig väljer du **skapa** igen längst ned i fönstret för att skapa Service Fabric-analys. Kontrol lera att den nya arbets ytan visas under **OMS-arbetsyta**. Den här åtgärden lägger till lösningen i arbets ytan som du skapade.

Om du använder Windows fortsätter du med följande steg för att ansluta Azure Monitor loggar till lagrings kontot där kluster händelserna lagras. 

>[!NOTE]
>Service Fabric-analys-lösningen stöds bara för Windows-kluster. För Linux-kluster, se vår artikel om [hur du konfigurerar Azure Monitor loggar för Linux-kluster](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Anslut Log Analytics arbets ytan till klustret 

1. Arbets ytan måste vara ansluten till de diagnostikdata som kommer från klustret. Gå till resurs gruppen där du skapade Service Fabric-analys-lösningen. Välj **ServiceFabric \<nameOfWorkspace\>** och gå till sidan Översikt. Därifrån kan du ändra lösnings inställningar, inställningar för arbets ytan och komma åt Log Analytics-arbetsytan.

2. På den vänstra navigerings menyn går du till **data källor för arbets ytan** och väljer **lagrings konto loggar**.

3. På sidan **lagrings konto loggar** väljer du **Lägg till** överst för att lägga till klustrets loggar i arbets ytan.

4. Välj **lagrings konto** för att lägga till rätt konto som skapats i klustret. Om du använde standard namnet är lagrings kontot **sfdg \<resourceGroupName\>**. Du kan också bekräfta detta med Azure Resource Manager-mallen som används för att distribuera klustret, genom att kontrol lera värdet som används för **applicationDiagnosticsStorageAccountName**. Om namnet inte visas rullar du ned och väljer **Läs in mer**. Välj lagrings kontots namn.

5. Ange data typen. Ange det som **Service Fabric händelser**.

6. Se till att källan ställs in automatiskt på **WADServiceFabric \* EventTable**.

7. Välj **OK** för att ansluta din arbets yta till klustrets loggar.

    ![Lägg till lagrings konto loggar i Azure Monitor loggar](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Kontot visas nu som en del av dina lagrings konto loggar i arbets ytans data källor.

Du har lagt till Service Fabric-analys-lösningen i en Log Analytics arbets yta som nu är korrekt ansluten till klustrets plattforms-och program logg tabell. Du kan lägga till ytterligare källor i arbets ytan på samma sätt.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Distribuera Azure Monitor loggar med Azure Resource Manager

När du distribuerar ett kluster med hjälp av en Resource Manager-mall skapar mallen en ny Log Analytics arbets yta, lägger till Service Fabric-lösningen till arbets ytan och konfigurerar den för att läsa data från lämpliga lagrings tabeller.

Du kan använda och ändra [den här exempel mal len](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) så att den uppfyller dina krav. Den här mallen gör följande

* Skapar ett Service Fabric-kluster med 5 noder
* Skapar en Log Analytics arbets yta och Service Fabric lösning
* Konfigurerar Log Analytics agenten för att samla in och skicka två exempel på prestanda räknare till arbets ytan
* Konfigurerar WAD för att samla in Service Fabric och skickar dem till Azure Storage-tabeller (WADServiceFabric * EventTable)
* Konfigurerar Log Analytics arbets ytan för att läsa händelserna från dessa tabeller


Du kan distribuera mallen som en Resource Manager-uppgradering till klustret med hjälp av `New-AzResourceGroupDeployment` API: et i Azure PowerShell-modulen. Ett exempel kommando skulle vara:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager identifierar att det här kommandot är en uppdatering av en befintlig resurs. Den bearbetar bara ändringarna mellan mallen som kör den befintliga distributionen och den nya mallen.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Distribuera Azure Monitor loggar med Azure PowerShell

Du kan också distribuera Log Analytics-resursen via PowerShell med hjälp av `New-AzOperationalInsightsWorkspace` kommandot. Kontrol lera att du har installerat [Azure PowerShell](/powershell/azure/install-az-ps)om du vill använda den här metoden. Använd det här skriptet för att skapa en ny Log Analytics arbets yta och lägga till Service Fabric-lösningen: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

När du är klar följer du stegen i föregående avsnitt för att ansluta Azure Monitor loggar till lämpligt lagrings konto.

Du kan också lägga till andra lösningar eller göra andra ändringar i Log Analytics arbets ytan med hjälp av PowerShell. Läs mer i [hantera Azure Monitor loggar med hjälp av PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Nästa steg
* [Distribuera Log Analytics agenten](service-fabric-diagnostics-oms-agent.md) på noderna för att samla in prestanda räknare och samla Docker statistik och loggar för dina behållare
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar
* [Använd Visa designer för att skapa anpassade vyer i Azure Monitor loggar](../azure-monitor/visualize/view-designer.md)
