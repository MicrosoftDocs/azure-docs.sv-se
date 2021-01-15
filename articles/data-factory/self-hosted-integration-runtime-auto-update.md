---
title: Lokalt installerad integrationskörning, automatisk uppdatering och avisering vid förfallodatum
description: Lär dig mer om den automatiska värdbaserade integrerings körningen automatisk uppdatering och förfallo avisering
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 8c51839e760dcca32bfc0d150c5e4d10767d95a4
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222708"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Lokalt installerad integrationskörning, automatisk uppdatering och avisering vid förfallodatum

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln beskriver hur du kan använda automatisk värd integrering för integration runtime till den senaste versionen och hur ADF hanterar versioner av integration runtime med egen värd.

## <a name="self-hosted-integration-runtime-auto-update"></a>Egen värd Integration Runtime automatisk uppdatering
När du installerar en lokal integrerings körning på en lokal dator eller en virtuell Azure-dator har du vanligt vis två alternativ för att hantera versionen av integration runtime med egen värd: automatisk uppdatering eller underhåll manuellt. Vanligt vis släpper ADF två nya versioner av egen värd integrerings körning varje månad, inklusive ny funktions version, fel korrigering eller förbättring. Vi rekommenderar därför att användare uppdaterar till den senaste versionen för att få den senaste funktionen och förbättringarna.

Det enklaste sättet är att aktivera automatisk uppdatering när du skapar eller redigerar integration runtime med egen värd. Sedan uppdateras den automatiskt till den senaste versionen. Du kan också schemalägga uppdateringen med den lämpligaste tids kort platsen som du vill.

![Aktivera automatisk uppdatering](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Du kan kontrol lera den senaste uppdateringen av datum/tid i din klient för integration runtime med egen värd.

![Skärm bild av att kontrol lera uppdaterings tiden](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> För att säkerställa stabiliteten för integration runtime med egen värd, men vi publicerar två versioner, uppdaterar vi bara den automatiskt en gång varje månad. Ibland kan du ibland se att den automatiskt uppdaterade versionen är den tidigare versionen av den senaste versionen. Om du vill hämta den senaste versionen går du till [Download Center](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Meddelande om att din egen värd Integration Runtime upphör att gälla
Om du manuellt vill styra vilken version av integration runtime med egen värd, kan du inaktivera inställningen för automatisk uppdatering och installera den manuellt. Varje version av integration runtime med egen värd upphör att gälla om ett år. Meddelandet som upphör visas i ADF-portalen och klienten för lokal integration runtime-klient **90 dagar** före förfallo datum.

## <a name="next-steps"></a>Nästa steg

- Granska [integration runtime-koncept i Azure Data Factory](./concepts-integration-runtime.md).

- Lär dig hur du [skapar en integration runtime med egen värd i Azure Portal](./create-self-hosted-integration-runtime.md).
