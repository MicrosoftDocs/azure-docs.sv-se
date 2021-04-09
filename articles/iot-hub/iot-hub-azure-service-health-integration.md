---
title: Kontrol lera Azure-IoT Hub tjänst och resurs hälsa | Microsoft Docs
description: Använd Azure Service Health och Azure Resource Health för att övervaka din IoT Hub
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: robinsh
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: Technical Support'
- devx-track-csharp
ms.openlocfilehash: 27fca7b76ab148fc355eb7d52ee0cbcbd3540458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92548583"
---
# <a name="check-iot-hub-service-and-resource-health"></a>Kontrol lera IoT Hub tjänst-och resurs hälsa

Azure IoT Hub integreras med [tjänsten Azure Service Health](../service-health/overview.md) för att ge dig möjlighet att övervaka service nivå hälsan för tjänsten IoT Hub och enskilda IoT-hubbar. Du kan också ställa in aviseringar som ska meddelas när statusen för IoT Hub tjänsten eller en IoT Hub-ändring. Tjänsten Azure Service Health är en kombination av tre mindre tjänster: Azure Resource Health, Azure Service Health och sidan Azure-status. I avsnitten i den här artikeln beskrivs varje tjänst och dess relation till IoT Hub i detalj.

Azure Service Health Service hjälper dig att övervaka händelser på tjänst nivå, t. ex. avbrott och uppgraderingar som kan påverka tillgängligheten för tjänsten IoT Hub och dina enskilda IoT-hubbar. IoT Hub integreras också med Azure Monitor för att tillhandahålla IoT Hub-plattforms mått och IoT Hub resurs loggar som du kan använda för att övervaka drift fel och villkor som inträffar i en speciell IoT-hubb. Mer information finns i [övervaka IoT Hub](monitor-iot-hub.md).

## <a name="check-health-of-an-iot-hub-with-azure-resource-health"></a>Kontrol lera hälso tillståndet för en IoT Hub med Azure Resource Health

Azure Resource Health är en del av Azure Service Health-tjänsten som spårar hälsan för enskilda resurser. Du kan kontrol lera hälso tillståndet för din IoT Hub direkt från portalen.

Följ dessa steg om du vill se status och status historik för din IoT Hub med hjälp av portalen:

1. I [Azure Portal](https://portal.azure.com)går du till din IoT-hubb i Azure Portal.

1. I den vänstra rutan under **support + fel sökning** väljer du **Resource Health**.

    :::image type="content" source="./media/iot-hub-azure-service-health-integration/iot-hub-resource-health.png" alt-text="Sidan resurs hälsa för en IoT Hub":::

Mer information om Azure Resource Health och hur du tolkar hälso data finns [Resource Health översikt](../service-health/resource-health-overview.md) i Azure Service Health-dokumentationen.

Du kan också välja **Lägg till resurs hälso avisering** om du vill konfigurera aviseringar som ska utlösas när hälso tillståndet för din IoT-hubb ändras. Mer information finns i [Konfigurera aviseringar för service Health-händelser](../service-health/alerts-activity-log-service-notifications-portal.md) och relaterade ämnen i Azure Service Health-dokumentationen.

## <a name="check-health-of-iot-hubs-in-your-subscription-with-azure-service-health"></a>Kontrol lera hälso tillståndet för IoT-hubbar i din prenumeration med Azure Service Health

Med Azure Service Health kan du kontrol lera hälso tillståndet för alla IoT-hubbar i din prenumeration.

Följ dessa steg om du vill kontrol lera hälso tillståndet för dina IoT-hubbar:

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Navigera till **service Health**  >  **resurs hälsa**.

3. Välj din prenumeration i list rutan och välj **IoT Hub** som resurs typ.

Mer information om Azure Service Health och hur du tolkar hälso data finns [service Health översikt](../service-health/service-health-overview.md) i Azure Service Health-dokumentationen.

Information om hur du konfigurerar aviseringar med Azure Service Health finns i [Konfigurera aviseringar för service Health-händelser](../service-health/alerts-activity-log-service-notifications-portal.md) och relaterade ämnen i Azure Service Health-dokumentationen.

## <a name="check-health-of-the-iot-hub-service-by-region-on-azure-status-page"></a>Kontrol lera hälso tillståndet för den IoT Hub tjänsten efter region på sidan Azure-status

Om du vill se status för IoT Hub och andra tjänster efter region World-Wide kan du använda [sidan Azure-status](https://status.azure.com/status). Mer information om sidan Azure-status finns i [Översikt över Azure status](../service-health/azure-status-overview.md) i Azure Service Health-dokumentationen.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Service Health, Azure Resource Health och Azure-status finns i [Azure Service Healths tjänsten](../service-health/overview.md) .
* Se [övervaka Azure-IoT Hub](monitor-iot-hub.md) för en beskrivning av övervakning av azure-IoT Hub.
