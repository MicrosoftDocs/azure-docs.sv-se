---
title: Vad är blockchain Data Manager för Azure blockchain-tjänsten
description: Blockchain Data Manager för att avbilda, transformera och leverera blockchain-data till Event Grid ämnen.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: chroyal
ms.openlocfilehash: 78c93880007259267b26cf53e93de722be1c7323
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77209451"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Vad är Blockchain Data Manager för Azure Blockchain Service?

Blockchain Data Manager fångar, transformerar och levererar Azure blockchain-tjänstens transaktions data till Azure Event Grid ämnen som tillhandahåller tillförlitlig och skalbar integrering av blockchain-redovisning med Azure-tjänster.

I de flesta scenarier med företags blockchain är en blockchain-redovisning en del av en lösning. Om du till exempel vill överföra en till gång från en entitet till en annan, behöver du en mekanism för att skicka transaktionen. Sedan behöver du en mekanism för att läsa redovisnings data för att se till att transaktionen genomfördes, accepterades och de resulterande tillstånds ändringarna sedan integreras med din kompletta lösning. I det här exemplet kan du, om du skriver ett smart kontrakt för att överföra till gångar, använda blockchain Data Manager för att integrera program i andra kedjan och data lager. För till gångs överförings exemplet levereras när en till gång överförs till blockchain, och ändringar av händelser och egenskaps status levereras av blockchain Data Manager via Event Grid. Du kan sedan använda flera möjliga händelse hanterare för Event Grid för att lagra blockchain data från kedjan eller reagera på status ändringar i real tid.

Blockchain Data Manager utför tre huvud funktioner: fånga, transformera och leverera.

![Blockchain Data Manager funktioner](./media/data-manager/functions.png)

## <a name="capture"></a>Capture

Varje blockchain Data Manager-instans ansluter till en Azure blockchain service member Transaction-nod. Endast användare med åtkomst till Transaction-noden kan skapa en anslutning som garanterar korrekt åtkomst kontroll till kund information. En blockchain Data Manager instansen fångar tillförlitligt alla rå block-och rå transaktions data från Transaction-noden och kan skalas för att stödja företags arbets belastningar.

## <a name="transform"></a>Transformering

Du kan använda blockchain Data Manager för att avkoda händelse-och egenskaps tillstånd genom att konfigurera smarta kontrakt program i blockchain Data Manager. Om du vill lägga till ett smart kontrakt anger du kontrakt ABI och bytekod. Blockchain Data Manager använder de smarta kontrakts artefakterna för att avkoda och identifiera kontrakts adresser. När du har lagt till blockchain-programmet till instansen upptäcker blockchain Data Manager dynamiskt adressen när det smarta kontraktet distribueras till konsortiet och skickar avkodad händelse-och egenskaps status till konfigurerade mål.

## <a name="deliver"></a>Leverera

Blockchain Data Manager har stöd för flera Event Grid ämnen utgående anslutningar för en specifik blockchain Data Manager-instans. Du kan skicka blockchain-data till ett enda mål eller skicka blockchain-data till flera mål. Med blockchain Data Manager kan du bygga en skalbar händelse baserad data publicerings lösning för alla blockchain-distributioner.

## <a name="configuration-options"></a>Konfigurationsalternativ

Du kan konfigurera blockchain-Data Manager för att uppfylla behoven i din lösning. Du kan till exempel etablera:

* En enskild blockchain Data Manager instans för en Azure blockchain-tjänst medlem.
* En blockchain Data Manager instans per Azure blockchain service Transaction-nod. Till exempel kan privata transaktions noder ha sina egna blockchain-Data Manager-instanser för att bibehålla konfidentialitet.
* En blockchain Data Manager instans kan stödja flera utgående anslutningar. En blockchain Data Manager-instans kan användas för att hantera alla integrerings platser för data publicering för en Azure blockchain service-medlem.

## <a name="next-steps"></a>Nästa steg

Försök att [skapa en Blockchain Data Manager instans](data-manager-portal.md) för en Azure blockchain-tjänst medlem.
