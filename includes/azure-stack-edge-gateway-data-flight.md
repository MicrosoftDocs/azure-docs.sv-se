---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96467785"
---
För data i flygning:

- Standard Transport Layer Security (TLS) 1,2 används för data som överförs mellan enheten och Azure. Det finns ingen återställning till TLS 1,1 och tidigare. Agent kommunikation kommer att blockeras om TLS 1,2 inte stöds. TLS 1,2 krävs också för Portal-och SDK-hantering.
- När klienter får åtkomst till din enhet via det lokala webb gränssnittet i en webbläsare, används standard TLS 1,2 som standard säkra protokoll.

  - Det bästa sättet är att konfigurera webbläsaren att använda TLS 1,2.
  - Enheten stöder bara TLS 1,2 och har inte stöd för äldre versioner av TLS 1,1 eller TLS 1,0.
- Vi rekommenderar att du använder SMB 3,0 med kryptering för att skydda data när du kopierar den från dina data servrar.
