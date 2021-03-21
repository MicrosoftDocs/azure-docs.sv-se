---
title: Hantera data till gångar i Azure Data Catalog
description: Artikeln visar hur du styr synlighet och ägarskap för data till gångar som registrerats i Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 0182f744e038bc8080b9fb96bfdb1eeda16b86c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674860"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Hantera data till gångar i Azure Data Catalog

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introduktion
Azure Data Catalog är utformad för identifiering av data källor så att du enkelt kan identifiera och förstå de data källor du behöver för att utföra analyser och fatta beslut. De här identifierings funktionerna gör den största påverkan när du och andra användare kan hitta och förstå de många tillgängliga data källorna. Med dessa element i åtanke är standard beteendet för Data Catalog för alla registrerade data källor som ska vara synliga för och kan identifieras av alla katalog användare.

Data Catalog ger dig inte till gång till själva data. Data åtkomsten styrs av data källans ägare. Med Data Catalog kan du identifiera data källor och visa metadata som är relaterade till de källor som är registrerade i katalogen.

Det kan dock finnas situationer där data källor endast bör vara synliga för vissa användare eller för medlemmar i vissa grupper. I sådana fall kan användare bli ägare till registrerade data till gångar i katalogen och sedan styra synligheten för de till gångar som de äger.

> [!NOTE]
> De funktioner som beskrivs i den här artikeln är bara tillgängliga i standard versionen av Azure Data Catalog. Den kostnads fria versionen tillhandahåller inte funktioner för ägarskap och begränsning av data till gångs synlighet.
>
>

## <a name="manage-ownership-of-data-assets"></a>Hantera ägarskap för data till gångar
Som standard ägs inte data till gångar som är registrerade i Data Catalog. Alla användare med behörighet att komma åt katalogen kan identifiera och kommentera dessa till gångar. Användare kan bli ägare till ägda data till gångar och sedan begränsa synligheten för de till gångar som de äger.

När en data till gång i Data Catalog ägs, kan endast användare som har behörighet för ägare identifiera till gången och visa dess metadata, och endast ägare kan ta bort till gången från katalogen.

> [!NOTE]
> Ägarskap i Data Catalog påverkar bara de metadata som lagras i katalogen. Ägarskap ger inte någon behörighet till den underliggande data källan.
>
>

### <a name="take-ownership"></a>Bli ägare
Användare kan bli ägare till data till gångar genom att välja alternativet **bli ägare** i Data Catalog portalen. Inga särskilda behörigheter krävs för att bli ägare till en icke-ägda data till gång. Alla användare kan bli ägare till en ägd data till gång.

### <a name="add-owners-and-co-owners"></a>Lägg till ägare och medägare
Om en data till gång redan ägs kan andra användare inte bara bli ägare. De måste läggas till som medägare av en befintlig ägare. Alla ägare kan lägga till ytterligare användare eller säkerhets grupper som medägare.

> [!NOTE]
> Vi rekommenderar att du har minst två personer som ägare till en ägd data till gång.
>
>

### <a name="remove-owners"></a>Ta bort ägare
Precis som en till gångs ägare kan lägga till medägare kan alla till gångs ägare ta bort alla medägare.

En till gångs ägare som tar bort sig själva som en ägare kan inte längre hantera till gången. Om till gångs ägaren tar bort sig själva som en ägare och det inte finns några andra medägare, återgår till gången till ett icke ägda tillstånd.

## <a name="control-visibility"></a>Kontroll synlighet
Data till gångs ägare kan styra synligheten för de data till gångar som de äger. Om du vill begränsa synligheten som standard, där alla Data Catalog användare kan identifiera och visa data till gången, kan till gångens ägare växla Synlighets inställningen från **alla** till **ägare & dessa användare** i egenskaperna för till gången. Ägare kan sedan lägga till vissa användare och säkerhets grupper.

> [!NOTE]
> När det är möjligt ska till gångens ägande-och Synlighets behörigheter tilldelas till säkerhets grupper och inte till enskilda användare.
>
>

## <a name="catalog-administrators"></a>Katalog administratörer
Data Catalog-administratörer är implicit medägare till alla till gångar i katalogen. Till gångs ägare kan inte ta bort synlighet från administratörer och administratörer kan hantera ägarskap och synlighet för alla data till gångar i katalogen.

## <a name="summary"></a>Sammanfattning
Med den Data Catalog gemensamt skapade-modellen för metadata och data till gångs identifiering kan alla katalog användare bidra och identifiera sig. Standard versionen av Data Catalog är utformad för ägarskap och hantering för att begränsa synligheten och användningen av vissa data till gångar.
