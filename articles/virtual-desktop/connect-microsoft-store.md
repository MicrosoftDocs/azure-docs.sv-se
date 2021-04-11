---
title: Ansluta till Microsoft Store klienten – Azure
description: Ansluta till virtuella Windows-datorer med hjälp av Microsoft Store-klienten.
author: Heidilohr
ms.topic: how-to
ms.date: 10/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 5662b32d8e2c9af457ded62b0302c2c64b31edea
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448023"
---
# <a name="connect-with-the-microsoft-store-client"></a>Ansluta med Microsoft Store-klienten

>Gäller för: Windows 10.

Du kan komma åt Windows virtuella Skriv bords resurser på enheter med Windows 10.

## <a name="install-the-microsoft-store-client"></a>Installera Microsoft Store-klienten

Du kan installera klienten för den aktuella användaren, som inte kräver administratörs behörighet. Alternativt kan administratören installera och konfigurera-klienten så att alla användare på enheten kan komma åt den.

När du har installerat kan klienten startas från Start-menyn genom att söka efter fjärr skrivbord.

Kom igång genom att [Ladda ned och installera-klienten från Microsoft Store](https://www.microsoft.com/store/productId/9WZDNCRFJ3PS).

## <a name="subscribe-to-a-workspace"></a>Prenumerera på en arbets yta

Prenumerera på arbets ytan som administratören har fått för att hämta listan över hanterade resurser som du har åtkomst till på din dator.

Så här prenumererar du på en arbets yta:

1. I fönstret anslutnings Center trycker du på **+ Lägg till** och sedan på **arbets ytor**.
2. Ange arbets ytans URL i URL-fältet för arbets ytan som du fick av administratören. URL: en för arbets ytan kan vara antingen en URL eller en e-postadress.
   
   - Om du använder en arbetsyte-URL använder du den URL som din administratör fick dig.
   - Om du ansluter från det virtuella Windows-skrivbordet använder du någon av följande URL: er beroende på vilken version av tjänsten du använder:
       - Windows Virtual Desktop (klassisk): `https://rdweb.wvd.microsoft.com/api/feeddiscovery/webfeeddiscovery.aspx` .
       - Virtuellt Windows-skrivbord: `https://rdweb.wvd.microsoft.com/api/arm/feeddiscovery` .
  
3. Tryck **på Prenumerera**.
4. Ange dina autentiseringsuppgifter när du uppmanas till det.
5. När du har prenumererat bör arbets ytorna visas i anslutnings centret.

Du kan lägga till, ändra eller ta bort arbets ytor utifrån ändringar som administratören har gjort.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om hur du använder Microsoft Store-klienten kan du läsa [komma igång med Microsoft Store-klienten](/windows-server/remote/remote-desktop-services/clients/windows/).