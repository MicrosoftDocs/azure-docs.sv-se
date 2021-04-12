---
title: Hög tillgänglighet och haveriberedskap
description: Lär dig hur du utformar ditt batch-program för ett regionalt avbrott.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97831014"
---
# <a name="design-your-batch-application-for-high-availability"></a>Utforma ditt batch-program för hög tillgänglighet

Azure Batch är tillgängligt i alla Azure-regioner, men när ett batch-konto skapas måste det kopplas till en speciell region. Alla åtgärder för batch-kontot gäller sedan för den regionen. Till exempel skapas pooler och associerade virtuella datorer i samma region som batch-kontot.

När du skapar ett program som använder batch måste du fundera över möjligheten för batch som inte är tillgänglig i en region. Det går att stöta på en sällsynt situation där det är problem med regionen som helhet, hela batch-tjänsten i regionen eller ditt specifika batch-konto.

Om programmet eller lösningen som använder batch alltid måste vara tillgängliga, ska den vara utformad för att antingen redundansväxla till en annan region eller alltid ha arbets belastningen delad mellan två eller flera regioner. Båda metoderna kräver minst två batch-konton, där varje konto finns i en annan region.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Flera batch-konton i flera regioner

Om du använder flera batch-konton i olika regioner kan programmet fortsätta att köras om ett batch-konto i en region blir otillgängligt. Om ditt program behöver ha hög tillgänglighet, är det särskilt viktigt att ha flera konton.

I vissa fall kan program vara utformade för att avsiktligt använda två eller flera regioner. Om du till exempel behöver en stor mängd kapacitet kan du behöva använda flera regioner för att hantera antingen ett storskaligt program eller för framtida tillväxt. De här programmen kräver också flera batch-konton (ett per region används).

## <a name="design-considerations-for-providing-failover"></a>Design överväganden för att tillhandahålla redundans

När du ger möjlighet att redundansväxla till en alternativ region måste alla komponenter i en lösning beaktas. Det räcker inte bara att ha ett andra batch-konto. I de flesta batch-program krävs till exempel ett Azure Storage-konto, där lagrings kontot och batch-kontot måste finnas i samma region för acceptabla prestanda.

Tänk på följande när du skapar en lösning som kan redundansväxla:

- Skapa alla nödvändiga konton i varje region, till exempel batch-kontot och lagrings kontot. Det kostar ingenting att skapa konton och debiteras bara när kontot används eller när data lagras.
- Se till att lämpliga [kvoter](batch-quota-limit.md) är inställda på alla konton i förväg, så att du kan allokera antalet kärnor som krävs med batch-kontot.
- Använd mallar och/eller skript för att automatisera distributionen av programmet i en region.
- Se till att programmets binärfiler och referens data är aktuella i alla regioner. Om du är uppdaterad kommer regionen att bli online snabbt utan att behöva vänta på uppladdning och distribution av filer. Om till exempel ett anpassat program som ska installeras på pooler lagras och refereras till med batch-programpaket, och om en ny version av programmet skapas, ska den överföras till varje batch-konto och refereras till av konfigurationen för poolen (eller göra den nya versionen till standard versionen).
- I det program som anropar batch, Storage och andra tjänster, gör det enkelt att växla över klienter eller belastningen till olika regioner.
- Överväg ofta att växla över till en alternativ region som en del av normal drift. Till exempel, med två distributioner i olika regioner, byter du till den alternativa regionen varje månad.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att skapa batch-konton med [Azure Portal](batch-account-create-portal.md), [Azure CLI](./scripts/batch-cli-sample-create-account.md), [PowerShell](batch-powershell-cmdlets-get-started.md)eller [batch Management-API: et](batch-management-dotnet.md).
- Lär dig mer om [standard kvoterna som är associerade med ett batch-konto](batch-quota-limit.md) och hur kvoter kan ökas.
