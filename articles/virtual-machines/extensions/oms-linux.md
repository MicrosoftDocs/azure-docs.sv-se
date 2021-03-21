---
title: Log Analytics-tillägg för virtuella datorer för Linux
description: Distribuera Log Analytics agenten på den virtuella Linux-datorn med ett tillägg för virtuell dator.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/18/2020
ms.openlocfilehash: 3ac6937d83bd2d21fefc09878408a54aa0eb41f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559094"
---
# <a name="log-analytics-virtual-machine-extension-for-linux"></a>Log Analytics-tillägg för virtuella datorer för Linux

## <a name="overview"></a>Översikt

Azure Monitor-loggar tillhandahåller funktioner för övervakning, avisering och aviserings reparation i molnet och lokala till gångar. Log Analytics tillägget för virtuell dator för Linux publiceras och stöds av Microsoft. Tillägget installerar Log Analytics agent på virtuella Azure-datorer och registrerar virtuella datorer i en befintlig Log Analytics-arbetsyta. Det här dokumentet innehåller information om plattformar, konfigurationer och distributions alternativ som stöds för Log Analytics virtuell dators tillägg för Linux.

>[!NOTE]
>Som en del av den pågående över gången från Microsoft Operations Management Suite (OMS) till Azure Monitor, kallas OMS-agenten för Windows eller Linux Log Analytics agent för Windows och Log Analytics agent för Linux.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem

