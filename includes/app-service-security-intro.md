---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/15/2020
ms.author: cephalin
ms.openlocfilehash: 0fccb1ed092190f88d197c9bf2cd17678a4d1731
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83649047"
---
Plattforms komponenterna i App Service, inklusive virtuella Azure-datorer, lagring, nätverks anslutningar, webb ramverk, hanterings-och integrations funktioner, är aktivt säkra och skärps. App Service genomgår en kontinuerlig kontroll av efterlevnaden för att se till att:

- Dina app-resurser [skyddas](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox) från de andra kundernas Azure-resurser.
- [VM-instanser och runtime-programvaran uppdateras regelbundet](../articles/app-service/overview-patch-os-runtime.md) för att lösa nyupptäckta sårbarheter. 
- Kommunikation av hemligheter (t. ex. anslutnings strängar) mellan din app och andra Azure-resurser (till exempel [SQL Database](https://azure.microsoft.com/services/sql-database/)) ligger kvar i Azure och korsar inte några nätverks gränser. Hemligheter krypteras alltid när de lagras.
- All kommunikation via App Service anslutnings funktioner, till exempel [hybrid anslutning](../articles/app-service/app-service-hybrid-connections.md), krypteras. 
- Anslutningar med verktyg för fjärrhantering som Azure PowerShell, Azure CLI, Azure SDK: er, REST API: er är krypterade.
- 24-timmars hot hantering skyddar infrastrukturen och plattformen mot skadlig kod, distribuerad denial-of-service (DDoS), man-in-the-Middle (MITM) och andra hot.

Mer information om infrastruktur-och plattforms säkerhet i Azure finns [Azure Säkerhetscenter](https://azure.microsoft.com/overview/trusted-cloud/).