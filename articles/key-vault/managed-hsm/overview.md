---
title: Översikt över Azure Managed HSM – Azure Managed HSM | Microsoft Docs
description: Azure Managed HSM är en moln tjänst som skyddar dina kryptografiska nycklar för moln program.
services: key-vault
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: overview
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
author: msmbaldwin
ms.openlocfilehash: 95feeebf009cae22cf3952df80596c12e75cbe4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357580"
---
# <a name="what-is-azure-key-vault-managed-hsm-preview"></a>Vad är Azure Key Vault hanterad HSM (för hands version)?

Azure Key Vault hanterad HSM är en fullständigt hanterad moln tjänst med hög tillgänglighet, en standard som är kompatibel med en enda klient, som gör att du kan skydda kryptografiska nycklar för dina moln program med hjälp av **FIPS 140-2 nivå 3** -verifierade HSM: er.  

## <a name="why-use-managed-hsm"></a>Varför ska jag använda hanterad HSM?

### <a name="fully-managed-highly-available-single-tenant-hsm-as-a-service"></a>Fullständigt hanterad HSM med enkel klient som en tjänst

- **Fullständigt hanterad**: HSM-etablering, konfiguration, uppdatering och underhåll hanteras av tjänsten. 
- **Hög tillgänglighet och zon elastisk** (där tillgänglighets zoner stöds): varje HSM-kluster består av flera HSM-partitioner som sträcker sig över minst två tillgänglighets zoner. Om maskin varan Miss lyckas migreras katalogpartitionerna för ditt HSM-kluster automatiskt till felfria noder.
- **Enskild klient**: varje hanterad HSM-instans är dedikerad till en enda kund och består av ett kluster med flera HSM-partitioner. Varje HSM-kluster använder en separat kundspecifik säkerhets domän som kryptografiskt isolerar varje kunds HSM-kluster.


### <a name="access-control-enhanced-data-protection--compliance"></a>Åtkomst kontroll, förbättrat data skydd & efterlevnad

- **Centraliserad nyckel hantering**: hantera viktiga, höga nycklar i hela organisationen på ett och samma ställe. Med detaljerad behörighet per nyckel kontrollerar du åtkomsten till varje nyckel i principen "minst privilegie rad åtkomst".
- **Isolerad åtkomst kontroll**: HANTERAd HSM-åtkomst kontroll modell gör att dedicerade HSM-kluster administratörer har fullständig kontroll över HSM: er som även hanterings gruppen, prenumerationen eller resurs gruppen administratörer inte kan åsidosätta.
- **Fips 140-2 nivå 3-verifierade HSM: er**: skydda dina data och uppfyller kraven för efterlevnad med FIPS ((Federal Information Protection standard)) 140-2 nivå 3, verifierade HSM: er. Hanterade HSM: er använder Marvell LiquidSecurity HSM-kort.
- **Övervaka och granska**: fullständigt integrerat med Azure Monitor. Hämta fullständiga loggar för all aktivitet via Azure Monitor. Använd Azure Log Analytics för analys och aviseringar.

### <a name="integrated-with-azure-and-microsoft-paassaas-services"></a>Integrerat med Azure och Microsoft PaaS/SaaS-tjänster 

- Generera (eller importera med [BYOK](hsm-protected-keys-byok.md)) nycklar och Använd dem för att kryptera dina data i andra Azure-tjänster, till exempel [Azure Storage](../../storage/common/customer-managed-keys-overview.md), [Azure SQL](../../azure-sql/database/transparent-data-encryption-byok-overview.md)och [Azure information Protection](/azure/information-protection/byok-price-restrictions).

### <a name="uses-same-api-and-management-interfaces-as-key-vault"></a>Använder samma API och hanterings gränssnitt som Key Vault

- Migrera enkelt dina befintliga program som använder ett valv (en flera innehavare) för att använda hanterade HSM: er.
- Använd samma program utvecklings-och distributions mönster för alla dina program oavsett nyckel hanterings lösning som används: flera klient valv eller hanterade HSM: er i en enda klient

### <a name="import-keys-from-your-on-premise-hsms"></a>Importera nycklar från din lokala HSM: er

- Generera HSM-skyddade nycklar i din lokala HSM och importera dem säkert till hanterad HSM

## <a name="next-steps"></a>Nästa steg
- Se [snabb start: etablera och aktivera en hanterad HSM med Azure CLI](quick-create-cli.md) för att skapa och aktivera en HANTERAd HSM
- Se [metod tips med Azure Key Vault hanterad HSM](best-practices.md)
