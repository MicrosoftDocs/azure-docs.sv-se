---
title: Loggnings fel och undantag i MSAL för python
titleSuffix: Microsoft identity platform
description: Lär dig hur du loggar fel och undantag i MSAL för python
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 560caa7e29ce12b58e151a1362aaf2c662646f13
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954808"
---
# <a name="logging-in-msal-for-python"></a>Loggning i MSAL för Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL för python-loggning

Loggning i MSAL python använder standard funktionen för python-loggning, till exempel `logging.info("msg")` kan du konfigurera MSAL-loggning på följande sätt (och se hur det fungerar i [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Aktivera fel söknings loggning för alla moduler

Som standard är loggningen i valfritt Python-skript inaktive rad. Om du vill aktivera fel söknings loggning för alla moduler i hela python-skriptet använder du:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Enbart tystnads MSAL loggning

Om du bara vill göra en MSAL-biblioteks loggning, samtidigt som du aktiverar fel söknings loggning i alla andra moduler i python-skriptet, inaktiverar du den loggning som används av MSAL python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Personliga och organisatoriska data i python

MSAL for python loggar inte personliga data eller organisations data. Det finns ingen egenskap för att aktivera eller inaktivera personlig eller organisations data inloggning.

Du kan använda standard-python-loggning för att logga vad du vill, men du är ansvarig för att säkert hantera känsliga data och följa regel krav.

Mer information om loggning i python finns i python  [-loggning: How-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).

---