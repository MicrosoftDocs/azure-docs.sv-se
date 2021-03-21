---
title: 'Snabb start: skapa en dedikerad SQL-pool med hjälp av Azure Portal'
description: Skapa en ny dedikerad SQL-pool med hjälp av Azure Portal genom att följa stegen i den här hand boken.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3537b126adf082c8b00a02d999789d8a32b3f51d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462009"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-the-azure-portal"></a>Snabb start: skapa en dedikerad SQL-pool med hjälp av Azure Portal

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera och analysera dina data. En dedikerad SQL-pool ger T-SQL-baserade beräknings-och lagrings funktioner. När du har skapat en dedikerad SQL-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och levereras för snabbare analytiska insikter.

I den här snabb starten får du lära dig hur du skapar en dedikerad SQL-pool i en Synapse-arbetsyta med hjälp av Azure Portal.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse-arbetsyta](./quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigera till arbets ytan Synapse

1. Gå till arbets ytan Synapse där den dedikerade SQL-poolen skapas genom att skriva tjänst namnet (eller resurs namnet direkt) i Sök fältet.
![Azure Portal Sök fältet med Synapse-arbetsytor som skrevs in. ](media/quickstart-create-sql-pool/create-sql-pool-00a.png) . 
1. I listan över arbets ytor skriver du namnet (eller en del av namnet) på arbets ytan som ska öppnas. I det här exemplet använder vi en arbets yta med namnet **contosoanalytics**.
![Lista över Synapse-arbetsytor filtrerade för att visa de som innehåller namnet contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-new-dedicated-sql-pool"></a>Skapa ny dedikerad SQL-pool

1. På arbets ytan Synapse där du vill skapa den dedicerade SQL-poolen väljer du kommandot **ny dedicerad SQL-pool** i det översta fältet.
![Översikt över Synapse-arbetsytan med en röd ruta runt kommandot för att skapa en ny dedikerad SQL-pool.](media/quickstart-create-sql-pool/create-sql-pool-portal-01.png)
2. Ange följande information på fliken **grundläggande** :

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **Namn på dedicerad SQL-pool** | Valfritt giltigt namn | Namnet på den dedikerade SQL-poolen. |
    | **Prestanda nivå** | DW100c | Ställ in på den minsta storleken för att minska kostnaderna för den här snabb starten |

  
    ![Dedikerad SQL-pool, fliken Skapa flöde – grunder.](media/quickstart-create-sql-pool/create-sql-pool-portal-02.png)

    > [!IMPORTANT]
    > Observera att det finns särskilda begränsningar för namn som dedikerade SQL-pooler kan använda. Namn får inte innehålla specialtecken, måste bestå av högst 15 tecken, får inte innehålla reserverade ord och vara unika i arbets ytan.

3. Välj **Nästa: Ytterligare inställningar**.
4. Välj **ingen** för att etablera den DEDIKERADe SQL-poolen utan data. Lämna standard sorteringen markerad.

    Om du vill återställa din dedikerade SQL-pool från en återställnings punkt väljer du **återställnings punkt**. Mer information om hur du utför en återställning finns i [instruktion: återställa en befintlig dedikerad SQL-pool](backuprestore/restore-sql-pool.md)

![dedikerad SQL-pool, fliken Skapa flöde-ytterligare inställningar.](media/quickstart-create-sql-pool/create-sql-pool-portal-03.png)

5. Välj **Granska + skapa**.
6. Se till att informationen ser korrekt ut baserat på vad som angavs tidigare. Välj **Skapa**.
![dedikerad SQL-pool skapa flöde-granska inställningar fliken.](media/quickstart-create-sql-pool/create-sql-pool-portal-04.png)

7. I det här läget startar resurs etablerings flödet.
 ![Skärm bild som visar sidan "distributionen är klar".](media/quickstart-create-sql-pool/create-sql-pool-portal-06.png)

8. När etableringen är klar visas en ny post för den nyligen skapade dedikerade SQL-poolen när du navigerar tillbaka till arbets ytan.
 ![SQL-pool skapa flödes resurs etablering.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)


När den dedikerade SQL-poolen har skapats kommer den att vara tillgänglig i arbets ytan för att läsa in data, bearbeta strömmar, läsa från sjö osv.

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort den dedikerade SQL-poolen från arbets ytan.
> [!WARNING]
> Om du tar bort en dedikerad SQL-pool tas både analys motorn och de data som lagras i databasen i den borttagna dedikerade SQL-poolen bort från arbets ytan. Det går inte längre att ansluta till den dedikerade SQL-poolen, och alla frågor, pipeliner och antecknings böcker som läser eller skriver till den här dedikerade SQL-poolen kommer inte längre att fungera.

Utför följande steg om du vill ta bort den dedikerade SQL-poolen:

1. Gå till bladet SQL-pooler på bladet arbets yta
1. Välj den dedicerade SQL-pool som ska tas bort (i det här fallet **contosowdw**)
1. Tryck på **ta bort** när du är markerat
1. Bekräfta borttagningen och tryck på knappen **ta bort** ![ dedikerad SQL-pool – Markera borttagnings bekräftelse.](media/quickstart-create-sql-pool/create-sql-pool-portal-11.png)
1. När processen har slutförts visas inte längre den dedikerade SQL-poolen i resurserna för arbets ytan.

## <a name="next-steps"></a>Nästa steg

- Se [snabb start: skapa en server lös Apache Spark pool i Synapse Studio med hjälp av webb verktyg](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en server lös Apache Spark pool med hjälp av Azure Portal](quickstart-create-apache-spark-pool-portal.md).
