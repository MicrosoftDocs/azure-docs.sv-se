---
title: Flytta Azure App Service resurser
description: Använd Azure Resource Manager för att flytta App Service resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: 27555a4616befca41c7e970e947afa1cd1ff7248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531380"
---
# <a name="move-guidance-for-app-service-resources"></a>Flytta vägledning för App Service resurser

I den här artikeln beskrivs stegen för att flytta App Service-resurser. Det finns särskilda krav för att flytta App Service resurser till en ny prenumeration.

## <a name="move-across-subscriptions"></a>Flytta över prenumerationer

När du flyttar en webbapp över prenumerationer gäller följande rikt linjer:

- Mål resurs gruppen får inte ha några befintliga App Service-resurser. App Service resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade TLS/SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service resurser i resurs gruppen måste flyttas tillsammans.
- Det går inte att flytta App Service miljöer till en ny resurs grupp eller prenumeration. Du kan dock flytta en webbapp och en app service-plan till en ny prenumeration utan att flytta App Service-miljön. Efter flyttningen finns inte webbappen längre i App Service-miljön.
- Du kan flytta ett certifikat som är bundet till en webbplats utan att ta bort TLS-bindningarna, förutsatt att certifikatet flyttas med alla andra resurser i resurs gruppen.
- App Service resurser kan bara flyttas från resurs gruppen där de ursprungligen skapades. Om en App Service resurs inte längre finns i den ursprungliga resurs gruppen flyttar du tillbaka den till den ursprungliga resurs gruppen. Flytta sedan resursen över prenumerationer.

Om du inte kommer ihåg den ursprungliga resurs gruppen kan du hitta den via diagnostik. För din webbapp väljer du **diagnostisera och lösa problem**. Välj sedan **konfiguration och hantering**.

![Välj diagnostik](./media/app-service-move-limitations/select-diagnostics.png)

Välj **alternativ för migrering**.

![Välj alternativ för migrering](./media/app-service-move-limitations/select-migration.png)

Välj alternativet för rekommenderade steg för att flytta webb programmet.

![Välj rekommenderade steg](./media/app-service-move-limitations/recommended-steps.png)

Du ser de rekommenderade åtgärderna innan du flyttar resurserna. Informationen omfattar den ursprungliga resurs gruppen för webbappen.

![Skärmdumpen visar rekommenderade steg för att flytta Microsoft dot-webbresurser.](./media/app-service-move-limitations/recommendations.png)

## <a name="move-support"></a>Stöd för att flytta

Information om hur du tar reda på vilka App Service resurser som kan flyttas finns i flytta support status för:

- [Microsoft. AppService](../move-support-resources.md#microsoftappservice)
- [Microsoft. CertificateRegistration](../move-support-resources.md#microsoftcertificateregistration)
- [Microsoft. DomainRegistration](../move-support-resources.md#microsoftdomainregistration)
- [Microsoft. Web](../move-support-resources.md#microsoftweb)

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](../move-resource-group-and-subscription.md).
