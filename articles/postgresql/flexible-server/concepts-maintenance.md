---
title: Schemalagt underhåll – Azure Database for PostgreSQL – flexibel Server
description: Den här artikeln beskriver det schemalagda underhålls funktionen i Azure Database for PostgreSQL-flexibel Server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: ffee15776a48b6495f78b6becf81c620e1dc4d69
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336317"
---
# <a name="scheduled-maintenance-in-azure-database-for-postgresql--flexible-server"></a>Schemalagt underhåll i Azure Database for PostgreSQL – flexibel server
 
Azure Database for PostgreSQL-flexibel Server utför regelbundet underhåll för att hålla din hanterade databas säker, stabil och uppdaterad. Under underhållet hämtar servern nya funktioner, uppdateringar och korrigeringar.
 
> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version
 
## <a name="selecting-a-maintenance-window"></a>Välja en underhålls period
 
Du kan schemalägga underhåll under en angiven dag i veckan och en tids period inom den dagen. Eller så kan du låta systemet välja dag och tids period för dig automatiskt. Oavsett hur du gör det meddelar systemet dig fem dagar innan du kör något underhåll. Systemet kommer också att meddela dig när underhållet har startats och när det har slutförts.
 
Aviseringar om kommande schemalagt underhåll kan vara:
 
* Skickas via e-post till en speciell adress
* Skickas via e-post till en Azure Resource Manager roll
* Skickas i ett textmeddelande (SMS) till mobila enheter
* Som en push-avisering till en Azure-app
* Som ett röstmeddelande
 
När du anger inställningar för underhållsschemat kan du välja en veckodag och en tidsperiod. Om du inte anger detta väljer systemet tider mellan 23:00 och 07:00 (i serverns tidszon). Du kan definiera olika scheman för varje flexibel server i din Azure-prenumeration. 
 
> [!IMPORTANT]
> Vanligtvis är tiden mellan schemalagda underhållshändelser för en server minst 30 dagar.
>
> Men om det finns en kritisk uppdatering, till exempel en allvarligt säkerhetsrisk kan aviseringsperioden vara kortare än fem dagar. Den kritiska uppdateringen kan tillämpas på servern även om ett lyckat schemalagt underhåll har utförts under de senaste 30 dagarna.

Du kan uppdatera schemaläggnings inställningarna när som helst. Om det finns ett underhåll som schemalagts för din flexibla Server och du uppdaterar inställningarna för schemaläggning, kommer den aktuella händelsen att fortsätta enligt schema och schema inställningarna ändras efter lyckad slut för ande. 

Om underhålls händelsen avbryts av systemet eller om den inte kan slutföras, kommer systemet att skapa ett meddelande om avbrott eller misslyckade underhålls händelser. Nästa försök att utföra underhåll kommer att schemaläggas enligt aktuella schemaläggnings inställningar och du får ett meddelande om det fem dagar i förväg.
 
## <a name="next-steps"></a>Nästa steg
 
* Lär dig hur du [ändrar underhålls schema](how-to-maintenance-portal.md)
* Lär dig hur du [får aviseringar om kommande underhåll](../../service-health/service-notifications.md) med Azure Service Health
* Lär dig hur du [ställer in aviseringar om kommande schemalagda underhålls händelser](../../service-health/resource-health-alert-monitor-guide.md)
