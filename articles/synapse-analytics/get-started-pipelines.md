---
title: 'Självstudie: Kom igång med integrering med pipelines'
description: I den här självstudien lär du dig att integrera pipelines och aktiviteter med hjälp av Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518260"
---
# <a name="integrate-with-pipelines"></a>Integrera med pipelines

I den här självstudien lär du dig att integrera pipelines och aktiviteter med hjälp av Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Skapa en pipeline och lägga till en notebook-aktivitet

1. I Synapse Studio du till **navet Integrera.**
1. Välj **+**  >  **Pipeline för** att skapa en ny pipeline. Klicka på det nya pipelineobjektet för att öppna Pipeline-designern.
1. Under **Aktiviteter** expanderar du **Mappen Synapse** och drar ett **Notebook-objekt** till designern.
1. Välj fliken **Inställningar** i notebook-aktivitetsegenskaperna. Använd listrutan för att välja en notebook-post från din aktuella Synapse-arbetsyta.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Schemalägg att pipelinen körs varje timme

1. I pipelinen väljer du Lägg **till**  >  **utlösare Ny/redigera**.
1. I **Välj utlösare** väljer **du** Ny och anger **Upprepning till** "var 1 timme".
1. Välj **OK**. 
1. Välj **Publicera alla**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Tvinga en pipeline att köras omedelbart

När pipelinen har publicerats kanske du vill köra den direkt utan att vänta på att en timme ska passera.

1. Öppna pipelinen.
1. Klicka på **Lägg till**  >  **utlösare nu**.

## <a name="monitor-pipeline-execution"></a>Övervaka pipelinekörning

1. Gå till **övervakningshubben.**
1. Välj **Pipelinekörningar** för att övervaka pipelinekörningsförloppet.
1. I den här vyn kan du växla mellan tabelldiagram **och** visa ett **grafiskt Gantt-diagram.** 
1. Klicka på ett pipelinenamn för att se status för aktiviteter i pipelinen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visualisera data med Power BI](get-started-visualize-power-bi.md)
