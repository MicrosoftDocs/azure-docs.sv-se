---
title: Skapa en Azure Stream Analytics kluster snabb start
description: Lär dig hur du skapar ett Azure Stream Analytics-kluster.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: eb36af5b3f1e142405b24ade16516d0268b4ab2f
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016566"
---
# <a name="quickstart-create-a-dedicated-azure-stream-analytics-cluster-using-azure-portal"></a>Snabb start: skapa ett dedikerat Azure Stream Analytics-kluster med Azure Portal

Använd Azure Portal för att skapa ett Azure Stream Analytics-kluster. Ett [Stream Analytics kluster](cluster-overview.md) är en distribution med en enda klient som kan användas för komplexa och krävande direkt uppspelnings användnings fall. Du kan köra flera Stream Analytics jobb på ett Stream Analytics-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Slut för ande av [snabb start: skapa ett Stream Analytics jobb med hjälp av Azure Portal](stream-analytics-quick-create-portal.md).

## <a name="create-a-stream-analytics-cluster"></a>Skapa ett Stream Analytics kluster

I det här avsnittet skapar du en Stream Analytics kluster resurs.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Skapa en resurs**. Skriv och välj **Stream Analytics kluster** i sökrutan *Sök i Marketplace* . Välj **Lägg till**.

   :::image type="content" source="./media/create-cluster/search-result.png" alt-text="Stream Analytics kluster Sök resultat.":::

1. På sidan **skapa Stream Analytics kluster** anger du de grundläggande inställningarna för det nya klustret.

   |Inställning|Värde|Beskrivning |
   |---|---|---|
   |Prenumeration|Prenumerationens namn|Välj den Azure-prenumeration som du vill använda för Stream Analytics klustret. |
   |Resursgrupp|Namn på resursgrupp|Välj en resursgrupp eller välj **Skapa ny** och ange sedan ett unikt namn för den nya resursgruppen. |
   |Klusternamn|Ett unikt namn|Ange ett namn för att identifiera Stream Analytics klustret.|
   |Plats|Den region som är närmast dina data källor och mottagare|Välj en geografisk plats som ska vara värd för ditt Stream Analytics-kluster. Använd den plats som ligger närmast dina data källor och mottagare för analys med låg latens.|
   |Kapacitet för strömnings enhet|36 till 216 |Bestäm klustrets storlek genom att uppskatta hur många Stream Analytics-jobb du planerar att köra och den totala SUs-SUs som jobbet kräver. Du kan börja med 36 SUs och sedan skala upp eller ned efter behov.|

   ![Skapa kluster](./media/create-cluster/create-cluster.png)

1. Välj **Granska + skapa**. Du kan hoppa över avsnittet **taggar** .

1. Granska kluster inställningarna och välj sedan **skapa**. Skapande av kluster är en långvarig åtgärd och kan ta ungefär 60 minuter att slutföra. Vänta tills portalsidan visar meddelandet **Distributionen är klar**. Under tiden kan du skapa och utveckla [Stream Analytics jobb](stream-analytics-quick-create-portal.md#create-a-stream-analytics-job) som du vill köra på det här klustret om du inte redan gjort det.

1. Välj **gå till resurs** för att gå till sidan Stream Analytics kluster.

## <a name="delete-your-cluster"></a>Ta bort klustret

Du kan ta bort Stream Analytics klustret om du inte planerar att köra Stream Analytics jobb på det. Ta bort klustret genom att följa stegen på Azure Portal:

1. Gå till **Stream Analytics jobb** under **Inställningar** och stoppa alla jobb som körs.

1. Gå till **Översikt** över klustret. Välj **ta bort** och följ instruktionerna för att ta bort klustret.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure Stream Analytics-kluster. Gå vidare till nästa artikel och lär dig hur du kör ett Stream Analytics jobb på klustret:

> [!div class="nextstepaction"]
> [Hantera Stream Analytics jobb i ett Stream Analytics kluster](manage-jobs-cluster.md)
