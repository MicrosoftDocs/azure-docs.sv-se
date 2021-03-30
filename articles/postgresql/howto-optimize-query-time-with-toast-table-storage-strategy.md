---
title: Optimera fråge tiden med hjälp av popup-tabellen lagrings strategi i Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du optimerar fråge tiden med en lagrings strategi för popup-tabeller på en Azure Database for PostgreSQL-enskild server.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86116190"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Optimera fråge tiden med popup-tabellens lagrings strategi 
Den här artikeln beskriver hur du optimerar fråge tider med lagrings strategin för den överändrade-attributets lagrings teknik (popup).

## <a name="toast-table-storage-strategies"></a>Lagrings strategier för popup-tabeller
Fyra olika strategier används för att lagra kolumner på disk som kan använda popup. De representerar olika kombinationer av komprimering och out-of-line-lagring. Strategin kan ställas in på nivån data typ och på kolumn nivå.
- **Plain** förhindrar antingen komprimering eller out-of-line-lagring. Det inaktiverar användningen av huvuden med enkel byte för varlena-typer. Plain är den enda möjliga strategin för kolumner med data typer som inte kan använda popup.
- **Utökad** tillåter både komprimering och out-of-line-lagring. Utökad är standardinställningen för de flesta data typer som kan använda popup. Komprimeringen görs först. Färdiga lagrings försök görs om raden fortfarande är för stor.
- **Externa** tillåter direkt lagring men inte komprimering. Användningen av external gör under Strängs åtgärder på bred text och bytea kolumner snabbare. Den här hastigheten kommer i takt med att det ökade lagrings utrymmet. De här åtgärderna är optimerade för att bara hämta de nödvändiga delarna av det färdiga rad värdet när det inte är komprimerat.
- **Main** tillåter komprimering men inte out-of-line-lagring. Direkt lagring utförs fortfarande för sådana kolumner, men endast som en sista utväg. Det inträffar när det inte finns något annat sätt att göra raden tillräckligt liten för att få plats på en sida.

## <a name="use-toast-table-storage-strategies"></a>Använda lagrings strategier för popup-tabeller
Om dina frågor har åtkomst till data typer som kan använda popup, bör du överväga att använda huvud strategin i stället för det utökade standard alternativet för att minska fråge tiderna. Main regelerar inte out-of-line-lagring. Om dina frågor inte har åtkomst till data typer som kan använda popup, kan det vara bra att behålla det utökade alternativet. En större del av raderna i huvud tabellen får plats i den delade buffertens cacheminne, vilket ger prestanda.

Om du har en arbets belastning som använder ett schema med breda tabeller och höga antal bokstäver, bör du överväga att använda PostgreSQL popup-tabeller. Ett exempel på en kund tabell hade fler än 350 kolumner med flera kolumner som sträcker sig över 255 tecken. När den har konverterats till huvud strategin för popup-tabellen minskade deras benchmark-fråga från 4203 sekunder till 467 sekunder. Det är en förbättring på 89 procent.

## <a name="next-steps"></a>Nästa steg
Granska din arbets belastning för tidigare egenskaper. 

Läs följande dokumentation om PostgreSQL: 
- [KAPITEL 68, fysisk lagring i databasen](https://www.postgresql.org/docs/current/storage-toast.html) 