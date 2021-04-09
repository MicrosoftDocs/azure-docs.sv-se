---
title: Uppdatera .NET för Apache Spark till version v 1.0 i HDI
description: Läs om hur du uppdaterar .NET för Apache Spark version till 1,0 i HDI och hur detta påverkar din befintliga kod och dina befintliga kluster.
author: Niharikadutta
ms.author: nidutta
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/05/2021
ms.openlocfilehash: a1602f29a6d0066ec3c99e990532411621652c47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788137"
---
# <a name="updating-net-for-apache-spark-to-version-v10--in-hdinsight"></a>Uppdatera .NET för Apache Spark till version v 1.0 i HDInsight

Det här dokumentet pratar om den första huvud versionen av [.net för Apache Spark](https://github.com/dotnet/spark)och hur det kan påverka dina aktuella produktions pipelines i HDInsight-kluster.

## <a name="about-net-for-apache-spark-version-100"></a>Om .NET för Apache Spark version 1.0.0

Det här är den första [större officiella versionen](https://github.com/dotnet/spark/releases/tag/v1.0.0) av .net för Apache Spark och ger DataFrame API-slutförande för Spark 2.4. x samt Spark 3.0. x tillsammans med andra funktioner. En fullständig lista över alla funktioner, förbättringar och fel korrigeringar finns i den officiella [v 1.0.0-versionen](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md).
En annan viktig sak att notera är att den här versionen **inte** är kompatibel med tidigare versioner av `Microsoft.Spark` och `Microsoft.Spark.Worker` . Ta en titt på [migreringsguiden](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) om du planerar att uppgradera ditt .net för att Apache Spark program ska vara kompatibelt med v-1.0.0.

## <a name="using-net-for-apache-spark-v10-in-hdinsight"></a>Använda .NET för Apache Spark v 1.0 i HDInsight

De aktuella HDI-klustren kommer inte att påverkas (dvs. de kommer fortfarande att ha samma version som tidigare). nyligen skapade HDI-kluster kommer att ha den senaste v 1.0.0-versionen av .NET för Apache Spark. Det innebär att om:

- **Du har ett äldre HDI-kluster**: om du vill uppgradera Spark .net-programmet till v 1.0.0 (rekommenderas) måste du uppdatera `Microsoft.Spark.Worker` versionen på ditt HDI-kluster. Mer information finns i [avsnittet Ändra versioner av .net för Apache Spark i HDI-kluster](#changing-net-for-apache-spark-version-on-hdinsight).
Om du inte vill uppdatera den aktuella versionen av .NET för Apache Spark i ditt program krävs inga ytterligare åtgärder.  

- **Du har ett nytt HDI-kluster**: om du vill uppgradera Spark .net-programmet till v 1.0.0 (rekommenderas) behöver du inte göra några steg för att ändra arbets TAGAREN på HDI, men du måste referera till [migreringsguiden](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) för att förstå de steg som krävs för att uppdatera din kod och dina pipeliner.
Om du inte vill ändra den aktuella versionen av .NET för Apache Spark i ditt program måste du ändra versionen på ditt HDI-kluster från v 1.0 (standard på nya kluster) till den version som du använder. Mer information finns i [avsnittet Ändra versioner av .net för Apache Spark i HDI-kluster](spark-dotnet-version-update.md#changing-net-for-apache-spark-version-on-hdinsight).  

## <a name="changing-net-for-apache-spark-version-on-hdinsight"></a>Ändra .NET för Apache Spark version på HDInsight

### <a name="deploy-microsoftsparkworker"></a>Distribuera Microsoft. Spark. Worker

`Microsoft.Spark.Worker` är en server dels komponent som finns på de enskilda arbetsnoderna i Spark-klustret. När du vill köra en C# UDF (användardefinierad funktion) måste Spark förstå hur du startar .NET CLR för att köra den här UDF-filen. `Microsoft.Spark.Worker` tillhandahåller en samling klasser för Spark som aktiverar den här funktionen. Välj arbets version beroende på vilken version av .NET Apache Spark du vill distribuera i HDI-klustret.

1. Hämta Linux-versionen Microsoft. Spark. Worker för just din version. Om du till exempel vill `.NET for Apache Spark v1.0.0` Hämta [Microsoft. Spark. Worker. netcoreapp 3.1. linux-x64-1.0.0. tar. gz](https://github.com/dotnet/spark/releases/tag/v1.0.0).  

2. Ladda ned [install-Worker.sh](https://github.com/dotnet/spark/blob/master/deployment/install-worker.sh) -skript för att installera de isolerade binärfilerna i steg 1 till alla arbetsnoder i ditt HDI-kluster.  

3. Överför ovanstående filer till det Azure Storage konto som ditt kluster har åtkomst till. Mer information finns i [artikeln om .net för Apache Spark HDI-distribution](/dotnet/spark/tutorials/hdinsight-deployment#upload-files-to-azure) .

4. Kör `install-worker.sh` skriptet på alla arbetsnoder i klustret med hjälp av skript åtgärder. Mer information hittar du i [artikeln om .net för Apache Spark HDI-distribution](/dotnet/spark/tutorials/hdinsight-deployment#run-the-hdinsight-script-action) .

### <a name="update-your-application-to-use-specific-version"></a>Uppdatera programmet till att använda en specifik version

Du kan uppdatera ditt .NET för Apache Spark-program om du vill använda en specifik version genom att välja den version av [Microsoft. Spark NuGet-paketet](https://www.nuget.org/packages/Microsoft.Spark/) som krävs i projektet. Ta en titt på versions informationen för den specifika versionen och [migreringsguiden](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) som nämnts ovan, om du väljer att uppdatera programmet till v-1.0.0.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="will-my-existing-hdi-cluster-with-version--100-start-failing-with-the-new-release"></a>Kommer mitt befintliga HDI-kluster med version < 1.0.0 inte att börja med den nya versionen?

Befintliga HDI-kluster fortsätter att ha samma tidigare version av .NET för Apache Spark och ditt befintliga program (med föregående version av Spark .NET) kommer inte att påverkas.

## <a name="next-steps"></a>Nästa steg

[Distribuera ditt .NET för Apache Spark-program i HDInsight](/dotnet/spark/tutorials/hdinsight-deployment)