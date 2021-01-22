---
title: Avancerat skydd – Azure Database for MariaDB
description: Avancerat skydd identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 98eacad1b8a10be175846401eaa733c888f765e1
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661627"
---
# <a name="azure-database-for-mariadb-advanced-threat-protection"></a>Azure Database for MariaDB Avancerat skydd

Avancerat skydd för Azure Database for MariaDB identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

> [!IMPORTANT]
> Avancerat skydd är i offentlig för hands version.

Avancerat skydd är en del av det avancerade data säkerhets erbjudandet, som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via [Azure Portal](https://portal.azure.com). Funktionen är tillgänglig för Generell användning och minnesoptimerade servrar.

> [!NOTE]
> Funktionen för avancerat skydd är **inte** tillgänglig i följande Azure-myndigheter och suveräna moln regioner: US Gov, Texas, US Gov, Arizona, US gov, IOWA, US, Gov Virginia, US DoD, östra, US DoD, centrala, Tyskland Central, Tyskland, norra, Kina, östra, Kina, östra 2. Besök [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) om du vill ha allmän produkt tillgänglighet.


## <a name="what-is-advanced-threat-protection"></a>Vad är Avancerat skydd?

Avancerat skydd för Azure Database for MariaDB ger ett nytt säkerhets lager som gör det möjligt för kunder att identifiera och svara på potentiella hot när de inträffar genom att tillhandahålla säkerhets aviseringar om avvikande aktiviteter. Användare får en avisering om misstänkt databas aktiviteter och potentiella sårbarheter, samt mönster för avvikande databas åtkomst och frågor. Avancerat skydd för Azure Database for MariaDB integrerar aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/), som innehåller information om misstänkt aktivitet och rekommenderar åtgärder för att undersöka och minimera hotet. Avancerat skydd för Azure Database for MariaDB gör det enkelt att hantera potentiella hot mot databasen utan att behöva vara säkerhets expert eller hantera avancerade säkerhets övervaknings system. 

![Koncept för avancerat skydd](media/concepts-data-access-and-security-threat-protection/advanced-threat-protection-concept.png)

## <a name="advanced-threat-protection-alerts"></a>Aviseringar för avancerat skydd 
Avancerat skydd för Azure Database for MariaDB identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser och kan utlösa följande aviseringar:
- **Åtkomst från ovanlig plats**: den här aviseringen utlöses när åtkomst mönstret ändras till Azure Database for MariaDB-servern, där någon har loggat in på Azure Database for MariaDB servern från en ovanlig geografisk plats. I vissa fall identifierar aviseringen en giltig åtgärd (ett nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Åtkomst från ovanliga Azure-datacenter**: den här aviseringen utlöses när åtkomst mönstret ändras till Azure Database for MariaDB-servern, där någon har loggat in på servern från ett ovanligt Azure-datacenter som setts på den här servern under den senaste perioden. I vissa fall identifierar aviseringen en legitim åtgärd (ditt nya program i Azure, Power BI). I andra fall identifierar aviseringen en skadlig åtgärd från en Azure-resurs/-tjänst (tidigare anställd, extern angripare).
- **Åtkomst från okända huvud objekt**: den här aviseringen utlöses när åtkomst mönstret ändras till Azure Database for MariaDB-servern, där någon har loggat in på servern med en ovanlig huvud server (Azure Database for MariaDB användare). I vissa fall identifierar aviseringen en giltig åtgärd (nytt program eller nytt underhåll av utvecklare). I andra fall identifierar aviseringen en skadlig åtgärd (tidigare anställd, extern angripare).
- **Access from a potentially harmful application** (Åtkomst från ett potentiellt skadligt program): Den här aviseringen utlöses när ett potentiellt skadligt program används för att få åtkomst till databasen. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen en attack med vanliga angreppsverktyg.
- **Brute force Azure Database for MariaDB autentiseringsuppgifter**: den här aviseringen utlöses när det finns ett onormalt stort antal misslyckade inloggningar med andra autentiseringsuppgifter. I vissa fall identifierar aviseringen pågående intrångstestning. I andra fall identifierar aviseringen ett nyckelsökningsangrepp.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](../security-center/security-center-introduction.md)
* Mer information om priser finns på sidan med [Azure Database for MariaDB priser](https://azure.microsoft.com/pricing/details/mariadb/) 
* Konfigurera [Azure Database for MariaDB Avancerat skydd](howto-database-threat-protection-portal.md) med hjälp av Azure Portal