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
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365475"
---
# <a name="integrate-with-pipelines"></a>Integrera med pipelines

I den här självstudien lär du dig att integrera pipelines och aktiviteter med hjälp av Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Skapa en pipeline och lägga till en notebook-aktivitet



1. I Synapse Studio du till **navet Integrera.**
1. Välj **+**  >  **Pipeline för** att skapa en ny pipeline. Klicka på det nya pipelineobjektet för att öppna Pipeline-designern.
1. Under **Aktiviteter** expanderar du **Mappen Synapse** och drar ett **Notebook-objekt** till designern.
1. Välj fliken **Inställningar** i notebook-aktivitetsegenskaperna. Använd listrutan för att välja valfri notebook-dator från din aktuella Synapse-arbetsyta.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Schemalägg att pipelinen körs varje timme

1. I pipelinen väljer du Lägg **till**  >  **utlösare Ny/redigera**.
1. I **Välj utlösare** väljer **du** Ny och anger **Upprepning till** "var 1 timme".
1. Välj **OK**. 
1. Välj **Publicera alla**. 


## <a name="monitor-pipeline-execution"></a>Övervaka pipelinekörning

1. När pipelinen har publicerats väljer du Lägg till utlösare nu för att köra pipelinen direkt, utan att vänta **på**  >  **nästa timme.**
1. I Synapse Studio du till **hubben Övervaka.**
1. Välj **Pipelinekörningar** för att övervaka pipelinekörningsförloppet.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visualisera data med Power BI](get-started-visualize-power-bi.md)
