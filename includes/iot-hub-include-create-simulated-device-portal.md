---
title: ta med fil
description: ta med fil
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/26/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 91b6f1ed06fbf1f5575650f96f4622b3df9ca083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98187042"
---
<!-- This is the instructions for creating a simulated device you can use for testing routing.-->

Skapa sedan en enhetsidentitet och spara nyckeln för framtida bruk. Den är enhetsidentiteten används av simuleringsprogrammet för att skicka meddelanden till IoT-hubben. Den här funktionen är inte tillgänglig i PowerShell eller när du använder en Azure Resource Manager mall. Följande steg beskriver hur du skapar den simulerade enheten med hjälp av [Azure Portal](https://portal.azure.com).

1. Öppna [Azure Portal](https://portal.azure.com) och logga in på ditt Azure-konto.

2. Välj **resurs grupper** och välj sedan din resurs grupp. I den här självstudien används **ContosoResources**.

3. Välj din IoT Hub i listan över resurser. I självstudien används **ContosoTestHub**. Välj **IoT-enheter** från hubbfönstret.

4. Välj **+ Ny**. I rutan Lägg till enhet fyller du i enhets-ID. I den här självstudien används **Contoso-Test-Device**. Lämna nycklarna tomma och markera **Generera nycklar automatiskt**. Kontrollera att **Connect device to IoT hub** (Anslut enhet till IoT-hubb) är aktiverat. Välj **Spara**.

   ![Skärmen Lägg till enhet](./media/iot-hub-include-create-simulated-device-portal/add-device.png)

5. Nu när den har skapats väljer du enheten för att se de genererade nycklarna. Välj kopierings ikonen på den primära nyckeln och spara den någonstans, till exempel anteckningar för test fasen i den här självstudien.

   ![Enhets informationen, inklusive nycklar](./media/iot-hub-include-create-simulated-device-portal/device-details.png)