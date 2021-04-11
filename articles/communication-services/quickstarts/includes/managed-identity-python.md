---
ms.openlocfilehash: c642b0e5f459b2412bca6588c8ae625142f0f59f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450224"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Lägg till hanterad identitet i kommunikations tjänst lösningen

### <a name="install-the-sdk-packages"></a>Installera SDK-paketen

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande `import` i koden för att använda Azure-identiteten.

```python
from azure.identity import DefaultAzureCredential
```

Exemplen nedan använder [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

Ett enkelt sätt att hoppa till med hjälp av hanterad identitets autentisering finns i [ge åtkomst med hanterad identitet](../managed-identity-from-cli.md)

För en mer djupgående titt på hur DefaultAzureCredential-objektet fungerar och hur du kan använda det på sätt som inte anges i den här snabb starten, se [klient bibliotek för Azure Identity för python](https://docs.microsoft.com/python/api/overview/azure/identity-readme)

### <a name="create-an-identity-and-issue-a-token"></a>Skapa en identitet och utfärda en token

I följande kod exempel visas hur du skapar ett tjänst klient objekt med hanterad identitet och sedan använder klienten för att utfärda en token för en ny användare:

```python
from azure.communication.identity import CommunicationIdentityClient

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Skicka ett SMS med en Azure-hanterad identitet
Följande kod exempel visar hur du skapar ett tjänst klient objekt med en Azure-hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```python
from azure.communication.sms import SmsClient

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

### <a name="list-all-your-purchased-phone-numbers"></a>Lista alla inköpta telefonnummer

I följande kod exempel visas hur du skapar ett tjänst klient objekt med en Azure-hanterad identitet. Använd sedan-klienten för att se alla inköpta telefonnummer som resursen har:

```python
from azure.communication.phonenumbers import PhoneNumbersClient

def list_purchased_phone_numbers(resource_endpoint):
     credential = DefaultAzureCredential()
     phone_numbers_client = PhoneNumbersClient(resource_endpoint, credential)

     return phone_numbers_client.list_purchased_phone_numbers()
```