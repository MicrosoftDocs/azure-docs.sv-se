---
title: Meddelanden i Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Skicka meddelanden till användare av appar som bygger på Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 8910797631dd86d75619c7a4691a25e88b8a9e09
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495835"
---
# <a name="communication-services-notifications"></a>Meddelanden om kommunikations tjänster

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]


Azure Communication Services chatt och anropar klient bibliotek skapar en meddelande kanal i real tid som gör det möjligt att skicka meddelanden till anslutna klienter på ett effektivt och tillförlitligt sätt. På så sätt kan du bygga avancerade funktioner i real tid i dina program utan att behöva implementera logik för komplicerad HTTP-avsökning. Men i mobila program förblir den här signal kanalen bara ansluten när ditt program är aktivt i förgrunden. Om du vill att användarna ska kunna ta emot inkommande samtal eller chatta när ditt program är i bakgrunden, bör du använda push-meddelanden.

Med push-meddelanden kan du skicka information från ditt program till användarnas mobila enheter. Du kan använda push-meddelanden för att visa en dialog ruta, spela upp ett ljud eller visa inkommande samtals gränssnitt. Azure Communication Services ger integration med [Azure Event Grid](../../event-grid/overview.md) och [Azure-Notification Hubs](../../notification-hubs/notification-hubs-push-notification-overview.md) som gör att du kan lägga till push-meddelanden till dina appar.

## <a name="trigger-push-notifications-via-azure-event-grid"></a>Utlös push-meddelanden via Azure Event Grid

Azure Communication Services integreras med [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) för att leverera real tids händelse meddelanden på ett tillförlitligt, skalbart och säkert sätt. Du kan utnyttja den här integrationen för att skapa en meddelande tjänst som levererar mobila push-meddelanden till användarna genom att skapa en Event Grid-prenumeration som utlöser en [Azure Function](../../azure-functions/functions-overview.md) -eller webhook.

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="Diagram över hur kommunikations tjänster integreras med Event Grid.":::

Lär dig mer om [händelse hantering i Azure Communication Services](./event-handling.md).

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>Leverera push-meddelanden via Azure Notification Hubs

Du kan ansluta en Azure Notification Hub till kommunikations tjänst resursen för att automatiskt skicka push-meddelanden till en användares mobila enhet när de får ett inkommande samtal. Du bör använda dessa push-meddelanden för att väcka ditt program från bakgrunden och visnings gränssnittet som låter användaren godkänna eller avböja anropet.

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="Diagram över hur kommunikations tjänster integreras med Azure Notification Hubs.":::

Kommunikations tjänster använder Azure Notification Hub som en direkt tjänst för att kommunicera med de olika plattformsspecifika Push Notification-tjänsterna med hjälp av [direkt sändnings](/rest/api/notificationhubs/direct-send) -API: et. På så sätt kan du återanvända dina befintliga Azure Notification Hub-resurser och-konfigurationer för att leverera låg latens, tillförlitliga anrops aviseringar till dina program.

> [!NOTE]
> För närvarande stöds endast push-meddelanden.

### <a name="notification-hub-provisioning"></a>Etablering av Notification Hub

Om du vill skicka push-meddelanden till klient enheter med hjälp av Notification Hubs [skapar du en Notification Hub](../../notification-hubs/create-notification-hub-portal.md) i samma prenumeration som kommunikations tjänst resursen. Du måste konfigurera Azure Notification Hub för Plattformsspecifikt meddelandesystem som du vill använda. Information om hur du hämtar push-meddelanden i din klient app från Notification Hubs finns i [komma igång med Notification Hubs](../../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md) och välj mål klient plattform i list rutan längst upp på sidan.

> [!NOTE]
> För närvarande stöds APN-och FCM-plattformarna.
APN-plattformen måste konfigureras med autentiseringsläge för token. Autentiseringsläget för certifikat stöds inte från och med nu.

