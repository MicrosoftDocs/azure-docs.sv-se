---
title: Azure Monitor Visa designer till arbets böcker konverterings Sammanfattning och åtkomst
description: Behörigheter som krävs för att komma åt arbets böcker vid över gången från vyer i Azure Monitor.
author: austonli
ms.author: aul
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 42cb8db0f67d3a01b2e8443e6cb7c47f0fc44c4b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043398"
---
# <a name="view-designer-to-workbooks-conversion-summary-and-access"></a>Visa designer till arbets böcker konverterings Sammanfattning och åtkomst
[View Designer](view-designer.md) är en funktion i Azure Monitor som gör att du kan skapa anpassade vyer som hjälper dig att visualisera data i arbets ytan Log Analytics, med diagram, listor och tids linjer. De fasas ut och ersätts med arbets böcker, vilket ger ytterligare funktioner. Den här artikeln beskriver hur du kan skapa en översikts Sammanfattning och behörigheter som krävs för att komma åt arbets böcker.

## <a name="creating-your-workspace-summary-from-azure-dashboard"></a>Skapa din arbets ytans Sammanfattning från Azure Dashboard
Visa designer-användare kan vara bekant med en översikts panel för att representera en uppsättning vyer. Om du vill ha en visuell översikt som Sammanfattning av arbets ytan i vyn designer, innehåller arbets böcker fästa steg som kan fästas på [Azure Portal instrument panelen](../../azure-portal/azure-portal-dashboards.md). Precis som översikts panelerna i sammanfattningen av arbets ytan länkas fasta arbets boks objekt direkt till vyn arbets bok.

Du kan dra nytta av de hög anpassnings funktioner som finns på Azure-instrumentpaneler, vilket möjliggör automatisk uppdatering, flyttning, storleks ändring och ytterligare filtrering för dina fästa objekt och visualiseringar. 

![Skärm bild som visar en anpassad Azure-instrumentpanel med titeln arbets yta.](media/view-designer-conversion-access/dashboard.png)

Skapa en ny Azure-instrumentpanel eller Välj en befintlig instrument panel för att börja fästa arbets boks objekt.

För att fästa enskilda objekt måste du aktivera PIN-ikonen för ditt specifika steg. Det gör du genom att välja motsvarande **redigerings** knapp för ditt steg och sedan välja kugg hjuls ikonen för att öppna **Avancerade inställningar**. Markera alternativet om du **alltid vill visa PIN-ikonen i det här steget** och en PIN-ikon visas i det övre högra hörnet av ditt steg. Med den här PIN-koden kan du fästa vissa visualiseringar på instrument panelen, t. ex. översikts paneler.

![Fäst steg](media/view-designer-conversion-access/pin-step.png)


Du kanske också vill fästa flera visualiseringar från arbets boken eller hela arbets bokens innehåll på en instrument panel. Om du vill fästa hela arbets boken väljer du **Redigera** i det övre verktygsfältet för att växla **redigerings läge**. En PIN-ikon visas, så att du kan fästa hela arbets boks objektet eller alla enskilda steg och visualiseringar i arbets boken.

![Fäst alla](media/view-designer-conversion-access/pin-all.png)



## <a name="sharing-and-viewing-permissions"></a>Dela och Visa behörigheter 
Arbets böcker har fördelen att det antingen är ett privat eller delat dokument. Som standard sparas sparade arbets böcker under **Mina rapporter**, vilket innebär att endast skaparen kan visa den här arbets boken.

Du kan dela dina arbets böcker genom att välja **delnings** ikonen i det övre verktygsfältet i **redigerings läge**. Du uppmanas att flytta din arbets bok till **delade rapporter**. då skapas en länk som ger direkt åtkomst till arbets boken.

För att en användare ska kunna visa en delad arbets bok måste de ha åtkomst till både prenumerationen och resurs gruppen som arbets boken sparas under.

![Prenumerations-baserad åtkomst](media/view-designer-conversion-access/subscription-access.png)

## <a name="next-steps"></a>Nästa steg

- [Vanliga åtgärder](view-designer-conversion-tasks.md)