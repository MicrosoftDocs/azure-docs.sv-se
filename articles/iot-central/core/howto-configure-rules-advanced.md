---
title: Använda arbets flöden för att integrera ditt Azure IoT Central-program med andra moln tjänster | Microsoft Docs
description: Den här artikeln visar dig som ett verktyg för att konfigurera regler och åtgärder som integrerar ditt IoT Central program med andra moln tjänster. Om du vill skapa en avancerad regel använder du en IoT Central anslutning i antingen Energis par eller Azure Logic Apps.
author: dominicbetts
ms.author: dobett
ms.date: 05/12/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 257855b4f7b1fae56ed8d6a063acfb0588da9b6a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92123328"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Använd arbets flöden för att integrera ditt Azure IoT Central-program med andra moln tjänster

*Den här artikeln gäller lösnings byggare.*

Du kan skapa regler i IoT Central som utlöser åtgärder, till exempel att skicka ett e-postmeddelande, som svar på telemetri-baserade villkor, till exempel enhetens temperatur som överskrider ett tröskelvärde.

Med Azure IoT Central v3-anslutaren för automatisk energi och Azure Logic Apps kan du skapa mer avancerade regler för att automatisera åtgärder i IoT Central:

- När en regel utlöses i din Azure IoT Central-app kan den utlösa ett arbets flöde i Energis par eller Azure Logic Apps. Dessa arbets flöden kan köra åtgärder i andra moln tjänster, till exempel Microsoft 365 eller en tjänst från tredje part.
- En händelse i en annan moln tjänst, t. ex. Microsoft 365, kan utlösa ett arbets flöde i Energis par automatisering eller Azure Logic Apps. Dessa arbets flöden kan köra åtgärder eller hämta data från IoT Central programmet.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här instruktions guiden behöver du en aktiv Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

För att konfigurera lösningen krävs ett IoT Central-program för version 3. Information om hur du kontrollerar program versionen finns i [om ditt program](./howto-get-app-info.md). Information om hur du skapar ett IoT Central program finns i [skapa ett Azure IoT Central-program](./quick-deploy-iot-central.md).

> [!NOTE]
> Om du använder en version 2 IoT Central program, se [Bygg arbets flöden med IoT Central-anslutaren i Azure Logic Apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) på dokumentations webbplatsen för tidigare versioner och använd Azure IoT Central v2-anslutaren

## <a name="trigger-a-workflow-from-a-rule"></a>Utlös ett arbets flöde från en regel

Innan du kan utlösa ett arbets flöde i Energis par-eller Azure Logic Apps måste du ha en regel i ditt IoT Central-program. Mer information finns i [Konfigurera regler och åtgärder i Azure IoT Central](./howto-configure-rules.md).

Så här lägger du till **Azure IoT Central v3 – för hands versions** koppling som en utlösare i energi automatisering:

1. I energi spar läge väljer du **+ skapa**, väljer fliken **anpassad** .
1. Sök efter *IoT Central* och välj **Azure IoT Central v3 – för hands versions** anslutning.
1. I listan över utlösare väljer du **när en regel utlöses (för hands version)**.
1. I steget **när en regel utlöses** väljer du ditt IoT Central-program och den regel som du använder.

Så här lägger du till **Azure IoT Central v3 – för hands versions** koppling som en utlösare i Azure Logic Apps:

1. I **Logic Apps designer** väljer du den **tomma Logic app** -mallen.
1. I designern väljer du fliken **anpassad** .
1. Sök efter *IoT Central* och välj **Azure IoT Central v3 – för hands versions** anslutning.
1. I listan över utlösare väljer du **när en regel utlöses (för hands version)**.
1. I steget **när en regel utlöses** väljer du ditt IoT Central-program och den regel som du använder.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Hitta Azure IoT Central – för hands versions koppling och välj utlösaren":::

Nu kan du lägga till fler steg i arbets flödet för att bygga upp ditt integrerings scenario.

## <a name="run-an-action"></a>Kör en åtgärd

Du kan köra åtgärder i ett IoT Central program från Energis par automatisering och Azure Logic Apps arbets flöden. Börja med att skapa ett arbets flöde och Använd en koppling för att definiera en utlösare för att starta arbets flödet. Använd sedan **Azure IoT Central v3 – för hands versions** koppling som åtgärd.

Så här lägger du till **Azure IoT Central v3 – för hands versions** koppling som en åtgärd i automatiserat läge:

1. I energi spar läge går du till panelen **Välj en åtgärd** och väljer fliken **anpassad** .
1. Sök efter *IoT Central* och välj **Azure IoT Central v3 – för hands versions** anslutning.
1. I listan med åtgärder väljer du den IoT Central åtgärd som du vill använda.
1. I åtgärds steget Slutför du konfigurationen för den åtgärd som du har valt. Välj sedan **Spara**.