När din Notification Hub har kon figurer ATS kan du associera den med din kommunikations tjänst resurs genom att tillhandahålla en anslutnings sträng för hubben med hjälp av Azure Resource Manager klienten eller via Azure Portal. Anslutnings strängen måste innehålla `Send` behörigheter. Vi rekommenderar att du skapar en annan åtkomst princip med `Send` enbart behörigheter som är specifika för din hubb. Läs mer om [Notification Hubs säkerhets-och åtkomst principer](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="using-the-azure-resource-manager-client-to-link-your-notification-hub"></a>Använda Azure Resource Manager-klienten för att länka Notification Hub

Logga in på Azure Resource Manager genom att köra följande och logga in med dina autentiseringsuppgifter.

```console
armclient login
```

 När du har loggat in kör du följande för att etablera Notification Hub:

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-link-your-notification-hub"></a>Använda Azure Portal för att länka din Notification Hub

I portalen navigerar du till din Azure Communication Services-resurs. I resursen kommunikations tjänster väljer du push-meddelanden på den vänstra menyn på sidan kommunikations tjänster och ansluter den meddelande hubb som du etablerade tidigare. Du måste ange anslutnings strängen och resourceId här:

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="Skärm bild som visar inställningarna för push-meddelanden inom Azure Portal.":::

> [!NOTE]
> Om Azure Notification Hub-anslutningssträngen har uppdaterats måste kommunikations tjänst resursen också uppdateras.
Alla ändringar på hur hubben länkas visas i data planet (d.v.s. När du skickar ett meddelande) inom en period på högst ``10`` minuter. Detta gäller även när navet är länkat för första gången **om** det fanns meddelanden som skickats tidigare.

### <a name="device-registration"></a>Enhetsregistrering

Se [röst samtals snabb start](../quickstarts/voice-video-calling/getting-started-with-calling.md) för att lära dig hur du registrerar din enhets hantering med kommunikations tjänster.

### <a name="troubleshooting-guide-for-push-notifications"></a>Fel söknings guide för push-meddelanden

När du inte ser push-meddelanden på enheten finns det tre platser där aviseringarna kan ha släppts:

- Azure-Notification Hubs accepterade inte meddelandet från Azure Communication Services
- Plattformsspecifikt meddelandesystem (till exempel APN och FCM) accepterade inte meddelandet från Azure Notification Hubs
- Plattformsspecifikt meddelandesystem levererade inte meddelandet till enheten.

Den första plats där ett meddelande kan släppas (Azure Notification Hubs inte accepterat meddelanden från Azure Communication Services). De andra två platserna finns i [diagnostisera avbrutna meddelanden i Azure Notification Hubs](../../notification-hubs/notification-hubs-push-notification-fixer.md).

Ett sätt att se om din kommunikations tjänst resurs skickar meddelanden till Azure Notification Hubs är genom att titta på `incoming messages` måttet från de länkade [Azure Notification Hub-måtten](../../azure-monitor/essentials/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs).

Här följer några vanliga fel konfigurationer som kan vara orsaken till varför Azure Notification Hub inte accepterar meddelanden från kommunikations tjänst resursen.

#### <a name="azure-notification-hub-not-linked-to-the-communication-services-resource"></a>Azure Notification Hub är inte länkad till kommunikations tjänst resursen

Det kan finnas fall då du inte länkar din Azure Notification Hub till kommunikations tjänst resursen. Du kan ta en titt på [avsnittet etablering av Notification Hub](#notification-hub-provisioning) för att se hur du länkar dem.

#### <a name="the-linked-azure-notification-hub-isnt-configured"></a>Den länkade Azure Notification Hub har inte kon figurer ATS

Du måste konfigurera den länkade Notification-hubben med Plattformsspecifikt meddelandesystem autentiseringsuppgifter för den plattform (till exempel iOS eller Android) som du vill använda. Mer information om hur du kan göra är att ta en titt på [Konfigurera push-meddelanden i en Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-azure-notification-hub-doesnt-exist"></a>Den länkade Azure Notification Hub finns inte

Azure Notification Hub som är länkad till kommunikations tjänst resursen finns inte längre. Kontrol lera att den länkade meddelande hubben fortfarande finns.

#### <a name="the-azure-notification-hub-apns-platform-is-configured-with-certificate-authentication-mode"></a>APN-plattformen för Azure Notification Hub har kon figurer ATS med autentiseringsläget för certifikat

Om du vill använda APN-plattformen med autentiseringsläget för certifikat, stöds den inte för närvarande. Du bör konfigurera APNs-plattformen med autentiseringsläge för token som anges i [Konfigurera push-meddelanden i en Notification Hub](../../notification-hubs/configure-notification-hub-portal-pns-settings.md).

#### <a name="the-linked-connection-string-doesnt-have-send-permission"></a>Den länkade anslutnings strängen har inte `Send` behörighet

Anslutnings strängen som du använde för att länka din Notification Hub till kommunikations tjänst resursen måste ha `Send` behörigheten. För mer information om hur du kan skapa en ny anslutnings sträng eller se den aktuella anslutnings strängen från din Azure Notification Hub kan du ta en titt i [Notification Hubs säkerhets-och åtkomst principer](../../notification-hubs/notification-hubs-push-notification-security.md)

#### <a name="the-linked-connection-string-or-azure-notification-hub-resourceid-arent-valid"></a>Den länkade anslutnings strängen eller Azure Notification Hub resourceId är inte giltig

Se till att konfigurera resursen för kommunikations tjänster med rätt anslutnings sträng och Azure Notification Hub resourceId

#### <a name="the-linked-connection-string-is-regenerated"></a>Den länkade anslutnings strängen återskapas

Om du återskapar anslutnings strängen för den länkade Azure Notification Hub måste du uppdatera anslutnings strängen med den nya i kommunikations tjänst resursen genom [att länka om Notification Hub](#notification-hub-provisioning).

## <a name="next-steps"></a>Nästa steg

* En introduktion till Azure Event Grid finns i [Vad är event Grid?](../../event-grid/overview.md)
* Mer information om Azure Notification Hub-koncepten finns i [azure Notification Hubs-dokumentationen](../../notification-hubs/index.yml)
