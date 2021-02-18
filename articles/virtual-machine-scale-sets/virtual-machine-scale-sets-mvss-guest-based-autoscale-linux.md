---
title: Använd Azure Autoscale med gäst mått i en mall för Linux-skalnings uppsättning
description: Lär dig hur du skalar med hjälp av gäst mått i en virtuell Linux-dators mall för skalnings uppsättning
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/26/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: 0605780651e1a3c54ae53d13a3f99e1124fa76db
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585001"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Skala med hjälp av gäst mått i en mall för en Linux-skalnings uppsättning

Det finns två breda typer av mått i Azure som samlas in från virtuella datorer och skalnings uppsättningar: värd mått och gäst mått. Om du vill använda standardvärden för processor, disk och nätverk på hög nivå är värd måtten en bra anpassning. Om du däremot behöver ett större urval av mått bör du titta på gäst mått.

Värd mått kräver inte ytterligare konfiguration eftersom de samlas in av den virtuella värddatorn, medan gäst mått kräver att du installerar [Windows Azure-diagnostik-tillägget](../virtual-machines/extensions/diagnostics-template.md) eller [Linux Azure-diagnostik-tillägget](../virtual-machines/extensions/diagnostics-linux.md) på den virtuella gäst datorn. En vanlig orsak till att använda gäst mått i stället för värd mått är att gäst mått ger större urval av mått än värd mått. Ett sådant exempel är minnes förbruknings mått som endast är tillgängliga via gäst mått. De värd mått som stöds visas [här](../azure-monitor/essentials/metrics-supported.md)och ofta använda gäst mått som visas [här](../azure-monitor/autoscale/autoscale-common-metrics.md). Den här artikeln visar hur du ändrar [mallen för grundläggande skalbara skalnings uppsättningar](virtual-machine-scale-sets-mvss-start.md) för att använda regler för autoskalning baserat på gäst mått för Linux-skalnings uppsättningar.

## <a name="change-the-template-definition"></a>Ändra mal len definition

I en [föregående artikel](virtual-machine-scale-sets-mvss-start.md) har vi skapat en grundläggande mall för skalnings uppsättningar. Vi kommer nu att använda den tidigare mallen och ändra den för att skapa en mall som distribuerar en Linux-skalnings uppsättning med gäst mått baserat autoskalning.

Lägg först till parametrar för `storageAccountName` och `storageAccountSasToken` . Diagnostics-agenten lagrar mått data i en [tabell](../cosmos-db/tutorial-develop-table-dotnet.md) i det här lagrings kontot. Från och med version 3,0 av Linux Diagnostics-agenten stöds inte längre en lagrings åtkomst nyckel. Använd i stället en [SAS-token](../storage/common/storage-sas-overview.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Ändra sedan skalnings uppsättningen `extensionProfile` så att den inkluderar tillägget diagnostik. I den här konfigurationen anger du resurs-ID för den skalnings uppsättning som du vill samla in mått från, samt det lagrings konto och SAS-token som ska användas för att lagra måtten. Ange hur ofta måtten ska aggregeras (i det här fallet varje minut) och vilka mått som ska spåras (i det här fallet procent använt minne). Mer detaljerad information om den här konfigurationen och andra mått än procent använt minne finns i [den här dokumentationen](../virtual-machines/extensions/diagnostics-linux.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Slutligen lägger du till en `autoscaleSettings` resurs för att konfigurera autoskalning baserat på dessa mått. Den här resursen har en `dependsOn` sats som refererar till skalnings uppsättningen för att säkerställa att skalnings uppsättningen finns innan du försöker Autoskala den. Om du väljer ett annat mått för automatisk skalning på, använder du `counterSpecifier` konfigurations tillägget för diagnostik som `metricName` i konfigurationen för automatisk skalning. Mer information om konfiguration av autoskalning finns i [metod tips för autoskalning](../azure-monitor/autoscale/autoscale-best-practices.md) och [Azure Monitor REST API referens dokumentation](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Nästa steg

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
