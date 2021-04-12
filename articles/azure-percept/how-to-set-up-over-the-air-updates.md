---
title: Konfigurera Azure IoT Hub för att distribuera uppdateringar som rör vid luften
description: Lär dig hur du konfigurerar Azure-IoT Hub för att distribuera uppdateringar över-Air till Azure percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064128"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Konfigurera Azure IoT Hub för att distribuera över Air-uppdateringar till din Azure percept DK

Håll dina Azure percept DK-säkra och uppdaterade med hjälp av över-Air-uppdateringar. I några enkla steg kan du konfigurera din Azure-miljö med enhets uppdatering för IoT Hub och distribuera de senaste uppdateringarna till din Azure percept DK.

## <a name="prerequisites"></a>Förutsättningar

- Azure percept DK (devkit)
- [Azure-prenumeration](https://azure.microsoft.com/free/)
- [Installations upplevelse för Azure PERCEPT DK](./quickstart-percept-dk-set-up.md): du har anslutit ditt dev-paket till ett Wi-Fi nätverk, skapat ett IoT Hub och anslutit ditt dev-paket till IoT Hub

## <a name="create-a-device-update-account"></a>Skapa ett konto för enhets uppdatering

1. Gå till [Azure Portal](https://portal.azure.com) och logga in med Azure-kontot som du använder med Azure-percept.

1. I Sök fältet högst upp på sidan, anger du **enhets uppdatering för IoT Hub**.

1. Välj **enhets uppdatering för IoT Hub** när den visas i Sök fältet.

1. Klicka på knappen **+ Lägg till** i den övre vänstra delen av sidan.

1. Välj den **Azure-prenumeration** och **resurs grupp** som är kopplad till din Azure percept-enhet och dess IoT Hub.

1. Ange ett **namn** och en **plats** för ditt enhets uppdaterings konto.

1. Granska informationen och välj **Granska + skapa**.

1. När distributionen är klar klickar **du på gå till resurs**.

## <a name="create-a-device-update-instance"></a>Skapa en enhets uppdaterings instans

1. I enhets uppdateringen för IoT Hub resurs klickar du på **instanser** under **instans hantering**.

1. Klicka på **+ skapa**, ange ett instans namn och välj den IoT Hub som är kopplad till din Azure percept-enhet. Det kan ta några minuter att slutföra.

1. Klicka på **Skapa**.

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub

1. På sidan instans hanterings **instanser** väntar du tills enhets uppdaterings instansen har flyttats till ett **lyckat** tillstånd. Klicka på ikonen **Uppdatera** för att uppdatera statusen.

1. Välj den instans som har skapats för dig och klicka på **konfigurera IoT Hub**. I det vänstra fönstret väljer **du jag accepterar ändringarna** och klickar på **Uppdatera**.

1. Vänta tills processen har slutförts.

## <a name="configure-access-control-roles"></a>Konfigurera åtkomst kontroll roller

Det sista steget gör det möjligt för dig att bevilja behörigheter för användare att publicera och distribuera uppdateringar.

1. I enhets uppdateringen för IoT Hub resurs klickar du på **åtkomst kontroll (IAM)**.

1. Klicka på **+ Lägg till** och välj sedan **Lägg till roll tilldelning**.

1. För **roll** väljer du **enhets uppdaterings administratör**. För **tilldela åtkomst till** Välj **användare, grupp eller tjänst princip**. För **Välj** väljer du ditt konto eller kontot för den person som ska distribuera uppdateringar. Klicka på **Spara**.

> [!TIP]
> Om du vill ge fler personer i din organisations åtkomst kan du upprepa det här steget och göra var och en av dessa användare till en **enhets uppdaterings administratör**.

## <a name="next-steps"></a>Nästa steg

Du är nu redo att [Uppdatera Azure percept dev-paketet över-Air](./how-to-update-over-the-air.md) med hjälp av enhets uppdatering för IoT Hub.