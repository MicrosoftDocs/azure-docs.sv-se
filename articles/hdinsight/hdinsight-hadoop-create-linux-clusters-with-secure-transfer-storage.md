---
title: Apache Hadoop & säker överförings lagring – Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med Azure-lagringskonton som använder säker överföring.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: a02da7237252811d89e2c19a29f49f0bf9bb3804
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945728"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop kluster med lagrings konton för säker överföring i Azure HDInsight

Funktionen [Säker överföring krävs](../storage/common/storage-require-secure-transfer.md) förbättrar säkerheten för ditt Azure Storage-konto genom att kräva att alla förfrågningar till ditt konto görs via en säker anslutning. Den här funktionen och wasbs-schemat stöds endast av HDInsight-kluster med version 3.6 eller senare.

> [!IMPORTANT]
> Aktivering av säker lagrings överföring efter att ett kluster har skapats kan resultera i fel med ditt lagrings konto och rekommenderas inte. Det är bättre att skapa ett nytt kluster med ett lagrings konto med säker överföring redan aktiverat.

## <a name="storage-accounts"></a>Lagringskonton

### <a name="azure-portal"></a>Azure Portal

Som standard aktive ras egenskapen för säker överföring som krävs när du skapar ett lagrings konto i Azure Portal.

Om du vill uppdatera ett befintligt lagrings konto med Azure Portal, se [Kräv säker överföring med Azure Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

För PowerShell-cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)kontrollerar du att parametern `-EnableHttpsTrafficOnly` har angetts till `1` .

Om du vill uppdatera ett befintligt lagrings konto med PowerShell, se [Kräv säker överföring med PowerShell](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell).

### <a name="azure-cli"></a>Azure CLI

För att skapa ett Azure CLI-kommando [AZ lagrings konto](/cli/azure/storage/account#az-storage-account-create), se till att parametern `--https-only` är inställd på `true` .

Information om hur du uppdaterar ett befintligt lagrings konto med Azure CLI finns i [Kräv säker överföring med Azure CLI](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli).

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Det finns flera alternativ för att lägga till ytterligare lagringskonton som använder säker överföring:

* Ändra Azure Resource Manager-mallen i det sista avsnittet.
* Skapa ett kluster med hjälp av [Azure-portalen](https://portal.azure.com) och ange ett länkat lagringskonto.
* Använd skriptåtgärder om du vill lägga till fler lagringskonton som använder säker överföring i ett befintligt HDInsight-kluster. Mer information finns i [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg

* Användning av Azure Storage (WASB) i stället för [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som standard data lager
* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](hdinsight-upload-data.md).