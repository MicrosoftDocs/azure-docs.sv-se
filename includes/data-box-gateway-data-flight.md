---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583048"
---
För data i flygning:

- Standard TLS 1,2 används för data som överförs mellan enheten och Azure. Det finns ingen återställning till TLS 1,1 och tidigare. Agent kommunikation kommer att blockeras om TLS 1,2 inte stöds. TLS 1,2 krävs också för Portal-och SDK-hantering.
- När klienter får åtkomst till din enhet via det lokala webb gränssnittet i en webbläsare, används standard TLS 1,2 som standard säkra protokoll.

    - Det bästa sättet är att konfigurera webbläsaren att använda TLS 1,2.
    - Om webbläsaren inte stöder TLS 1,2 kan du använda TLS 1,1 eller TLS 1,0.
- Vi rekommenderar att du använder SMB 3,0 med kryptering för att skydda data när du kopierar den från dina data servrar.
