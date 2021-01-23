---
title: Planera för Intel SGX-enclaves och attestering i Azure SQL Database
description: Planera distributionen av Always Encrypted med säkra enclaves i Azure SQL Database.
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
ms.openlocfilehash: 4448ce051b0c9e73865e8057cc4f224c9cbeb571
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98732752"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Planera för Intel SGX-enclaves och attestering i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Always Encrypted med säker enclaves för Azure SQL Database finns för närvarande i **offentlig för hands version**.

[Always Encrypted med säker enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) i Azure SQL Database använder [Intel SGX-Enclaves (Software Guard Extensions)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) och kräver [Microsoft Azure attestering](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Planera för Intel SGX i Azure SQL Database

Intel SGX är en maskinvarubaserad miljö för betrodd körnings miljö. Intel SGX är tillgänglig för databaser som använder [vCore-modellen](service-tiers-vcore.md) och maskin varu generering i [DC-serien](service-tiers-vcore.md?#dc-series) . För att se till att du kan använda Always Encrypted med säker enclaves i din databas, måste du antingen välja maskin varu generationens maskin varu generation när du skapar databasen, eller så kan du uppdatera den befintliga databasen för att använda maskin varu generationens maskin varu generation.

> [!NOTE]
> Intel SGX är inte tillgänglig i andra maskin varu generationer än DC-serien. Till exempel är Intel SGX inte tillgänglig för Gen5-maskinvara och är inte tillgänglig för databaser som använder DTU- [modellen](service-tiers-dtu.md).

> [!IMPORTANT]
> Innan du konfigurerar maskin varu generationens maskin varu generation för din databas, kontrol lera den regionala tillgängligheten för DC-serien och se till att du förstår dess prestanda begränsningar. Mer information finns i [DC-serien](service-tiers-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Planera för attestering i Azure SQL Database

[Microsoft Azure attestering](../../attestation/overview.md) (för hands version) är en lösning för att intyga betrodda körnings miljöer (tees), inklusive Intel SGX-Enclaves i Azure SQL-databaser med hjälp av maskin varu generering i DC-serien.

Om du vill använda Azure-attestering för att intyga Intel SGX-enclaves i Azure SQL Database måste du:

1. Skapa en [attesterings leverantör](../../attestation/basic-concepts.md#attestation-provider) och konfigurera den med en policy för attestering. 

2. Ge din logiska Azure SQL-Server åtkomst till den skapade providern för attestering.

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Roller och ansvars områden när du konfigurerar SGX-enclaves och attestering

Att konfigurera din miljö för att stödja Intel SGX-enclaves och attestering för Always Encrypted i Azure SQL Database innebär att konfigurera komponenter av olika typer: Microsoft Azure attestering, Azure SQL Database och program som utlöser enklaven-attestering. Att konfigurera komponenter av varje typ utförs av användare som antar en av följande distinkta roller:

- Attesterings administratör – skapar en attesterings-Provider i Microsoft Azure attestering, skapar attesterings principen, beviljar Azure SQL Logical Server åtkomst till attesteringsservern och delar den attesterings-URL som pekar på principen till program administratörer.
- Azure SQL Database-administratör – aktiverar SGX-enclaves i databaser genom att välja maskin varu generering i DC-serien och förser administratören med identiteten för den logiska Azure SQL-servern som behöver åtkomst till attesteringsservern.
- Program administratör – konfigurerar program med den attesterings-URL som erhållits från attesterings administratören.

I produktions miljöer (hantering av faktiska känsliga data) är det viktigt att din organisation följer separering av roller när du konfigurerar attestering, där varje enskild roll antas av olika personer. Om målet för att distribuera Always Encrypted i din organisation är att minska risken för attack yta genom att se till att Azure SQL Database administratörer inte kan komma åt känsliga data, Azure SQL Database administratörer inte heller att kontrol lera attesterings principer.

## <a name="next-steps"></a>Nästa steg

- [Aktivera Intel SGX för din Azure SQL-databas](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Se även

- [Självstudie: komma igång med Always Encrypted med säker enclaves i Azure SQL Database](always-encrypted-enclaves-getting-started.md)