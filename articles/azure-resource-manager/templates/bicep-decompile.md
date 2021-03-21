---
title: Konvertera mallar mellan JSON och bicep
description: Beskriver kommandon för att konvertera Azure Resource Manager mallar från bicep till JSON och från JSON till bicep.
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 6d242f5846996cd0f5b9510a1a2b9f2bf063a0c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103422227"
---
# <a name="converting-arm-templates-between-json-and-bicep"></a>Konvertera ARM-mallar mellan JSON och bicep

Den här artikeln beskriver hur du konverterar Azure Resource Manager-mallar (ARM-mallar) från JSON till bicep och från bicep till JSON.

Du måste ha [BICEP CLI installerat](bicep-install.md) för att kunna köra konverterings kommandona.

Konverterings kommandona ger mallar som fungerar som likvärdiga. De kan dock vara identiska i implementeringen. Om du konverterar en mall från JSON till bicep och sedan tillbaka till JSON resulterar det förmodligen i en mall med annan syntax än den ursprungliga mallen. De konverterade mallarna ger samma resultat när de distribueras.

## <a name="convert-from-json-to-bicep"></a>Konvertera från JSON till bicep

Bicep CLI innehåller ett kommando för att dekompilera en befintlig JSON-mall till en bicep-fil. Om du vill dekompilera en JSON-fil använder du:

```azurecli
bicep decompile mainTemplate.json
```

Det här kommandot ger en start punkt för bicep-redigering. Kommandot fungerar inte för alla mallar. För närvarande kan kapslade mallar bara dekompileras om de använder utvärderings omfattningen "internt". Mallar som använder kopierings slingor kan inte dekompileras.

## <a name="convert-from-bicep-to-json"></a>Konvertera från bicep till JSON

Bicep CLI innehåller också ett kommando för att konvertera bicep till JSON. Använd följande för att bygga en JSON-fil:

```azurecli
bicep build mainTemplate.bicep
```

## <a name="export-template-and-convert"></a>Exportera mall och konvertera

Du kan exportera mallen för en resurs grupp och sedan skicka den direkt till Decompile-kommandot. I följande exempel visas hur du dekompilerar en exporterad mall.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group export --name "your_resource_group_name" > main.json
bicep decompile main.json
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Export-AzResourceGroup -ResourceGroupName "your_resource_group_name" -Path ./main.json
bicep decompile main.json
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

[Exportera mallen](export-template-portal.md) via portalen.

Används `bicep decompile <filename>` i den nedladdade filen.

---

## <a name="side-by-side-view"></a>Sida-vid-sida-vy

Med [bicep-Playground](https://aka.ms/bicepdemo) kan du Visa motsvarande JSON-och bicep-filer sida vid sida. Du kan välja en exempel mall för att se båda versionerna. `Decompile`Du kan också välja att ladda upp din egen JSON-mall och Visa motsvarande bicep-fil.

## <a name="next-steps"></a>Nästa steg

Information om bicep finns i [själv studie kursen om bicep](./bicep-tutorial-create-first-bicep.md).
