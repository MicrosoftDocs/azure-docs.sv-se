---
title: Azure Key Vault säkerhetsvärldar | Microsoft Docs
description: Azure Key Vault är en tjänst för flera innehavare. Den använder en pool med HSM:er i varje Azure-region. Alla regioner i en geografisk region delar en kryptografisk gräns.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 0d82a3cb4c08d47b6827072378b9827037d32412
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751822"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Azure Key Vault säkerhetsvärldar och geografiska gränser

Azure-produkter är tillgängliga i ett antal [Geografiska områden i Azure,](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)där varje Azure-geografi innehåller en eller flera regioner. Till exempel innehåller geografin Europa två regioner – Europa, norra och Europa, västra – medan den enda regionen i brasiliens geografiska område är Brasilien, södra.

Azure Key Vault är en tjänst för flera innehavare som använder en pool med maskinvarusäkerhetsmoduler (HSM). Alla HSM:er i ett geografiskt område delar samma kryptografiska gräns, vilket kallas en "säkerhetsvärld". Varje geografiskt område motsvarar en enda säkerhetsvärld och vice versa.

USA, östra och USA, västra delar samma säkerhetsvärld eftersom de tillhör det geografiska USA). På samma sätt delar alla Azure-regioner i Japan samma säkerhetsvärld, precis som alla Azure-regioner i Australien och så vidare.

>[!NOTE]
> Ett undantag är att USA DOD, östra och USA DOD CENTRAL har sina egna säkerhetsvärldar.

## <a name="backup-and-restore-behavior"></a>Beteende för säkerhetskopiering och återställning

En säkerhetskopia som tas av en nyckel från ett nyckelvalv i en Azure-region kan återställas till ett nyckelvalv i en annan Azure-region, så länge båda dessa villkor är sanna:

- Båda Azure-regionerna tillhör samma geografiska område.
- Båda nyckelvalven tillhör samma Azure-prenumeration.

Till exempel kan en säkerhetskopia av en nyckel i ett nyckelvalv i Indien, västra återställas till ett annat nyckelvalv i samma prenumeration i geografin Indien (regionerna Indien, västra, Indien, centrala och Indien, södra).

## <a name="next-steps"></a>Nästa steg

- Se [Microsoft-produkter efter region](https://azure.microsoft.com/regions/services/)
