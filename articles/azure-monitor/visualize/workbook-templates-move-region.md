---
title: Mallar för Azure Monitor-arbetsböcker – flytta regioner
description: Flytta en arbetsboksmall till en annan region
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: 9787118b1912a5697c02e116be99b1f396748937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100621538"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Flytta en mall för Azure-arbetsböcker till en annan region

Den här artikeln beskriver hur du flyttar resurser i Azure-arbetsböcker till en annan Azure-region. Du kan flytta dina resurser till en annan region av olika anledningar. Till exempel, för att dra nytta av en ny Azure-region, för att bara distribuera funktioner eller tjänster som är tillgängliga i vissa regioner, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven.

Det finns för närvarande inget Portal gränssnitt för att skapa mallar för arbets boks resurser, det enda nuvarande sättet att skapa dem är [via Azure Resource Manager mall (arm-mall) distributioner](../visualize/workbooks-automate.md). Därför är det enklaste sättet att flytta en mall att återanvända den tidigare ARM-mallen och uppdatera den så att den distribueras till den nya regionen.

## <a name="prerequisites"></a>Förutsättningar

* Se till att mallar för arbets böcker stöds i mål regionen.

## <a name="prepare"></a>Förbereda

* Identifiera den tidigare använda ARM-mallen för arbets boks mal len.

## <a name="move"></a>Flytta

1. Uppdatera den tidigare använda mallen för att referera till den nya regionen.

   > [!NOTE]
   > Du kan behöva använda ett nytt namn för arbets boks mal len för att undvika dubblettnamn.

2. Distribuera den uppdaterade mallen via ARM-mall-distribution för att skapa en ny arbetsboksmall i önskad region.

## <a name="verify"></a>Verifiera

Använd Azure-arbetsböckerna bläddra i gränssnittet för att hitta den nya distribuerade arbets boks mal len. Se till att platsen är mål platsen.

## <a name="clean-up"></a>Rensa

När din arbetsboksmall har skapats i den nya regionen tar du bort mallen för den ursprungliga arbets boken i föregående region.
1. Hitta arbets boks mal len i Azure-arbetsböcker bläddra i användar gränssnittet.
2. Välj den mall för arbets bok som ska tas bort.
3. Välj kommandot Ta bort.

Om du har bytt namn på din arbetsboksmall för att importera den till en ny region kan du byta namn på arbets boks mal len till det tidigare namnet när det ursprungliga objektet har tagits bort med hjälp av kommandot "rename" i vyn resurs mal len för Azure-arbetsböcker.

## <a name="next-steps"></a>Nästa steg

Behöver du flytta en arbets bok i stället för en mall? Se hur du [flyttar en Azure-arbetsbok till en annan region](./workbooks-move-region.md).

