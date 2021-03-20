---
title: Versioner som stöds – Azure Database for MySQL flexibel Server
description: Lär dig vilka versioner av MySQL-servern som stöds i den Azure Database for MySQL flexibla servern
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93242270"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Versioner som stöds för Azure Database for MySQL-flexibel Server


> [!IMPORTANT]
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.


Azure Database for MySQL flexibel Server drivs av [MySQL Community Edition](https://www.mysql.com/products/community/)med hjälp av InnoDB-motorn.

MySQL använder namngivnings schemat X. Y. Z. X är huvud versionen, Y är den lägre versionen och Z är den version av fel korrigeringen. Mer information om schemat finns i [MySQL-dokumentationen](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> Du kan ta reda på vilken version MySQL-serverinstansen har med hjälp av kommandot `SELECT VERSION();` i MySQL-prompten.

Azure Database for MySQL stöder för närvarande följande versioner:

## <a name="mysql-version-57"></a>MySQL version 5,7

Fel korrigerings version: 5.7.29

Tjänsten utför automatisk uppdatering av underliggande maskin vara, operativ system och databas motor. Korrigeringen innehåller säkerhets-och program uppdateringar. För MySQL-motorn ingår lägre versions uppgraderingar också som en del av den planerade underhålls versionen. Användare kan konfigurera uppdaterings schema som ska hanteras av systemet eller definiera deras anpassade schema. Under underhålls planen tillämpas korrigeringen och servern kan behöva starta om som en del av korrigerings processen för att slutföra uppdateringen. Med det anpassade schemat kan användarna göra sin förväntad korrigerings cykel och välja en underhålls period med minimal påverkan på verksamheten. I allmänhet följer tjänsten varje månads versions schema som en del av den kontinuerliga integreringen och versionen.

Se versions [anteckningar](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) för MySQL för att lära dig mer om förbättringar och korrigeringar i den här versionen.

## <a name="managing-updates-and-upgrades"></a>Hantera uppdateringar och uppgraderingar
Tjänsten hanterar automatiskt korrigeringar för fel korrigering av versions uppdateringar. Till exempel 5.7.29 till 5.7.30.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Bygg en PHP-app i Windows med MySQL](../../app-service/tutorial-php-mysql-app.md)<br/>
>[Bygga PHP-app på Linux med MySQL](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[Utveckla Java-baserad våren-app med MySQL](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>