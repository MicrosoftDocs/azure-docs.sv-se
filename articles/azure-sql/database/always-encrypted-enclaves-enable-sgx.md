---
title: Aktivera Intel SGX för din Azure SQL Database
description: Lär dig hur du aktiverar Intel SGX för Always Encrypted med säker enclaves i Azure SQL Database genom att välja en SGX-aktiverad maskin varu generering.
keywords: kryptera data, SQL-kryptering, databas kryptering, känsliga data, Always Encrypted, säker enclaves, SGX, attestering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 01/15/2021
ms.openlocfilehash: ded1406c47bb3f00c366da7a5b28319f3712f8a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98733764"
---
# <a name="enable-intel-sgx-for-your-azure-sql-database"></a>Aktivera Intel SGX för din Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted med säker enclaves för Azure SQL Database finns för närvarande i **offentlig för hands version**.

[Always Encrypted med säker enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) i Azure SQL Database använder [Intel Software Guard-tillägg (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) enclaves. För att Intel SGX ska vara tillgänglig måste-databasen använda [vCore-modellen](service-tiers-vcore.md) och maskin varu genereringen för [DC-serien](service-tiers-vcore.md#dc-series) .

Att konfigurera maskin varu generationens maskin varu generation för att aktivera Intel SGX-enclaves är Azure SQL Database administratörens ansvar. Se [roller och ansvars områden när du konfigurerar SGX-enclaves och attestering](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX är inte tillgänglig i andra maskin varu generationer än DC-serien. Till exempel är Intel SGX inte tillgänglig för Gen5-maskinvara och är inte tillgänglig för databaser som använder DTU- [modellen](service-tiers-dtu.md).

> [!IMPORTANT]
> Innan du konfigurerar maskin varu generationens maskin varu generation för din databas, kontrol lera den regionala tillgängligheten för DC-serien och se till att du förstår dess prestanda begränsningar. Mer information finns i [DC-serien](service-tiers-vcore.md#dc-series).

Detaljerade anvisningar för hur du konfigurerar en ny eller befintlig databas för att använda en speciell maskin varu generering finns i [välja en maskin varu generering](service-tiers-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Nästa steg

- [Konfigurera Azure-attestering för din Azure SQL Database-Server](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Se även

- [Självstudie: komma igång med Always Encrypted med säker enclaves i Azure SQL Database](always-encrypted-enclaves-getting-started.md)