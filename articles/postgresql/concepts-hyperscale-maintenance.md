---
title: Schemalagt underhåll – Azure Database for PostgreSQL-storskalig (citus)
description: I den här artikeln beskrivs funktionen för schemalagda underhåll i Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: dee7257a296f523dbc9040d65fe68c14edf928f6
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106793"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Schemalagt underhåll i Azure Database for PostgreSQL – storskalig (citus)

Azure Database for PostgreSQL-Scale (citus) är ett regelbundet underhåll för att hålla din hanterade databas säker, stabil och uppdaterad.  Under underhållet får alla noder i Server gruppen nya funktioner, uppdateringar och korrigeringar.

Viktiga funktioner i schemalagt underhåll för storskalig (citus):

* Uppdateringar tillämpas samtidigt på alla noder i Server gruppen
* Meddelanden om kommande underhåll skickas till Azure Service Health fem dagar i förväg
* Det finns vanligt vis minst 30 dagar mellan lyckade underhålls händelser för en Server grupp
* Önskad dag i veckan och tids perioden under den dagen för underhålls start kan definieras för varje server grupp individuellt

## <a name="selecting-a-maintenance-window-and-notification-about-upcoming-maintenance"></a>Välja en underhålls period och meddelanden om kommande underhåll

Du kan schemalägga underhåll under en angiven dag i veckan och en tids period inom den dagen. Alternativt kan du låta systemet välja en dag och en tids period åt dig automatiskt. Oavsett hur du gör det meddelar systemet dig fem dagar innan du kör något underhåll. Systemet kommer också att meddela dig när underhållet har startats och när det har slutförts.

Aviseringar om kommande schemalagt underhåll skickas till Azure Service Health och kan vara:

* Skickas via e-post till en speciell adress
* Skickas via e-post till en Azure Resource Manager roll
* Skickas i ett textmeddelande (SMS) till mobila enheter
* Som en push-avisering till en Azure-app
* Som ett röstmeddelande

När du anger inställningar för underhållsschemat kan du välja en veckodag och en tidsperiod. Om du inte anger så kommer systemet att välja tider mellan 23:00 och 7am i Server gruppens regions tid. Du kan definiera olika scheman för varje storskalig (citus)-Server grupp i din Azure-prenumeration.

> [!IMPORTANT]
> Det finns vanligt vis minst 30 dagar mellan lyckade schemalagda underhålls händelser för en Server grupp.
>
> Men om det finns en kritisk uppdatering, till exempel en allvarligt säkerhetsrisk kan aviseringsperioden vara kortare än fem dagar. Den kritiska uppdateringen kan tillämpas på servern även om ett lyckat schemalagt underhåll har utförts under de senaste 30 dagarna.

Du kan uppdatera schemaläggnings inställningarna när som helst. Om det schemalagda underhållet har schemalagts för citus-servergruppen och du uppdaterar schemat, kommer befintliga händelser att fortsätta som ursprungligen schemalagt. Inställnings ändringen börjar gälla när befintliga händelser har slutförts.

Om underhållet Miss lyckas eller avbryts skapas ett meddelande i systemet.
Det kommer att försöka utföra underhåll igen enligt de aktuella schemaläggnings inställningarna och meddela dig fem dagar före nästa underhålls händelse.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [ändrar underhålls schema](howto-hyperscale-maintenance.md)
* Lär dig hur du [får aviseringar om kommande underhåll](../service-health/service-notifications.md) med Azure Service Health
* Lär dig hur du [ställer in aviseringar om kommande schemalagda underhålls händelser](../service-health/resource-health-alert-monitor-guide.md)
