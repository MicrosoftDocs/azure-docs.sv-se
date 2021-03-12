---
ms.openlocfilehash: 50707b46445803ee27118ee72b90a237a3e76200
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103021059"
---
## <a name="add-managed-identity-to-your-communication-services-solution"></a>Lägg till hanterad identitet i kommunikations tjänst lösningen

### <a name="install-the-client-library-packages"></a>Installera klient biblioteks paketen

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="use-the-client-library-packages"></a>Använda klient biblioteks paketen

Lägg till följande `import` i koden för att använda Azure-identiteten.

```python
from azure.identity import DefaultAzureCredential
```

Exemplen nedan använder [DefaultAzureCredential](/python/api/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

Information om hur du registrerar program i utvecklings miljön och konfigurerar miljövariabler finns i [bevilja åtkomst med hanterad identitet](../managed-identity-from-cli.md)

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
