---
title: Samla in mått för Windows skalnings uppsättning i Azure Monitor med mall
description: Skicka gäst operativ systemets mått till Azure Monitor Metric Store med hjälp av en Resource Manager-mall för en skalnings uppsättning för virtuella Windows-datorer
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: db410133bab530db731eee58048d1f9d26a1a3fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621362"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-by-using-an-azure-resource-manager-template-for-a-windows-virtual-machine-scale-set"></a>Skicka gäst operativ systemets mått till Azure Monitor Mät lagringen med hjälp av en Azure Resource Manager mall för en skalnings uppsättning för virtuella Windows-datorer

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Genom att använda [tillägget Azure Monitor Windows Azure-diagnostik (wad)](../agents/diagnostics-extension-overview.md)kan du samla in mått och loggar från gäst operativ systemet (gäst operativ system) som körs som en del av en virtuell dator, en moln tjänst eller ett Azure Service Fabric-kluster. Tillägget kan skicka telemetri till många olika platser i listan i den tidigare länkade artikeln.  

I den här artikeln beskrivs processen för att skicka gäst operativ systemets prestanda mått för en skalnings uppsättning för virtuella Windows-datorer till Azure Monitor data lager. Från och med Windows Azure-diagnostik version 1,11 kan du skriva mått direkt till Azure Monitor mått lager där standard plattforms mått redan har samlats in. Genom att lagra dem på den här platsen kan du komma åt samma åtgärder som är tillgängliga för plattforms mått. Åtgärderna omfattar nästan aviseringar i real tid, diagram, routning, åtkomst från REST API och mycket annat. Tidigare skrev Windows Azure-diagnostik-tillägget till Azure Storage men inte till Azure Monitor data lagret.  

Om du är nybörjare på Resource Manager-mallar kan du läsa mer om [mall distributioner](../../azure-resource-manager/management/overview.md) och deras struktur och syntax.  

## <a name="prerequisites"></a>Förutsättningar

