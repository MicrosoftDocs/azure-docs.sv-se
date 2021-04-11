---
title: Snabb start för att använda Azure App konfiguration med python-appar | Microsoft Docs
description: I den här snabb starten skapar du en python-app med Azure App konfiguration för att centralisera lagring och hantering av program inställningar separat från din kod.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat
ms.openlocfilehash: 1be74ceeb1f4808a8df8f56b71b66aa870cdd91d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800881"
---
# <a name="quickstart-create-a-python-app-with-azure-app-configuration"></a>Snabb start: skapa en python-app med Azure App konfiguration

I den här snabb starten ska du använda Azure App konfiguration för att centralisera lagring och hantering av program inställningar med hjälp av [klient biblioteket för Azure App konfiguration för python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- Python 2,7 eller 3,6 eller senare – information om hur du konfigurerar python i Windows finns i [python i Windows-dokumentationen]( https://docs.microsoft.com/windows/python/)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **konfigurations Utforskaren**  >  **skapa**  >  **nyckel-värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

8. Välj **Använd**.

## <a name="setting-up-the-python-app"></a>Konfigurera python-appen

1. I den här självstudien skapar du en ny katalog för projektet med namnet *app-Configuration-snabb start*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Växla till den nyligen skapade katalogen *app-Configuration-snabb start* .

    ```console
    cd app-configuration-quickstart
    ```

1. Installera klient biblioteket för Azure App konfiguration med hjälp av `pip install` kommandot.

    ```console
    pip install azure-appconfiguration
    ```

1. Skapa en ny fil med namnet *app-Configuration-QuickStart.py* i katalogen *app-Configuration-snabb start* och Lägg till följande kod:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

> [!NOTE]
> Kodfragmenten i den här snabb starten hjälper dig att komma igång med klient biblioteket för app-konfiguration för python. För ditt program bör du även överväga att hantera undantagen efter dina behov. Mer information om undantags hantering finns i vår [python SDK-dokumentation](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## <a name="configure-your-app-configuration-connection-string"></a>Konfigurera anslutnings strängen för din app-konfiguration

1. Ange en miljö variabel med namnet **AZURE_APP_CONFIG_CONNECTION_STRING** och ange den som åtkomst nyckel till appens konfigurations arkiv. Kör följande kommando på kommando raden:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Starta om kommando tolken så att ändringen börjar gälla. Skriv ut värdet för miljövariabeln för att kontrol lera att den är korrekt inställd.

## <a name="code-samples"></a>Kodexempel

I exempel kods tycken i det här avsnittet visas hur du utför vanliga åtgärder med klient biblioteket för app-konfiguration för python. Lägg till dessa kodfragment i `try` blocket i *app-Configuration-QuickStart.py* -filen som du skapade tidigare.

> [!NOTE]
> Klient biblioteket för app Configuration refererar till ett nyckel värdes objekt som `ConfigurationSetting` . I den här artikeln kallas därför **nyckel värden** i appens konfigurations arkiv som **konfigurations inställningar**.

* [Anslut till ett konfigurations Arkiv för appen](#connect-to-an-app-configuration-store)
* [Hämta en konfigurations inställning](#get-a-configuration-setting)
* [Lägg till en konfigurations inställning](#add-a-configuration-setting)
* [Hämta en lista över konfigurations inställningar](#get-a-list-of-configuration-settings)
* [Lås en konfigurations inställning](#lock-a-configuration-setting)
* [Lås upp en konfigurations inställning](#unlock-a-configuration-setting)
* [Uppdatera en konfigurations inställning](#update-a-configuration-setting)
* [Ta bort en konfigurations inställning](#delete-a-configuration-setting)

### <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

Följande kodfragment skapar en instans av **AzureAppConfigurationClient** med hjälp av anslutnings strängen som lagras i miljövariablerna.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### <a name="get-a-configuration-setting"></a>Hämta en konfigurations inställning

Följande kodfragment hämtar en konfigurations inställning efter `key` namn.

```python
    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)
```

### <a name="add-a-configuration-setting"></a>Lägg till en konfigurations inställning

Följande kodfragment skapar ett- `ConfigurationSetting` objekt med `key` fälten och `value` anropar- `add_configuration_setting` metoden. Den här metoden kommer att utlösa ett undantag om du försöker lägga till en konfigurations inställning som redan finns i butiken. Om du vill undvika det här undantaget kan du i stället använda metoden [set_configuration_setting](#update-a-configuration-setting) .

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)
```

### <a name="get-a-list-of-configuration-settings"></a>Hämta en lista över konfigurations inställningar

Följande kodfragment hämtar en lista över konfigurations inställningar. `key_filter` `label_filter` Argumenten och kan anges för att filtrera nyckel värden baserat på respektive `key` `label` . Mer information om filtrering finns i så här frågar du efter [konfigurations inställningar](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)
```

### <a name="lock-a-configuration-setting"></a>Lås en konfigurations inställning

Lås statusen för ett nyckel värde i app-konfigurationen anges av `read_only` `ConfigurationSetting` objektets attribut. Om `read_only` är `True` , är inställningen låst. `set_read_only`Metoden kan anropas med `read_only=True` argument för att låsa konfigurations inställningen.

```python
    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))
```

### <a name="unlock-a-configuration-setting"></a>Lås upp en konfigurations inställning

Om `read_only` attributet för en `ConfigurationSetting` är `False` , är inställningen låst. `set_read_only`Metoden kan anropas med `read_only=False` argument för att låsa upp konfigurations inställningen.

```python
    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))
```

### <a name="update-a-configuration-setting"></a>Uppdatera en konfigurations inställning

`set_configuration_setting`Metoden kan användas för att uppdatera en befintlig inställning eller skapa en ny inställning. Följande kodfragment ändrar värdet för en befintlig konfigurations inställning.

```python
    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)
```

### <a name="delete-a-configuration-setting"></a>Ta bort en konfigurations inställning

Följande kodfragment tar bort en konfigurations inställning efter `key` namn.

```python
    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)
```

## <a name="run-the-app"></a>Kör appen

I den här snabb starten har du skapat en python-app som använder klient biblioteket för Azure App konfiguration för att hämta en konfigurations inställning som skapats via Azure Portal, lägga till en ny inställning, hämta en lista över befintliga inställningar, låsa och låsa upp en inställning, uppdatera en inställning och slutligen ta bort en inställning.

I det här läget bör din *app-Configuration-QuickStart.py* -fil ha följande kod:

```python
import os
from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting

try:
    print("Azure App Configuration - Python Quickstart")
    # Quickstart code goes here

    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)

    retrieved_config_setting = app_config_client.get_configuration_setting(key='TestApp:Settings:Message')
    print("\nRetrieved configuration setting:")
    print("Key: " + retrieved_config_setting.key + ", Value: " + retrieved_config_setting.value)

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:NewSetting',
        value='New setting value'
    )
    added_config_setting = app_config_client.add_configuration_setting(config_setting)
    print("\nAdded configuration setting:")
    print("Key: " + added_config_setting.key + ", Value: " + added_config_setting.value)

    filtered_settings_list = app_config_client.list_configuration_settings(key_filter="TestApp*")
    print("\nRetrieved list of configuration settings:")
    for item in filtered_settings_list:
        print("Key: " + item.key + ", Value: " + item.value)

    locked_config_setting = app_config_client.set_read_only(added_config_setting, read_only=True)
    print("\nRead-only status for " + locked_config_setting.key + ": " + str(locked_config_setting.read_only))

    unlocked_config_setting = app_config_client.set_read_only(locked_config_setting, read_only=False)
    print("\nRead-only status for " + unlocked_config_setting.key + ": " + str(unlocked_config_setting.read_only))

    added_config_setting.value = "Value has been updated!"
    updated_config_setting = app_config_client.set_configuration_setting(added_config_setting)
    print("\nUpdated configuration setting:")
    print("Key: " + updated_config_setting.key + ", Value: " + updated_config_setting.value)

    deleted_config_setting = app_config_client.delete_configuration_setting(key="TestApp:Settings:NewSetting")
    print("\nDeleted configuration setting:")
    print("Key: " + deleted_config_setting.key + ", Value: " + deleted_config_setting.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

I konsol fönstret navigerar du till den katalog som innehåller *app-Configuration-QuickStart.py* -filen och kör följande python-kommando för att köra appen:

```console
python app-configuration-quickstart.py
```

Du bör se följande utdata:

```output
Azure App Configuration - Python Quickstart

Retrieved configuration setting:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration

Added configuration setting:
Key: TestApp:Settings:NewSetting, Value: New setting value

Retrieved list of configuration settings:
Key: TestApp:Settings:Message, Value: Data from Azure App Configuration
Key: TestApp:Settings:NewSetting, Value: New setting value

Read-only status for TestApp:Settings:NewSetting: True

Read-only status for TestApp:Settings:NewSetting: False

Updated configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!

Deleted configuration setting:
Key: TestApp:Settings:NewSetting, Value: Value has been updated!
```

## <a name="clean-up-resources"></a>Rensa resurser


[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en ny app konfigurations lagring och lär dig hur du får åtkomst till nyckel värden från en python-app.

Ytterligare kod exempel finns på:

> [!div class="nextstepaction"]
> [Klient biblioteks exempel för Azure App konfiguration](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration/samples)
