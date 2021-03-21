---
title: ta med fil
description: ta med fil
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 137aca7c6c857ee6e833c359b710e1c1848d15ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95555705"
---
## <a name="deleting-personal-information"></a>Ta bort personlig information

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Personlig information är relevant för import/export-tjänsten (via portalen och API: et) under import-och export åtgärder. Data som används under dessa processer är:

- Kontaktnamn
- Telefonnummer
- E-post
- Gatuadress
- City
- Postnummer
- Tillstånd
- Land/region
- Enhets-ID
- Transportföretagets kontonummer
- Spårningsnummer för leveransen

När ett import/export-jobb skapas, ger användarna kontakt information och en leverans adress. Personlig information lagras på upp till två olika platser: i jobbet och valfritt i Portal inställningarna. Personlig information lagras endast i Portal inställningar om du markerar kryss rutan, **sparar bärvåg och retur adress som standard** under avsnittet *RETUR leverans information* i export processen.

Personlig kontakt information kan tas bort på följande sätt:

- Data som sparas med jobbet tas bort med jobbet. Användare kan ta bort jobb manuellt och slutförda jobb tas automatiskt bort efter 90 dagar. Du kan manuellt ta bort jobben via REST API eller Azure Portal. Om du vill ta bort jobbet i Azure Portal går du till import/export-jobbet och klickar på *ta bort* från kommando fältet. Mer information om hur du tar bort ett import/export-jobb via REST API finns i [ta bort ett import/export-jobb](/previous-versions/azure/storage/common/storage-import-export-cancelling-and-deleting-jobs).

- Kontakt information som sparas i Portal inställningarna kan tas bort genom att du tar bort Portal inställningarna. Du kan ta bort Portal inställningar genom att följa dessa steg:
  - Logga in på [Azure-portalen](https://portal.azure.com).
  - Klicka på ikonen *Inställningar* för ![ Azure-inställningar](media/storage-import-export-delete-personal-info/azure-settings-icon.png)
  - Klicka på *Exportera alla inställningar* (om du vill spara de aktuella inställningarna i en `.json` fil).
  - Klicka på *ta bort alla inställningar och privata instrument paneler* för att ta bort alla inställningar inklusive Sparad kontakt information.

Mer information hittar du i sekretess policyn för Microsoft i [säkerhets Center](https://www.microsoft.com/trustcenter)