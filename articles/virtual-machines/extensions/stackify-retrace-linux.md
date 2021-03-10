---
title: Stackify-tillägget för Azure Linux-agenten
description: Distribuera Stackify för att spåra om Linux-agenten på en virtuell Linux-dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 04/12/2018
ms.openlocfilehash: 1fc437637fde524da125af9191bf9de79a2e9c37
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559009"
---
# <a name="stackify-retrace-linux-agent-extension"></a>Stackify för att spåra om Linux-Agent

## <a name="overview"></a>Översikt

Stackify tillhandahåller produkter som spårar information om ditt program för att hjälpa till att hitta och åtgärda problem snabbt. För Developer-team är det en fullständigt integrerad miljö med program prestanda med flera miljöer. Det kombinerar flera verktyg varje utvecklings grupps behov.

Att spåra är det enda verktyg som tillhandahåller alla följande funktioner i alla miljöer i en enda plattform.

* Hantering av program prestanda (APM)
* Program-och Server loggning
* Fel spårning och övervakning
* Server, program och anpassade mått

**Om Stackify Linux Agent-tillägg**

Det här tillägget innehåller en installations Sök väg för Linux-agenten för spårning. 

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem 

Renstackspår-agenten kan köras mot dessa Linux-distributioner

| Distribution | Version |
|---|---|
| Ubuntu | 16,04 LTS 14,04 LTS, 16,10 och 17,04 |
| Debian | 7.9 + och 8.2 +, 9 |
| Red Hat | 6,7 +, 7.1 + |
| CentOS | 6.3 +, 7.0 + |

### <a name="internet-connectivity"></a>Internetanslutning

Stackify agent-tillägget för Linux kräver att den virtuella mål datorn är ansluten till Internet. 

Du kan behöva justera nätverks konfigurationen för att tillåta anslutningar till Stackify, se https://support.stackify.com/hc/en-us/articles/207891903-Adding-Exceptions-to-a-Firewall . 


## <a name="extension-schema"></a>Tilläggsschema

---

Följande JSON visar schemat för tillägget Stackify renstackspår agent. Tillägget kräver `environment` och `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

## <a name="template-deployment"></a>Malldistribution 

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra Stackify för att spåra Linux-agenten under en Azure Resource Manager mall-distribution.  

JSON för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON påverkar värdet för resurs namn och typ. Mer information finns i Ange namn och typ för underordnade resurser.

Följande exempel förutsätter att Stackify-tillägget för att spåra Linux är kapslat i den virtuella dator resursen. Vid kapsling av tilläggs resursen placeras JSON i objektet "resurser": [] på den virtuella datorn.

Tillägget kräver `environment` och `activationKey` .

```json
    {
      "type": "extensions",
      "name": "StackifyExtension",
      "apiVersion": "[variables('apiVersion')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Compute/virtualMachines',variables('vmName'))]"
      ],
      "properties": {
        "publisher": "Stackify.LinuxAgent.Extension",
        "type": "StackifyLinuxAgentExtension",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "environment": "myEnvironment"
        },
        "protectedSettings": {
          "activationKey": "myActivationKey"
        }
      }
    }      
```

När du placerar tillägg-JSON i roten för mallen, innehåller resurs namnet en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.

```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "<parentVmResource>/StackifyExtension",
        "apiVersion": "[variables('apiVersion')]",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Stackify.LinuxAgent.Extension",
            "type": "StackifyLinuxAgentExtension",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "environment": "myEnvironment"
            },
            "protectedSettings": {
              "activationKey": "myActivationKey"
            }
        }
    }
```


## <a name="powershell-deployment"></a>PowerShell-distribution

`Set-AzVMExtension`Kommandot kan användas för att distribuera Stackify för att spåra virtuella datorer för Linux-agenten till en befintlig virtuell dator. Innan du kör kommandot måste offentliga och privata konfigurationer lagras i en PowerShell hash-tabell.

Tillägget kräver `environment` och `activationKey` .

```powershell
$PublicSettings = @{"environment" = "myEnvironment"}
$ProtectedSettings = @{"activationKey" = "myActivationKey"}

Set-AzVMExtension -ExtensionName "Stackify.LinuxAgent.Extension" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Stackify.LinuxAgent.Extension" `
    -ExtensionType "StackifyLinuxAgentExtension" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution 

Verktyget Azure CLI kan användas för att distribuera Stackify-tillägget för att spåra virtuella Linux-Gent till en befintlig virtuell dator.  

Tillägget kräver `environment` och `activationKey` .

```azurecli
az vm extension set --publisher 'Stackify.LinuxAgent.Extension' --version 1.0 --name 'StackifyLinuxAgentExtension' --protected-settings '{"activationKey":"myActivationKey"}' --settings '{"environment":"myEnvironment"}'  --resource-group 'myResourceGroup' --vm-name 'myVmName'
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="error-codes"></a>Felkoder

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 10 | Installations fel | wget krävs |
| 20 | Installations fel | python krävs |
| 30 | Installations fel | sudo krävs |
| 40 | Installations fel | activationKey krävs |
| 51 | Installations fel | OS-distribution stöds inte |
| 60 | Installations fel | miljö krävs |
| 70 | Installations fel | Okänt |
| 80 | Aktivera fel | Det gick inte att konfigurera tjänsten |
| 90 | Aktivera fel | Det gick inte att starta tjänsten |
| 100 | Inaktivera fel | Det gick inte att stoppa tjänsten |
| 110 | Inaktivera fel | Det gick inte att ta bort tjänsten |
| 120 | Fel vid avinstallation | Det gick inte att stoppa tjänsten |

Om du behöver mer hjälp kan du kontakta Stackify-supporten på https://support.stackify.com .