---
title: Vad är Azure Resource-arbetskraft?
description: Lär dig mer om Azure Resource-arbetskraft
author: rayne-wiselman
manager: evansma
ms.service: azure-migrate
ms.topic: overview
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 06d6352f018238318c3bb4625ae86a2974f14569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99820171"
---
# <a name="what-is-azure-resource-mover"></a>Vad är Azure Resource-arbetskraft?

Den här artikeln innehåller en översikt över tjänsten Azure Resource personal. Med resurs förflyttningen kan du flytta Azure-resurser mellan Azure-regioner.

Du kan flytta resurser till olika Azure-regioner till:

- **Justera till en regions lansering**: flytta resurser till en nyligen introducerad Azure-region som inte var tillgänglig tidigare.
- **Justera för tjänster/funktioner**: flytta resurser för att dra nytta av tjänster eller funktioner som är tillgängliga i en speciell region.
- **Svara på affärs utveckling**: flytta resurser till en region som svar på affärs ändringar, till exempel fusioner eller förvärv.
- **Justera mot närhet**: flytta resurser till en region som är lokal för din verksamhet.
- **Uppfylla data kraven**: flytta resurser för att anpassas efter data placering krav eller data klassificerings behov.
- **Svara på distributions krav**: flytta resurser som har distribuerats i fel eller flytta som svar på kapacitets behoven.
- **Svara på** inaktive ring: flytta resurser eftersom en region tas ur bruk.


## <a name="why-use-resource-mover"></a>Varför ska jag använda resurs förflyttning?

Resurs förflyttning ger:

- En enda hubb för att flytta resurser mellan regioner.
- Minskad flytt tid och komplexitet. Allt du behöver finns på en enda plats.
- En enkel och konsekvent upplevelse för att flytta olika typer av Azure-resurser.
- Ett enkelt sätt att identifiera beroenden för resurser som du vill flytta. Detta hjälper dig att flytta relaterade resurser tillsammans, så att allt fungerar som förväntat i mål regionen efter flytten.
- Automatisk rensning av resurser i käll regionen, om du vill ta bort dem efter flytten.
- Prestandatester. Du kan prova en flytt och sedan ta bort den om du inte vill göra en fullständig flytt.

## <a name="move-across-regions"></a>Flytta mellan regioner

Om du vill flytta resurser mellan regioner väljer du de resurser som du vill flytta. Resurs förflyttningen verifierar resurserna och matchar eventuella beroenden som de har på andra resurser. Om det finns beroenden har du ett par alternativ:
- Flytta de beroende resurserna till mål regionen.
- Flytta inte de beroende resurserna, men använd motsvarande resurser i mål regionen i stället.

När alla beroenden har lösts guidar resurs förflyttningen dig genom en enkel flyttnings process.

1. Du har inaktiverat en inledande flytt.
2. Efter den första flyttningen kan du välja om du vill genomföra och slutföra flytten eller om du vill ta bort flytten.
3. När flyttningen är färdig kan du välja om du vill ta bort resurserna på käll platsen.

Du kan flytta resurser mellan regioner i resurs förflyttnings hubben eller inifrån en resurs grupp. [Läs mer](select-move-tool.md)

## <a name="what-resources-can-i-move-across-regions"></a>Vilka resurser kan jag flytta mellan regioner?

Med hjälp av resurs förflyttning kan du för närvarande flytta följande resurser över flera regioner:

- Virtuella Azure-datorer och tillhör ande diskar
- Krypterade virtuella Azure-datorer och associerade diskar. Detta inkluderar virtuella datorer med Azure Disk Encryption aktiverat och virtuella Azure-datorer som använder standard kryptering på Server sidan (både med plattforms hanterade nycklar och Kundhanterade nycklar)
- Nätverkskort
- Tillgänglighetsuppsättningar 
- Virtuella Azure-nätverk 
- Offentliga IP-adresser
- Nätverkssäkerhetsgrupper (NSG:er)
- Interna och offentliga belastningsutjämnare 
- Azure SQL-databaser och elastiska pooler


## <a name="next-steps"></a>Nästa steg

[Läs mer](about-move-process.md) om resurs förflyttnings komponenter och flyttnings processen.
