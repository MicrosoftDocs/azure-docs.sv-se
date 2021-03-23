---
title: Skala ett program i Azure våren-molnet | Microsoft Docs
description: Lär dig hur du skalar ett program med Azure våren Cloud i Azure Portal
ms.service: spring-cloud
ms.topic: how-to
ms.author: brendm
author: bmitchell287
ms.date: 10/06/2019
ms.custom: devx-track-java
ms.openlocfilehash: 5632f9a6126615255306cc89425bd08a9ffa9753
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878453"
---
# <a name="scale-an-application-in-azure-spring-cloud"></a>Skala ett program i Azure våren Cloud

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

Den här dokumentationen visar hur du skalar ett mikrotjänstprogram med hjälp av moln instrument panelen för Azure våren i Azure Portal.

Skala ditt program uppåt och nedåt genom att ändra antalet virtuella processorer (virtuella processorer) och mängden minne. Skala ditt program i och ut genom att ändra antalet program instanser.

När du är klar vet du hur du ska göra snabba manuella ändringar i varje program i din tjänst. Skalningen börjar gälla om några sekunder och kräver inga kod ändringar eller omdistributioner.

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa de här procedurerna behöver du:

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 
* En distribuerad Azure våren Cloud Service-instans.  Följ [snabb starten när du distribuerar en app via Azure CLI](spring-cloud-quickstart.md) för att komma igång.
* Minst ett program som redan har skapats i din tjänst instans.

## <a name="navigate-to-the-scale-page-in-the-azure-portal"></a>Gå till sidan skala i Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Gå till **översikts** sidan för Azure våren-molnet.

1. Välj den resurs grupp som innehåller din tjänst.

1. Välj fliken **appar** under **Inställningar** på menyn på vänster sida av sidan.

1. Välj det program som du vill skala. I det här exemplet väljer du appen med namnet **Account-service**. Du bör sedan se programmets **översikts** sida.

1. Gå till fliken **Scale (skala** ) under **Inställningar** på menyn på vänster sida av sidan. Du bör se alternativ för skalning av de attribut som visas i följande avsnitt.

## <a name="scale-your-application"></a>Skala ditt program

Tänk på följande när du ändrar skalnings attributen:

* **Processorer**: det maximala antalet processorer per program instans är fyra. Det totala antalet processorer för ett program är det värde som anges här multiplicerat med antalet program instanser.

* **Minne/GB**: den maximala mängden minne per program instans är 8 GB. Den totala mängden minne för ett program är det värde som anges här multiplicerat med antalet program instanser.

* **Antal instanser av app**: på standard-nivån kan du skala ut till maximalt 20 instanser. Det här värdet ändrar antalet separata instanser av mikrotjänst programmet som körs.

Se till att välja **Spara** för att tillämpa skalnings inställningarna.

![Skalnings tjänsten i Azure Portal](media/spring-cloud-tutorial-scale-manual/scale-up-out.png)

Efter några sekunder visas de ändringar du gjort på sidan **Översikt** med mer information som är tillgänglig på fliken **program instanser** . Skalning kräver inte kod ändringar eller omdistribution.

## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån
Om du är på Basic-nivån och är begränsad av en eller flera av dessa [gränser](spring-cloud-quotas.md), kan du uppgradera till standard nivån. Om du vill göra detta går du till menyn pris nivå genom att först välja kolumnen standard nivå och klicka på knappen **Uppgradera** .

## <a name="next-steps"></a>Nästa steg

I det här exemplet förklaras hur du skalar ett Azure våren Cloud-program manuellt. Information om hur du övervakar ett program genom att konfigurera aviseringar finns i [Konfigurera autoskalning](spring-cloud-tutorial-setup-autoscale.md).

> [!div class="nextstepaction"]
> [Lär dig hur du konfigurerar aviseringar](spring-cloud-tutorial-alerts-action-groups.md)
