---
ms.openlocfilehash: 9da0cbc3777359994fac3778dcc126c844754861
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657670"
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
import azure.communication.identity 

def create_identity_and_get_token(resource_endpoint):
     credential = DefaultAzureCredential()
     client = CommunicationIdentityClient(endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])
     
     return token_response
```

### <a name="send-an-sms-with-azure-managed-identity"></a>Skicka ett SMS med en Azure-hanterad identitet

Följande kod exempel visar hur du skapar ett tjänst klient objekt med en Azure-hanterad identitet och sedan använder klienten för att skicka ett SMS-meddelande:

```python
from azure.communication.sms import (
    PhoneNumberIdentifier,
    SendSmsOptions,
    SmsClient
)

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     credential = DefaultAzureCredential()
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_phone_number=PhoneNumberIdentitifier(from_phone_number),
          to_phone_numbers=[PhoneNumberIdentifier(to_phone_number)],
          message=message_content,
          send_sms_options=SendSmsOptions(enable_delivery_report=True))  # optional property
     )
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om autentisering](../../concepts/authentication.md)

Du kanske också vill:

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för python](/net/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../../quickstarts/telephony-sms/send.md)
- [Läs mer om SMS](../../concepts/telephony-sms/concepts.md)