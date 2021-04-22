---
title: Azure IoT Central administratörsguide
description: Azure IoT Central är en IoT-programplattform som gör skapandet av IoT-lösningar enklare. Den här artikeln innehåller en översikt över administratörsrollen i IoT Central.
author: philmea
ms.author: philmea
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 658ebe027565a3acaf427a7b3dbf3963701069d8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868653"
---
# <a name="iot-central-administrator-guide"></a>IoT Central administratörsguide

*Den här artikeln gäller för administratörer.*

Med IoT Central program kan du övervaka och hantera miljontals enheter under hela livscykeln. Den här guiden är för administratörer som hanterar IoT Central program.

I IoT Central kan en administratör:

- Hanterar användare och roller i programmet.
- Hanterar säkerhet, till exempel enhetsautentisering.
- Konfigurerar programinställningar.
- Uppgraderar program.
- Exporterar och delar program.
- Övervakar programmets hälsotillstånd.

## <a name="users-and-roles"></a>Användare och roller

IoT Central använder ett rollbaserat åtkomstkontrollsystem för att hantera användarbehörigheter i ett program. IoT Central har tre inbyggda roller för administratörer, lösningsbyggare och operatörer. En administratör kan skapa anpassade roller med specifika uppsättningar med behörigheter. En administratör ansvarar för att lägga till användare i ett program och tilldela dem till roller.

Mer information finns i [Hantera användare och roller i ditt IoT Central program.](howto-manage-users-roles.md)

## <a name="application-security"></a>Programsäkerhet

Enheter som ansluter till ditt IoT Central använder vanligtvis X.509-certifikat eller signaturer för delad åtkomst (SAS) som autentiseringsuppgifter. Administratören hanterar de gruppcertifikat eller nycklar som enhetens autentiseringsuppgifter härleds från.

Mer information finns i [X.509-gruppregistrering,](concepts-get-connected.md#x509-group-enrollment) [SAS-gruppregistrering](concepts-get-connected.md#sas-group-enrollment)och Så här rullar du [X.509-enhetscertifikat.](how-to-roll-x509-certificates.md)

Administratören kan också skapa och hantera DE API-token som ett klientprogram använder för att autentisera med ditt IoT Central program. Klientprogram använder REST API för att interagera med IoT Central.

## <a name="configure-an-application"></a>Konfigurera ett program

Administratören kan konfigurera beteende och utseende för ett IoT Central program. Mer information finns i:

- [Ändra programnamn och URL](howto-administer.md#change-application-name-and-url)
- [Anpassa användargränssnittet](howto-customize-ui.md)
- [Flytta ett program till en annan prisplan](howto-view-bill.md)
- [Konfigurera filuppladdningar](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>Exportera ett program

En administratör kan:

- Skapa en kopia av ett program om du bara behöver en dubblett av ditt program. Du kan till exempel behöva en dubblettkopia för testning.
- Skapa en programmall från ett befintligt program om du planerar att skapa flera kopior.

Mer information finns i Exportera [Azure IoT-programmet](howto-use-app-templates.md).

## <a name="migrate-to-a-new-version"></a>Migrera till en ny version

En administratör kan migrera ett program till en nyare version. För närvarande är ett nyligen skapat program ett V3-program. En administratör kan behöva migrera ett V2-program till ett V3-program.

Mer information finns i [Migrera V2-IoT Central till V3.](howto-migrate.md)

## <a name="monitor-application-health"></a>Övervaka programhälsa

En administratör kan använda IoT Central för att utvärdera hälsotillståndet för anslutna enheter och hälsotillståndet för dataexporter som körs.

För att visa måtten kan en administratör använda diagram i Azure Portal, en REST API, PowerShell- eller Azure CLI-frågor.

Mer information finns i [Övervaka den övergripande hälsan för ett IoT Central program](howto-monitor-application-health.md).

## <a name="tools"></a>Verktyg

Många av de verktyg som du använder som administratör finns i avsnittet **Administration** i varje IoT Central program. Du kan också använda följande verktyg för att utföra vissa administrativa uppgifter:

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure-portalen](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du administrerar ditt Azure IoT Central program föreslår vi att du går vidare med att lära dig mer om att hantera användare och roller [i](howto-manage-users-roles.md) Azure IoT Central.
