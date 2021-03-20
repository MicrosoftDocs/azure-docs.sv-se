---
title: Azure Database for PostgreSQL-flexibel Server (för hands version) – schemalagt underhåll – Azure Portal
description: Lär dig hur du konfigurerar schemalagda underhålls inställningar för en Azure Database for PostgreSQL-flexibel Server från Azure Portal.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: be6040b8b84a4b86746d62bd2f1c07f0ffea0a3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91336300"
---
# <a name="manage-scheduled-maintenance-settings-for-azure-database-for-postgresql--flexible-server"></a>Hantera schemalagda underhålls inställningar för Azure Database for PostgreSQL – flexibel Server
 
Du kan ange underhålls alternativ för varje flexibel server i din Azure-prenumeration. Alternativen omfattar underhålls schema och meddelande inställningar för kommande och avslutade underhålls händelser.

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version.

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här instruktions guiden behöver du:
- En [Azure Database for PostgreSQL-flexibel Server](quickstart-create-server-portal.md)
 
## <a name="specify-maintenance-schedule-options"></a>Ange alternativ för underhålls schema
 
1. På sidan PostgreSQL-Server går du till rubriken **Inställningar** och väljer **Underhåll** för att öppna alternativ för schemalagda underhåll.
2. Standardschemat (system Managed) är en slumpmässig dag i veckan och 60-minuters period för underhålls start mellan 23:00 och 7am lokal server. Om du vill anpassa det här schemat väljer du **anpassat schema**. Du kan sedan välja en önskad dag i veckan och ett 60-minuters fönster för underhållets start tid.
 
## <a name="notifications-about-scheduled-maintenance-events"></a>Meddelanden om schemalagda underhålls händelser
 
Du kan använda Azure Service Health för att [Visa meddelanden](../../service-health/service-notifications.md) om kommande och utfört schemalagt underhåll på din flexibla Server. Du kan också [ställa in](../../service-health/resource-health-alert-monitor-guide.md) aviseringar i Azure Service Health för att få meddelanden om underhålls händelser.
 
## <a name="next-steps"></a>Nästa steg  
 
* Läs mer om [schemalagt underhåll i Azure Database for PostgreSQL – flexibel Server](concepts-maintenance.md)
* Lean- [Azure Service Health](../../service-health/overview.md)
