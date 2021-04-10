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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578761"
---
# <a name="logging-in-msal-for-python"></a>Loggning i MSAL för Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL för python-loggning

Loggning i MSAL för python utnyttjar [loggningsmodulen i python-standardbiblioteket](https://docs.python.org/3/library/logging.html). Du kan konfigurera MSAL-loggning på följande sätt (och se hur det fungerar i [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Aktivera fel söknings loggning för alla moduler

Som standard är loggningen i valfritt Python-skript inaktive rad. Om du vill aktivera utförlig loggning för **alla** python-moduler i skriptet använder du `logging.basicConfig` med en nivå på `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Då skrivs alla logg meddelanden som har tilldelats till loggningsmodulen till standardutdata.

### <a name="configure-msal-logging-level"></a>Konfigurera loggnings nivå för MSAL

Du kan konfigurera loggnings nivån för MSAL för python-loggen genom att använda- `logging.getLogger()` metoden med inloggnings namnet `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Konfigurera MSAL-loggning med Azure App Insights

Python-loggar ges till en logg hanterare som som standard är `StreamHandler` . Om du vill skicka MSAL-loggar till en Application Insights med en Instrumentation-nyckel använder du `AzureLogHandler` biblioteket som tillhandahållits `opencensus-ext-azure` .

Installera genom att `opencensus-ext-azure` lägga till `opencensus-ext-azure` paketet från pypi till dina beroenden eller pip-installationen:

```console
pip install opencensus-ext-azure
```

Ändra sedan standard hanteraren för `"msal"` logg leverantören till en instans av `AzureLogHandler` med en instrumentande nyckel som angetts i `APP_INSIGHTS_KEY` miljövariabeln:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Personliga och organisatoriska data i python

MSAL for python loggar inte personliga data eller organisations data. Det finns ingen egenskap för att aktivera eller inaktivera personlig eller organisations data inloggning.

Du kan använda standard-python-loggning för att logga vad du vill, men du är ansvarig för att säkert hantera känsliga data och följa regel krav.

Mer information om loggning i python finns i python  [-loggning: How-to](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Nästa steg

Fler kod exempel finns i [kod exempel för Microsoft Identity Platform](sample-v2-code.md).
