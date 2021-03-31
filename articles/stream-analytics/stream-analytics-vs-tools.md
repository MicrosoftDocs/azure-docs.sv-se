---
title: Visa Azure Stream Analytics jobb i Visual Studio
description: Lär dig att visa, starta och stoppa, testa anslutningar, kontrol lera resultat och exportera dina Azure Stream Analytics-jobb med Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 07/10/2018
ms.openlocfilehash: 4b568fa8d9e2fb8bd00acb27b0dc3f7c45c09c57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019133"
---
# <a name="use-visual-studio-to-view-azure-stream-analytics-jobs"></a>Använda Visual Studio för att visa Azure Stream Analytics-jobb

Azure Stream Analytics Tools för Visual Studio gör det enkelt för utvecklare att hantera sina Stream Analytics-jobb direkt från IDE. Med Azure Stream Analytics verktyg kan du:
- [Skapa nya jobb](stream-analytics-quick-create-vs.md)
- Starta, stoppa och [övervaka jobb](stream-analytics-monitor-jobs-use-vs.md)
- Kontrol lera jobb resultat
- Exportera befintliga jobb till ett projekt
- Testa anslutningar för indata och utdata
- [Kör frågor lokalt](stream-analytics-vs-tools-local-run.md)

Lär dig hur du [installerar Azure Stream Analytics verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md).

## <a name="explore-the-job-view"></a>Utforska vyn jobb

Du kan använda vyn jobb för att interagera med Azure Stream Analytics-jobb från Visual Studio.

### <a name="open-the-job-view"></a>Öppna vyn jobb

1. I **Server Explorer** väljer du **Stream Analytics jobb** och väljer sedan **Uppdatera**. Jobbet bör visas under **Stream Analytics jobb**.

    ![Stream Analytics Server Explorer-lista](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-list-jobs-01.png)

2. Expandera noden jobb och dubbelklicka på noden **jobb Visa** för att öppna en jobb visning.
    
   ![Expanderad jobb-nod](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-job-view-01.png)

### <a name="start-and-stop-jobs"></a>Starta och stoppa jobb

Azure Stream Analytics-jobb kan hanteras fullständigt från vyn jobb i Visual Studio. Använd kontrollerna för att starta, stoppa eller ta bort ett jobb.
    
   ![Stream Analytics jobb kontroller](./media/stream-analytics-vs-tools/azure-stream-analytics-job-view-controls.png)

## <a name="check-job-results"></a>Kontrol lera jobb resultat

Stream Analytics verktyg för Visual Studio stöder för hands versionen av utdata för Azure Data Lake Storage-och blob-lagring. Om du vill visa resultat dubbelklickar du bara på noden utdata i jobb diagrammet i **vyn jobb** och anger lämpliga autentiseringsuppgifter.

   ![Stream Analytics Job BLOB-utdata](./media/stream-analytics-vs-tools/stream-analytics-blob-preview.png)

## <a name="export-jobs-to-a-project"></a>Exportera jobb till ett projekt

Du kan exportera ett befintligt jobb till ett projekt på två sätt.

1. I **Server Explorer**, under noden Stream Analytics jobb, högerklickar du på noden jobb. Välj **Exportera till nytt Stream Analytics projekt**.
    
   ![Exportera jobb till projekt](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-01.png)
    
    Det genererade projektet visas i **Solution Explorer**.
    
   ![Solution Explorer](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-02.png)

2. I vyn jobb väljer du **generera projekt**.
    
   ![Generera projekt från jobb-vy](./media/stream-analytics-vs-tools/stream-analytics-tools-for-vs-export-job-03.png)

## <a name="test-connections"></a>Test anslutningar

Anslutningar för indata och utdata kan testas från **vyn jobb** genom att välja ett alternativ i list rutan **Testa anslutning** .

   ![Listruta för test anslutning](./media/stream-analytics-vs-tools/stream-analytics-test-connection-dropdown.png)

**Test anslutnings** resultaten visas i fönstret **utdata** .

   ![Testa anslutnings resultat](./media/stream-analytics-vs-tools/stream-analytics-test-connection-results.png)

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera Azure Stream Analytics jobb med Visual Studio](stream-analytics-monitor-jobs-use-vs.md)
* [Snabb start: skapa ett Stream Analytics jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Integrera och utveckla kontinuerligt med Stream Analytics-verktyg](stream-analytics-tools-for-visual-studio-cicd.md)
