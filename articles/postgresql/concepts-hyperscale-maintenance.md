---
title: Schemalagt underhåll – Azure Database for PostgreSQL-storskalig (citus)
description: I den här artikeln beskrivs funktionen för schemalagda underhåll i Azure Database for PostgreSQL-storskalig (citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0d11ec8815cc0f5082f95c5331321f043e86eece
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027673"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--hyperscale-citus"></a>Schemalagt underhåll i Azure Database for PostgreSQL – storskalig (citus)

Azure Database for PostgreSQL-Scale (citus) är ett regelbundet underhåll för att hålla din hanterade databas säker, stabil och uppdaterad.  Under underhållet får alla noder i Server gruppen nya funktioner, uppdateringar och korrigeringar.

Viktiga funktioner i schemalagt underhåll för storskalig (citus):

* Uppdateringar tillämpas samtidigt på alla noder i Server gruppen
* Meddelanden om kommande underhåll skickas till Azure Service Health fem dagar i förväg
* Det finns vanligt vis minst 30 dagar mellan lyckade underhålls händelser för en Server grupp

## <a name="notification-about-upcoming-maintenance"></a>Meddelande om kommande underhåll

Aviseringar om kommande schemalagt underhåll skickas till Azure Service Health och kan vara:

* Skickas via e-post till en speciell adress
* Skickas via e-post till en Azure Resource Manager roll
* Skickas i ett textmeddelande (SMS) till mobila enheter
* Som en push-avisering till en Azure-app
* Som ett röstmeddelande

> [!IMPORTANT]
> Det finns vanligt vis minst 30 dagar mellan lyckade schemalagda underhålls händelser för en Server grupp.
>
> Men om det finns en kritisk uppdatering, till exempel en allvarligt säkerhetsrisk kan aviseringsperioden vara kortare än fem dagar. Den kritiska uppdateringen kan tillämpas på servern även om ett lyckat schemalagt underhåll har utförts under de senaste 30 dagarna.

Om underhållet Miss lyckas eller avbryts skapas ett meddelande i systemet.
Det kommer att försöka utföra underhåll igen enligt de aktuella schemaläggnings inställningarna och meddela dig fem dagar före nästa underhålls händelse.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [får aviseringar om kommande underhåll](../service-health/service-notifications.md) med Azure Service Health
* Lär dig hur du [ställer in aviseringar om kommande schemalagda underhålls händelser](../service-health/resource-health-alert-monitor-guide.md)
