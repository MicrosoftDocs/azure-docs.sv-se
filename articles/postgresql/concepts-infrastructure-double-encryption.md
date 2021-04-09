---
title: Infrastruktur dubbel kryptering – Azure Database for PostgreSQL
description: Lär dig mer om att använda Double Infrastructure Encryption för att lägga till ett andra lager av kryptering med en tjänst hanterade nycklar.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 83635b732318a4ada76d1d71c1ce419cae8b35e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98630154"
---
# <a name="azure-database-for-postgresql-infrastructure-double-encryption"></a>Azure Database for PostgreSQL infrastruktur Double Encryption

Azure Database for PostgreSQL använder lagrings [kryptering av data i vila](concepts-security.md#at-rest) för data med hjälp av Microsofts hanterade nycklar. Data, inklusive säkerhets kopior, krypteras på disk och den här krypteringen är alltid aktive rad och kan inte inaktive ras. Krypteringen använder FIPS 140-2-validerad kryptografisk modul och ett AES 256-bitars chiffer för Azure Storage-kryptering.

Infrastrukturens dubbla kryptering lägger till ett andra lager av kryptering med hjälp av tjänstens hanterade nycklar. Den använder FIPS 140-2-verifierade kryptografiska modulen, men med en annan krypteringsalgoritm. Detta ger ytterligare ett skydds lager för dina data i vila. Den nyckel som används i infrastrukturens dubbla kryptering hanteras också av tjänsten Azure Database for PostgreSQL. Infrastruktur Double Encryption är inte aktiverat som standard eftersom det ytterligare lagret av kryptering kan påverka prestandan.

> [!NOTE]
> Den här funktionen stöds bara för pris nivåer för "Generell användning" och "Minnesoptimerade" i Azure Database for PostgreSQL.

Kryptering av infrastruktur lager har fördelen att implementeras på det lager som ligger närmast lagrings enheten eller nätverks kabeln. Azure Database for PostgreSQL implementerar två krypterings lager med hjälp av tjänstens hanterade nycklar. Även om det fortfarande är tekniskt i tjänst lagret, är det mycket nära maskin vara som lagrar data i vila. Du kan även aktivera data kryptering i vila med hjälp av [kundhanterad nyckel](concepts-data-encryption-postgresql.md) för den etablerade postgresql-servern.  

Implementering på infrastruktur lager har också stöd för en mångfald av nycklar. Infrastrukturen måste vara medveten om olika kluster för datorer och nätverk. Därför används olika nycklar för att minimera den förstärkta radien för infrastruktur attacker och en rad olika maskin-och nätverks problem. 

> [!NOTE]
> Att använda dubbel kryptering i infrastrukturen påverkar prestanda på den Azure Database for PostgreSQL servern på grund av den ytterligare krypterings processen.

## <a name="benefits"></a>Fördelar

Infrastruktur Double Encryption för Azure Database for PostgreSQL ger följande fördelar:

1. **Ytterligare skillnader i kryptografi implementering** – den planerade flytten till maskinvarubaserad kryptering kommer ytterligare att öka implementeringarna genom att tillhandahålla en maskinvarubaserad implementering utöver den programvarubaserad implementeringen.
2. **Implementerings fel** – två lager av kryptering i infrastruktur lager skyddar mot fel i cachelagring eller minnes hantering i högre skikt som exponerar oformaterade data. Dessutom säkerställer de två lagren även fel i implementeringen av krypteringen i allmänhet.

Kombinationen av dessa ger starkt skydd mot vanliga hot och svagheter som används för att attackera kryptografi.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Scenarier som stöds med dubbel kryptering i infrastrukturen

De krypterings funktioner som tillhandahålls av Azure Database for PostgreSQL kan användas tillsammans. Nedan visas en sammanfattning av de olika scenarier som du kan använda:

|  ##   | Standard kryptering | Dubbel infrastrukturkryptering | Data kryptering med Kundhanterade nycklar  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Ja*              | *Nej*                             | *Nej*                                         |
| 2     | *Ja*              | *Ja*                            | *Nej*                                         |
| 3     | *Ja*              | *Nej*                             | *Ja*                                        |
| 4     | *Ja*              | *Ja*                            | *Ja*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - Scenario 2 och 4 kommer att påverka prestandan på den Azure Database for PostgreSQL servern på grund av det extra lagret av infrastruktur kryptering.
> - Det går bara att konfigurera infrastruktur med dubbel kryptering för Azure Database for PostgreSQL när servern skapas. När servern har allokerats kan du inte ändra lagrings krypteringen. Du kan dock fortfarande aktivera data kryptering med Kundhanterade nycklar för servern som skapats med/utan dubbel kryptering i infrastrukturen.

## <a name="limitations"></a>Begränsningar

För Azure Database for PostgreSQL har stöd för infrastruktur med dubbel kryptering med hjälp av tjänstens hanterade nycklar följande begränsningar:

* Stöd för den här funktionen är begränsat till **generell användning** och **minnesoptimerade pris nivåer** .
* Den här funktionen stöds bara i regioner och servrar som har stöd för lagring upp till 16 TB. För en lista över Azure-regioner som stöder lagring upp till 16 TB, se [lagrings dokumentationen](concepts-pricing-tiers.md#storage).

    > [!NOTE]
    > - Alla **nya** postgresql-servrar som skapats i de regioner som anges ovan har även stöd för data kryptering med kund Manager-nycklar. I det här fallet kvalificeras inte servrar som skapats genom återställning av tidpunkt (PITR) eller Läs repliker som "nya".
    > - Om du vill verifiera att den etablerade servern har stöd för upp till 16 TB kan du gå till bladet pris nivå i portalen och se om skjutreglaget för lagring kan flyttas upp till 16 TB. Om du bara kan flytta skjutreglaget upp till 4 TB kanske servern inte stöder kryptering med kundens hanterade nycklar. men data krypteras med hjälp av tjänst hanterade nycklar hela tiden. Kontakta AskAzureDBforPostgreSQL@service.microsoft.com om du har några frågor.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar infrastruktur Double Encryption för Azure Database for PostgreSQL](howto-double-encryption.md).
