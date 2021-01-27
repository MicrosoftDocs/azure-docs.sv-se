---
title: Importera och exportera ritningar med PowerShell
description: Lär dig hur du arbetar med dina skiss definitioner som kod. Dela, käll kontroll och hantera dem med hjälp av export-och import kommandona.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919994"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Importera och exportera skiss definitioner med PowerShell

Azure-ritningar kan hanteras fullständigt via Azure Portal. När organisationer förflyttar sig i sin användning av Azure-ritningar bör de börja tänka på skiss definitioner som hanterad kod. Det här konceptet kallas ofta infrastruktur som kod (IaC). Genom att behandla dina skiss definitioner som kod får du ytterligare fördelar utöver vad Azure Portal erbjuder. Här är några av de här fördelarna:

- Dela skiss definitioner
- Säkerhetskopiera dina skiss definitioner
- Återanvända skiss definitioner i olika klienter eller prenumerationer
- Placera skiss definitionerna i käll kontrollen
  - Automatiserad testning av skiss definitioner i test miljöer
  - Stöd för kontinuerlig integrering och för kontinuerlig distribution (CI/CD)

Vad dina skäl har, kan du hantera dina skiss definitioner som kod har fördelar. Den här artikeln visar hur du använder- `Import-AzBlueprintWithArtifact` och- `Export-AzBlueprintWithArtifact` kommandona i modulen [AZ. skiss](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter en måttlig arbets kunskap för Azure-ritningar. Om du inte har gjort det, kan du arbeta igenom följande artiklar:

- [Skapa en skiss i portalen](../create-blueprint-portal.md)
- Läs om [distributions faser](../concepts/deployment-stages.md) och [skissens livs cykel](../concepts/lifecycle.md)
- [Skapa](../create-blueprint-powershell.md) och [Hantera](./manage-assignments-ps.md) skiss definitioner och tilldelningar med PowerShell

Om den inte redan är installerad följer du anvisningarna i [Lägg till modulen AZ. skiss](./manage-assignments-ps.md#add-the-azblueprint-module) för att installera och validera modulen **AZ. skiss** från PowerShell-galleriet.

## <a name="folder-structure-of-a-blueprint-definition"></a>Mappstruktur för en skiss definition

Innan du tittar på att exportera och importera ritningar ska vi titta på hur filerna som utgör skiss definitionen är strukturerade. En skiss definition ska lagras i en egen mapp.

> [!IMPORTANT]
> Om inget värde skickas till **namn** parametern för `Import-AzBlueprintWithArtifact` cmdleten, används namnet på mappen som skiss definitionen är lagrad i.

Tillsammans med skiss definitionen, som måste namnges `blueprint.json` , är de artefakter som skiss definitionen består av. Varje artefakt måste finnas i undermappen med namnet `artifacts` .
Tillsammans bör strukturen för skiss definitionen som JSON-filer i mappar se ut så här:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Exportera skiss definitionen

Stegen för att exportera skiss definitionen är enkla. Det kan vara praktiskt att exportera skiss definitionen för att dela, säkerhetskopiera eller placera i käll kontrollen.

- **Skiss** [krävs]
  - Anger skiss definitionen
  - Används `Get-AzBlueprint` för att hämta referens objekt
- **OutputPath** [krävs]
  - Anger sökvägen för att spara skiss definitionens JSON-filer till
  - Utdatafilerna finns i en undermapp med namnet på skiss definitionen
- **Version** (valfritt)
  - Anger versionen som ska matas om **skiss** referens-objektet innehåller referenser till mer än en version.

1. Hämta en referens till skiss definitionen som ska exporteras från prenumerationen som visas som `{subId}` :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Använd `Export-AzBlueprintWithArtifact` cmdleten för att exportera den angivna skiss definitionen:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Importera skiss definitionen

När du har antingen en [exporterad skiss definition](#export-your-blueprint-definition) eller en manuellt skapad skiss definition i den [begärda mappstrukturen](#folder-structure-of-a-blueprint-definition), kan du importera skiss definitionen till en annan hanterings grupp eller prenumeration.

Exempel på inbyggda skiss definitioner finns i [Azure Blueprint GitHub lagrings platsen](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins).

- **Namn** [obligatoriskt]
  - Anger namnet på den nya skiss definitionen
- **InputPath** [krävs]
  - Anger sökvägen för att skapa skiss definitionen från
  - Måste matcha den [nödvändiga mappstrukturen](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (valfritt)
  - Hanterings gruppens ID för att spara skiss definitionen till om inte den aktuella kontexten är standard
  - Du måste ange antingen **ManagementGroupId** eller **SubscriptionId**
- **SubscriptionId** (valfritt)
  - Prenumerations-ID för att spara skiss definitionen till om inte den aktuella kontexten är standard
  - Du måste ange antingen **ManagementGroupId** eller **SubscriptionId**

1. Använd `Import-AzBlueprintWithArtifact` cmdleten för att importera den angivna skiss definitionen:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

När du har importerat skiss definitionen [tilldelar du den med PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Information om hur du skapar avancerade skiss definitioner finns i följande artiklar:

- Använd [statiska och dynamiska parametrar](../concepts/parameters.md).
- Anpassa ordningsföljden [för skiss ordning](../concepts/sequencing-order.md).
- Skydda distributioner med [skiss resurs låsning](../concepts/resource-locking.md).
- [Hantera ritningar som kod](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).