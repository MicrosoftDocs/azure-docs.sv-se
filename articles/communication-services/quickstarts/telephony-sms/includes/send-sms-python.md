---
title: ta med fil
description: ta med fil
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: b975b129f4384c09006a22b1ab124b1906a869d3
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803205"
---
Kom igång med Azure Communication Services med hjälp av tjänsten python SMS-klient bibliotek för kommunikations tjänster för att skicka SMS-meddelanden.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2,7 eller 3.6 +.
- En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- Ett SMS-aktiverat telefonnummer. [Hämta ett telefonnummer](../get-phone-number.md).

### <a name="prerequisite-check"></a>Krav kontroll

- Kör kommandot i ett terminalfönster-eller kommando fönster `python --version` för att kontrol lera att python är installerat.
- Om du vill visa de telefonnummer som är associerade med kommunikations tjänst resursen loggar du in på [Azure Portal](https://portal.azure.com/), letar upp resursen för kommunikations tjänster och öppnar fliken **telefonnummer** i det vänstra navigerings fönstret.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna terminalen eller kommando fönstret, skapa en ny katalog för din app och navigera till den.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Använd en text redigerare för att skapa en fil med namnet **send-SMS.py** i projektets rot Katalog och lägga till strukturen för programmet, inklusive grundläggande undantags hantering. Du kommer att lägga till alla käll koder för den här snabb starten till den här filen i följande avsnitt.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services SMS-klient biblioteket för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services SMS-klient biblioteket för python.

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Den här klassen krävs för alla SMS-funktioner. Du instansierar det med din prenumerations information och använder den för att skicka SMS-meddelanden.                                                                                                                 |
| SmsSendResult               | Den här klassen innehåller resultatet från SMS-tjänsten.                                          |

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera en **SmsClient** med anslutnings strängen. Koden nedan hämtar anslutnings strängen för resursen från en miljö variabel med namnet `COMMUNICATION_SERVICES_CONNECTION_STRING` . Lär dig hur [du hanterar anslutnings strängen](../../create-communication-resource.md#store-your-connection-string)för din resurs.

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-a-11-sms-message"></a>Skicka ett 1:1 SMS-meddelande

Om du vill skicka ett SMS-meddelande till en enda mottagare anropar du ```send``` metoden från **SmsClient** med ett enda mottagares telefonnummer. Du kan också skicka valfria parametrar för att ange om leverans rapporten ska vara aktive rad och för att ange anpassade taggar. Lägg till den här koden i slutet av `try` blocket i **send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>,
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Ersätt `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänsten och `<to-phone-number>` med telefonnumret som du vill skicka ett meddelande till. 

## <a name="send-a-1n-sms-message"></a>Skicka ett 1: N SMS-meddelande

Om du vill skicka ett SMS-meddelande till en lista över mottagare anropar du ```send``` metoden från **SmsClient** med en lista över mottagarens telefonnummer. Du kan också skicka valfria parametrar för att ange om leverans rapporten ska vara aktive rad och för att ange anpassade taggar. Lägg till den här koden i slutet av `try` blocket i **send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Ersätt `<from-phone-number>` med ett SMS-aktiverat telefonnummer som är associerat med kommunikations tjänsten och `<to-phone-number-1>` `<to-phone-number-2>` med de telefonnummer som du vill skicka ett meddelande till. 

## <a name="optional-parameters"></a>Valfria parametrar

`enable_delivery_report`Parametern är en valfri parameter som du kan använda för att konfigurera leverans rapportering. Detta är användbart för scenarier där du vill generera händelser när SMS-meddelanden levereras. Se snabb starten [Hantera SMS-händelser](../handle-sms-events.md) för att konfigurera leverans rapportering för SMS-meddelanden.

`tag`Parametern är en valfri parameter som du kan använda för att konfigurera anpassad taggning.

## <a name="run-the-code"></a>Kör koden

Kör programmet från program katalogen med `python` kommandot.

```console
python send-sms.py
```
