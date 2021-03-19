---
title: 'Terraform: Distribuera frontend-webbappen och backend-webbappen säkert ansluten med VNet-integrering och privat slut punkt'
description: Lär dig hur du använder terraform-providern för att App Service distribuera två webbappar anslutna säkert med privat slut punkt och VNet-integrering
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91739841"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Skapa två webbappar anslutna på ett säkert sätt med privat slut punkt och VNet-integrering

Den här artikeln illustrerar ett exempel på användning av [privat slut punkt](../networking/private-endpoint.md) och regional [VNet-integrering](../web-sites-integrate-with-vnet.md) för att ansluta två webbappar (frontend och Server del) på ett säkert sätt i följande steg:
- Distribuera ett VNet
- Skapa det första under nätet för integreringen
- Skapa det andra under nätet för den privata slut punkten. du måste ange en speciell parameter för att inaktivera nätverks principer
- Distribuera en App Service plan av typen PremiumV2 eller PremiumV3, som krävs för privat slut punkts funktion
- Skapa frontend-webbappen med vissa appinställningar för att använda den privata DNS-zonen, [Mer information](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Anslut frontend-webbappen till integrations under nätet
- Skapa backend-webbappen
- Skapa en privat DNS-zon med namnet på den privata länk zonen för Web App-privatelink.azurewebsites.net
- Länka den här zonen till VNet
- Skapa den privata slut punkten för backend-webbappen i slut punkts under nätet och registrera DNS-namn (webbplats och SCM) i den tidigare skapade privata DNS-zonen

## <a name="how-to-use-terraform-in-azure"></a>Så här använder du terraform i Azure

Gå till [Azure-dokumentationen](/azure/developer/terraform/) och lär dig hur du använder terraform med Azure.

## <a name="the-complete-terraform-file"></a>Den fullständiga terraform-filen

Om du vill använda den här filen måste du ändra egenskapen namn för frontwebapp och bakwebapp-resurser (webapp-namnet måste vara ett unikt DNS-namn i hela världen). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Nästa steg


> [Lär dig mer om hur du använder terraform i Azure](/azure/developer/terraform/)