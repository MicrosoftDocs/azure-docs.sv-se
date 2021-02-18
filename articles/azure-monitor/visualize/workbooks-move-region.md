---
title: Azure Monitor arbets böcker – flytta regioner
description: Så här flyttar du en arbets bok till en annan region
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: d58d9ab48fa16fb5258f097ed4567e539e21c72c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100622841"
---
# <a name="move-an-azure-workbook-to-another-region"></a>Flytta en Azure-arbetsbok till en annan region

Den här artikeln beskriver hur du flyttar resurser i Azure-arbetsböcker till en annan Azure-region. Du kan flytta dina resurser till en annan region av olika anledningar. Till exempel, för att dra nytta av en ny Azure-region, för att bara distribuera funktioner eller tjänster som är tillgängliga i vissa regioner, för att uppfylla interna principer och styrnings krav, eller som svar på kapacitets planerings kraven.

## <a name="prerequisites"></a>Förutsättningar

* Se till att arbets böcker stöds i mål regionen.

* Dessa anvisningar gäller både för delade arbets böcker ( `microsoft.insights/workbooks` ) och privata arbets böcker ( `microsoft.insights/myworkbooks` ) som sparas i Azure Monitor och på de flesta resurs typer.

  Men för arbets böcker som är specifikt länkade till Application Insights resurs typ lagras dessa arbets böcker i Azure-regionen där Application Insights resursen sparas.

  Arbets böckerna kan inte flyttas individuellt till en annan region.

## <a name="move"></a>Flytta

Det enklaste sättet att flytta en Azure-arbetsbok är att använda "Spara som" i arbets bokens verktyg i användar gränssnittet för portalen:

1. Öppna mål arbets boken i arbets bokens visnings program.
2. Använd knappen Redigera för att ange redigerings läge.
3. Använd knappen "Spara som" i verktygsfältet.
4. I formuläret Spara väljer du ett namn och önskad region för arbets boken. Se till att de andra fälten för prenumeration, resurs grupp och delning är lämpliga.

   > [!NOTE]
   > Du kan behöva använda ett nytt namn för arbets boken för att undvika dubblettnamn.

5. Spara. 

## <a name="verify"></a>Verifiera

Använd Azure-arbetsböcker bläddra i gränssnittet för att hitta den nya arbets boken. Se till att platsen är mål platsen.

## <a name="clean-up"></a>Rensa

När din arbets bok har skapats i den nya regionen tar du bort den ursprungliga arbets boken i föregående region.
1. Öppna den ursprungliga arbets boken i arbets boks läsaren.
2. Använd knappen Redigera för att ange redigerings läge.
3. Välj Ta bort arbets bok i list rutan Redigera verktyg (Penn ikonen).

Om du har bytt namn på din arbets bok för att importera den till en ny region, kan du byta namn på arbets boken till föregående namn efter att den ursprungliga arbets boken har tagits bort med hjälp av verktygsfältet Redigera i verktygsfältet Redigera-läge i list rutan "Byt namn".

## <a name="next-steps"></a>Nästa steg

Behöver du flytta en arbetsboksmall i stället för en arbets bok? Se hur du [flyttar en mall för Azure-arbetsböcker till en annan region](./workbook-templates-move-region.md).

Se hur du [distribuerar arbets böcker och mallar för arbets böcker](../visualize/workbooks-automate.md) via arm-mallar.
