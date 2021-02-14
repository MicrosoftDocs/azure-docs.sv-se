---
title: Begränsningar för Azure Arc Enabled PostgreSQL-skalning
description: Begränsningar för Azure Arc Enabled PostgreSQL-skalning
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b1a56c8acf1789690c01f1c16b7c37a237720e39
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100418239"
---
# <a name="limitations-of-azure-arc-enabled-postgresql-hyperscale"></a>Begränsningar för Azure Arc Enabled PostgreSQL-skalning

I den här artikeln beskrivs begränsningar för Azure Arc-aktiverade PostgreSQL-skalning. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="backup-and-restore"></a>Säkerhetskopiering och återställning

- Tidpunkt för återställning (t. ex. återställning till visst datum och tid) till samma server grupp stöds inte. När du gör en återställning vid ett tillfälle måste du återställa till en annan server grupp som du har distribuerat innan du återställer. När du har återställt till den nya server gruppen kan du ta bort den ursprungliga Server gruppen.
- Att återställa hela innehållet i en säkerhets kopia (i stället för att återställa upp till en viss tidpunkt) till samma server grupp stöds för PostgreSQL version 12. Det finns inte stöd för PostgreSQL-version 11 på grund av en begränsning av PostgreSQL-motorn med tids linjer. Om du vill återställa hela innehållet i en säkerhets kopia för en PostgreSQL-Server grupp av version 11 måste du återställa den till en annan server grupp.


## <a name="databases"></a>Databaser

Det finns inte stöd för att vara värd för mer än en databas i en Server grupp.


## <a name="security"></a>Säkerhet

Hantering av användare och roller stöds inte. Fortsätt att använda postgres-standardanvändaren för tillfället.

## <a name="roles-and-responsibilities"></a>Roller och ansvarsområden

Rollerna och ansvars områdena mellan Microsoft och dess kunder skiljer sig åt mellan Azure PaaS Services (plattform som en tjänst) och Azure Hybrid (t. ex. Azure Arc Enabled PostgreSQL storskalig). 

### <a name="frequently-asked-questions"></a>Vanliga frågor och svar

I tabellen nedan sammanfattas svar på vanliga frågor om support roller och ansvars områden.

| Fråga                      | Azure-plattform som en tjänst (PaaS) | Azure Arc hybrid tjänster |
|:----------------------------------|:------------------------------------:|:---------------------------:|
| Vem tillhandahåller infrastrukturen?  | Microsoft                          | Kund                  |
| Vem tillhandahåller program varan? *       | Microsoft                          | Microsoft                 |
| Vilka åtgärder fungerar? | Microsoft                          | Kund                  |
| Tillhandahåller Microsoft service avtal?      | Ja                                | Inga                        |
| Vem är ansvarig för service avtal? | Microsoft                          | Kund                  |

\* Azure-tjänster

__Varför tillhandahåller inte Microsoft service avtal på Azure Arc Hybrid Services?__ Eftersom Microsoft inte äger infrastrukturen och inte använder den. Kunderna gör.

## <a name="next-steps"></a>Nästa steg

- **Prova.** Kom igång snabbt med [Azure Arc-rivstart med](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) på Azure Kubernetes service (AKS), AWS elastisk Kubernetes service (EKS), Google Cloud Kubernetes Engine (GKE) eller i en virtuell Azure-dator. 

- **Skapa en egen.** Följ de här stegen för att skapa på ditt eget Kubernetes-kluster: 
   1. [Installera klient verktyg](install-client-tools.md)
   2. [Skapa data styrenheten för Azure-bågen](create-data-controller.md)
   3. [Skapa en Azure Database for PostgreSQL storskalig Server grupp på Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Läs mer om Azure Arc-aktiverade data tjänster](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Läs om Azure-bågen](https://aka.ms/azurearc)