- Din prenumeration måste vara registrerad med [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Du måste ha [Azure PowerShell](/powershell/azure) installerat, eller så kan du använda [Azure Cloud Shell](../../cloud-shell/overview.md). 

- Din VM-resurs måste finnas i en [region som stöder anpassade mått](../platform/metrics-custom-overview.md#supported-regions).

## <a name="set-up-azure-monitor-as-a-data-sink"></a>Konfigurera Azure Monitor som en data mottagare 
Azure-diagnostik-tillägget använder en funktion som kallas **data mottagare** för att dirigera mått och loggar till olika platser. Följande steg visar hur du använder en Resource Manager-mall och PowerShell för att distribuera en virtuell dator med hjälp av den nya Azure Monitor data mottagaren. 

## <a name="author-a-resource-manager-template"></a>Redigera en Resource Manager-mall 
I det här exemplet kan du använda en offentligt tillgänglig [exempel mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-autoscale):  

- **Azuredeploy.jspå** är en förkonfigurerad Resource Manager-mall för distribution av en skalnings uppsättning för virtuella datorer.

- **Azuredeploy.parameters.jspå** är en parameter fil som lagrar information som det användar namn och lösen ord som du vill ange för din virtuella dator. Under distributionen använder Resource Manager-mallen de parametrar som anges i den här filen. 

Ladda ned och spara båda filerna lokalt. 

###  <a name="modify-azuredeployparametersjson"></a>Ändra azuredeploy.parameters.jspå
Öppna filen **azuredeploy.parameters.js** :  
 
- Ange ett **vmSKU** som du vill distribuera. Vi rekommenderar Standard_D2_v3. 
- Ange en **windowsOSVersion** som du vill använda för den virtuella datorns skalnings uppsättning. Vi rekommenderar 2016-datacenter. 
- Ange namnet på den virtuella datorns skalnings uppsättnings resurs som ska distribueras med hjälp av en **vmssName** -egenskap. Ett exempel är **VMSS-wad-test**.    
- Ange antalet virtuella datorer som du vill köra på skalnings uppsättningen för den virtuella datorn med hjälp av egenskapen **instanceCount** .
- Ange värden för **adminUsername** och **adminPassword** för den virtuella datorns skal uppsättning. Dessa parametrar används för fjärråtkomst till de virtuella datorerna i skalnings uppsättningen. Om du inte vill att den virtuella datorn ska ha kapats ska du **inte** använda dem i den här mallen. Robotar Genomsök Internet efter användar namn och lösen ord i offentliga GitHub-databaser. De kan förmodligen testa virtuella datorer med dessa standardvärden. 


###  <a name="modify-azuredeployjson"></a>Ändra azuredeploy.jspå
Öppna filen **azuredeploy.js** . 

Lägg till en variabel som ska innehålla lagrings konto informationen i Resource Manager-mallen. Alla loggar eller prestanda räknare som anges i konfigurations filen för diagnostik skrivs till både Azure Monitor mått lager och det lagrings konto som du anger här: 

```json
"variables": { 
//add this line       
"storageAccountName": "[concat('storage', uniqueString(resourceGroup().id))]", 
```
 
Hitta den virtuella datorns skalnings uppsättnings definition i avsnittet resurser och Lägg till **identitet** -avsnittet i konfigurationen. Detta tillägg säkerställer att Azure tilldelar den en system identitet. Det här steget garanterar också att de virtuella datorerna i skalnings uppsättningen kan generera gäst mått om sig själva för att Azure Monitor:  

```json
    { 
      "type": "Microsoft.Compute/virtualMachineScaleSets", 
      "name": "[variables('namingInfix')]", 
      "location": "[resourceGroup().location]", 
      "apiVersion": "2017-03-30", 
      //add these lines below
      "identity": { 
           "type": "systemAssigned" 
       }, 
       //end of lines to add
```

I den virtuella datorns skalnings uppsättnings resurs hittar du avsnittet **virtualMachineProfile** . Lägg till en ny profil med namnet **extensionsProfile** för att hantera tillägg.  


I **extensionProfile** lägger du till ett nytt tillägg i mallen som visas i avsnittet **VMSS-wad-Extension** .  Det här avsnittet är de hanterade identiteterna för tillägg för Azure-resurser som säkerställer att de resultat som genereras godkänns av Azure Monitor. Fältet **namn** kan innehålla vilket namn som helst. 

Följande kod från MSI-tillägget lägger även till tillägget diagnostik och konfigurationen som en tilläggs resurs till den virtuella datorns skalnings uppsättnings resurs. Du kan lägga till eller ta bort prestanda räknare efter behov: 

```json
          "extensionProfile": { 
            "extensions": [ 
            // BEGINNING of added code  
            // Managed identities for Azure resources   
                { 
                 "name": "VMSS-WAD-extension", 
                 "properties": { 
                       "publisher": "Microsoft.ManagedIdentity", 
                       "type": "ManagedIdentityExtensionForWindows", 
                       "typeHandlerVersion": "1.0", 
                       "autoUpgradeMinorVersion": true, 
                       "settings": { 
                             "port": 50342 
                           }, 
                       "protectedSettings": {} 
                     } 
                                
            }, 
            // add diagnostic extension. (Remove this comment after pasting.)
            { 
              "name": "[concat('VMDiagnosticsVmExt','_vmNodeType0Name')]", 
              "properties": { 
                   "type": "IaaSDiagnostics", 
                   "autoUpgradeMinorVersion": true, 
                   "protectedSettings": { 
                        "storageAccountName": "[variables('storageAccountName')]", 
                        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')),'2015-05-01-preview').key1]", 
                        "storageAccountEndPoint": "https://core.windows.net/" 
                   }, 
                   "publisher": "Microsoft.Azure.Diagnostics", 
                   "settings": { 
                        "WadCfg": { 
                              "DiagnosticMonitorConfiguration": { 
                                   "overallQuotaInMB": "50000", 
                                   "PerformanceCounters": { 
                                       "scheduledTransferPeriod": "PT1M", 
                                       "sinks": "AzMonSink", 
                                       "PerformanceCounterConfiguration": [ 
                                          { 
              "counterSpecifier": "\\Memory\\% Committed Bytes In Use", 
                                              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Available Bytes", 
              "sampleRate": "PT15S" 
           }, 
           { 
              "counterSpecifier": "\\Memory\\Committed Bytes", 
              "sampleRate": "PT15S" 
           } 
                                       ] 
                                 }, 
                                 "EtwProviders": { 
                                       "EtwEventSourceProviderConfiguration": [ 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Actors", 
                                              "scheduledTransferKeywordFilter": "1", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                              "eventDestination": "ServiceFabricReliableActorEventTable" 
                                           } 
                                           }, 
                                           { 
                                              "provider": "Microsoft-ServiceFabric-Services", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricReliableServiceEventTable" 
                                              } 
                                           } 
                                     ], 
                                     "EtwManifestProviderConfiguration": [ 
                                           { 
                                              "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8", 
                                              "scheduledTransferLogLevelFilter": "Information", 
                                              "scheduledTransferKeywordFilter": "4611686018427387904", 
                                              "scheduledTransferPeriod": "PT5M", 
                                              "DefaultEvents": { 
                                                   "eventDestination": "ServiceFabricSystemEventTable" 
                                              } 
                                          } 
                                     ] 
                               } 
                               }, 
                               "SinksConfig": { 
                                     "Sink": [ 
                                          { 
                                              "name": "AzMonSink", 
                                              "AzureMonitor": {} 
                                          } 
                                      ] 
                               } 
                         }, 
                         "StorageAccount": "[variables('storageAccountName')]" 
                         }, 
                        "typeHandlerVersion": "1.11" 
                  } 
           } 
            ] 
          }
          }
      }
    },
    //end of added code plus a few brackets. Be sure that the number and type of brackets match properly when done. 
    {
      "type": "Microsoft.Insights/autoscaleSettings",
...
```


Lägg till en **dependsOn** för lagrings kontot så att det skapas i rätt ordning: 

```json
"dependsOn": [ 
"[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]", 
"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]" 
//add this line below
"[concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]" 
```

Skapa ett lagrings konto om det inte redan har skapats i mallen: 

```json
"resources": [
// add this code    
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
    "accountType": "Standard_LRS"
    }
},
// end added code
{ 
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[variables('virtualNetworkName')]",
```

Spara och Stäng båda filerna. 

## <a name="deploy-the-resource-manager-template"></a>Distribuera Resource Manager-mallen 

> [!NOTE]  
> Du måste köra Azure-diagnostik-tillägget version 1,5 eller högre **och** ha egenskapen **aktiverat autoupgrademinorversion:** inställd på **True** i Resource Manager-mallen. Azure läser sedan in rätt tillägg när den virtuella datorn startas. Om du inte har de här inställningarna i mallen ändrar du dem och distribuerar om mallen. 


Använd Azure PowerShell för att distribuera Resource Manager-mallen:  

1. Starta PowerShell. 
1. Logga in på Azure med `Login-AzAccount` .
1. Hämta din lista över prenumerationer med hjälp av `Get-AzSubscription` .
1. Ange den prenumeration som du ska skapa eller uppdatera den virtuella datorn: 

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>" 
   ```
1. Skapa en ny resurs grupp för den virtuella dator som distribueras. Kör följande kommando: 

   ```powershell
    New-AzResourceGroup -Name "VMSSWADtestGrp" -Location "<Azure Region>" 
   ```

   > [!NOTE]  
   > Kom ihåg att använda en Azure-region som är aktive rad för anpassade mått. Kom ihåg att använda en [Azure-region som är aktive rad för anpassade mått](../platform/metrics-custom-overview.md#supported-regions).
 
1. Kör följande kommandon för att distribuera den virtuella datorn:  

   > [!NOTE]  
   > Om du vill uppdatera en befintlig skalnings uppsättning, ökar tilläggets **steg** till slutet av kommandot. 
 
   ```powershell
   New-AzResourceGroupDeployment -Name "VMSSWADTest" -ResourceGroupName "VMSSWADtestGrp" -TemplateFile "<File path of your azuredeploy.JSON file>" -TemplateParameterFile "<File path of your azuredeploy.parameters.JSON file>"  
   ```

1. När distributionen har slutförts bör du hitta skalnings uppsättningen för den virtuella datorn i Azure Portal. Den ska generera mått till Azure Monitor. 

   > [!NOTE]  
   > Du kan stöta på fel kring de valda **vmSkuSize**. I så fall går du tillbaka till **azuredeploy.jspå** filen och uppdaterar standardvärdet för parametern **vmSkuSize** . Vi rekommenderar att du försöker **Standard_DS1_v2**. 


## <a name="chart-your-metrics"></a>Diagrammets mått 

1. Logga in på Azure-portalen. 

1. På den vänstra menyn väljer du **övervaka**. 

1. Välj **mått** på sidan **övervaka** . 

   ![Övervakaren – mått Sidan](media/collect-custom-metrics-guestos-resource-manager-vmss/metrics.png) 

1. Ändra agg regerings perioden till de **senaste 30 minuterna**.  

1. I den nedrullningsbara menyn resurs väljer du den skalnings uppsättning för virtuella datorer som du har skapat.  

1. I den nedrullningsbara menyn namn områden väljer du **Azure. VM. Windows. gäst**. 

1. I den nedrullningsbara menyn mått väljer du **minnes \% allokerade byte som används**.  

Du kan också välja att använda dimensionerna för det här måttet för att skapa diagram för den för en viss virtuell dator eller för att rita varje virtuell dator i skalnings uppsättningen. 



## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [anpassade mått](../platform/metrics-custom-overview.md).
