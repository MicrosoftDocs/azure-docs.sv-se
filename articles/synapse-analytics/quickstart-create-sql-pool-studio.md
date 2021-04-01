---
title: 'Snabb start: skapa en dedikerad SQL-pool med Synapse Studio'
description: Skapa en dedikerad SQL-pool med Synapse Studio genom att följa stegen i den här hand boken.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql
ms.date: 10/16/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: 3644891f12a6475ec9cfec51f572df4742481e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541852"
---
# <a name="quickstart-create-a-dedicated-sql-pool-using-synapse-studio"></a>Snabb start: skapa en dedikerad SQL-pool med Synapse Studio

Azure Synapse Analytics erbjuder olika analys verktyg som hjälper dig att mata in, transformera, modellera och analysera dina data. En dedikerad SQL-pool ger T-SQL-baserade beräknings-och lagrings funktioner. När du har skapat en dedikerad SQL-pool på din Synapse-arbetsyta kan data läsas in, modelleras, bearbetas och levereras för snabbare analytiska insikter.

I den här snabb starten beskrivs stegen för att skapa en dedikerad SQL-pool i en Synapse-arbetsyta med hjälp av Synapse Studio.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Synapse-arbetsyta](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Navigera till arbets ytan Synapse

1. Gå till arbets ytan Synapse där den dedikerade SQL-poolen skapas genom att skriva tjänst namnet (eller resurs namnet direkt) i Sök fältet.

    ![Azure Portal Sök fältet med Synapse-arbetsytor som anges i.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. I listan över arbets ytor skriver du namnet (eller en del av namnet) på arbets ytan som ska öppnas. I det här exemplet använder vi en arbets yta med namnet **contosoanalytics**.

    ![Lista över Synapse-arbetsytor filtrerade för att visa de som innehåller namnet contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Starta Synapse Studio

1. Från arbets ytans översikt väljer du **webb-URL: en för arbets ytan** för att starta Synapse Studio.

    ![Översikt över Azure Portal Synapse-arbetsyta med webb adressen för arbets ytan markerad.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-dedicated-sql-pool-in-synapse-studio"></a>Skapa en dedikerad SQL-pool i Synapse Studio

1. På Start sidan för Synapse Studio navigerar du till **hanterings hubben** i det vänstra navigerings fönstret genom att välja ikonen **Hantera** .

    ![Synapse Studio-start sida med avsnittet hanterings hubb markerat.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. I hanterings hubben går du till avsnittet **SQL-pooler** för att se den aktuella listan över SQL-pooler som är tillgängliga i arbets ytan.

    ![Synapse Studio Management Hub med SQL-pooler har valts](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Välj **+ nytt** kommando så visas guiden Skapa ny SQL-pool. 

    ![Synapse Studio Management Hub-lista över SQL-pooler.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Ange följande information på fliken **grundläggande** :

    | Inställning | Föreslaget värde | Beskrivning |
    | :------ | :-------------- | :---------- |
    | **SQL-poolnamn** | contosoedw | Detta är det namn som den dedikerade SQL-poolen kommer att ha. |
    | **Prestanda nivå** | DW100c | Ange den minsta storleken för att minska kostnaderna för den här snabb starten |

    ![SQL-pooler skapa flöde – grunder-fliken.](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Observera att det finns särskilda begränsningar för namn som dedikerade SQL-pooler kan använda. Namn får inte innehålla specialtecken, måste bestå av högst 15 tecken, får inte innehålla reserverade ord och vara unika i arbets ytan.

4. På nästa flik, **ytterligare inställningar**, väljer du **ingen** för att etablera SQL-poolen utan data. Låt standard sorteringen vara markerad.

    Om du vill återställa din dedikerade SQL-pool från en återställnings punkt väljer du **återställnings punkt**. Mer information om hur du utför en återställning finns i [instruktion: återställa en befintlig dedikerad SQL-pool](backuprestore/restore-sql-pool.md)

    ![Fliken Skapa flöde för SQL-pool – ytterligare inställningar.](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Vi lägger inte till några taggar nu, så härnäst väljer du **Granska + skapa**.

1. På fliken **Granska + skapa** ser du till att informationen ser korrekt ut baserat på vad som angavs tidigare och tryck på **skapa**. 

    ![SQL-pool skapa flöde-granska inställningar fliken.](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. I det här läget startar resurs etablerings flödet.

1. När etableringen är klar visas en ny post för den nyligen skapade SQL-poolen när du navigerar tillbaka till arbets ytan.

    ![SQL-pool skapa flödes resurs etablering.](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. När den dedikerade SQL-poolen har skapats kommer den att vara tillgänglig i arbets ytan för att läsa in data, bearbeta strömmar, läsa från sjö osv.

## <a name="clean-up-dedicated-sql-pool-using-synapse-studio"></a>Rensa en dedikerad SQL-pool med Synapse Studio    

Följ stegen nedan för att ta bort den dedikerade SQL-poolen från arbets ytan med Synapse Studio.
> [!WARNING]
> Om du tar bort en dedikerad SQL-pool tas analys motorn bort från arbets ytan. Det går inte längre att ansluta till poolen, och alla frågor, pipeliner, skript som använder den här dedikerade SQL-poolen kommer inte längre att fungera.

Om du vill ta bort den dedikerade SQL-poolen gör du följande:

1. Gå till SQL-poolerna i hanterings navet i Synapse Studio.
1. Välj ellipsen i den dedikerade SQL-poolen som ska tas bort (i det här fallet **contosoedw**) för att Visa kommandona för den DEDIKERADe SQL-poolen:

    ![Lista över SQL-pooler med den nyligen skapade poolen vald.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Tryck på **ta bort**.
1. Bekräfta borttagningen och tryck på knappen **ta bort** .
1. När processen har slutförts visas inte längre den dedikerade SQL-poolen i resurserna för arbets ytan.

## <a name="next-steps"></a>Nästa steg
 
- Se [snabb start: skapa en Apache Spark Notebook](quickstart-apache-spark-notebook.md).
- Se [snabb start: skapa en dedikerad SQL-pool med hjälp av Azure Portal](quickstart-create-sql-pool-portal.md).
