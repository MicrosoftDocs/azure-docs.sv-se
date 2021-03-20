---
title: Etablera en Azure Spring Cloud-instans med terraform
description: Etablera en Azure våren Cloud-instans med terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96533799"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Etablera en Azure våren Cloud-instans med terraform

**Den här artikeln gäller för:** ✔️ Java ✔️ C #

I det här exemplet skapas en Azure våren Cloud-instans med terraform. Procedurerna vägleder dig genom skapandet av följande resurser:

> [!div class="checklist"]
> * Resursgrupp
> * Azure våren Cloud-instans
> * Azure Storage för Log Analytics

> [!NOTE]
> För terraform support använder du en av HashiCorps community Support-kanaler till terraform:
>
> * Frågor, use-Cases och användbara mönster: [avsnittet terraform i HashiCorp community portal](https://discuss.hashicorp.com/c/terraform-core)
> * Provider-relaterade frågor: [terraform providers-avsnittet i HashiCorp community-portalen](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Förutsättningar

- **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.

## <a name="create-configuration-file"></a>Skapa konfigurationsfilen

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Öppna [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Starta Cloud Shell redigeraren:

    ```bash
    code main.tf
    ```

1. Konfigurationen i det här steget modellerar Azure-resurser, inklusive en Azure-resurs grupp och en Azure våren-moln instans.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Spara filen (**&lt; CTRL>S**) och avsluta redigeraren (**&lt; CTRL>Q**).

## <a name="apply-the-configuration"></a>Tillämpa konfigurationen

I det här avsnittet använder du flera terraform-kommandon för att köra konfigurationen.

1. Kommandot [terraform init](https://www.terraform.io/docs/commands/init.html) initierar arbetskatalogen. Kör följande kommando i Cloud Shell:

    ```bash
    terraform init
    ```

1. Kommandot [terraform-plan](https://www.terraform.io/docs/commands/plan.html) används för att validera konfigurations syntaxen. `-out`Parametern dirigerar resultaten till en fil. Utdatafilen kan användas senare för att tillämpa konfigurationen. Kör följande kommando i Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Kommandot [terraform Apply](https://www.terraform.io/docs/commands/apply.html) används för att tillämpa konfigurationen. Kommandot anger utdatafilen från föregående steg. Det här kommandot skapar Azure-resurser. Kör följande kommando i Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Om du vill verifiera resultatet i Azure Portal bläddrar du till den nya resurs gruppen. Den nya **Azure våren Cloud** -instansen visas i den nya resurs gruppen.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Uppdatera konfigurationen för att konfigurera loggar och mått

Det här avsnittet visar hur du uppdaterar konfigurationen för att aktivera logg och mått för Azure våren Cloud med ett Azure Storage-konto.

1. Starta Cloud Shell redigeraren:

    ```bash
    code main.tf
    ```

1. Lägg till följande konfiguration i slutet av filen:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Spara filen (**&lt; CTRL>S**) och avsluta redigeraren (**&lt; CTRL>Q**).

1. Som i föregående avsnitt kör du följande kommando för att göra ändringarna:

    ```bash
    terraform plan --out plan.out
    ```

1. Kör `terraform apply` kommandot för att tillämpa konfigurationen.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort de resurser som skapats i den här artikeln när de inte längre behövs.

Kör kommandot [terraform förstör](https://www.terraform.io/docs/commands/destroy.html) för att ta bort de Azure-resurser som skapats i den här övningen:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera och konfigurera terraform för att etablera Azure-resurser](/azure/developer/terraform/getting-started-cloud-shell).