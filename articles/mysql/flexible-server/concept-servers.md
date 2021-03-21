---
title: Server koncept – Azure Database for MySQL flexibel Server
description: Det här avsnittet innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL flexibel Server
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: b664dd406a1ab90b4ea5e85005a69935f345c609
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102034667"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Server koncept i Azure Database for MySQL flexibel Server (för hands version)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

Den här artikeln innehåller överväganden och rikt linjer för att arbeta med Azure Database for MySQL flexibla servrar.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Vad är en Azure Database for MySQL flexibel Server?

Azure Database for MySQL flexibel Server är en fullständigt hanterad databas tjänst som kör community-versionen av MySQL. I allmänhet är tjänsten utformad för att ge flexibilitet och konfigurations anpassningar baserat på användar kraven. Det är samma server konstruktion för MySQL som du kanske är van vid i den lokala världen. Mer specifikt hanteras den flexibla servern, ger dig till gång till prestanda, bättre Server hantering och kontroll och visar åtkomst och funktioner på server nivå.

En Azure Database for MySQL flexibel Server:

- Skapas i en Azure-prenumeration.
- Är överordnad resurs för databaser.
- Tillåter MySQL-konfiguration som exponeras via server parametrar (länk till Server parameter begrepp).
- Utför automatiserade säkerhets kopieringar och stöder återställning av tidpunkter.
- Innehåller ett namn område för databaser.
- Är en behållare med stark livs längd – ta bort en server och tar bort de inneslutna databaserna.
- Samlar resurser i en region.
- Stöd för kund tillhandahållet Server underhålls schema
- Möjlighet att distribuera flexibla servrar i en zon redundant installation för förbättrad hög tillgänglighet
- Tillhandahåller en virtuell nätverks integrering för databas Server åtkomst
- Ger möjlighet att spara kostnader genom att pausa den flexibla servern när den inte används
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna: inloggning, brand vägg, användare, roller, konfigurationer osv.
- Stöder huvud version MySQL 5,7 och MySQL 8,0. Mer information finns i [Azure Database for MySQL motor versioner som stöds](./../concepts-supported-versions.md).

I en Azure Database for MySQL flexibel Server kan du skapa en eller flera databaser. Du kan välja att skapa en enskild databas per server för att använda alla resurser eller för att skapa flera databaser för att dela resurserna. Priserna är strukturerad per server, baserat på konfiguration av beräknings nivå, virtuella kärnor och lagring (GB). Mer information finns i [beräkning och lagring](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Stoppa/starta en Azure Database for MySQL flexibel Server

Azure Database for MySQL flexibla servern ger dig möjlighet att **stoppa** servern när den inte används och **Starta** servern när du återupptar aktiviteten. Detta görs i grunden för att spara kostnaderna på databas servrarna och bara betala för resursen när den används. Detta blir ännu mer viktigt för arbets belastningar för utveckling och testning och när du bara använder servern för en del av dagen. När du stoppar servern kommer alla aktiva anslutningar att tas bort. Senare kan du antingen använda [Azure Portal](how-to-stop-start-server-portal.md) eller CLI när du vill ta tillbaka servern online.

När servern är i **stoppat** läge debiteras inte serverns beräkning. Lagrings utrymmet fortsätter dock att faktureras eftersom serverns lagrings utrymme är kvar för att säkerställa att datafiler är tillgängliga när servern startas igen.

> [!IMPORTANT]
> När du **stoppar** servern förblir den kvar i det läget under de kommande 7 dagarna i en storlek. Om du inte **startar** den manuellt under den här tiden startas servern automatiskt i slutet av sju dagar. Du kan välja att **stoppa** det igen om du inte använder servern.

När tids servern stoppas kan inga hanterings åtgärder utföras på servern. För att kunna ändra konfigurations inställningar på-servern måste du [starta-servern](how-to-stop-start-server-portal.md). Läs [begränsningarna för att stoppa/starta](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Hur gör jag för att hantera en server?

Du kan hantera Azure Database for MySQL flexibel server med hjälp av [Azure Portal](./quickstart-create-server-portal.md) eller [Azure CLI](./quickstart-create-server-cli.md).

## <a name="next-steps"></a>Nästa steg

-   Lär dig mer om att [skapa server](./quickstart-create-server-portal.md)
-   Läs mer om [övervakning och aviseringar](./how-to-alert-on-metric.md)

