---
title: Tillfälliga anslutnings fel – Azure Database for MariaDB
description: Lär dig hur du hanterar tillfälliga anslutnings fel för Azure Database for MariaDB.
keywords: MySQL-anslutning, anslutnings sträng, anslutnings problem, tillfälligt fel, anslutnings fel
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 2a651d87411654f1a52c4a097f115ba848ce569c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660046"
---
# <a name="handling-of-transient-connectivity-errors-for-azure-database-for-mariadb"></a>Hantering av tillfälliga anslutnings fel för Azure Database for MariaDB

Den här artikeln beskriver hur du hanterar tillfälliga fel som ansluter till Azure Database for MariaDB.

## <a name="transient-errors"></a>Tillfälliga fel

Ett tillfälligt fel, även kallat ett tillfälligt fel, är ett fel som kommer att lösa sig självt. Oftast manifestet de här felen som en anslutning till databas servern som tappas bort. Det går inte att öppna nya anslutningar till en server. Tillfälliga fel kan uppstå när ett maskin-eller nätverks fel uppstår. En annan orsak kan vara en ny version av en PaaS-tjänst som distribueras. De flesta av dessa händelser begränsas automatiskt av systemet på mindre än 60 sekunder. En bra metod för att utforma och utveckla program i molnet är att vänta på tillfälliga fel. Anta att de kan ske i vilken komponent som helst och att ha rätt logik på plats för att hantera dessa situationer.

## <a name="handling-transient-errors"></a>Hantering av tillfälliga fel

Tillfälliga fel ska hanteras med logiken för omprövning. Situationer som måste beaktas:

* Ett fel inträffar när du försöker öppna en anslutning
* En inaktiv anslutning bryts på Server sidan. När du försöker utfärda ett kommando kan det inte köras
* En aktiv anslutning som för närvarande kör ett kommando ignoreras.

Det första och det andra fallet är ganska rakt framåt till referensen. Försök att öppna anslutningen igen. När du lyckas har det tillfälliga felet minimerats av systemet. Du kan använda din Azure Database for MariaDB igen. Vi rekommenderar att du väntar innan du försöker ansluta igen. Stäng av om de första Återförsöken inte fungerar. På så sätt kan systemet använda alla resurser som är tillgängliga för att lösa fel situationen. Ett lämpligt mönster att följa är:

* Vänta i 5 sekunder innan ditt första försök.
* För varje följande försök ökar vänte tiden exponentiellt, upp till 60 sekunder.
* Ange ett högsta antal återförsök som programmet anser att åtgärden misslyckades.

När en anslutning med en aktiv transaktion Miss lyckas är det svårare att hantera återställningen korrekt. Det finns två fall: om transaktionen var skrivskyddad, är det säkert att öppna anslutningen igen och försöka utföra transaktionen igen. Om transaktionen även skrevs till databasen, måste du bestämma om transaktionen återställdes eller om den lyckades innan det tillfälliga felet uppstod. I så fall kanske du inte har fått bekräftelse bekräftelse från databas servern.

Ett sätt att göra detta är att generera ett unikt ID på klienten som används för alla återförsök. Du skickar det här unika ID: t som en del av transaktionen till servern och lagrar den i en kolumn med en unik begränsning. På så sätt kan du på ett säkert sätt försöka utföra transaktionen igen. Det kommer att lyckas om den tidigare transaktionen återställs och det unika ID: t för klienten som genererats inte redan finns i systemet. Det går inte att ange en dubblett av nyckeln om det unika ID: t tidigare lagrades eftersom den tidigare transaktionen slutfördes.

När ditt program kommunicerar med Azure Database for MariaDB via mellanprodukter från tredje part, frågar du leverantören om mellanprodukter innehåller omprövnings logik för tillfälliga fel.

Se till att testa att du testar logiken igen. Försök till exempel att köra din kod medan du skalar upp eller ned beräknings resurserna för Azure Database for MariaDB-servern. Ditt program bör hantera de korta stillestånds tiden som påträffas under den här åtgärden utan problem.

## <a name="next-steps"></a>Nästa steg

* [Felsöka anslutningsproblem till Azure Database for MariaDB](howto-troubleshoot-common-connection-issues.md)
