---
title: Distribuera en uppdatering med enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Distribuera en uppdatering med enhets uppdatering för Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101680026"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Distribuera en uppdatering med enhets uppdatering för IoT Hub

Lär dig hur du distribuerar en uppdatering till en IoT-enhet med hjälp av enhets uppdatering för IoT Hub.

## <a name="prerequisites"></a>Förutsättningar

* [Åtkomst till en IoT Hub med enhets uppdatering för IoT Hub aktive rad](create-device-update-account.md). Vi rekommenderar att du använder en S1-nivå (standard) eller högre för IoT Hub. 
* [Minst en uppdatering har importer ATS för den etablerade enheten.](import-update.md) 
* En IoT-enhet (eller Simulator) etablerad för enhets uppdatering i IoT Hub.
* [En tagg har tilldelats till den IoT-enhet som du försöker uppdatera. Enheten ingår i minst en uppdaterings grupp.](create-update-group.md)
* Webbläsare som stöds:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Distribuera en uppdatering

1. Gå till [Azure Portal](https://portal.azure.com)

2. Gå till bladet enhets uppdatering i din IoT Hub.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="IoT Hub" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Välj fliken grupper överst på sidan. [Läs mer](device-update-groups.md) om enhets grupper. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Fliken grupper" lightbox="media/deploy-update/updated-view.png":::

4. Visa listan över uppdatera efterlevnad för diagrammet och gruppen grupper. Du bör se en ny uppdatering tillgänglig för enhets gruppen med en länk till uppdateringen under väntande uppdateringar (du kan behöva uppdatera en gång). [Läs mer om att uppdatera efterlevnad.](device-update-compliance.md) 

5. Välj den tillgängliga uppdateringen.

6. Bekräfta att rätt grupp har valts som mål grupp. Schemalägg distributionen och välj sedan distribuera uppdatering.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Välj uppdatera" lightbox="media/deploy-update/select-update.png":::

7. Visa diagrammet efterlevnad. Nu bör du se att uppdateringen pågår. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Uppdatering pågår" lightbox="media/deploy-update/update-in-progress.png":::

8. När enheten har uppdaterats bör du se att ditt Compliance-diagram och distributions information uppdateras för att avspegla samma. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Uppdateringen är klar" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Övervaka en uppdaterings distribution

1. Välj fliken distributioner högst upp på sidan.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Fliken distributioner" lightbox="media/deploy-update/deployments-tab.png":::

2. Välj den distribution du skapade för att Visa distributions informationen.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Distributionsinformation" lightbox="media/deploy-update/deployment-details.png":::

3. Välj Uppdatera för att visa den senaste statusinformationen. Fortsätt med den här processen tills statusen har ändrats till slutfört.


## <a name="retry-an-update-deployment"></a>Försök igen med en uppdaterings distribution

Om distributionen Miss lyckas av någon anledning kan du försöka att distribuera igen för misslyckade enheter. 

1. Gå till fliken distributioner och välj den distribution som har misslyckats. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Distributionsinformation" lightbox="media/deploy-update/deployment-details.png":::

2. Klicka på enhets statusen "misslyckades" i fönstret detaljerad distributions information.

3. Klicka på "försök att göra om misslyckade enheter" och bekräfta bekräftelse meddelandet. 

## <a name="next-steps"></a>Nästa steg

[Felsök vanliga problem](troubleshoot-device-update.md)
