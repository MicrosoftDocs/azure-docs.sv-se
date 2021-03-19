---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: d965b89659a9e3dc01035221a729f094c336eb5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "66244670"
---
| Entitet                                       | Konventioner   |
|----------------------------------------------|-----------------------------------------------------|
| Behållar namn för Block-Blob och Page BLOB | Måste vara ett giltigt DNS-namn som är mellan 3 och 63 tecken långt. <br>  Måste börja med en bokstav eller en siffra. <br> Får bara innehålla gemena bokstäver, siffror och bindestreck (-). <br> Varje bindestreck (-) måste föregås och följas av en bokstav eller siffra. <br> Efterföljande bindestreck tillåts inte i namn. |
| Resurs namn för Azure Files                  | Samma som ovan                                          |
| Katalog-och fil namn för Azure Files     |<li> Skift läges känsligt, SKIFT läges okänsligt och får inte överskrida 255 tecken. </li><li> Kan inte sluta med snedstreck (/). </li><li>Om den anges tas den bort automatiskt. </li><li> Följande tecken är inte tillåtna: <code>" \\ / : \| < > * ?</code></li><li> Reserverade URL-tecken måste undantas korrekt. </li><li> Otillåtna tecken för URL-sökväg är inte tillåtna. Kod punkter som \\ uE000 är inte giltiga Unicode-tecken. Vissa ASCII-eller Unicode-tecken, t. ex. styr tecken (0x00 till 0x1F, \\ u0081 osv.), är inte heller tillåtna. För regler som styr Unicode-strängar i HTTP/1.1 se RFC 2616, avsnitt 2,2: Basic rules och RFC 3987. </li><li> Följande fil namn är inte tillåtna: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, klock $, punkt tecken (.) och två punkt tecken (..).</li>|
| Blobnamn för blockblobar och sidblobar      | </li><li>Blobnamn är skiftlägeskänsliga och kan innehålla valfri kombination av tecken. </li><li>Ett blobnamn måste vara mellan 1 och 1 024 tecken långt. </li><li>Reserverade URL-tecken måste undantas korrekt. </li><li>Antalet sökvägssegment som blobnamnet består av får inte överskrida 254. Ett segment är strängen mellan avgränsningstecken (till exempel snedstreck ”/”) som motsvarar namnet på en virtuell katalog.</li> |
