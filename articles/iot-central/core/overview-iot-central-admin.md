---
title: Administratörs guide för Azure IoT Central
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över administratörs rollen i IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110499"
---
# <a name="iot-central-administrator-guide"></a>IoT Central administratörs guide

*Den här artikeln gäller för administratörer.*

Med ett IoT Central program kan du övervaka och hantera miljon tals enheter under deras livs cykel. Den här guiden är för administratörer som hanterar IoT Central-program.

I IoT Central, en administratör:

- Hanterar användare och roller i programmet.
- Hanterar säkerhet, till exempel enhetsautentisering.
- Konfigurerar program inställningar.
- Uppgraderar program.
- Exporterar och delar program.
- Övervakar program hälsa.

## <a name="users-and-roles"></a>Användare och roller

IoT Central använder ett rollbaserad åtkomst kontroll system för att hantera användar behörigheter i ett program. IoT Central har tre inbyggda roller för administratörer, lösnings byggare och operatörer. En administratör kan skapa anpassade roller med angivna behörighets uppsättningar. En administratör ansvarar för att lägga till användare till ett program och tilldela dem till roller.

Mer information finns i [Hantera användare och roller i ditt IoT Central-program](howto-manage-users-roles.md).

## <a name="application-security"></a>Programsäkerhet

Enheter som ansluter till ditt IoT Central-program använder vanligt vis X. 509-certifikat eller signaturer för delad åtkomst (SAS) som autentiseringsuppgifter. Administratören hanterar de grupp certifikat eller nycklar som enhetens autentiseringsuppgifter härleds från.

Mer information finns i [grupp registrering för x. 509](concepts-get-connected.md#x509-group-enrollment), SAS- [gruppregistrering](concepts-get-connected.md#sas-group-enrollment)och [hur du registrerar X. 509 enhets certifikat](how-to-roll-x509-certificates.md).

Administratören kan också skapa och hantera API-token som ett klient program använder för att autentisera med ditt IoT Central-program. Klient program använder REST API för att interagera med IoT Central.

## <a name="configure-an-application"></a>Konfigurera ett program

Administratören kan konfigurera ett IoT Central programs beteende och utseende. Mer information finns i:

- [Ändra program namn och URL](howto-administer.md#change-application-name-and-url)
- [Anpassa användargränssnittet](howto-customize-ui.md)
- [Flytta ett program till ett annat pris avtal](howto-view-bill.md)
- [Konfigurera fil överföringar](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exportera ett program

En administratör kan:

- Skapa en kopia av ett program om du bara behöver en kopia av ditt program. Du kan till exempel behöva en kopia för testning.
- Skapa en program mall från ett befintligt program om du planerar att skapa flera kopior.

Mer information finns i [Exportera ditt Azure IoT-program](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrera till en ny version

En administratör kan migrera ett program till en nyare version. För närvarande är ett nyligen skapat program ett v3-program. En administratör kan behöva migrera ett v2-program till ett v3-program.

Mer information finns i [migrera ditt V2 IoT Central-program till v3](howto-migrate.md).

## <a name="monitor-application-health"></a>Övervaka programhälsa

En administratör kan använda IoT Central mått för att utvärdera hälsan hos anslutna enheter och hälsan för att köra data exporter.

Om du vill visa måtten kan en administratör använda diagram i Azure Portal, en REST API-eller PowerShell-eller Azure CLI-fråga.

Mer information finns i [övervaka den övergripande hälsan för ett IoT Central program](howto-monitor-application-health.md).

## <a name="tools"></a>Verktyg

Många av de verktyg som du använder som administratör finns i avsnittet **Administration** i varje IoT Central-program. Du kan också använda följande verktyg för att utföra vissa administrativa uppgifter:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure-portalen](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar ditt Azure IoT Central-program är det föreslagna nästa steg att lära dig mer om att [Hantera användare och roller](howto-manage-users-roles.md) i Azure IoT Central.
