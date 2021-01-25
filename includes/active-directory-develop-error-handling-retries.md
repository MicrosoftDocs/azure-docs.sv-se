---
author: mmacy
ms.author: marsma
ms.date: 11/25/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: d0377e3e918a8431c35c1f5b15a1c4b54eeae3b4
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "98760695"
---
## <a name="retrying-after-errors-and-exceptions"></a>Försöker igen efter fel och undantag

Du förväntas implementera dina egna principer för återförsök när du anropar MSAL. MSAL gör HTTP-anrop till Azure AD-tjänsten och ibland kan fel uppstå. Till exempel kan nätverket stängas av eller servern är överbelastad.  

### <a name="http-429"></a>HTTP 429

När service token Server (STS) är överbelastad med för många begär Anden returneras HTTP-fel 429 med ett tips om hur länge du kan försöka igen i fältet `Retry-After` svar.