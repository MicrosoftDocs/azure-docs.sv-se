---
title: Pakethantering
description: Lär dig hur du lägger till och hanterar bibliotek som används av Apache Spark i Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: e8ad6d072af6979eb8509068c1dcd239e7840950
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598022"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Hantera bibliotek för Apache Spark i Azure Synapse Analytics
Bibliotek ger återanvändbar kod som du kanske vill inkludera i dina program eller projekt. 

Du kan behöva uppdatera Server lösa Apache Spark pool miljö av olika anledningar. Du kan till exempel se att:
- ett av dina kärn beroenden släppte en ny version.
- du behöver ett extra paket för att träna din Machine Learning-modell eller förbereda dina data.
- du har hittat ett bättre paket och behöver inte längre det gamla paketet.
- ditt team har skapat ett anpassat paket som du behöver tillgängligt i Apache Spark-poolen.

Om du vill göra tredje part eller lokalt skapad kod tillgänglig för dina program, kan du installera ett bibliotek på någon av Server lös Apache Spark pooler eller Notebook-sessionen.
  
## <a name="default-installation"></a>Standard installation
Apache Spark i Azure Synapse Analytics har en fullständig Anacondas-installation plus extra bibliotek. Du hittar den fullständiga biblioteks listan på [Apache Spark versions stöd](apache-spark-version-support.md). 

När en spark-instans startar, tas dessa bibliotek automatiskt med. Du kan lägga till ytterligare paket på nivån för Spark-poolen eller-sessionen.

## <a name="workspace-packages"></a>Paket för arbets ytor
När du utvecklar anpassade program eller modeller kan ditt team utveckla olika kod artefakter som hjul-eller jar-filer för att paketera din kod. 

I Synapse kan arbets ytans paket vara anpassade eller privata hjul-eller jar-filer. Du kan ladda upp dessa paket till din arbets yta och senare tilldela dem till en speciell Spark-pool. När de har tilldelats installeras dessa arbets ytans paket automatiskt på alla sessioner med Spark-pool.

Mer information om hur du hanterar bibliotek för arbets ytor finns i följande instruktions guider:

- [Python-arbetsyte paket (för hands version): ](./apache-spark-manage-python-packages.md#install-wheel-files) Ladda upp python-Wheel-filer som ett paket för arbets ytor och Lägg senare till paketen till vissa Server lös Apache Spark pooler.
- [Scala/Java-arbetsyte paket (för hands version): ](./apache-spark-manage-scala-packages.md#workspace-packages) Ladda upp Scala och java jar-filer som ett paket för arbets ytor och Lägg senare till paketen till vissa Server lös Apache Spark pooler.

## <a name="pool-packages"></a>Paket för pool
I vissa fall kanske du vill standardisera den uppsättning paket som används på en viss Apache Spark pool. Den här standardiseringen kan vara användbar om samma paket ofta installeras av flera personer i din grupp. 

Med hjälp av funktionerna för hantering av Azure Synapse Analytics-pooler kan du konfigurera standard uppsättningen bibliotek som du vill installera på en specifik server lös Apache Spark pool. De här biblioteken installeras ovanpå [bas körnings miljön](./apache-spark-version-support.md). 

För närvarande stöds inte pool hantering för python. För python använder Synapse Spark-pooler Conda för att installera och hantera ett python-paket beroenden. När du anger dina bibliotek på en samlings nivå kan du nu ange en requirements.txt eller en miljö. yml. Den här miljö konfigurations filen används varje gång en spark-instans skapas från den Spark-poolen. 

Mer information om dessa funktioner finns i dokumentationen om hantering av [python-pooler](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Om paketet som du installerar är stort eller tar lång tid att installera, påverkar detta start tiden för Spark-instansen.
> - Det finns inte stöd för att ändra PySpark-, python-, Scala-/Java-, .NET-eller Spark-versionen.
> - Det finns inte stöd för att installera paket från PyPI i DEP-aktiverade arbets ytor.

## <a name="session-scoped-packages"></a>Paket med sessionsbaserade omfång
När du utför interaktiv data analys eller maskin inlärning kan du ofta se att du vill testa nya paket eller så kan du behöva paket som inte redan finns i Apache Spark-poolen. I stället för att uppdatera poolinställningarna kan användarna nu använda paket som är baserade på sessioner för att lägga till, hantera och uppdatera sessions beroenden.

Med session-omfångs paket kan användare definiera paket beroenden i början av sessionen. När du installerar ett paket som omfattas av en session har endast den aktuella sessionen åtkomst till de angivna paketen. Detta innebär att de här sessionens omfångs paket inte påverkar andra sessioner eller jobb med samma Apache Spark pool. Dessutom installeras dessa bibliotek ovanpå bas körnings-och Poolnivå-paketen. 

Mer information om hur du hanterar sessionsbaserade paket finns i följande instruktions guider:

- [Python-sessionsobjekt (för hands version):](./apache-spark-manage-python-packages.md) I början av en session anger du en Conda- *miljö. yml* för att installera ytterligare python-paket från populära databaser. 
- [Scala/Java-sessionsobjekt: ](./apache-spark-manage-scala-packages.md) I början av sessionen anger du en lista över jar-filer att installera med `%%configure` .

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)
