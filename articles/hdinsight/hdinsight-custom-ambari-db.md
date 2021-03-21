---
title: Anpassad Apache Ambari-databas på Azure HDInsight
description: Lär dig hur du skapar HDInsight-kluster med din egen anpassade Apache Ambari-databas.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 01/12/2021
ms.openlocfilehash: fe38ddc594060c78a2d26e9b25476e38736b4cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946062"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Konfigurera HDInsight-kluster med en anpassad Ambari-databas

Apache Ambari fören klar hanteringen och övervakningen av ett Apache Hadoop-kluster. Ambari är ett enkelt sätt att använda webb gränssnitt och REST API. Ambari ingår i HDInsight-kluster och används för att övervaka klustret och göra konfigurations ändringar.

I det normala klustret skapas, som beskrivs i andra artiklar, till exempel [Konfigurera kluster i HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari distribueras i en [S0 Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) som hanteras av HDInsight och inte är tillgänglig för användarna.

Med den anpassade Ambari DB-funktionen kan du distribuera ett nytt kluster och konfigurera Ambari i en extern databas som du hanterar. Distributionen görs med en Azure Resource Manager-mall. Den här funktionen har följande fördelar:

- Anpassning – du väljer databasens storlek och bearbetnings kapacitet. Om du har stora kluster bearbetnings intensiva arbets belastningar kan en Ambari-databas med lägre specifikationer bli en Flask hals för hanterings åtgärder.
- Flexibilitet – du kan skala databasen efter behov för att passa dina behov.
- Kontroll – du kan hantera säkerhets kopiering och säkerhet för din databas på ett sätt som passar dina organisations krav.

I resten av den här artikeln beskrivs följande saker:

- krav för att använda funktionen för anpassad Ambari-databas
- de steg som krävs för att etablera HDInsight-kluster med din egen externa databas för Apache Ambari

## <a name="custom-ambari-db-requirements"></a>Anpassade Ambari DB-krav

Du kan distribuera en anpassad Ambari-databas med alla kluster typer och-versioner. Flera kluster kan inte använda samma Ambari-databas.

Den anpassade Ambari DB har följande andra krav:

- Namnet på databasen får inte innehålla bindestreck eller blank steg
- Du måste ha en befintlig Azure SQL DB-server och-databas.
- Den databas som du anger för Ambari-installationen måste vara tom. Det ska inte finnas några tabeller i standardvärdet för dbo-schemat.
- Den användare som används för att ansluta till databasen ska ha behörigheterna Välj, CREATE TABLE och infoga för databasen.
- Aktivera alternativet för att [tillåta åtkomst till Azure-tjänster](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) på den server där du kommer att vara värd för Ambari.
- Hantering av IP-adresser från HDInsight-tjänsten måste tillåtas i brand Väggs regeln. Se [hanterings-IP-adresser för HDInsight](hdinsight-management-ip-addresses.md) för en lista med IP-adresser som måste läggas till i brand Väggs regeln på server nivå.

Kom ihåg följande saker när du är värd för Apache Ambari DB i en extern databas:

- Du är ansvarig för de ytterligare kostnaderna för Azure SQL DB som innehåller Ambari.
- Säkerhetskopiera din anpassade Ambari DB med jämna mellanrum. Azure SQL Database skapar säkerhets kopior automatiskt, men tiden för kvarhållning av säkerhets kopior varierar. Mer information finns i [Läs mer om automatisk SQL Database säkerhets kopiering](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Distribuera kluster med en anpassad Ambari-databas

Om du vill skapa ett HDInsight-kluster som använder din egen externa Ambari-databas använder du [snabb starts mal len anpassad AMBARI DB](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Redigera parametrarna i om `azuredeploy.parameters.json` du vill ange information om det nya klustret och databasen som ska innehålla Ambari.

Du kan börja distribuera med hjälp av Azure CLI. Ersätt `<RESOURCEGROUPNAME>` med den resurs grupp där du vill distribuera klustret.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="database-sizing"></a>Databas storlek

Följande tabell innehåller rikt linjer för vilka Azure SQL DB-nivåer som ska väljas baserat på storleken på ditt HDInsight-kluster.

| Antal arbetsnoder | Obligatorisk DB-nivå |
|---|---|
| <= 4 | S0 |
| >4 && <= 8 | S1 |
| >8 && <= 16 | S2 |
| >16 && <= 32 | S3 |
| >32 && <= 64 | S4 |
| >64 && <= 128 | P2 |
| >128 | Kontakta supporten |

## <a name="next-steps"></a>Nästa steg

- [Använda extern metadatalagring i Azure HDInsight](hdinsight-use-external-metadata-stores.md)
