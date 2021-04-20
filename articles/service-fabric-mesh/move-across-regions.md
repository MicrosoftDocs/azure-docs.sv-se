---
title: Flytta ett Service Fabric Mesh till en annan region
description: Du kan flytta Service Fabric Mesh resurser genom att distribuera en kopia av din aktuella mall till en ny Azure-region.
author: erikadoyle
ms.author: edoyle
ms.topic: how-to
ms.date: 01/14/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 9f3fcdc56b4e8d7873872212cb62f57a7669b459
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726636"
---
# <a name="move-a-service-fabric-mesh-application-to-another-azure-region"></a>Flytta ett Service Fabric Mesh till en annan Azure-region

> [!IMPORTANT]
> Förhandsversionen av Azure Service Fabric Mesh har dragits tillbaka. Nya distributioner tillåts inte längre via Service Fabric Mesh API. Stödet för befintliga distributioner fortsätter till och med 28 april 2021.
> 
> Mer information finns i [Azure Service Fabric Mesh förhandsversionen.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

I den här artikeln beskrivs hur du flyttar Service Fabric Mesh program och dess resurser till en annan Azure-region. Du kan flytta dina resurser till en annan region av flera olika orsaker. Till exempel, som svar på avbrott, för att få funktioner eller tjänster som endast är tillgängliga i specifika regioner, för att uppfylla interna princip- och styrningskrav eller som svar på kapacitetsplaneringskrav.

 [Service Fabric Mesh har inte stöd för](../azure-resource-manager/management/move-support-resources.md#microsoftservicefabricmesh) möjligheten att direkt flytta resurser mellan Azure-regioner. Du kan dock flytta resurser indirekt genom att distribuera en kopia av din aktuella Azure Resource Manager-mall till den nya målregionen och sedan omdirigera ingresstrafik och beroenden till det nyligen skapade Service Fabric Mesh-programmet.

## <a name="prerequisites"></a>Förutsättningar

* Ingress-kontrollant [(till exempel Application Gateway](../application-gateway/index.yml)) som fungerar som mellanhand för att dirigera trafik mellan klienter och ditt Service Fabric Mesh program
* Service Fabric Mesh (förhandsversion) i Azure-målregionen ( `westus` `eastus` , eller `westeurope` )

## <a name="prepare"></a>Förbereda

1. Ta en "ögonblicksbild" av det aktuella tillståndet för ditt Service Fabric Mesh-program genom att exportera Azure Resource Manager mallen och parametrarna från den senaste distributionen. Det gör du genom att följa stegen i [Exportera mall efter distribution med](../azure-resource-manager/templates/export-template-portal.md#export-template-after-deployment) hjälp av Azure Portal. Du kan också använda [Azure CLI,](../azure-resource-manager/management/manage-resource-groups-cli.md#export-resource-groups-to-templates) [Azure PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)eller [REST API](/rest/api/resources/resourcegroups/exporttemplate).

2. Om det är [tillämpligt exporterar du andra resurser i samma](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource-group) resursgrupp för omdistribution i målregionen.

3. Granska (och redigera, om det behövs) den exporterade mallen för att se till att de befintliga egenskapsvärdena är de som du vill använda i målregionen. Den nya `location` (Azure-regionen) är en parameter som du anger under omdistributionen.

## <a name="move"></a>Flytta

1. Skapa en ny resursgrupp (eller använd en befintlig) i målregionen.

2. Med den exporterade mallen följer du stegen i Distribuera [resurser från en anpassad mall med](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) hjälp av Azure Portal. Du kan också använda [Azure CLI,](../azure-resource-manager/templates/deploy-cli.md) [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)eller [REST API](../azure-resource-manager/templates/deploy-rest.md).

3. Vägledning om hur du flyttar relaterade resurser, till [exempel Azure Storage-konton,](../storage/common/storage-account-move.md)finns i vägledningen för enskilda tjänster som anges i avsnittet [Flytta Azure-resurser mellan regioner.](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)

## <a name="verify"></a>Verifiera

1. När distributionen är klar testar du programslutpunkterna för att verifiera programmets funktioner.

2. Du kan också kontrollera statusen för ditt program genom att kontrollera programstatusen ([az mesh app show](/cli/azure/ext/mesh/mesh/app#ext-mesh-az-mesh-app-show)) och granska programloggarna och kommandona ( az mesh[code-package-log](/cli/azure/ext/mesh/mesh/code-package-log)) [med hjälp av Azure Service Fabric Mesh CLI](./service-fabric-mesh-quickstart-deploy-container.md#set-up-service-fabric-mesh-cli).

## <a name="commit"></a>Checka in

När du har bekräftat motsvarande funktioner för ditt Service Fabric Mesh-program i målregionen konfigurerar du inresskontrollanten (till exempel [Application Gateway](../application-gateway/redirect-overview.md)) för att omdirigera trafik till det nya programmet.

## <a name="clean-up-source-resources"></a>Rensa källresurser

För att slutföra flytten av Service Fabric Mesh program tar [du bort källprogrammet och/eller den överordnade resursgruppen](../azure-resource-manager/management/delete-resource-group.md).

## <a name="next-steps"></a>Nästa steg

* [Flytta Azure-resurser mellan regioner](../azure-resource-manager/management/move-resources-overview.md#move-resources-across-regions)
* [Stöd för att flytta Azure-resurser mellan regioner](../azure-resource-manager/management/move-support-resources.md)
* [Flytta resurser till en ny resursgrupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Resurser som kan flyttas](../azure-resource-manager/management/move-support-resources.md
)
