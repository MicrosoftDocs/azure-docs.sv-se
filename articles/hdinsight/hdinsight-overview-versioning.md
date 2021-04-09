---
title: Introduktion till versioner – Azure HDInsight
description: Lär dig hur du arbetar med versions hantering i Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 6db4c7856ebdf75d5bf94de1e3110bb25bc93e69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103493874"
---
# <a name="how-versioning-works-in-hdinsight"></a>Så här fungerar versions hantering i HDInsight

HDInsight-tjänsten har två huvud komponenter: en resurs leverantör och Apache Hadoop komponenter som distribueras i ett kluster. 

## <a name="hdinsight-resource-provider"></a>HDInsight-resurs leverantör

Resurser i Azure görs tillgängliga av en resurs leverantör. HDInsight-resurs leverantören ansvarar för att skapa, hantera och ta bort kluster.

## <a name="hdinsight-images"></a>HDInsight-bilder

HDInsight använder avbildningar för att samla in program med öppen källkod (OSS) som kan distribueras i ett kluster. De här avbildningarna innehåller grundläggande Ubuntu-operativsystem och kärn komponenter som Spark, Hadoop, Kafka, HBase eller Hive.

## <a name="versioning-in-hdinsight"></a>Versions hantering i HDInsight

Microsoft uppgraderar regelbundet avbildningarna och HDInsight-resurs leverantören för att inkludera nya förbättringar och funktioner.

Nya HDInsight-versioner kan skapas när ett eller flera av följande stämmer:

- Större ändringar eller uppdateringar av HDInsight Resource Provider-funktioner
- Större versioner av OSS-komponenter
- Större ändringar i operativ systemet Ubuntu

En ny avbildnings version skapas när något av följande stämmer:

- Större eller mindre versioner och uppdateringar av OSS-komponenter
- Korrigeringar eller korrigeringar för en komponent i avbildningen

## <a name="next-steps"></a>Nästa steg

- [Apache-komponenter och-versioner i HDInsight](./hdinsight-component-versioning.md)
