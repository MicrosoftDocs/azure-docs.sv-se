---
title: 'Snabb start: skapa en server lös Apache Spark pool med hjälp av Azure Portal'
description: Skapa en server lös Apache Spark-pool med hjälp av Azure Portal genom att följa stegen i den här hand boken.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: spark
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3fc7a7f137701590e4bddd791a3fdd2d1eaeca4a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461600"
---
# <a name="quickstart-create-a-new-serverless-apache-spark-pool-using-the-azure-portal"></a>Snabb start: skapa en ny server lös Apache Spark-pool med hjälp av Azure Portal

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera, analysera och distribuera dina data. En Apache Spark pool tillhandahåller funktioner för Big data Compute med öppen källkod. När du har skapat en Apache Spark-pool på din Synapse-arbetsyta, kan data läsas in, modelleras, bearbetas och distribueras för snabbare analytiska insikter.

I den här snabb starten får du lära dig hur du använder Azure Portal för att skapa en Apache Spark pool i en Synapse-arbetsyta.

> [!IMPORTANT]
> Faktureringen för Spark-instanser beräknas per minut, oavsett om du använder dem eller inte. Se till att stänga av Spark-instansen när du är färdig med den, eller ange en kort tids gräns. Mer information finns i avsnittet **Rensa resurser** i den här artikeln.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse Analytics-arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigera till arbets ytan Synapse 
1. Gå till arbets ytan Synapse där Apache Spark-poolen skapas genom att skriva tjänst namnet (eller resurs namnet direkt) i Sök fältet.
![Azure Portal Sök fältet med Synapse-arbetsytor som anges i.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. I listan över arbets ytor skriver du namnet (eller en del av namnet) på arbets ytan som ska öppnas. I det här exemplet använder vi en arbets yta med namnet **contosoanalytics**.
![Lista över Synapse-arbetsytor filtrerade för att visa de som innehåller namnet contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)


## <a name="create-new-apache-spark-pool"></a>Skapa ny Apache Spark pool

1. I arbets ytan Synapse där du vill skapa Apache Spark-poolen väljer du **ny Apache Spark pool**.
    ![Översikt över Synapse-arbetsytan med en röd ruta runt kommandot för att skapa en ny Apache Spark pool](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-01.png)
2. Ange följande information på fliken **grundläggande** :

    |Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Namn på Apache Spark bassäng** | Ett giltigt poolnamn | Detta är det namn som Apache Spark-poolen kommer att ha. |
    | **Node-storlek** | Liten (4 vCPU/32 GB) | Ange den minsta storleken för att minska kostnaderna för den här snabb starten |
    | **Automatisk skalning** | Inaktiverad | Den här snabb starten behöver inte autoskalas |
    | **Antal noder** | 5 | Begränsa kostnaderna för den här snabb starten med en liten storlek |


    ![Fliken Skapa Flow-grunder för Apache Spark pool.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-02.png)
    > [!IMPORTANT]
    > Observera att det finns vissa begränsningar för de namn som Apache Spark pooler kan använda. Namn får bara innehålla bokstäver eller siffror, måste bestå av högst 15 tecken, måste inledas med en bokstav, får inte innehålla reserverade ord och vara unika i arbets ytan.

3. Välj **Nästa: ytterligare inställningar** och granska standardinställningarna. Ändra inte några standardinställningar.
    ![Skärm bild som visar sidan "skapa Apache Spark pool" med fliken "ytterligare inställningar" vald.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-03.png)

4. Välj **Nästa: Taggar**. Lägg inte till några taggar.
    ![Apache Spark fliken Skapa flöde-ytterligare inställningar.](media/quickstart-create-apache-spark-pool/create-spark-pool-03-tags.png)

5. Välj **Granska + skapa**.

6. Se till att informationen ser korrekt ut baserat på vad som tidigare har angetts och välj **skapa**.
    ![Apache Spark fliken Skapa flöde-granska inställningar.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-05.png)

7. I det här läget startar resurs etablerings flödet och anger när det är klart.
    ![Skärm bild som visar sidan "Översikt" med meddelandet "distributionen är klar" visas.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-06.png)

8. När etableringen är klar visas en ny post för den nyligen skapade Apache Spark-poolen när du navigerar tillbaka till arbets ytan.
    ![Apache Spark poolen skapa flödes resurs etablering.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-07.png)

9. Det finns inga resurser att köra, inga avgifter för Spark, du har skapat metadata om de Spark-instanser som du vill skapa.

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Apache Spark-poolen från arbets ytan.
> [!WARNING]
> Om du tar bort en Apache Spark pool tas analys motorn bort från arbets ytan. Det kommer inte längre att gå att ansluta till poolen, och alla frågor, pipeliner och antecknings böcker som använder den här Apache Spark-poolen fungerar inte längre.

Gör så här om du vill ta bort Apache Spark poolen:

1. Gå till bladet Apache Spark pooler på arbets ytan.
2. Välj den Apache Spark pool som ska tas bort (i det här fallet **contosospark**).
3. Tryck på **ta bort**.
 ![Lista över Apache Spark pooler med den nyligen skapade poolen vald.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-08.png)
4. Bekräfta borttagningen och tryck på knappen **ta bort** .
 ![Bekräftelse dialog ruta för att ta bort den valda Apache Spark poolen.](media/quickstart-create-apache-spark-pool/create-spark-pool-portal-10.png)
5. När processen har slutförts visas inte längre den Apache Spark poolen i arbets ytans resurser.

## <a name="next-steps"></a>Nästa steg

- Se [snabb start: skapa en Apache Spark Notebook](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en dedikerad SQL-pool med hjälp av Azure Portal](quickstart-create-sql-pool-portal.md).
