---
title: Flytta ett Service Fabric nät-program till en annan region
description: Du kan flytta Service Fabric nät resurser genom att distribuera en kopia av din aktuella mall till en ny Azure-region.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 1b59d482b8b88e37da2d61636ff3f254a46ba5c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626095"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Flytta ett Service Fabric nät program till en annan Azure-region

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här artikeln beskriver hur du flyttar ditt Service Fabric-nätprogram och dess resurser till en annan Azure-region. Du kan flytta dina resurser till en annan region av olika anledningar. Till exempel, som svar på drifts störningar, för att få funktioner eller tjänster som endast är tillgängliga i vissa regioner, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven.

 [Service Fabric nät har inte stöd](../azure-resource-manager/management/region-move-support.md#microsoftservicefabricmesh) för möjligheten att direkt flytta resurser mellan Azure-regioner. Du kan dock flytta resurser indirekt genom att distribuera en kopia av din aktuella Azure Resource Manager-mall till den nya mål regionen och sedan omdirigera inkommande trafik och beroenden till det nyskapade Service Fabric-Nätprogrammet.

## <a name="prerequisites"></a>Förutsättningar

* Ingångs kontroll (till exempel [Application Gateway](../application-gateway/index.yml)) som fungerar som en mellanhand för att dirigera trafik mellan klienter och ditt Service Fabric-nätprogram
* Service Fabric nät (för hands version) tillgänglighet i Azure-regionen för målet ( `westus` , `eastus` eller `westeurope` )

## <a name="prepare"></a>Förbereda

1. Ta en ögonblicks bild av det aktuella läget för ditt Service Fabric nätprogram genom att exportera Azure Resource Manager-mallen och parametrarna från den senaste distributionen. Det gör du genom att följa stegen i [Exportera mall efter distributionen](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) med hjälp av Azure Portal. Du kan också använda [Azure CLI](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates), [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).

2. Vid behov kan du [Exportera andra resurser i samma resurs grupp](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) för omdistribution i mål regionen.

3. Granska (och redigera vid behov) den exporterade mallen för att se till att de befintliga egenskapsvärdena är de som du vill använda i mål regionen. Den nya `location` (Azure-region) är en parameter som du kommer att ange under omdistributionen.

## <a name="move"></a>Flytta

1. Skapa en ny resurs grupp (eller Använd en befintlig) i mål regionen.

2. Med den exporterade mallen följer du stegen i [distribuera resurser från anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) med hjälp av Azure Portal. Du kan också använda [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)eller [REST API](../azure-resource-manager/templates/deploy-rest.md).

3. Information om hur du flyttar relaterade resurser som [Azure Storage konton](../storage/common/storage-account-move.md)finns i rikt linjer för enskilda tjänster som listas under avsnittet [Flytta Azure-resurser mellan regioner](../azure-resource-manager/management/move-region.md).

## <a name="verify"></a>Verifiera

1. När distributionen är klar testar du programmets slut punkter för att verifiera programmets funktioner.

2. Du kan också kontrol lera status för programmet genom att kontrol lera program status ([AZ nät app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) och granska program loggarna och ([AZ-paket-log](/cli/azure/ext/mesh/mesh/code-package-log)) kommandon med hjälp av [Azure Service Fabric nät masken](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Checka in

När du har bekräftat motsvarande funktioner i ditt Service Fabric nätprogram i mål regionen konfigurerar du ingångs styrenheten (till exempel [Application Gateway](../application-gateway/redirect-overview.md)) för att dirigera om trafik till det nya programmet.

## <a name="clean-up-source-resources"></a>Rensa käll resurser

[Ta bort käll programmet och/eller den överordnade resurs gruppen](../azure-resource-manager/management/delete-resource-group.md)om du vill slutföra flytten av Service Fabric nätappen.

## <a name="next-steps"></a>Nästa steg

* [Flytta Azure-resurser mellan regioner](../azure-resource-manager/management/move-region.md)
* [Stöd för att flytta Azure-resurser mellan regioner](../azure-resource-manager/management/region-move-support.md)
* [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Resurser som kan flyttas](../azure-resource-manager/management/move-support-resources.md
)
