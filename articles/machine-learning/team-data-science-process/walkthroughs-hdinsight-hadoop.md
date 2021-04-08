---
title: Analys av Azure HDInsight Hadoop att använda Hive-team data science process
description: Exempel på en grupp data vetenskaps process som går igenom användningen av Hive på Azure HDInsight Hadoop att göra förutsägelse analyser.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864170"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Genom gång av HDInsight Hadoop-data vetenskap med Hive i Azure 

De här genom gången använder Hive med ett HDInsight Hadoop-kluster för att göra förutsägelse analyser. De följer de steg som beskrivs i processen för team data vetenskap. En översikt över processen för team data vetenskap finns i [data science process](overview.md). En introduktion till Azure HDInsight finns i [Introduktion till Azure HDInsight, Hadoop Technology stack och Hadoop-kluster](../../hdinsight/hadoop/apache-hadoop-introduction.md).

Ytterligare data vetenskaps genom gångar som kör team data vetenskaps processen grupperas efter den **plattform** som de använder. Se [genom gångar som kör team data science-processen](walkthroughs.md) för en specificering av dessa exempel.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Förutsägelse taxi-tips med Hive med HDInsight Hadoop

Genom gången [Använd HDInsight Hadoop-kluster](hive-walkthrough.md) används data från New York taxin för att förutsäga: 

- Om ett tips betalas 
- Fördelningen av Tip-belopp

Scenariot implementeras med hjälp av Hive med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/). Du får lära dig hur du lagrar, utforskar och funktions tekniker data från en offentligt tillgänglig NYC taxi resa och avgifts data uppsättning. Du kan också använda Azure Machine Learning för att bygga och distribuera modeller.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Förutsäg annons klickningar med Hive med HDInsight Hadoop

[Använd Azure HDInsight Hadoop-kluster på en data uppsättning på 1 TB TB](hive-criteo-walkthrough.md) använder en offentligt tillgänglig [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) Klicka på data uppsättning för att förutsäga om ett tips betalas och de förväntade beloppen. Scenariot implementeras med hjälp av Hive med ett [Azure HDInsight Hadoop kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktions tekniker och data insamlings exempel. Den använder Azure Machine Learning för att skapa, träna och räkna upp en binär klassificerings modell för att förutsäga om en användare klickar på en annons. Genom gången visar hur du publicerar en av dessa modeller som en webb tjänst.


## <a name="next-steps"></a>Nästa steg

En beskrivning av de viktiga komponenter som utgör team data vetenskaps processen finns i [Översikt över team data science-processen](overview.md).

En beskrivning av livs cykeln för team data vetenskaps process som du kan använda för att strukturera data vetenskaps projekt finns i [livs cykeln för team data science process](lifecycle.md). Livs cykeln beskriver stegen, från början till slut, som vanligt vis följer de projekt som körs. 

