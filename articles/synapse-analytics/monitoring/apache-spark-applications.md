---
title: Övervaka Apache Spark program med Synapse Studio
description: Använd Synapse Studio för att övervaka dina Apache Spark-program.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584532"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Använd Synapse Studio för att övervaka dina Apache Spark-program

Med Azure Synapse Analytics kan du använda Apache Spark för att köra antecknings böcker, jobb och andra typer av program på dina Apache Spark-pooler på din arbets yta.

I den här artikeln beskrivs hur du övervakar Apache Spark program, så att du kan hålla koll på den senaste statusen, problemen och förloppet.

Den här självstudien omfattar följande uppgifter:

* Övervakare som kör Apache Spark program
* Visa slutförda Apache Spark program
* Visa avbrutna Apache Spark program
* Det gick inte att felsöka Apache Spark programmet

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar med den här självstudien måste du uppfylla följande krav:

- En Synapse Studio-arbetsyta. Instruktioner finns i [skapa en Synapse Studio-arbetsyta](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- En Apache Spark pool.

## <a name="view-apache-spark-applications"></a>Visa Apache Spark program 
Du kan visa alla Apache Spark program från **övervaka**  ->  **Apache Spark program**.
   ![Apache Spark-program](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Visa slutförda Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de slutförda Apache Spark-programmen väljer du Apache Spark programmet och visar informationen.

  ![Välj slutfört jobb](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Kontrol lera **slutförda aktiviteter**, **status** och **total varaktighet**.

2. Uppdatera logg fråga.

3. Öppna Apache Spark historik Server länk genom att klicka på **Spark historik Server**.

4. Kontrol lera **sammanfattnings** informationen.

5. Kontrol lera **loggarna**. Du kan välja olika typer av loggar i list rutan och du kan hämta logg informationen genom att klicka på **Hämta loggar** och markera kryss rutan för **filter fel och varningar** för att filtrera felen och varningarna som du behöver.

6. Du kan se en översikt över jobbet i diagrammet genererat jobb. Som standard visar diagrammet alla jobb. Du kan filtrera den här vyn efter **jobb-ID**.

7. Som standard är visnings **förloppet** markerat. Du kan kontrol lera data flödet genom att välja **status** för Läs-och / **Skriv** /  / **varaktighet** i list rutan **Visa** .

8. Om du vill spela upp jobbet klickar du på knappen **uppspelning** . Du kan stoppa genom att klicka på **stopp** -knappen när som helst.

9. Använd mus rullning eller rullnings list för att zooma in och zooma ut grafen. du kan också välja **Zooma så** att det passar på skärmen.

10. I noden jobb diagram visas följande information om varje steg:

    * Identitet.

    * Namn eller beskrivning.

    * Totalt aktivitets nummer.

    * Lästa data: summan av storleken på indata och den blandade Läs storleken.

    * Data skrivning: summan av storlek och blandnings storlek för utdata.

    * Körnings tid: tiden mellan start tiden för det första försöket och slut för ande tiden för det senaste försöket.

    * Radantal: summan av inmatnings poster, utgående poster, blandar Läs poster och blanda Skriv poster.

    * Pågår.

     ![Visa slutfört jobb](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Klicka på **Visa information** i diagrammet så visas informationen för scenen.

    ![information för steg](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Övervakare som kör Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de Apache Spark-program som körs väljer du sändnings Apache Spark programmet och visar informationen. Om Apache Spark programmet fortfarande körs kan du övervaka förloppet.

   ![Välj pågående jobb](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Kontrol lera **slutförda aktiviteter**, **status** och **total varaktighet**.

2. **Avbryt** Apache Spark programmet.

3. **Uppdatera** Logg fråga.

4. Klicka på **Spark UI** knappen för att gå till Spark-jobbet.

5. Visa grafen. Du kan se en översikt över jobbet i diagrammet genererat jobb. Se steg 6, 7, 8, 9, 10 av [vyn har slutförts Apache Spark programmet](#view-completed-apache-spark-application).

6. Kontrol lera **sammanfattnings** informationen.

7. Kontrol lera diagnostiken på fliken **diagnostik** .

8. Kontrol lera **loggarna** på den här fliken. Du kan välja olika typer av loggar i list rutan och du kan hämta logg informationen genom att klicka på **Hämta loggar** och markera kryss rutan för **filter fel och varningar** för att filtrera felen och varningarna som du behöver.

    ![Visa jobb som körs](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Visa avbrutna Apache Spark program

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om de avbrutna Apache Spark-programmen väljer du Apache Spark programmet och visar informationen.

 ![Välj avbrutet jobb](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Kontrol lera **slutförda aktiviteter**, **status** och **total varaktighet**.

2. Uppdatera logg frågan.

3. Öppna Apache historik Server länk genom att klicka på **Spark historik Server**.

4. Visa grafen. Du kan se en översikt över jobbet i diagrammet genererat jobb. Se steg 6, 7, 8, 9, 10 av [vyn har slutförts Apache Spark programmet](#view-completed-apache-spark-application).

5. Kontrol lera **sammanfattnings** informationen.

6. Kontrol lera **loggarna**. Du kan välja olika typer av loggar i list rutan och du kan hämta logg informationen genom att klicka på **Hämta** loggar och markera kryss rutan för **filter fel och varningar** för att filtrera felen och varningarna som du behöver.

7. Klicka på **Visa information** i diagrammet så visas informationen för scenen.

   ![Visa avbrutna jobb](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Det gick inte att felsöka Apache Spark programmet

Öppna **övervakaren** och välj **Apache Spark program**. Om du vill visa information om misslyckade Apache Spark-program väljer du Apache Spark programmet och visar informationen.

![Välj misslyckat jobb](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Kontrol lera **slutförda aktiviteter**, **status** och **total varaktighet**.

2. Uppdatera logg fråga.

3. Öppna Apache Spark historik Server länk genom att klicka på **Spark historik Server**.

4. Visa grafen. Du kan se en översikt över jobbet i diagrammet genererat jobb. Se steg 6, 7, 8, 9, 10 av [vyn har slutförts Apache Spark program](#view-completed-apache-spark-application)

5. Kontrol lera **sammanfattnings** informationen.

6. Kontrol lera fel informationen.

   ![jobb information misslyckades](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Jämför Apache Spark program
Klicka på knappen **Jämför program** och välj ett program för att jämföra prestanda. användarna kan intuitivt se skillnaden mellan de två programmen.

![Jämför program](./media/how-to-monitor-spark-applications/compare-applications.png)

1. Använd musen för att hovra ett program och sedan visas ikonen **Jämför program** .

2. Klicka på ikonen **Jämför program** och popup-sidan jämför program visas.

3. Klicka på knappen **Välj program** för att öppna sidan **Välj jämförelse program** .

4. När du väljer jämförelse programmet måste användarna antingen ange programmets URL eller välja från listan återkommande. Klicka sedan på knappen **OK** . 

   ![Välj jämförelse program](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. Jämförelse resultatet kommer att visas på sidan jämför program.

   ![jämförelse resultat](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>Nästa steg

Mer information om övervakning av pipeline-körningar finns i artikeln [övervaka pipeline som körs med hjälp av Synapse Studio](how-to-monitor-pipeline-runs.md) .
