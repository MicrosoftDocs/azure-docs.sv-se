---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b7c097306ba46d4f0024aecc55994508e2d8a090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011770"
---
För att skydda data i dina Azure-filresurser mot data förlust eller skada, lagrar alla Azure-filresurser flera kopior av varje fil när de skrivs. Beroende på arbets Belastningens krav kan du välja ytterligare grad av redundans. Azure Files stöder för närvarande följande alternativ för redundans av data:

- **Lokalt redundant**: lokalt redundant lagring, som ofta kallas LRS, innebär att varje fil lagras tre gånger i ett Azure Storage-kluster. Detta skyddar mot data förlust på grund av maskin varu fel, till exempel en felaktig disk enhet.
- **Zon redundant**: zon redundant lagring, som ofta kallas ZRS, innebär att varje fil lagras tre gånger i tre olika Azure Storage-kluster. Precis som med lokalt redundant lagring ger zon redundansen tre kopior av varje fil, men dessa kopior är fysiskt isolerade i tre olika lagrings kluster i olika Azures *tillgänglighets zoner*. Tillgänglighets zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. En skrivning till lagring godkänns inte förrän den skrivs till lagrings klustren i alla tre tillgänglighets zoner. 
- **Geo-redundant** lagring: Geo-redundant lagring, som ofta kallas GRS, är som lokalt redundant lagring, i att en fil lagras tre gånger i ett Azure Storage-kluster i den primära regionen. Alla skrivningar replikeras sedan asynkront till en Microsoft-definierad sekundär region. Geo-redundant lagring tillhandahåller 6 kopior av data spridningen mellan två Azure-regioner. I händelse av en större katastrof, t. ex. den permanenta förlusten av en Azure-region på grund av en naturlig katastrof eller liknande händelse, kommer Microsoft att utföra en redundansväxling så att den sekundära är den primära, som betjänar alla åtgärder. Eftersom replikeringen mellan de primära och sekundära regionerna är asynkron, kommer data som ännu inte repliker ATS till den sekundära regionen att gå förlorade i händelse av en större katastrof. Du kan också utföra en manuell redundansväxling av ett Geo-redundant lagrings konto.
- **Redundant geo-zon**: redundant lagring med geo-zoner, som ofta kallas GZRS, är som zon redundant lagring, i att en fil lagras tre gånger i tre olika lagrings kluster i den primära regionen. Alla skrivningar replikeras sedan asynkront till en Microsoft-definierad sekundär region. Redundansväxlingen för geo-Zone-redundant lagring fungerar på samma sätt som för Geo-redundant lagring.

Standard Azure-filresurser stöder alla fyra typer av redundans, medan Premium Azure-filresurser endast stöder lokalt redundant och zon lagring.

GPv2-lagrings konton (General Purpose version 2) ger två ytterligare alternativ för redundans som inte stöds av Azure Files: Läs tillgängligt Geo-redundant lagrings utrymme, som ofta kallas RA-GRS och Läs tillgängligt geo-Zone-redundant lagring, ofta kallat RA-GZRS. Du kan etablera Azure-filresurser i lagrings konton med dessa alternativ inställda, men Azure Files stöder inte läsning från den sekundära regionen. Azure-filresurser som har distribuerats till Läs tillgängliga geo-eller geo-zoner-redundanta lagrings konton debiteras som Geo-redundant eller geo-zon-redundant lagring.