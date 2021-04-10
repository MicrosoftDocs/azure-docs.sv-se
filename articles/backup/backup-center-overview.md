---
title: Översikt över säkerhets kopierings Center
description: Den här artikeln innehåller en översikt över backup Center för Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: b42bb2719d03108212f62428dc97ed814899c63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102506062"
---
# <a name="overview-of-backup-center"></a>Översikt över säkerhets kopierings Center

Backup Center ger en **enhetlig hanterings upplevelse** i Azure för företag för att styra, övervaka, hantera och analysera säkerhets kopior i stor skala. Därför är det konsekvent med Azures inbyggda hanterings upplevelser.

Några av de viktigaste fördelarna med säkerhets kopierings Center är:

* **En ruta med glas för att hantera säkerhets kopieringar** – backup Center har utformats för att fungera väl i en stor och distribuerad Azure-miljö. Du kan använda Backup Center för att effektivt hantera säkerhets kopior som sträcker sig över flera arbets belastnings typer, valv, prenumerationer, regioner och [Azure Lighthouse](../lighthouse/overview.md) -klienter.
* **Data Center-Centred Management** – säkerhets kopierings Center innehåller vyer och filter som är centrerade på de data källor som du säkerhetskopierar (till exempel virtuella datorer och databaser). På så sätt kan en resurs ägare eller en säkerhets kopierings administratör övervaka och köra säkerhets kopieringar av objekt utan att behöva fokusera på vilket valv ett objekt säkerhets kopie ras till. En viktig funktion i den här designen är möjligheten att filtrera vyer efter data källans egenskaper, t. ex. DataSource-prenumeration, resurs grupp för data källa och DataSource-taggar. Om din organisation exempelvis följer en metod för att tilldela virtuella datorer som tillhör olika avdelningar, kan du använda Backup Center för att filtrera säkerhets kopierings information baserat på taggarna för de underliggande virtuella datorerna som säkerhets kopie ras utan att behöva fokusera på taggen i valvet.
* **Anslutna upplevelser** – backup Center tillhandahåller interna integreringar för befintliga Azure-tjänster som möjliggör hantering i stor skala. Till exempel använder backup Center den [Azure Policya](../governance/policy/overview.md) upplevelsen för att hjälpa dig att styra dina säkerhets kopior. Det utnyttjar också [Azure-arbetsböcker](../azure-monitor/visualize/workbooks-overview.md) och [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md) som hjälper dig att visa detaljerade rapporter om säkerhets kopior. Så du behöver inte lära dig några nya principer för att använda de varierande funktionerna som finns i Backup Center. Du kan också identifiera community-resurser från säkerhets kopierings centret.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Backup Center stöds för närvarande för säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, SAP HANA i säkerhets kopiering av virtuella Azure-datorer, Azure Files säkerhets kopiering, Azure blobbar, Azure-Managed Disks säkerhets kopiering och Azure Database for PostgreSQL säkerhets kopiering av servern.
* Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="get-started"></a>Kom igång

Kom igång med att använda Backup Center genom att söka efter **Backup Center** i Azure Portal och gå till instrument panelen för **säkerhets kopierings Center** .

![Sök Center för säkerhets kopiering](./media/backup-center-overview/backup-center-search.png)

Den första skärmen som visas är **översikten**. Den innehåller två paneler – **jobb** och **säkerhets kopierings instanser**.

![Panel för säkerhets kopierings Center](./media/backup-center-overview/backup-center-overview-widgets.png)

På panelen **jobb** får du en sammanfattande vy över alla säkerhets kopierings-och återställnings jobb som har utlösts i reserv fastigheten under de senaste 24 timmarna. Du kan visa information om antalet jobb som har slutförts, misslyckats och pågår. Genom att välja något av talen på den här panelen kan du Visa mer information om jobb för en viss typ av data källa, åtgärds typ och status.

På panelen **säkerhets kopierings instanser** får du en sammanfattande vy över alla säkerhets kopierings instanser i reserv egendomen. Du kan till exempel se antalet säkerhets kopierings instanser som är i läget Soft Deleted jämfört med antalet instanser som fortfarande har kon figurer ATS för skydd. Genom att välja något av talen på den här panelen kan du Visa mer information om säkerhets kopierings instanser för en viss typ av data källa och skydds tillstånd. Du kan också Visa alla säkerhets kopierings instanser vars underliggande data källa inte hittas (data källan kan ha tagits bort eller så har du inte åtkomst till data källan).

Titta på följande video för att förstå funktionerna i säkerhets kopierings Center:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Följ [Nästa steg](#next-steps) för att förstå de olika funktionerna som finns i Backup Center och hur du kan använda dessa funktioner för att hantera reserv fastigheten på ett effektivt sätt.

## <a name="next-steps"></a>Nästa steg

* [Övervaka och hantera säkerhets kopior](backup-center-monitor-operate.md)
* [Styr din reserv egendom](backup-center-govern-environment.md)
* [Få insikter om dina säkerhets kopior](backup-center-obtain-insights.md)
* [Utföra åtgärder med Backup Center](backup-center-actions.md)