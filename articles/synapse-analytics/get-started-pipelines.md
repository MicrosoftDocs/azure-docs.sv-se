---
title: 'Självstudie: kom igång med att integrera med pipelines'
description: I den här självstudien får du lära dig hur du integrerar pipelines och aktiviteter med Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219410"
---
# <a name="integrate-with-pipelines"></a>Integrera med pipelines

I den här självstudien får du lära dig hur du integrerar pipelines och aktiviteter med Synapse Studio. 

## <a name="overview"></a>Översikt

Du kan integrera en mängd olika uppgifter i Azure Synapse.

1. Gå till **integrerings** hubben i Synapse Studio.
1. Välj **+**  >  **pipeline** för att skapa en ny pipeline. Öppna pipeline-designern genom att klicka på det nya pipeline-objektet.
1. Under **aktiviteter** expanderar du mappen **Synapse** och drar ett **anteckningsbok** -objekt till designern.
1. Välj fliken **Inställningar** i egenskaperna för notebook-aktiviteten. Använd List rutan för att välja en antecknings bok från din aktuella Synapse-arbetsyta. 
1. I pipelinen väljer du **Lägg till utlösare**  >  **ny/redigera**.
1. I **Välj utlösare** väljer du **ny** och ställer in **upprepningen** på "var 1 timme".
1. Välj **OK**. 
1. Välj **Publicera alla**. 


## <a name="monitor-pipeline"></a>Övervaka pipeline

1. När pipelinen har publicerats för att få pipelinen att köras omedelbart, utan att vänta på nästa timma, väljer du **Lägg till utlösare för Utlös**  >  **nu**.
1. I Synapse Studio går du till **Monitor** Hub och väljer pipeline- **körningar** för att övervaka förlopp för pipelining.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visualisera data med Power BI](get-started-visualize-power-bi.md)
