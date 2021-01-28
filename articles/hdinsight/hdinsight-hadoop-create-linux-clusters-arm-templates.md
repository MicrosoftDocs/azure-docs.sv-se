---
title: Skapa Apache Hadoop kluster med mallar – Azure HDInsight
description: Lär dig hur du skapar kluster för HDInsight med hjälp av Resource Manager-mallar
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/07/2020
ms.openlocfilehash: c36cee8ce7076093499a76ec0952ad37bd7511a7
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945864"
---
# <a name="create-apache-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

I den här artikeln får du lära dig flera sätt att skapa Azure HDInsight-kluster med hjälp av [Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md). Om du vill veta mer om andra verktyg och funktioner för att skapa kluster klickar du på tabbväljaren överst på den här sidan. Se även metoder för att [skapa kluster](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="resource-manager-templates"></a>Mallar för Resurshanteraren

En Resource Manager-mall gör det enkelt att skapa följande resurser för programmet i en enda, koordinerad åtgärd:

* HDInsight-kluster och deras beroende resurser (t. ex. standard lagrings kontot).
* Andra resurser (till exempel Azure SQL Database att använda [Apache Sqoop](https://sqoop.apache.org/)).

I mallen definierar du de resurser som behövs för programmet. Du anger också distributions parametrar för inmatade värden för olika miljöer. Mallen består av JSON och uttryck som du använder för att skapa värden för din distribution.

Du kan hitta exempel på HDInsight-mallar i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?term=hdinsight). Använd [Visual Studio-kod](https://code.visualstudio.com/#alt-downloads) mellan plattformar med [Resource Manager-tillägget](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) eller en text redigerare för att spara mallen i en fil på din arbets Station.

Mer information om Resource Manager-mallar finns i följande artiklar och exempel:

* [Redigera Azure Resource Manager mallar](../azure-resource-manager/templates/template-syntax.md)
* [Distribuera ett program med Azure Resource Manager mallar](../azure-resource-manager/templates/deploy-powershell.md)
* Referens för [Microsoft. HDInsight/kluster-](/azure/templates/microsoft.hdinsight/allversions) mall
* [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Hdinsight&pageNumber=1&sort=Popular)

## <a name="generate-templates"></a>Skapa mallar

Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration med hjälp av olika verktyg. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs. Mer information finns i [Exportera mallar](../azure-resource-manager/templates/export-template-portal.md).

## <a name="deploy-using-the-portal"></a>Distribuera med portalen

Du kan distribuera en Resource Manager-mall med hjälp av Azure Portal. Mer information finns i [distribuera resurser från en anpassad mall](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Du kan distribuera en Resource Manager-mall med Azure PowerShell. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-azure-cli"></a>Distribuera med Azure CLI

Du kan distribuera en Resource Manager-mall med Azure CLI. Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure CLI](../azure-resource-manager/templates/deploy-cli.md) och [distribuera en privat Resource Manager-mall med SAS-token och Azure CLI](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="deploy-using-the-rest-api"></a>Distribuera med hjälp av REST API

Du kan distribuera en Resource Manager-mall med REST API. Mer information finns i [distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Distribuera med Visual Studio

 Använd Visual Studio för att skapa ett resurs grupps projekt och distribuera det till Azure via användar gränssnittet. Du väljer vilken typ av resurser som ska ingå i projektet. Resurserna läggs automatiskt till i Resource Manager-mallen. Projektet innehåller också ett PowerShell-skript för att distribuera mallen.

En introduktion till att använda Visual Studio med resurs grupper finns i [skapa och Distribuera Azure-resurs grupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="troubleshoot"></a>Felsöka

Om du får problem med att skapa HDInsight-kluster läser du [åtkomstkontrollkrav](hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig flera olika sätt att skapa ett HDInsight-kluster. Mer information finns i följande artiklar:

* Fler HDInsight-relaterade mallar finns i [snabb starts mallar för Azure](https://azure.microsoft.com/resources/templates/?term=hdinsight).
* Ett exempel på hur du distribuerar resurser via .NET-klient biblioteket finns i [distribuera resurser med hjälp av .NET-bibliotek och en mall](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Ett djupgående exempel på hur du distribuerar ett program finns i [etablera och distribuera mikrotjänster förutsägbart i Azure](../app-service/deploy-complex-application-predictably.md).
* Information om hur du distribuerar lösningen till olika miljöer finns i [Utvecklings- och testmiljöer i Microsoft Azure](../devtest-labs/devtest-lab-overview.md).
* Mer information om avsnitten i Azure Resource Manager-mallen finns i [Redigera mallar](../azure-resource-manager/templates/template-syntax.md).
* En lista över de funktioner som du kan använda i en Azure Resource Manager-mall finns i [Template Functions](../azure-resource-manager/templates/template-functions.md).
