---
ms.openlocfilehash: 42d079a2aa98549b12aafecdd8d58f3361db8b4d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307471"
---
## <a name="setting-up"></a>Konfigurera

## <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna terminalen eller kommando fönstret Skapa en ny katalog för din app och navigera till den.

```console
mkdir managed-identity-quickstart && cd managed-identity-quickstart
```

### <a name="install-the-sdk-packages"></a>Installera SDK-paketen

```console
pip install azure-identity
pip install azure-communication-identity
pip install azure-communication-sms
```

### <a name="create-a-new-file"></a>Skapa en ny fil
Öppna och spara en ny fil i din skapade mapp som kallas `managed-identity.py` , vi kommer att placera vår kod i den här filen.

### <a name="use-the-sdk-packages"></a>Använda SDK-paketen

Lägg till följande- `import` instruktioner överst i filen för att använda SDK: er som vi har installerat.

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient
```

### <a name="create-a-defaultazurecredential"></a>Skapa en DefaultAzureCredential

Vi använder [DefaultAzureCredential](/python/api/azure-identity/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer. Eftersom vi kommer att använda den under den här snabb starten skapar vi den överst i filen.

```python
     credential = DefaultAzureCredential()
```

## <a name="create-an-identity-and-issue-a-token-with-managed-identities"></a>Skapa en identitet och utfärda en token med hanterade identiteter.

Nu ska vi lägga till kod som använder den skapade autentiseringsuppgiften för att utfärda en VoIP-åtkomsttoken. Vi kallar den här koden senare på:

```python
def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response
```

### <a name="send-an-sms-with-managed-identities"></a>Skicka ett SMS med hanterade identiteter
Ett annat exempel på att använda hanterade identiteter är att lägga till den här koden som använder samma autentiseringsuppgift för att skicka ett SMS:

```python
def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     sms_client.send(
          from_=from_phone_number,
          to_=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
```

## <a name="write-our-main-code"></a>Skriv vår huvud kod

Med våra funktioner skapade kan vi nu skriva huvud koden som anropar de funktioner som vi har skrivit tidigare.

```python
# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```

Den sista `managed-identity.py` filen bör se ut ungefär så här:

```python
from azure.identity import DefaultAzureCredential
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.sms import SmsClient

credential = DefaultAzureCredential()

def create_identity_and_get_token(resource_endpoint):
     client = CommunicationIdentityClient(resource_endpoint, credential)

     user = client.create_user()
     token_response = client.get_token(user, scopes=["voip"])

     return token_response

def send_sms(resource_endpoint, from_phone_number, to_phone_number, message_content):
     sms_client = SmsClient(resource_endpoint, credential)

     response = sms_client.send(
          from_=from_phone_number,
          to=[to_phone_number],
          message=message_content,
          enable_delivery_report=True  # optional property
     )
     return response

# You can find your endpoint and access key from your resource in the Azure portal
# e.g. "https://<RESOURCE_NAME>.communication.azure.com";
endpoint = "https://<RESOURCE_NAME>.communication.azure.com/"

print("Retrieving new Access Token, using Managed Identities");
result = create_identity_and_get_token(endpoint);
print(f'Retrieved Access Token: {result.token}');

print("Sending SMS using Managed Identities");

# You will need a phone number from your resource to send an SMS.
sms_result = send_sms(endpoint, "<FROM_NUMBER>", "<TO_NUMBER>", "Hello from Managed Identities");
print(f'SMS ID: {sms_result[0].message_id}');
print(f'Send Result Successful: {sms_result[0].successful}');
```
## <a name="run-the-program"></a>Kör programmet

När allt är klart kan du köra filen genom att ange `python managed-identity.py` från projektets katalog. Om allt gick bra bör du se något som liknar följande.

```Bash
    $ python managed-identity.py
    Retrieving new Access Token, using Managed Identities
    Retrieved Access Token: ey...Q
    Sending SMS using Managed Identities
    SMS ID: Outgoing_2021040602194...._noam
    Send Result Successful: true
```
