---
title: Apache Hadoop & säker överföringslagring – Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med Azure-lagringskonton som använder säker överföring.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/18/2020
ms.openlocfilehash: 22804015ebf0344c00e60c88f780fe22ba440b52
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774997"
---
# <a name="apache-hadoop-clusters-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop-kluster med lagringskonton för säker överföring i Azure HDInsight

Funktionen [Säker överföring krävs](../storage/common/storage-require-secure-transfer.md) förbättrar säkerheten för ditt Azure Storage-konto genom att kräva att alla förfrågningar till ditt konto görs via en säker anslutning. Den här funktionen och wasbs-schemat stöds endast av HDInsight-kluster med version 3.6 eller senare.

> [!IMPORTANT]
> Att aktivera säker lagringsöverföring när du har skapat ett kluster kan resultera i fel med ditt lagringskonto och rekommenderas inte. Det är bättre att skapa ett nytt kluster med ett lagringskonto med säker överföring redan aktiverat.

## <a name="storage-accounts"></a>Lagringskonton

### <a name="azure-portal"></a>Azure Portal

Som standard är egenskapen säker överföring obligatorisk aktiverad när du skapar ett lagringskonto i Azure Portal.

Information om hur du uppdaterar ett befintligt lagringskonto med Azure Portal finns i [Kräv säker överföring med Azure Portal](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-for-an-existing-storage-account).

### <a name="powershell"></a>PowerShell

För PowerShell-cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount)ser du till att `-EnableHttpsTrafficOnly` parametern är inställd på `1` .

Information om hur du uppdaterar ett befintligt lagringskonto med PowerShell [finns i Kräv säker överföring med PowerShell.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-powershell)

### <a name="azure-cli"></a>Azure CLI

För Azure CLI-kommandot [az storage account create ser](/cli/azure/storage/account#az_storage_account_create)du till att `--https-only` parametern är inställd på `true` .

Information om hur du uppdaterar ett befintligt lagringskonto med Azure CLI [finns i Kräv säker överföring med Azure CLI.](../storage/common/storage-require-secure-transfer.md#require-secure-transfer-with-azure-cli)

## <a name="add-additional-storage-accounts"></a>Lägga till fler lagringskonton

Det finns flera alternativ för att lägga till ytterligare lagringskonton som använder säker överföring:

* Ändra Azure Resource Manager-mallen i det sista avsnittet.
* Skapa ett kluster med hjälp av [Azure-portalen](https://portal.azure.com) och ange ett länkat lagringskonto.
* Använd skriptåtgärder om du vill lägga till fler lagringskonton som använder säker överföring i ett befintligt HDInsight-kluster. Mer information finns i [Add additional storage accounts to HDInsight](hdinsight-hadoop-add-storage.md) (Lägga till fler lagringskonton till HDInsight).

## <a name="next-steps"></a>Nästa steg

* Användning av Azure Storage (WASB) i stället för [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som standarddatalager
* Mer information om hur HDInsight använder Azure Storage finns i [Använda Azure Storage med HDInsight](hdinsight-hadoop-use-blob-storage.md).
* Mer information om hur du överför data till HDInsight finns i [Överföra data till HDInsight](hdinsight-upload-data.md).