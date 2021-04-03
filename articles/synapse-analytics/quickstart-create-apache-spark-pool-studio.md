---
title: 'Snabb start: skapa en server lös Apache Spark pool med Synapse Studio'
description: Skapa en server lös Apache Spark-pool med Synapse Studio genom att följa stegen i den här hand boken.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 10/16/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: d280e96b354f38a86545ed5f6bcaa6c9a314c46b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462025"
---
# <a name="quickstart-create-a-serverless-apache-spark-pool-using-synapse-studio"></a>Snabb start: skapa en server lös Apache Spark pool med Synapse Studio

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera, analysera och hantera dina data. Apache Spark pool erbjuder Big data Compute-funktioner med öppen källkod. När du har skapat en Apache Spark-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och hanteras för att få insikter.  

I den här snabb starten beskrivs stegen för att skapa en Apache Spark-pool i en Synapse-arbetsyta med hjälp av Synapse Studio.

> [!IMPORTANT]
> Faktureringen för Spark-instanser beräknas per minut, oavsett om du använder dem eller inte. Se till att stänga av Spark-instansen när du är färdig med den, eller ange en kort tids gräns. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse-arbetsyta](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigera till arbets ytan Synapse

1. Gå till arbets ytan Synapse där Apache Spark-poolen skapas genom att skriva tjänst namnet (eller resurs namnet direkt) i Sök fältet.

    ![Azure Portal Sök fältet med Synapse-arbetsytor som anges i.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. I listan över arbets ytor skriver du namnet (eller en del av namnet) på arbets ytan som ska öppnas. I det här exemplet använder vi en arbets yta med namnet **contosoanalytics**.

    ![Lista över Synapse-arbetsytor filtrerade för att visa de som innehåller namnet contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Starta Synapse Studio 

I översikten för arbets ytan väljer du **webb adressen för arbets ytan** för att öppna Synapse Studio.

![Översikt över Azure Portal Synapse-arbetsyta med starta Synapse Studio markerat.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-the-apache-spark-pool-in-synapse-studio"></a>Skapa Apache Spark-poolen i Synapse Studio

1. På Start sidan för Synapse Studio navigerar du till **hanterings hubben** i det vänstra navigerings fönstret genom att välja ikonen **Hantera** .

    ![Synapse Studio-start sida med avsnittet hanterings hubb markerat.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. I hanterings hubben går du till avsnittet **Apache Spark pooler** för att se den aktuella listan över Apache Spark pooler som är tillgängliga i arbets ytan.
    
    ![Synapse Studio-hanterings hubb med Apache Spark pooler-navigering vald](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-22.png)

1. Välj **+ ny** och guiden Ny Apache Spark pool skapas visas. 

1. Ange följande information på fliken **grundläggande** :

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Namn på Apache Spark bassäng** | contosospark | Detta är det namn som Apache Spark-poolen kommer att ha. |
    | **Node-storlek** | Liten (4 vCPU/32 GB) | Ange den minsta storleken för att minska kostnaderna för den här snabb starten |
    | **Automatisk skalning** | Inaktiverad | Vi behöver inte autoskalning i den här snabb starten |
    | **Antal noder** | 8 | Begränsa kostnaderna i den här snabb starten med en liten storlek|
       
    ![Grunderna för Synapse Studio New Apache Spark pool](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-24.png)
    
    > [!IMPORTANT]
    > Observera att det finns vissa begränsningar för de namn som Apache Spark pooler kan använda. Namn får bara innehålla bokstäver eller siffror, måste bestå av högst 15 tecken, måste inledas med en bokstav, får inte innehålla reserverade ord och vara unika i arbets ytan.

1. På nästa flik (ytterligare inställningar) lämnar du alla inställningar som standard.

1. Vi lägger inte till några taggar nu, så välj **Granska + skapa**.

1. På fliken **Granska + skapa** ser du till att informationen ser korrekt ut baserat på vad som angavs tidigare och tryck på **skapa**. 

    ![Skapa Synapse Studio New Apache Spark pool](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-26.png)

1. Den Apache Spark poolen startar etablerings processen.

1. När etableringen är klar visas den nya Apache Spark poolen i listan.
    
    ![Synapse Studio New Apache Spark pool List](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-28.png)

## <a name="clean-up-apache-spark-pool-resources-using-synapse-studio"></a>Rensa Apache Spark pool resurser med Synapse Studio

Följ stegen nedan för att ta bort Apache Spark-poolen från arbets ytan med Synapse Studio.
> [!WARNING]
> Om du tar bort en spark-pool tas analys motorn bort från arbets ytan. Det går inte längre att ansluta till poolen, och alla frågor, pipeliner och antecknings böcker som använder den här Spark-poolen kommer inte längre att fungera.

Gör så här om du vill ta bort Apache Spark poolen:

1. Navigera till Apache Spark pooler i hanterings hubben i Synapse Studio.
1. Välj ellipsen bredvid Apache-poolen som ska tas bort (i det här fallet **contosospark**) för att Visa kommandona för den Apache Spark poolen.

    ![Lista över Apache Spark pooler med den nyligen skapade poolen vald.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-29.png)

1. Tryck på **ta bort**.
1. Bekräfta borttagningen och tryck på knappen **ta bort** .
1. När processen har slutförts visas inte längre den Apache Spark poolen i arbets ytans resurser. 

## <a name="next-steps"></a>Nästa steg

- Se [snabb start: skapa en Apache Spark pool i Synapse Studio med hjälp av webb verktyg](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en Apache Spark pool med hjälp av Azure Portal](quickstart-create-apache-spark-pool-portal.md).
