---
title: Översikt över Azure Managed HSM – Azure Managed HSM | Microsoft Docs
description: Azure Managed HSM är en molntjänst som skyddar dina kryptografiska nycklar för molnprogram.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 04/01/2021
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: a9df83535bf0ee7a6c484a7682392d2ad0ef59f7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815332"
---
# <a name="what-is-azure-key-vault-managed-hsm"></a>Vad är Azure Key Vault Managed HSM?

Azure Key Vault Managed HSM är en fullständigt hanterad molntjänst som uppfyller standardkrav för en enskild klientorganisation och som gör att du kan skydda kryptografiska nycklar för dina molnprogram med **FIPS 140-2 Level 3-verifierade** HSM:er. Prisinformation finns i avsnittet Hanterade HSM-pooler [Azure Key Vault sidan med prisinformation.](https://azure.microsoft.com/pricing/details/key-vault/) 

## <a name="why-use-managed-hsm"></a>Varför ska jag använda Managed HSM?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Fullständigt hanterad HSM som en tjänst med hög tillgänglig en enda klientorganisation

- **Fullständigt hanterad:** HSM-etablering, konfiguration, uppdatering och underhåll hanteras av tjänsten. 
- **Högtillgänglig och zontålig** (där tillgänglighetszoner stöds): Varje HSM-kluster består av flera HSM-partitioner som sträcker sig över minst två tillgänglighetszoner. Om maskinvaran misslyckas migreras medlemspartitioner för ditt HSM-kluster automatiskt till felfria noder.
- **Enskild klientorganisation:** Varje hanterad HSM-instans är dedikerad till en enda kund och består av ett kluster med flera HSM-partitioner. Varje HSM-kluster använder en separat kundspecifik säkerhetsdomän som kryptografiskt isolerar varje kunds HSM-kluster.


### <a name="access-control-enhanced-data-protection--compliance"></a>Åtkomstkontroll, förbättrat dataskydd & efterlevnad

- **Centraliserad nyckelhantering:** Hantera kritiska nycklar med högt värde i organisationen på ett och samma ställe. Med detaljerade behörigheter per nyckel styr du åtkomsten till varje nyckel enligt principen "lägsta privilegierad åtkomst".
- **Isolerad åtkomstkontroll:** Den hanterade HSM-modellen för "lokal RBAC"-åtkomstkontroll gör att utsedda HSM-klusteradministratörer kan ha fullständig kontroll över HSM:erna som inte ens hanteringsgrupps-, prenumerations- eller resursgruppsadministratörer kan åsidosätta.
- **FIPS 140-2 Level 3-verifierade** HSM:er: Skydda dina data och uppfylla efterlevnadskraven med FIPS ((Federal Information Protection Standard)) 140-2 Level 3-verifierade HSM:er. Hanterade HSM:er använder Managed LiquidSecurity HSM-kort.
- **Övervaka och granska:** helt integrerad med Azure Monitor. Hämta fullständiga loggar för all aktivitet via Azure Monitor. Använd Azure Log Analytics för analys och aviseringar.
- **Datahemhemlighet:** Hanterad HSM lagrar/bearbetar inte kunddata utanför den region där kunden distribuerar HSM-instansen.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrerad med Azure- och Microsoft PaaS/SaaS-tjänster 

- Generera (eller importera med [BYOK)-nycklar](hsm-protected-keys-byok.md)och använd dem för att kryptera dina vilodata i Azure-tjänster som [Azure Storage,](../../storage/common/customer-managed-keys-overview.md) [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md) [och Azure Information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Använder samma API och hanteringsgränssnitt som Key Vault

- Migrera enkelt dina befintliga program som använder ett valv (en flera innehavare) för att använda hanterade HSM:er.
- Använd samma programutvecklings- och distributionsmönster för alla dina program oavsett vilken nyckelhanteringslösning som används: valv för flera innehavare eller hanterade HSM:er med en enda klientorganisation

### <a name="import-keys-from-your-on-premise-hsms"></a>Importera nycklar från dina lokala HSM:er

- Generera HSM-skyddade nycklar i din lokala HSM och importera dem på ett säkert sätt till Managed HSM

## <a name="next-steps"></a>Nästa steg
- Se [Snabbstart: Etablera och aktivera en hanterad HSM med Azure CLI för](quick-create-cli.md) att skapa och aktivera en hanterad HSM
- Se [Metodtips för att Azure Key Vault Managed HSM](best-practices.md)
