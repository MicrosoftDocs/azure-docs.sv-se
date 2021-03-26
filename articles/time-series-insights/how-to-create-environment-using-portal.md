---
title: Konfigurera en Gen2-miljö med Azure Portal-Azure Time Series Insights Gen2 | Microsoft Docs
description: Lär dig hur du konfigurerar en miljö i Azure Time Series Insights Gen2 med Azure Portal.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: 09068d966df871d4b6804978a543db50bccbee37
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952855"
---
# <a name="create-an-azure-time-series-insights-gen2-environment-using-the-azure-portal"></a>Skapa en Azure Time Series Insights Gen2-miljö med hjälp av Azure Portal

I den här artikeln beskrivs hur du skapar en Azure Time Series Insights Gen2-miljö med hjälp av [Azure Portal](https://portal.azure.com/).

I självstudien om miljö etablering får du stegvisa anvisningar genom processen. Du får lära dig mer om att välja rätt tids serie-ID och Visa exempel från två JSON-nyttolaster.</br>

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWzk3P]

## <a name="overview"></a>Översikt

När du etablerar en Azure Time Series Insights Gen2-miljö skapar du följande Azure-resurser:

* En Azure Time Series Insights Gen2-miljö som följer pris modellen betala per användning
* Ett Azure Storage konto
* En valfri varm lagring för snabbare och obegränsade frågor

> [!TIP]
>
> * Lär dig [hur du planerar din miljö](./how-to-plan-your-environment.md).
> * Läs om hur du [lägger till en Event Hub-källa](./how-to-ingest-data-event-hub.md) eller hur du [lägger till en källa för IoT Hub](./how-to-ingest-data-iot-hub.md).

Du lär dig hur du:

1. Koppla varje Azure Time Series Insights gen 2-miljö till en händelse källa. Du kommer också att ange en egenskap för timestamp-ID och en unik konsument grupp för att säkerställa att miljön har åtkomst till lämpliga händelser.

1. När etableringen är klar kan du ändra dina åtkomst principer och andra miljöattribut så att de passar dina affärs behov.

   > [!NOTE]
   > Det första steget är valfritt när du konfigurerar en miljö. Om du hoppar över det här steget måste du koppla en händelse källa till miljön senare så att data kan börja flöda till din miljö och kan nås via frågan.

## <a name="create-the-environment"></a>Skapa miljön

Så här skapar du en Azure Time Series Insights gen 2-miljö:

1. Skapa en Azure Time Series Insights resurs under *Sakernas Internet* på [Azure Portal](https://portal.azure.com/).

1. Välj **Gen2 (L1)** som **nivå**. Ange ett miljö namn och välj den prenumerations grupp och resurs grupp som du vill använda. Välj sedan en plats som stöds för att vara värd för miljön.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration.png" alt-text="Skapa en Azure Time Series Insights-instans." lightbox="media/how-to-create-environment-using-portal/environment-configuration.png":::

1. Ange ett Time Series-ID.

   :::image type="content" source="media/how-to-create-environment-using-portal/environment-configuration-2.png" alt-text="Skapa en Azure Time Series Insights miljö konfiguration, fortsatte." lightbox="media/how-to-create-environment-using-portal/environment-configuration-2.png":::

   > [!NOTE]
   >
   > * Time Series-ID: t är Skift läges *känsligt* och *oföränderligt*. (Den kan inte ändras efter att den har angetts.)
   > * Time Series-ID: n kan vara upp till *tre* nycklar. Tänk på det som en primär nyckel i en databas som unikt representerar varje enhets sensor som skickar data till din miljö. Det kan vara en egenskap eller en kombination av upp till tre egenskaper.
   > * Läs mer om [hur du väljer ett Time Series-ID](./how-to-select-tsid.md)

1. Skapa ett Azure Storage konto genom att välja ett lagrings konto namn, en konto typ och ange ett alternativ för [replikering](../storage/common/redundancy-migration.md?tabs=portal) . Om du gör det skapas ett Azure Storage-konto automatiskt. Som standard skapas [generell användning v2](../storage/common/storage-account-overview.md) -konto. Kontot skapas i samma region som den Azure Time Series Insights Gen2-miljö som du har valt tidigare.
Du kan också ta med din egen lagring (BYOS) via en [Azure Resource Manager-mall](./time-series-insights-manage-resources-using-azure-resource-manager-template.md) när du skapar en ny Azure Time Series Gen2-miljö.

1. **(Valfritt)** Aktivera varmt lagrings utrymme för din miljö om du vill ha snabbare och obegränsade frågor över de senaste data i din miljö. Du kan också skapa eller ta bort ett varmt lager via alternativet **lagrings konfiguration** i det vänstra navigerings fönstret när du har skapat en Azure Time Series Insights Gen2-miljö.

1. **(Valfritt)** Du kan lägga till en händelse källa nu. Du kan också vänta tills instansen har etablerats.

   * Azure Time Series Insights stöder [azure IoT Hub](./how-to-ingest-data-iot-hub.md) och [Azure Event Hubs](./how-to-ingest-data-event-hub.md) som alternativ för händelse källan. Även om du bara kan lägga till en enskild händelse källa när du skapar miljön, kan du lägga till en annan händelse källa senare.

     Du kan välja en befintlig konsument grupp eller skapa en ny konsument grupp när du lägger till händelse källan. Se till att händelse källan använder en unik konsument grupp för din miljö för att läsa data i den.

   * Välj lämplig timestamp-egenskap. Som standard använder Azure Time Series Insights den meddelande-köade tiden för varje händelse källa.

     > [!TIP]
     > Den meddelande-som är i kö kanske inte är den bästa konfigurerade inställningen som används i scenarier med batch-händelser eller historiska data överförings scenarier. I sådana fall måste du kontrol lera att du har angett att du vill använda eller inte använder en timestamp-egenskap.

   :::image type="content" source="media/how-to-create-environment-using-portal/configure-event-source.png" alt-text="Fliken Konfiguration av händelse källa" lightbox="media/how-to-create-environment-using-portal/configure-event-source.png":::

1. Välj **Granska + skapa** för att bekräfta att din miljö har etablerats och kon figurer ATS på det sätt som du vill.

    :::image type="content" source="media/how-to-create-environment-using-portal/environment-confirmation.png" alt-text="Granska + fliken Skapa" lightbox="media/how-to-create-environment-using-portal/environment-confirmation.png":::

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Time Series Insights allmänt tillgängliga miljöer och Gen2-miljöer genom att läsa [Planera din miljö](./how-to-plan-your-environment.md).
* Lär dig mer om att strömma inmatnings [händelse källor](./concepts-streaming-ingestion-event-sources.md) för din Azure Time Series Insights Gen2-miljö.
* Läs mer om [hur du hanterar din miljö](./how-to-provision-manage.md).
