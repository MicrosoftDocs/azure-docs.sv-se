---
title: ta med fil
description: ta med fil
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: d9c2aea284a2ab84b5d45fe35a35785adfc88123
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832081"
---
### <a name="publish-the-device-template"></a>Publicera enhetsmallen

Innan du kan lägga till en enhet i programmet måste du publicera enhets mal len:

1. I enhets mal len **lva Edge Gateway v2** väljer du **publicera**.

1. I **mallen publicera den här enheten på program** sidan väljer du **publicera**.

**Lva Edge Gateway v2** är nu tillgängligt som enhets typ som ska användas på sidan **enheter** i programmet.

## <a name="migrate-the-gateway-device"></a>Migrera Gateway-enheten

Den befintliga **gatewayen – 001-** enheten använder enhets mal len **lva Edge Gateway** . Migrera enheten till den nya enhets mal len om du vill använda ditt nya distributions manifest:

Så här migrerar du en **Gateway – 001-** enhet:

1. Gå till sidan **enheter** och välj den **Gateway-001-** enhet som ska markeras i listan.

1. Välj **Migrera**. Om ikonen **migrera** inte visas väljer du **...** om du vill se fler alternativ.

    :::image type="content" source="media/iot-central-video-analytics-part4/migrate-device.png" alt-text="Migrera Gateway-enheten till en ny version":::

1. I listan i dialog rutan **migrera** väljer du **lva Edge Gateway v2** och väljer sedan **migrera**.

Efter några sekunder slutförs migreringen. Enheten använder nu **lva Edge Gateway v2** -enhets mal len med det anpassade distributions manifestet.

Det finns nu inga enheter som använder den ursprungliga **lva Edge Gateway** Device-mallen. Ta bort den här enhets mal len:

1. Gå till sidan **enhetsspecifika mallar** och välj enhets mal len **lva Edge Gateway** .

1. Välj **ta bort** för att ta bort enhets mal len.

### <a name="get-the-device-credentials"></a>Hämta autentiseringsuppgifter för enheten

Du behöver de autentiseringsuppgifter som tillåter att enheten ansluter till ditt IoT Central-program. Hämta autentiseringsuppgifterna för enheten:

1. På sidan **enheter** väljer du **Gateway-001-** enheten.

1. Välj **Anslut**.

1. På sidan **enhets anslutning** gör du en anteckning i *scratchpad.txt* -filen för **ID-omfånget**, **enhets-ID: t** och enhetens **primär nyckel**. Du använder dessa värden senare.

1. Se till att anslutnings metoden är inställd på **signatur för delad åtkomst**.

1. Välj **Stäng**.

