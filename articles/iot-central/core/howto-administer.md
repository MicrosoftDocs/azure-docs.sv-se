---
title: Ändra program inställningarna för Azure IoT Central | Microsoft Docs
description: Som administratör kan du hantera ditt Azure IoT Central-program genom att ändra program namn, URL, ladda upp avbildning och ta bort ett program
author: viv-liu
ms.author: viviali
ms.date: 12/19/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 81cd2ca1cb47e6fdfb4858df930b73c1bd10118a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091827"
---
# <a name="change-iot-central-application-settings"></a>Ändra IoT Central program inställningar



Den här artikeln beskriver hur du, som administratör, kan hantera program genom att ändra program namn och URL, ladda upp avbildning och ta bort ett program i ditt Azure IoT Central-program.

För att få åtkomst till och använda avsnittet **Administration** måste du ha rollen **administratör** för ett Azure IoT Central-program. Om du skapar ett Azure IoT Central-program tilldelas du automatiskt rollen som **administratör** för programmet.

## <a name="change-application-name-and-url"></a>Ändra program namn och URL

På sidan **program inställningar** kan du ändra namnet och URL: en för ditt program och sedan välja **Spara**.

![Sidan program inställningar](media/howto-administer/image0-a.png)

Om administratören skapar ett anpassat tema för ditt program, innehåller den här sidan ett alternativ för att dölja **program namnet** i användar gränssnittet. Det här alternativet är användbart om program logo typen i det anpassade temat innehåller program namnet. Mer information finns i [Anpassa Azure IoT Central UI](./howto-customize-ui.md).

> [!Note]
> Om du ändrar din URL kan din gamla URL tas av en annan Azure IoT Central-kund. Om detta händer är det inte längre tillgängligt för dig att använda. När du ändrar din URL fungerar inte längre den gamla URL: en och du måste meddela användarna om den nya URL: en som ska användas.

## <a name="delete-an-application"></a>Ta bort ett program

Använd knappen **ta bort** för att ta bort ditt IoT Central program permanent. Den här åtgärden tar permanent bort alla data som är associerade med programmet.

> [!Note]
> Om du vill ta bort ett program måste du också ha behörighet att ta bort resurser i Azure-prenumerationen som du valde när du skapade programmet. Mer information finns i [tilldela Azure-roller för att hantera åtkomst till dina Azure-prenumerations resurser](../../role-based-access-control/role-assignments-portal.md).

## <a name="manage-programmatically"></a>Hantera programmässigt

IoT Central Azure Resource Manager SDK-paket är tillgängliga för Node, python, C#, ruby, Java och go. Du kan använda paketen för att skapa, Visa, uppdatera eller ta bort IoT Central program. Paketen innehåller hjälp program för hantering av autentiserings-och fel hantering.

Du hittar exempel på hur du kan använda Azure Resource Manager SDK: er på [https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples](https://github.com/Azure-Samples/azure-iot-central-arm-sdk-samples) .

Mer information finns i följande GitHub-databaser och paket:

| Språk | Lagringsplats | Paket |
| ---------| ---------- | ------- |
| Node | [https://github.com/Azure/azure-sdk-for-js](https://github.com/Azure/azure-sdk-for-js) | [https://www.npmjs.com/package/@azure/arm-iotcentral](https://www.npmjs.com/package/@azure/arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Go | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig mer om att [Hantera användare och roller](howto-manage-users-roles.md) i Azure IoT Central.