Så här lägger du till **Azure IoT Central v3 – för hands versions** koppling som åtgärd i Azure Logic Apps:

1. I **Logic Apps designer** väljer du fliken **anpassad** i panelen **Välj en åtgärd** .
1. Sök efter *IoT Central* och välj **Azure IoT Central v3 – för hands versions** anslutning.
1. I listan med åtgärder väljer du den IoT Central åtgärd som du vill använda.
1. I åtgärds steget Slutför du konfigurationen för den åtgärd som du har valt. Välj sedan **Spara**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Hitta Azure IoT Central v3-anslutningen och välj en åtgärd":::

## <a name="list-of-actions"></a>Lista med åtgärder

I följande lista visas alla tillgängliga IoT Central åtgärder i **Azure IoT Central v3 – för hands versions** anslutning och deras konfigurations alternativ. Många av fälten kan ha dynamiskt genererat innehåll. Ett föregående steg kan till exempel bestämma vilket enhets-ID som det aktuella steget agerar på.

### <a name="create-or-update-a-device"></a>Skapa eller uppdatera en enhet

Använd den här åtgärden för att skapa eller uppdatera en enhet i IoT Central programmet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för enheten som ska skapas eller uppdateras. |
| Godkända | Välj om enheten har godkänts för att ansluta till IoT Central. |
| Enhetsbeskrivning | En detaljerad beskrivning av enheten. |
| Enhetsnamn | Enhetens visnings namn. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |
| Simulerade | Välj om enheten simuleras. |

### <a name="delete-a-device"></a>Ta bort en enhet

Använd den här åtgärden för att ta bort en enhet från IoT Central-programmet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |

### <a name="execute-a-device-command"></a>Köra ett enhets kommando

Använd den här åtgärden för att köra ett kommando som definierats i en av enhetens gränssnitt.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets komponent | Gränssnittet i enhets mal len som innehåller kommandot. |
| Enhetskommando | Välj ett av kommandona i det valda gränssnittet. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |
| Enhets kommando begär nytto Last | Om kommandot kräver en begär ande nytto Last lägger du till det här.  |

> [!NOTE]
> Du kan inte välja en enhets komponent förrän du har valt en enhets mall.

### <a name="get-a-device-by-id"></a>Hämta en enhet kopplad till ett ID

Använd den här åtgärden för att hämta enhetens information.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |

Du kan använda den returnerade informationen i de dynamiska uttrycken i andra åtgärder. Enhets informationen som returnerades är: **godkänd**, **Body**, **enhets Beskrivning**, **enhets namn**, **enhets mal len**, **etablerad** och **simulerad**.

### <a name="get-device-cloud-properties"></a>Hämta egenskaper för enhets moln

Använd den här åtgärden för att hämta moln egenskaps värden för en enskild enhet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |

Du kan använda de returnerade moln egenskaps värdena i de dynamiska uttrycken i andra åtgärder.

### <a name="get-device-properties"></a>Hämta enhets egenskaper

Använd den här åtgärden för att hämta egenskaps värden för en enskild enhet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |

Du kan använda de returnerade egenskapsvärdena i de dynamiska uttrycken i andra åtgärder.

### <a name="get-device-telemetry-value"></a>Hämta enhetens telemetri-värde

Använd den här åtgärden för att hämta telemetri-värden för en speciell enhet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |

Du kan använda de värden för telemetri som returnerades i de dynamiska uttrycken i andra åtgärder.

### <a name="update-device-cloud-properties"></a>Uppdatera enhetens moln egenskaper

Använd den här åtgärden för att uppdatera moln egenskaps värden för en enskild enhet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |
| Molnegenskaper | När du har valt en enhets mall läggs ett fält till för varje moln egenskap som definierats i mallen. |

### <a name="update-device-properties"></a>Uppdatera enhets egenskaper

Använd den här åtgärden för att uppdatera skrivbara egenskaps värden för en enskild enhet.

| Fält | Beskrivning |
| ----- | ----------- |
| Program | Välj i listan med IoT Central-program. |
| Enhet | Unikt ID för den enhet som ska tas bort. |
| Enhets mall | Välj i listan med enhetsspecifika mallar i IoT Central programmet. |
| Skrivbara egenskaper | När du har valt en enhets mall läggs ett fält till för varje skrivbar egenskap som definieras i mallen. |

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du skapar en avancerad regel i ditt Azure IoT Central-program, kan du lära dig hur du [analyserar enhets data i ditt azure IoT Central-program](howto-create-analytics.md)