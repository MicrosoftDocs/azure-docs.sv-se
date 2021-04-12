---
title: Azure Database for PostgreSQL-storskalig (citus) – schemalagt underhåll – Azure Portal
description: Lär dig hur du konfigurerar schemalagda underhålls inställningar för en Azure Database for PostgreSQL-storskalig (citus) från Azure Portal.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 04dd37e3e9abbfbec7badb036802e645cc7732b0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108520"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--hyperscale-citus"></a>Hantera schemalagda underhålls inställningar för Azure Database for PostgreSQL – storskalig (citus)

Du kan ange underhålls alternativ för varje storskalig (citus) Server grupp i din Azure-prenumeration. Alternativen omfattar underhålls schema och meddelande inställningar för kommande och avslutade underhålls händelser.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- En [Azure Database for PostgreSQL-storskalig (citus)-Server grupp](quickstart-create-hyperscale-portal.md)

## <a name="specify-maintenance-schedule-options"></a>Ange alternativ för underhålls schema

1. Välj **Underhåll** för att öppna alternativ för schemalagda underhåll under **inställnings** rubriken på den citus-Server grupp sidan.
2. Standardschemat (systemhanterat) är en slumpmässig dag i veckan och 30-minuters period för underhålls start mellan 23:00 och 7am Server gruppens [Azure-regions tid](https://go.microsoft.com/fwlink/?linkid=2143646). Om du vill anpassa det här schemat väljer du **anpassat schema**. Du kan sedan välja en önskad dag i veckan och ett 30-minuters fönster för underhållets start tid.

## <a name="notifications-about-scheduled-maintenance-events"></a>Meddelanden om schemalagda underhålls händelser

Du kan använda Azure Service Health om du vill [Visa meddelanden](../service-health/service-notifications.md) om kommande och tidigare schemalagda underhåll på din citus-Server grupp. Du kan också [ställa in](../service-health/resource-health-alert-monitor-guide.md) aviseringar i Azure Service Health för att få meddelanden om underhålls händelser.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [schemalagt underhåll i Azure Database for PostgreSQL – storskalig (citus)](concepts-hyperscale-maintenance.md)
* Lean- [Azure Service Health](../service-health/overview.md)
