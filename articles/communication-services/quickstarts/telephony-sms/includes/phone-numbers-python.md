---
ms.openlocfilehash: 283d7d1c83dc4a68901c17c36b548e00e1044e34
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629397"
---
## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 eller senare.
- En distribuerad kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Öppna Terminal-eller kommando fönstret och skapa en ny katalog för din app och navigera sedan till den.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Använd en text redigerare för att skapa en fil med namnet phone_numbers_sample. py i projektets rot Katalog och Lägg till följande kod. Vi lägger till den återstående snabb starts koden i följande avsnitt.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Installera paketet

När du fortfarande är i program katalogen installerar du Azure Communication Services administrations klient bibliotek för python-paket med hjälp av `pip install` kommandot.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Autentisera telefonnumret-klienten

`PhoneNumbersClient`Har Aktiver ATS för att använda Azure Active Directory autentisering. Att använda `DefaultAzureCredential` objektet är det enklaste sättet att komma igång med Azure Active Directory och du kan installera det med hjälp av `pip install` kommandot. 

```console
pip install azure-identity
```

`DefaultAzureCredential`Om du skapar ett objekt måste du ha `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` och `AZURE_TENANT_ID` redan angett miljövariabler med motsvarande värden från ditt registrerade Azure AD-program.

När du har installerat `azure-identity` biblioteket kan vi fortsätta autentisera klienten.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Du kan också använda slut punkten och åtkomst nyckeln från kommunikations resursen för att autentisera möjliga.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure Portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Functions

När du `PhoneNumbersClient` har autentiserats kan vi börja arbeta med de olika funktionerna som kan utföras.

### <a name="search-for-available-phone-numbers"></a>Sök efter tillgängliga telefonnummer

Innan du kan köpa telefonnumret måste du först söka efter telefonnummer som är tillgängliga. Om du vill söka efter telefonnummer anger du rikt nummer, tilldelnings typ, [telefonnummerets kapacitet](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), [telefonnummer typ](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)och kvantitet (Standardvärdet är inställt på 1). Observera att det är valfritt att ange rikt nummer för det kostnads fria telefonnumret för nummer.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Köp telefonnummer

Resultatet av att söka efter telefonnummer är en `PhoneNumberSearchResult` . Detta innehåller en `searchId` som kan skickas till inköps nummer-API: n för att hämta numren i sökningen. Observera att om du anropar API: t för inköps samtal debiteras ditt Azure-konto.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient, 
    PhoneNumberCapabilityType, 
    PhoneNumberAssignmentType, 
    PhoneNumberType, 
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Hämta inköps telefonnummer

Efter ett inköps nummer kan du hämta det från klienten. 

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Du kan också hämta alla inköpta telefonnummer.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Uppdatera funktioner för telefonnummer

Du kan uppdatera funktionerna i ett tidigare inköpt telefonnummer.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Versions nummer

Du kan släppa ett inköpt telefonnummer.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Kör koden

Gå till den katalog som innehåller phone_numbers_sample. py-filen från en konsol tolk och kör sedan följande python-kommando för att köra appen.

```console
./phone_numbers_sample.py
```
