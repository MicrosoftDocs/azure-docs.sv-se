---
title: Introduktion till Azure Web Application-brandvägg
description: Den här artikeln innehåller en översikt över brand väggen för Azure Web Application (WAF)
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/18/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 49024c86d09f5cdd9e8b04d5a49f60021660b0c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "79475795"
---
# <a name="what-is-azure-web-application-firewall"></a>Vad är Azure-brandväggen för webbaserade program?

Brandväggen för webbaserade program (WAF) tillhandahåller ett centraliserat skydd för dina webbprogram mot vanliga sårbarheter och hot. Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. SQL-inmatning och Cross-Site-skript är bland de vanligaste angrepp.

![Översikt över WAF](media/overview/wafoverview.png)

Det är svårt att förhindra sådana angrepp i program koden. Det kan kräva rigoröst underhåll, uppdatering och övervakning på flera skikt i programtopologin. En centraliserad brand vägg för webbaserade program gör säkerhets hanteringen mycket enklare. En WAF ger också program administratörer bättre garantier för skydd mot hot och intrång.

En WAF-lösning kan reagera på ett säkerhetshot snabbare genom att centralt korrigera en känd sårbarhet, i stället för att skydda varje enskilt webb program.

## <a name="supported-service"></a>Tjänst som stöds

WAF kan distribueras med Azure Application Gateway, Azure-dörren och Azure Content Delivery Network-tjänsten (CDN) från Microsoft. WAF på Azure CDN är för närvarande en offentlig för hands version.  WAF har funktioner som är anpassade för varje enskild tjänst. Mer information om WAF-funktioner för varje tjänst finns i översikten för varje tjänst.

## <a name="next-steps"></a>Nästa steg

- Mer information om brand vägg för webbaserade program på Application Gateway finns i [brand vägg för webbaserade program på Azure Application Gateway](./ag/ag-overview.md).
- Mer information om brand vägg för webbaserade program i Azures frontend-tjänst finns i [brand vägg för webbaserade program i Azures frontend-tjänst](./afds/afds-overview.md).
- Mer information om brand vägg för webbaserade program på Azure CDN-tjänsten finns i [brand vägg för webbaserade program på Azure CDN-tjänsten](./cdn/cdn-overview.md)
