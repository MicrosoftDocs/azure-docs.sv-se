---
title: För hands version av Basic-nivå – storskalig (citus)-Azure Database for PostgreSQL
description: Basic-nivån för en nod för Azure Database for PostgreSQL-storskalig (citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c8cad5eb0983715a7cc7c18249243e93a2c498d7
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024048"
---
# <a name="basic-tier-preview"></a>Basic-nivå (för hands version)

> [!IMPORTANT]
> Nivån för den storskaliga (citus) Basic-nivån är för närvarande en för hands version.  Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Basic-nivån i Azure Database for PostgreSQL-storskalig (citus) är ett enkelt sätt att skapa en liten Server grupp som du kan skala senare. Även om Server grupper på standard nivån har en koordinator-nod och minst två arbetsnoder, Kör Basic-nivån allt i en enda databas-nod.

Förutom att använda färre noder har Basic-nivån alla funktioner i standard-nivån. Precis som på standard nivån stöder den hög tillgänglighet, Läs repliker och tabell lagring med kolumn lagring, bland andra funktioner.

## <a name="choosing-basic-vs-standard-tier"></a>Välja Basic vs Standard-nivå

Basic-nivån kan vara ett ekonomiskt och användbart distributions alternativ för inledande utveckling, testning och kontinuerlig integrering. Den använder en enskild databas-nod och presenterar samma SQL-API som standard nivån. Du kan testa program med Basic-nivån och senare kan du kontrol lera att gränssnittet är detsamma på [Standard nivån](howto-hyperscale-scale-grow.md#add-worker-nodes) .

Basic-nivån är också lämplig för mindre arbets belastningar i produktionen (när den visas för förhands granskning i allmän tillgänglighet). Det finns utrymme att *skala lodrätt på nivån* Basic genom att öka antalet Server virtuella kärnor.

Använd standard nivån när du behöver en större skala direkt. Den minsta tillåtna Server gruppen har en koordinator nod och två arbetare. Du kan välja att använda fler noder baserat på din användnings fall, enligt beskrivningen i vår [inledande storleks](howto-hyperscale-scale-initial.md) beskrivning.

## <a name="next-steps"></a>Nästa steg

* Lär dig att [etablera Basic-nivån](quickstart-create-hyperscale-basic-tier.md)
* När du är klar kan du läsa mer i [så här](howto-hyperscale-scale-grow.md#add-worker-nodes) tar du en examen från Basic-nivån till standard nivån
* [Lagrings alternativet kolumner](concepts-hyperscale-columnar.md) är tillgängligt i både Basic-och standard-nivån
