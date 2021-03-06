---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "67187980"
---
För data i flygning:

- Standard TLS 1,2 används för data som överförs mellan enheten och Azure. Det finns ingen återställning till TLS 1,1 och tidigare. Agent kommunikation kommer att blockeras om TLS 1,2 inte stöds. TLS 1,2 krävs också för Portal-och SDK-hantering.
- När klienter får åtkomst till din enhet via det lokala webb gränssnittet i en webbläsare, används standard TLS 1,2 som standard säkra protokoll.

    - Det bästa sättet är att konfigurera webbläsaren att använda TLS 1,2.
    - Om webbläsaren inte stöder TLS 1,2 kan du använda TLS 1,1 eller TLS 1,0.
- Vi rekommenderar att du använder SMB 3,0 med kryptering för att skydda data när du kopierar den från dina data servrar.
