---
title: Konfigurera Azure IoT Hub för att distribuera över Air-uppdateringar
description: Lär dig hur du konfigurerar Azure IoT Hub för att distribuera uppdateringar via Air till Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101663816"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Konfigurera Azure IoT Hub för att distribuera över Air-uppdateringar till din Azure percept DK
Håll dina Azure percept DK-säkra och uppdaterade med hjälp av över-Air-uppdateringar. I några enkla steg kan du konfigurera din Azure-miljö med enhets uppdatering för IoT Hub och distribuera de senaste uppdateringarna till din Azure percept DK.

## <a name="create-a-device-update-account"></a>Skapa ett konto för enhets uppdatering

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med Azure-kontot som du använder med Azure-percept 

1. I fönstret Sök högst upp på sidan börjar du skriva "enhets uppdatering för IoT Hub"

1. Välj **enhets uppdatering för IoT Hub** som det visas i fönstret Sök.

1. Klicka på knappen **+ Lägg till** längst upp till vänster på sidan.

1. Välj den Azure-prenumeration och resurs grupp som är kopplad till din Azure percept-enhet (detta är den plats där IoT Hub från onboarding finns).

1. Ange ett namn och en plats för ditt enhets uppdaterings konto

1. Granska informationen och välj sedan **Granska + skapa**.
 
1. När distributionen är klar klickar **du på gå till resurs**.
 
## <a name="create-a-device-update-instance"></a>Skapa en enhets uppdaterings instans
Skapa nu en instans i enhets uppdateringen för IoT Hub konto.

1. I enhets uppdateringen för IoT Hub resurs klickar du på **instanser** under **instans hantering**.
 
1. Klicka på **+ skapa**, ange ett instans namn och välj den IoT Hub som är kopplad till din Azure percept-enhet (d.v.s. skapad under onboarding-upplevelsen). Det kan ta några minuter att slutföra.
 
1. Klicka på **Skapa**

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub

1. På sidan instans hanterings **instanser** väntar du tills enhets uppdaterings instansen har flyttats till ett **lyckat** tillstånd. Klicka på **uppdaterings** ikonen bredvid **ta bort** om du vill uppdatera statusen.
 
1. Välj den instans som har skapats för dig och klicka sedan på **konfigurera IoT Hub**. I det vänstra fönstret väljer **du jag accepterar ändringarna** och klickar på **Uppdatera**.
 
1. Vänta tills processen har slutförts.
 
## <a name="configure-access-control-roles"></a>Konfigurera åtkomst kontroll roller
Det sista steget gör det möjligt för dig att bevilja behörigheter för användare att publicera och distribuera uppdateringar.

1. I enhets uppdateringen för IoT Hub resurs klickar du på **åtkomst kontroll (IAM)**
 
2. Klicka på **+ Lägg till** och välj sedan **Lägg till roll tilldelning**
 
3. För **roll** väljer du **enhets uppdaterings administratör**. För **tilldela åtkomst till** Välj **användare, grupp eller tjänst princip**. För **väljer** du Välj ditt konto eller kontot för den person som ska distribuera uppdateringar. Klicka sedan på **Spara**. 

    > [!TIP]
    > Om du vill ge fler personer i din organisations åtkomst kan du upprepa det här steget och göra var och en av dessa användare till en **enhets uppdaterings administratör**.

## <a name="next-steps"></a>Nästa steg

Du har nu ställt in och kan [Uppdatera Azure percept dev-paketet över-Air](./how-to-update-over-the-air.md) med hjälp av enhets uppdatering för IoT Hub. Gå till Azure-IoT Hub som du använder för din Azure percept-enhet.