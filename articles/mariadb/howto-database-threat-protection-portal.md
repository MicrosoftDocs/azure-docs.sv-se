---
title: Avancerat skydd – Azure Portal-Azure Database for MariaDB
description: Skydd mot hot för Azure Database for MariaDB identifierar avvikande databas aktiviteter som indikerar potentiella säkerhetshot mot databasen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 7734feddabb1a4a86e7932da3ef4adc57352637e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665114"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Avancerat skydd för Azure Database for MariaDB

Avancerat skydd för Azure Database for MariaDB identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Avancerat skydd är en del av det avancerade data säkerhets erbjudandet, som är ett enhetligt paket för avancerade säkerhetsfunktioner. Avancerat skydd kan nås och hanteras via [Azure Portal](https://portal.azure.com).

> [!IMPORTANT]
> Avancerat skydd är i offentlig för hands version. Den här funktionen är tillgänglig i alla regioner i Azure där Azure Database for MariaDB distribueras för Generell användning och minnesoptimerade servrar.

> [!NOTE]
> Funktionen för avancerat skydd är **inte** tillgänglig i följande Azure-myndigheter och suveräna moln regioner: US Gov, Texas, US Gov, Arizona, US gov, IOWA, US, Gov Virginia, US DoD, östra, US DoD, centrala, Tyskland Central, Tyskland, norra, Kina, östra, Kina, östra 2. Besök [produkter som är tillgängliga efter region](https://azure.microsoft.com/global-infrastructure/services/) om du vill ha allmän produkt tillgänglighet.

## <a name="set-up-threat-detection"></a>Konfigurera hot identifiering
1. Starta Azure Portal på [https://portal.azure.com](https://portal.azure.com) .
2. Gå till konfigurations sidan för den Azure Database for MariaDB-server som du vill skydda. I säkerhets inställningarna väljer du **Avancerat skydd (för hands version)**.
3. På konfigurations sidan för **Advanced Threat Protection (för hands version)** :

   - Aktivera avancerat skydd på servern.
   - I **Inställningar för avancerat skydd** i rutan **skicka aviseringar till** text anger du en lista över e-postmeddelanden som ska ta emot säkerhets aviseringar vid identifiering av avvikande databas aktiviteter.
  
   ![Konfigurera hot identifiering](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Utforska avvikande databas aktiviteter

Du får ett e-postmeddelande när du har identifierat avvikande databas aktiviteter. E-postmeddelandet innehåller information om den misstänkta säkerhets händelsen, inklusive typen av avvikande aktiviteter, databasens namn, Server namn, program namn och händelse tiden. Dessutom innehåller e-postmeddelandet information om möjliga orsaker och rekommenderade åtgärder för att undersöka och minimera det potentiella hotet mot databasen.
 
1. Klicka på länken **Visa nya aviseringar** i e-postmeddelandet för att starta Azure Portal och visa sidan för Azure Security Center aviseringar, som ger en översikt över aktiva hot som har identifierats i SQL-databasen.
    
    ![Avvikande aktivitets rapport](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Visa aktiva hot:

    ![Aktiva hot](./media/howto-database-threat-protection-portal/active-threats.png)

2. Klicka på en avisering om du vill ha mer information och åtgärder för att undersöka det här hotet och åtgärda framtida hot.
    
    ![Speciell avisering](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Utforska aviseringar om hot identifiering

SQL Database hot identifiering integrerar sina aviseringar med [Azure Security Center](https://azure.microsoft.com/services/security-center/). En panel för SQL hot identifiering i databasen och SQL ATP-blad i Azure Portal spårar statusen för aktiva hot.

Klicka på **avisering om hot identifiering** för att starta sidan Azure Security Center aviseringar och få en översikt över aktiva SQL-hot som har identifierats i databasen.

   ![Avisering om hot identifiering](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Security Center](../security-center/security-center-introduction.md)
* Mer information om priser finns på sidan med [Azure Database for MariaDB priser](https://azure.microsoft.com/pricing/details/mariadb/)