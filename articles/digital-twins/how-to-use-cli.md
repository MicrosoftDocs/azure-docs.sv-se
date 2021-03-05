---
title: Använda Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: Se hur du kommer igång med och använder Azure Digitals flätade CLI.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: a24b8b18dd109f1d8ed5acaa7de55ce5a3cc1eb9
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102201115"
---
# <a name="use-the-azure-digital-twins-cli"></a>Använda Azure Digital Twins CLI

Förutom att hantera Azure Digitals-instansen i Azure Portal, har Azure digitala dubbla, ett kommando som har **angetts för [Azure CLI](/cli/azure/what-is-azure-cli)** som du kan använda för att utföra de flesta större åtgärder med tjänsten, inklusive:
* Hantera en digital Azure-instans
* Hantera modeller
* Hantera digitala dubbla
* Hantera dubbla relationer
* Konfigurera slut punkter
* Hantera [vägar](concepts-route-events.md)
* Konfigurera [säkerhet](concepts-security.md) via rollbaserad åtkomst kontroll i Azure (Azure RBAC)

Kommando uppsättningen heter **AZ DT** och är en del av [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Du kan visa en fullständig lista över kommandon och deras användning som en del av referens dokumentationen för `az iot` kommando uppsättningen: [ *AZ DT* kommando referens](/cli/azure/ext/azure-iot/dt).

## <a name="uses-deploy-and-validate"></a>Använder (distribuera och validera)

Förutom att hantera instansen generellt är CLI också ett användbart verktyg för distribution och validering.
* Kontroll Plans kommandona kan användas för att göra distributionen av en ny instans repeterbar eller automatiserad.
* Du kan använda data Plans kommandon för att snabbt kontrol lera värden i din instans och kontrol lera att åtgärderna har slutförts som förväntat.

## <a name="get-the-command-set"></a>Hämta kommando uppsättningen

Azures digitala dubbla kommandon är en del av [Azure IoT-tillägget för Azure CLI (Azure-IoT)](https://github.com/Azure/azure-iot-cli-extension), så följ de här stegen för att se till att du har det senaste `azure-iot` tillägget med **AZ DT** -kommandon.

### <a name="cli-version-requirements"></a>Versions krav för CLI

Om du använder Azure CLI med PowerShell, kräver tilläggs paketet att din Azure CLI-version är **2.3.1** eller högre.

Du kan kontrol lera versionen av Azure CLI med detta CLI-kommando:
```azurecli
az --version
```

Instruktioner för hur du installerar eller uppdaterar Azure CLI till en nyare version finns i [*Installera Azure CLI*](/cli/azure/install-azure-cli).

### <a name="get-the-extension"></a>Hämta tillägget

Du kan kontrol lera att du har den senaste versionen av `azure-iot` tillägget med de här stegen. Du kan köra dessa kommandon i [Azure Cloud Shell](../cloud-shell/overview.md) eller i ett [lokalt Azure CLI](/cli/azure/install-azure-cli).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Nästa steg

Utforska CLI och dess fullständiga kommando uppsättningar via referens dokumenten:
* [*AZ DT* -kommando referens](/cli/azure/ext/azure-iot/dt)