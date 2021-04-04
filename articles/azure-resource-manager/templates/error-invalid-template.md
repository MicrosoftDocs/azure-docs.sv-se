---
title: Ogiltiga mal linne fel
description: Beskriver hur du löser ogiltiga fel i mallar när du distribuerar Azure Resource Manager-mallar.
ms.topic: troubleshooting
ms.date: 05/22/2020
ms.openlocfilehash: ba19d3c4e72a765e2aaff7393915b77a80daf2ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96185801"
---
# <a name="resolve-errors-for-invalid-template"></a>Åtgärda fel med en ogiltig mall

Den här artikeln beskriver hur du löser ogiltiga fel i mallar.

## <a name="symptom"></a>Symptom

När du distribuerar en mall får du ett fel meddelande som anger att:

```
Code=InvalidTemplate
Message=<varies>
```

Fel meddelandet beror på typen av fel.

## <a name="cause"></a>Orsak

Det här felet kan bero på flera olika typer av fel. De omfattar vanligt vis en syntax eller ett strukturellt fel i mallen.

<a id="syntax-error"></a>

## <a name="solution-1---syntax-error"></a>Lösning 1 – syntaxfel

Om du får ett fel meddelande som anger att mallen inte kunde verifieras, kan du ha problem med syntaxfel i mallen.

```
Code=InvalidTemplate
Message=Deployment template validation failed
```

Det här felet är enkelt att göra eftersom mall-uttryck kan vara invecklade. Till exempel har följande namn tilldelning för ett lagrings konto en uppsättning hakparenteser, tre funktioner, tre uppsättningar parenteser, en uppsättning enkla citat tecken och en egenskap:

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
```

Om du inte anger motsvarande syntax genererar mallen ett värde som skiljer sig från din avsikt.

När du får den här typen av fel granskar du syntaxen för uttrycket noggrant. Överväg att använda en JSON-redigerare som [Visual Studio](create-visual-studio-deployment-project.md) eller [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), som kan varna dig om syntaxfel.

<a id="incorrect-segment-lengths"></a>

## <a name="solution-2---incorrect-segment-lengths"></a>Lösning 2 – Felaktiga segment längder

Ett annat ogiltigt fel uppstår när resurs namnet inte är i rätt format.

```
Code=InvalidTemplate
Message=Deployment template validation failed: 'The template resource {resource-name}'
for type {resource-type} has incorrect segment lengths.
```

En rot nivå resurs måste ha ett mindre segment i namnet än resurs typen. Varje segment särskiljs med ett snedstreck. I följande exempel har typen två segment och namnet har ett segment, så det är ett **giltigt namn**.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "myHostingPlanName",
  ...
}
```

Men nästa exempel är **inte ett giltigt namn** eftersom det har samma antal segment som typen.

```json
{
  "type": "Microsoft.Web/serverfarms",
  "name": "appPlan/myHostingPlanName",
  ...
}
```

För underordnade resurser har typen och namnet samma antal segment. Det här antalet segment är meningsfullt eftersom det fullständiga namnet och typen för det underordnade innehåller det överordnade namnet och typen. Det fullständiga namnet har därför fortfarande ett mindre segment än den fullständiga typen.

```json
"resources": [
  {
    "type": "Microsoft.KeyVault/vaults",
    "name": "contosokeyvault",
    ...
    "resources": [
      {
        "type": "secrets",
        "name": "appPassword",
        ...
      }
    ]
  }
]
```

Det kan vara svårt att hämta segmenten med hjälp av resurs hanterarens typer som tillämpas på alla resurs leverantörer. Att till exempel använda ett resurs lås på en webbplats kräver en typ med fyra segment. Därför är namnet tre segment:

```json
{
  "type": "Microsoft.Web/sites/providers/locks",
  "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
  ...
}
```

<a id="parameter-not-valid"></a>

## <a name="solution-3---parameter-is-not-valid"></a>Lösning 3-parametern är inte giltig

Om du anger ett parameter värde som inte är ett av de tillåtna värdena visas ett meddelande som liknar följande fel:

```
Code=InvalidTemplate;
Message=Deployment template validation failed: 'The provided value {parameter value}
for the template parameter {parameter name} is not valid. The parameter value is not
part of the allowed values
```

Kontrol lera de tillåtna värdena i mallen och ange en under distribution. Mer information om tillåtna parameter värden finns i [avsnittet parametrar i Azure Resource Manager mallar](template-syntax.md#parameters).

<a id="too-many-resource-groups"></a>

## <a name="solution-4---too-many-target-resource-groups"></a>Lösning 4-för många mål resurs grupper

Du kan se det här felet i tidigare distributioner eftersom du var begränsad till fem mål resurs grupper i en enda distribution. I maj 2020 ökade gränsen till 800 resurs grupper. Mer information finns i [Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp](./deploy-to-resource-group.md).

<a id="circular-dependency"></a>

## <a name="solution-5---circular-dependency-detected"></a>Lösning 5 – cirkulärt beroende har identifierats

Du får det här felet när resurserna är beroende av varandra på ett sätt som förhindrar att distributionen startar. En kombination av beroenden gör att två eller flera resurser väntar på andra resurser som också väntar. Till exempel är Resource1 beroende av Resource3, Resource2 beroende av Resource1 och Resource3 beroende av Resource2. Du kan vanligt vis lösa det här problemet genom att ta bort onödiga beroenden.

Så här löser du ett cirkulärt beroende:

1. Leta upp den resurs som identifierats i det cirkulära beroendet i mallen.
2. För resursen kontrollerar du egenskapen **dependsOn** och alla användningar av **referens** funktionen för att se vilka resurser den är beroende av.
3. Granska resurserna för att se vilka resurser de är beroende av. Följ beroendena tills du märker en resurs som är beroende av den ursprungliga resursen.
5. För de resurser som ingår i det cirkulära beroendet undersöker du noga all användning av egenskapen **dependsOn** för att identifiera eventuella beroenden som inte behövs. Ta bort dessa beroenden. Om du är osäker på att ett beroende krävs kan du prova med att ta bort det.
6. Distribuera om mallen.

Att ta bort värden från **dependsOn** -egenskapen kan orsaka fel när du distribuerar mallen. Om du får ett fel meddelande lägger du till beroendet i mallen igen.

Om den metoden inte löser det cirkulära beroendet bör du överväga att flytta en del av distributions logiken till underordnade resurser (till exempel tillägg eller konfigurations inställningar). Konfigurera de underordnade resurserna som ska distribueras efter de resurser som ingår i det cirkulära beroendet. Anta till exempel att du distribuerar två virtuella datorer, men du måste ange egenskaper för var och en som refererar till den andra. Du kan distribuera dem i följande ordning:

1. vm1
2. VM2
3. Tillägget på VM1 är beroende av VM1 och VM2. Tillägget anger värden för VM1 som hämtas från VM2.
4. Tillägget på VM2 är beroende av VM1 och VM2. Tillägget anger värden för VM2 som hämtas från VM1.

Samma metod fungerar för App Service appar. Överväg att flytta konfigurations värden till en underordnad resurs till app-resursen. Du kan distribuera två webb program i följande ordning:

1. webapp1
2. webapp2
3. config för webapp1 är beroende av webapp1 och webapp2. Den innehåller appinställningar med värden från webapp2.
4. config för webapp2 är beroende av webapp1 och webapp2. Den innehåller appinställningar med värden från webapp1.