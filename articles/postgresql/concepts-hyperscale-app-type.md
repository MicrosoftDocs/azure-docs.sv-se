---
title: Fastställa programType-Scale Scale (citus)-Azure Database for PostgreSQL
description: Identifiera ditt program för effektiv distribuerad data modellering
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90895959"
---
# <a name="determining-application-type"></a>Bestämmer program typ

Att köra effektiva frågor på en citus-servergrupp kräver att tabellerna distribueras på rätt sätt mellan servrar. Den rekommenderade distributionen varierar beroende på typ av program och dess fråge mönster.

Det finns många olika typer av program som fungerar bra på citus (i stor skala). Det första steget i data modellering är att identifiera vilket av dem som liknar ditt program.

## <a name="at-a-glance"></a>Snabbt

| Program med flera klienter                                 | Realtidsprogram                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| Ibland dussintals eller hundratals tabeller i ett schema          | Små antal tabeller                                |
| Frågor som rör en klient organisation (företag/butik) i taget | Relativt enkla analysfrågor med sammansättningar |
| OLTP-arbetsbelastningar för att betjäna webbklienter                    | Hög inmatningsvolym av främst oföränderliga data           |
| OLAP-arbetsbelastningar som betjänar analysfrågor per klient   | Centreras ofta runt stor händelsetabell            |

## <a name="examples-and-characteristics"></a>Exempel och egenskaper

**Program för flera innehavare**

> Detta är vanligt vis SaaS program som hanterar andra företag, konton eller organisationer. De flesta SaaS-program är i relation till varandra. De har en naturlig dimension som du kan använda för att distribuera data mellan noder: bara Shard efter klient \_ -ID.
>
> Med citus) kan du skala ut databasen till miljon tals klienter utan att behöva skapa en ny arkitektur för ditt program. Du kan behålla de relationella semantiker du behöver, t. ex. kopplingar, Foreign Key-begränsningar, transaktioner, syra och konsekvens.
>
> -   **Exempel**: webbplatser som är värdar för Store-front för andra företag, till exempel en digital marknadsförings lösning eller ett verktyg för sälj automatisering.
> -   **Egenskaper**: frågor som rör en enskild klient i stället för att koppla samman information mellan klienter. Detta inkluderar OLTP-arbetsbelastningar för att betjäna webb klienter och OLAP-arbetsbelastningar som betjänar analys frågor per klient. Att ha dussin tals eller hundratals tabeller i databasschemat är också en indikator för data modellen för flera innehavare.
>
> Om du skalar en app med flera klient organisationer med storskalig skalning (citus) krävs det också minimala ändringar i program koden. Vi har stöd för populära ramverk som Ruby on-räler och django.

**Analys i real tid**

> Program som behöver massiv parallellitet, koordinera hundratals kärnor för snabba resultat till numeriska, statistiska eller inventerings frågor.  Genom horisontell partitionering och parallellt av SQL-frågor över flera noder gör storskaliga (citus) det möjligt att utföra frågor i real tid över miljard tals poster i under en sekund.
>
> Tabeller i real tids analys data modeller distribueras vanligt vis av kolumner som användar \_ -ID, värd \_ -ID eller enhets \_ -ID.
>
> -   **Exempel**: instrument paneler för kund riktade analyser som kräver svars tider under en sekund.
> -   **Egenskaper**: få tabeller, som ofta centreras runt en stor tabell med enhets-, plats-eller användar händelser och kräver hög inmatnings volym av de mest oföränderliga data. Relativt enkla (men beräknings intensiva) analys frågor som omfattar flera agg regeringar och grupp genom.

Om din situation liknar ovanstående, är nästa steg att bestämma hur du ska Shard dina data i Server gruppen. Databas administratörens \' Val av distributions kolumner måste matcha åtkomst mönster för vanliga frågor för att säkerställa prestanda.

## <a name="next-steps"></a>Nästa steg

* [Välj en distributions kolumn](concepts-hyperscale-choose-distribution-column.md) för tabeller i ditt program för att distribuera data på ett effektivt sätt
