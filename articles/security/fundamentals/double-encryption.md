---
title: Dubbel kryptering i Microsoft Azure
description: I den här artikeln beskrivs hur Microsoft Azure ger Double kryptering för data i vila och data under överföring.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88227319"
---
# <a name="double-encryption"></a>Dubbel kryptering
Dubbel kryptering är den plats där två eller flera oberoende lager av kryptering är aktiverade för att skydda mot kompromisser i ett enda krypterings lager. Genom att använda två lager av kryptering minimeras hot som medföljer kryptering av data. Exempel:

- Konfigurations fel i data krypteringen
- Implementerings fel i krypteringsalgoritmen
- Kompromettera en enskild krypterings nyckel

Azure tillhandahåller Double kryptering för data i vila och data under överföring.

## <a name="data-at-rest"></a>Vilande data
Microsofts metod för att aktivera två lager av kryptering för data i vila är:

- **Disk kryptering med Kundhanterade nycklar**. Du anger din egen nyckel för disk kryptering. Du kan ta med dina egna nycklar till din Key Vault (BYOK – Bring Your Own Key) eller generera nya nycklar i Azure Key Vault för att kryptera de önskade resurserna.
- **Infrastruktur kryptering med hjälp av plattforms hanterade nycklar**.  Som standard krypteras diskar automatiskt i vila med hjälp av plattforms hanterade krypterings nycklar.

## <a name="data-in-transit"></a>Data under överföring
Microsofts metod för att aktivera två lager av kryptering för data som överförs är:

- **Överföring av kryptering med hjälp av Transport Layer Security (TLS) 1,2 för att skydda data när de reser mellan moln tjänsterna och dig**. All trafik som lämnar ett Data Center krypteras under överföringen, även om trafik målet är en annan domänkontrollant i samma region. TLS 1,2 är det standard säkerhets protokoll som används. TLS ger stark autentisering, meddelande sekretess och integritet (aktiverar identifiering av meddelande manipulation, avlyssning och förfalskning), samverkan, algoritm flexibilitet och enkel distribution och användning.
- **Ytterligare krypterings lager som tillhandahålls i infrastruktur skiktet**. En krypterings metod för data länk skikt som använder IEEE 802.1 AE MAC Security Standards (kallas även MACsec) tillämpas från punkt-till-plats över den underliggande nätverks maskin varan. När Azure-kundtrafiken flyttas mellan data Center – utanför fysiska gränser som inte styrs av Microsoft (eller på uppdrag av Microsoft), krypteras och dekrypteras paketen på enheterna innan de skickas, vilket förhindrar att fysiska "man-in-the-The-The-The-on-the-wiretapping"-attacker används. Eftersom den här tekniken är integrerad i själva nätverks maskin varan tillhandahåller den en kryptering på nätverks maskin varan utan en mätbar fördröjning för länk fördröjning. Den här MACsec-krypteringen är aktive ras som standard för all Azure-trafik inom en region eller mellan regioner, och ingen åtgärd krävs för kundernas del att aktivera.

## <a name="next-steps"></a>Nästa steg
Lär dig hur [kryptering används i Azure](encryption-overview.md).
