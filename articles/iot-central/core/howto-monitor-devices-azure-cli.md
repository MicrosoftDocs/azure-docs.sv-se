---
title: Övervaka enhetsanslutningen med Azure IoT Central Explorer
description: Övervaka enhetsmeddelanden och observera enhetstvillingens ändringar via IoT Central Explorer CLI.
author: viv-liu
ms.author: viviali
ms.date: 03/27/2020
ms.topic: how-to
ms.service: iot-central
ms.custom: devx-track-azurecli, device-developer
services: iot-central
manager: corywink
ms.openlocfilehash: 30a823b7e78145224929b427e9e37522a7e29f09
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871299"
---
# <a name="monitor-device-connectivity-using-azure-cli"></a>Övervaka enhetsanslutningar med Azure CLI

*Det här avsnittet gäller för enhetsutvecklare och lösningsutvecklare.*

Använd Azure CLI IoT-tillägget för att se meddelanden som dina enheter skickar till IoT Central och observera ändringar i enhetstvillingen. Du kan använda det här verktyget för att felsöka och observera enhetsanslutningar och diagnostisera problem med enhetsmeddelanden som inte når molnet eller enheter som inte svarar på tvillingändringar.

[Mer information finns i referensen för Azure CLI-tillägg](/cli/azure/iot/central)

## <a name="prerequisites"></a>Förutsättningar

+ Azure CLI installerat och är version 2.7.0 eller senare. Kontrollera versionen av Azure CLI genom att köra `az --version` . Lär dig hur du installerar och uppdaterar från [Azure CLI-dokument](/cli/azure/install-azure-cli)
+ Ett arbets- eller skolkonto i Azure som läggs till som en användare i IoT Central program.

## <a name="install-the-iot-central-extension"></a>Installera IoT Central tillägget

Kör följande kommando från kommandoraden för att installera:

```azurecli
az extension add --name azure-iot
```

Kontrollera versionen av tillägget genom att köra:

```azurecli
az --version
```

Du bör se att tillägget azure-iot är 0.9.9 eller högre. Om den inte är det kör du:

```azurecli
az extension update --name azure-iot
```

## <a name="using-the-extension"></a>Använda tillägget

I följande avsnitt beskrivs vanliga kommandon och alternativ som du kan använda när du kör `az iot central` . Om du vill visa en fullständig uppsättning kommandon och alternativ skickar `--help` du till eller något av dess `az iot central` underkommandon.

### <a name="login"></a>Inloggning

Börja med att logga in på Azure CLI. 

```azurecli
az login
```

### <a name="get-the-application-id-of-your-iot-central-app"></a>Hämta program-ID för din IoT Central app
I **Administrations-/programinställningar** kopierar du **program-ID:t**. Du använder det här värdet i senare steg.

### <a name="monitor-messages"></a>Övervaka meddelanden
Övervaka de meddelanden som skickas till din IoT Central från dina enheter. Utdata innehåller alla rubriker och anteckningar.

```azurecli
az iot central diagnostics monitor-events --app-id <app-id> --properties all
```

### <a name="view-device-properties"></a>Visa enhetsegenskaper
Visa aktuella enhetsegenskaper för läsning och läsning/skrivning för en viss enhet.

```azurecli
az iot central device twin show --app-id <app-id> --device-id <device-id>
```

## <a name="next-steps"></a>Nästa steg

Om du är enhetsutvecklare är ett förslag på nästa steg att läsa om [enhetsanslutning i Azure IoT Central](./concepts-get-connected.md).