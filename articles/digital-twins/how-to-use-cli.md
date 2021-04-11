---
title: Använda Azure Digital Twins CLI
titleSuffix: Azure Digital Twins
description: Se hur du kommer igång med och använder Azure Digitals flätade CLI.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5037450d401153811899b8d769ca92af7ce4068e
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103784"
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

Kommando uppsättningen heter **AZ DT** och är en del av [Azure IoT-tillägget för Azure CLI](https://github.com/Azure/azure-iot-cli-extension). Du kan visa en fullständig lista över kommandon och deras användning som en del av referens dokumentationen för `az iot` kommando uppsättningen: [ *AZ DT* kommando referens](/cli/azure/dt).

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

Azure CLI kommer automatiskt att uppmana dig att installera tillägget vid den första användningen av ett kommando som kräver det.

Du kan också använda följande kommando för att installera tillägget när som helst (eller uppdatera det om det visar sig att du redan har en äldre version). Kommandot kan köras antingen i [Azure Cloud Shell](../cloud-shell/overview.md) eller i en [lokal Azure CLI](/cli/azure/install-azure-cli).

```azurecli-interactive
az extension add --upgrade -n azure-iot
```

## <a name="next-steps"></a>Nästa steg

Utforska CLI och dess fullständiga kommando uppsättningar via referens dokumenten:
* [*AZ DT* -kommando referens](/cli/azure/dt)