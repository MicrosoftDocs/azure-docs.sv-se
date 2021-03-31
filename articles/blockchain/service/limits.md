---
title: Service gränser i Azure blockchain
description: Översikt över tjänster och funktionella gränser i Azure blockchain-tjänsten
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "80676521"
---
# <a name="limits-in-azure-blockchain-service"></a>Gränser i Azure blockchain-tjänsten

Azure blockchain-tjänsten har tjänster och funktionella gränser, till exempel antalet noder som en medlem kan ha, konsortiets begränsningar och lagrings mängder.

## <a name="pricing-tier"></a>Prisnivå

De maximala gränserna för transaktioner och validator-noder beror på om du etablerar Azure blockchain service på Basic-eller standard pris nivån.

| Prisnivå | Max transaktions noder | Max verifierings-noder |
|:---|:---:|:---:|
| Grundläggande | 10 | 1 |
| Standard | 10 | 2 |

Ditt konsortiums nätverk bör ha minst två noder i Azure blockchain-tjänstens standard nivå. På standard-nivå-noderna finns två verifierade noder. Det krävs fyra validator-noder för att uppfylla den [Byzantine fel toleransen på Istanbul](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Använd Basic-nivån för utveckling, testning och bevis på koncept. Använd standard nivån för distributioner av produktions nivåer. Du bör också använda *standard* nivån om du använder blockchain Data Manager eller när du skickar en stor mängd privata transaktioner.

Det finns inte stöd för att ändra pris nivån mellan Basic och standard när medlems skapande har skapats.

## <a name="storage-capacity"></a>Lagringskapacitet

Den maximala mängden lagring som kan användas per nod för redovisnings data och loggar är 1,8 terabyte.

Det finns inte stöd för minskning av redovisning och logg lagrings storlek.
## <a name="consortium-limits"></a>Konsortial gränser

* **Konsortiet och medlems namn måste vara unika** för andra konsortier och medlems namn i Azure blockchain-tjänsten.

* **Det går inte att ändra namn på medlemmar och konsortier**

* **Alla medlemmar i ett konsortium måste vara på samma pris nivå**

* **Alla medlemmar som ingår i ett konsortium måste finnas i samma region**

    Den första medlemmen som skapas i ett konsortium bestämmer regionen. Inbjudna medlemmar till konsortiet måste finnas i samma region som den första medlemmen. Att begränsa alla medlemmar till samma region bidrar till att säkerställa att nätverks konsensus inte påverkas negativt.

* **Ett konsortium måste ha minst en administratör**

    Om det bara finns en administratör i ett konsortium, kan de inte ta bort sig själva från konsortiet eller ta bort sina medlemmar förrän en annan administratör läggs till eller höjs i konsortiet.

* **Medlemmar som har tagits bort från konsortiet kan inte läggas till igen**

    De måste i stället ombjudas till att ansluta till konsortiet och skapa en ny medlem. Deras befintliga medlems resurser tas inte bort för att bevara historiska transaktioner.

* **Alla medlemmar i ett konsortium måste använda samma redovisnings version**

    Mer information om korrigeringar, uppdateringar och redovisnings versioner som är tillgängliga i Azure blockchain-tjänsten finns i [korrigering, uppdateringar och versioner](ledger-versions.md).

## <a name="performance"></a>Prestanda

Använd inte *ETH. uppskatta* gasen för varje transaktions överföring. Funktionen *ETH. uppskattning* är minnes intensiv. Anrop av funktionen flera gånger minskar drastiskt transaktioner per sekund.

Använd om möjligt ett försiktigt gas värde för att skicka transaktioner och minimera användningen av *ETH. uppskattning*.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om principer för system uppdatering och uppgraderingar – [uppdatering, uppdateringar och versioner](ledger-versions.md).