Mer information om Linux-distributioner som stöds finns i artikeln [Översikt över Azure Monitors agenter](../../azure-monitor/agents/agents-overview.md#supported-operating-systems) .

### <a name="agent-and-vm-extension-version"></a>Version för agent och VM-tillägg
Följande tabell innehåller en mappning av versionen av Log Analytics VM-tillägget och Log Analytics agent-paketet för varje version. En länk till versions informationen för Log Analytics agents paketets version ingår. Versions information innehåller information om fel korrigeringar och nya funktioner som är tillgängliga för en specifik agent version.  

| Version för Log Analytics Linux VM-tillägg | Log Analytics agent paket version | 
|--------------------------------|--------------------------|
| 1.13.33 | [1.13.33](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.33-0) |
| 1.13.27 | [1.13.27](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.27-0) |
| 1.13.15 | [1.13.9-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.13.9-0) |
| 1.12.25 | [1.12.15-0](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.12.15-0) |
| 1.11.15 | [1.11.0 – 9](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.11.0-9) |
| 1.10.0 | [1.10.0-1](https://github.com/microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.10.0-1) |
| 1.9.1 | [1.9.0-0](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.9.0-0) |
| 1.8.11 | [1.8.1 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.8.1.256)| 
| 1.8.0 | [1.8.0 – 256](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/1.8.0-256)| 
| 1.7.9 | [1.6.1-3](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.1.3)| 
| 1.6.42.0 | [1.6.0-42](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_v1.6.0-42)| 
| 1.4.60.2 | [1.4.4 – 210](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.4-210)| 
| 1.4.59.1 | [1.4.3 – 174](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.3-174)|
| 1.4.58.7 | [14.2 – 125](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-125)|
| 1.4.56.5 | [1.4.2 – 124](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.2-124)|
| 1.4.55.4 | [1.4.1-123](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-123)|
| 1.4.45.3 | [1.4.1-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.1-45)|
| 1.4.45.2 | [1.4.0-45](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent_GA_v1.4.0-45)|
| 1.3.127.5 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)| 
| 1.3.127.7 | [1.3.5-127](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201705-v1.3.5-127)|
| 1.3.18.7 | [1.3.4-15](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/tag/OMSAgent-201704-v1.3.4-15)|  

### <a name="azure-security-center"></a>Azure Security Center

Azure Security Center etablerar automatiskt Log Analytics agenten och ansluter den till en standard arbets yta för Log Analytics som skapats av ASC i din Azure-prenumeration. Om du använder Azure Security Center ska du inte köra stegen i det här dokumentet. Om du gör det skrivs den konfigurerade arbets ytan över och anslutningen till Azure Security Center bryts.

### <a name="internet-connectivity"></a>Internetanslutning

Log Analytics agent-tillägget för Linux kräver att den virtuella mål datorn är ansluten till Internet. 

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för Log Analytics agent-tillägget. Tillägget kräver arbetsyte-ID och arbets ytans nyckel från mål Log Analytics arbets ytan. dessa värden [finns i arbets ytan Log Analytics](../../azure-monitor/vm/quick-collect-linux-computer.md#obtain-workspace-id-and-key) i Azure Portal. Eftersom arbets ytans nyckel ska behandlas som känsliga data, bör den lagras i en konfiguration med skyddad konfiguration. Skyddade inställnings data för Azure VM-tillägg krypteras och endast dekrypteras på den virtuella mål datorn. Observera att **workspaceId** och **workspaceKey** är Skift läges känsliga.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

>[!NOTE]
>Schemat ovan förutsätter att det kommer att placeras på rot nivån i mallen. Om du antecknar den i den virtuella dator resursen i mallen `type` `name` ska egenskaperna och ändras, enligt beskrivningen. [](#template-deployment)

### <a name="property-values"></a>Egenskaps värden

| Name | Värde/exempel |
| ---- | ---- |
| apiVersion | 2018-06-01 |
| utgivare | Microsoft. EnterpriseCloud. Monitoring |
| typ | OmsAgentForLinux |
| typeHandlerVersion | 1.13 |
| workspaceId (t. ex.) | 6f680a37-00c6-41c7-a93f-1437e3462574 |
| workspaceKey (t. ex.) | z4bU3p1/GrnWpQkky4gdabWXAhbWSTz70hm4m2Xt92XI + rSRgE8qVvRhsGo9TXffbrTahyrwv35W0pOqQAU7uQ = = |


## <a name="template-deployment"></a>Malldistribution

>[!NOTE]
>Vissa komponenter i Log Analytics VM-tillägget levereras också i det [virtuella diagnostik-tillägget](./diagnostics-linux.md). På grund av den här arkitekturen kan konflikter uppstå om båda tilläggen instansieras i samma ARM-mall. Undvik dessa installations tids konflikter genom att använda [ `dependsOn` direktivet](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) för att se till att tilläggen installeras i tur och ordning. Tilläggen kan installeras i valfri ordning.

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Mallar är idealiska när du distribuerar en eller flera virtuella datorer som kräver konfiguration av distributions konfiguration, till exempel onboarding till Azure Monitor loggar. En exempel på en Resource Manager-mall som innehåller det virtuella dator tillägget Log Analytics agent finns i [Azure snabb starts galleriet](https://github.com/Azure/azure-quickstart-templates/tree/master/201-oms-extension-ubuntu-vm). 

JSON-konfigurationen för ett tillägg för virtuell dator kan kapslas i den virtuella dator resursen eller placeras på rot-eller toppnivå i en Resource Manager JSON-mall. Placeringen av JSON-konfigurationen påverkar värdet för resurs namn och typ. Mer information finns i [Ange namn och typ för underordnade resurser](../../azure-resource-manager/templates/child-resource-name-type.md). 

Följande exempel förutsätter att VM-tillägget är kapslat i den virtuella dator resursen. Vid kapsling av tilläggs resursen placeras JSON i `"resources": []` objektet på den virtuella datorn.

```json
{
  "type": "extensions",
  "name": "OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

När du placerar tillägg-JSON i roten för mallen, innehåller resurs namnet en referens till den överordnade virtuella datorn och typen återspeglar den kapslade konfigurationen.  

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "<parentVmResource>/OMSExtension",
  "apiVersion": "2018-06-01",
  "location": "<location>",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', <vm-name>)]"
  ],
  "properties": {
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "type": "OmsAgentForLinux",
    "typeHandlerVersion": "1.13",
    "settings": {
      "workspaceId": "myWorkspaceId"
    },
    "protectedSettings": {
      "workspaceKey": "myWorkSpaceKey"
    }
  }
}
```

## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att distribuera Log Analytics-agentens VM-tillägg till en befintlig virtuell dator. Ersätt *myWorkspaceKey* -värdet nedan med din arbetsyte nyckel och *myWorkspaceId* -värdet med ditt arbetsyte-ID. Du hittar dessa värden i Log Analytics arbets ytan i Azure Portal under *Avancerade inställningar*. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --protected-settings '{"workspaceKey":"myWorkspaceKey"}' \
  --settings '{"workspaceId":"myWorkspaceId"}'
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utökning av utdata loggas i följande fil:

```
/opt/microsoft/omsagent/bin/stdout
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

| Felkod | Innebörd | Möjlig åtgärd |
| :---: | --- | --- |
| 9 | Aktivera anropad för tidigt | [Uppdatera Azure Linux-agenten](./update-linux-agent.md) till den senaste tillgängliga versionen. |
| 10 | Den virtuella datorn är redan ansluten till en Log Analytics-arbetsyta | Om du vill ansluta den virtuella datorn till arbets ytan som anges i tilläggs schemat anger du stopOnMultipleConnections till falskt i offentliga inställningar eller tar bort den här egenskapen. Den här virtuella datorn debiteras en gång för varje arbets yta som den är ansluten till. |
| 11 | Ogiltig konfiguration angavs för tillägget | Följ de föregående exemplen för att ange alla egenskaps värden som krävs för distribution. |
| 17 | Installations problem för Log Analytics-paket | 
| 19 | Installations problem för OMI-paket | 
| 20 | Installations problem för SCX-paket |
| 51 | Det här tillägget stöds inte för den virtuella datorns åtgärds system | |
| 52 | Tillägget misslyckades på grund av ett saknat beroende | Se utdata och loggfiler för mer information om vilket beroende som saknas. |
| 53 | Tillägget misslyckades på grund av saknade eller felaktiga konfigurations parametrar | Se utdata och loggfiler för mer information om vad som gick fel. Kontrol lera också att arbetsyte-ID: t är korrekt och att datorn är ansluten till Internet. |
| 55 | Det går inte att ansluta till Azure Monitor tjänsten eller nödvändiga paket saknas eller så är dpkg Package Manager låst| Kontrol lera att datorn har Internet åtkomst eller att en giltig HTTP-proxy har angetts. Kontrol lera också att arbetsyte-ID: t är korrekt och att du har installerat de olika verktygen. |

Ytterligare felsöknings information finns i [fel söknings guiden Log Analytics-agent-för-Linux](../../azure-monitor/visualize/vmext-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
