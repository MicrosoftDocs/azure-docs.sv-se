---
title: Lagring på BareMetal för Oracle-arbetsbelastningar
description: Lär dig mer om lagring som erbjuds av BareMetal-infrastrukturen för Oracle-arbetsbelastningar.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 685f4d401d05d897b91ad12dbd79f7d175db05c6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559212"
---
# <a name="storage-on-baremetal-for-oracle-workloads"></a>Lagring på BareMetal för Oracle-arbetsbelastningar

I den här artikeln ger vi en översikt över lagringen som erbjuds av BareMetal Infrastructure för Oracle-arbetsbelastningar.

BareMetal Infrastructure for Oracle erbjuder lagring för NetApp Network File System (NFS). NFS-lagring kräver inte RAC-certifiering (Oracle Real Application Clusters). Mer information finns i [Oracle RAC Technologies Matrix for Linux Clusters (Matris för Oracle RAC-tekniker för Linux-kluster).](https://www.oracle.com/database/technologies/tech-generic-linux-new.html)

Det här lagringserbjudandet omfattar nivå 3-stöd från en OEM-partner som använder antingen A700- eller A800s-lagringsstyrenheter.

BareMetal Infrastructure Storage erbjuder följande premiumlagringsfunktioner:

- Lagringsvolymer för data/logg/kvorum/FSA erbjuds via dNFS-protokollet.
- Diskredundans (*Skydd mot upp till två diskfel*).
- Skala ut dina data till flera volymer som är begränsade till 100 TB per volym.
- Skala ut till flera lagringsstyrenheter upp till 12 styrenheter.
- Ingen hantering på disknivå *(lägg till/ta bort diskar)* tas automatiskt hand av Infra.
- Inget driftstopp för omfördelning av filinnehållet till olika volymer.
- Möjlighet att växa/krympa volymer.
- SnapCenter-integrering för säkerhetskopiering med hjälp av kloning och SnapVault.
- Datakryptering i vila med stöd för FIPS (140-2).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om korrigeringsöverväganden för BareMetal-infrastrukturen.

> [!div class="nextstepaction"]
> [Korrigeringsöverväganden för BareMetal för Oracle](oracle-baremetal-patching.md)

