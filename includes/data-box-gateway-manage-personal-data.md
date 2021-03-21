---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: ebd0e48c9f197439e838efbc936537ca5da3520f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583091"
---
- **Beställnings information**. När en order skapas lagras leverans adress, e-postadress och kontakt information för användaren i Azure Portal. Informationen som sparas omfattar:
  - Kontaktnamn
  - Telefonnummer
  - E-postadress
  - Gatuadress
  - City
  - POST nummer
  - Tillstånd
  - Land/provins/region
  - Spårningsnummer för leveransen

    Order Detaljer krypteras och lagras i tjänsten. Tjänsten behåller informationen tills du uttryckligen tar bort resursen eller ordningen. Borttagningen av resursen och motsvarande ordning blockeras från den tidpunkt då enheten levereras tills enheten återgår till Microsoft.

- **Leverans adress**. När en beställning har placerats tillhandahåller Data Box-enhet tjänsten leverans adressen till tredjeparts-leverantörer som UPS.

- **Dela användare**. Användare på enheten kan även komma åt data som finns på resurserna. En lista över användare som kan komma åt resurs data kan visas. När resurserna tas bort raderas även den här listan.