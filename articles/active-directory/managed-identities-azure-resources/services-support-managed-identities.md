---
title: Azure-tjänster som har stöd för hanterade identiteter – Azure AD
description: Lista över tjänster som stöder hanterade identiteter för Azure-resurser och Azure AD-autentisering
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 058873df989c444ebe06fc20a2f8a40fd2d3c594
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594554"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till en tjänst som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i din kod. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster har stöd för hanterade identiteter för Azure-resurser:


### <a name="azure-api-management"></a>Azure API Management

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Få][check]  | Inte tillgängligt  | ![Få][check] |

I följande lista kan du konfigurera hanterad identitet för Azure App konfiguration (i regioner där det är tillgängligt):

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Få][check]  | ![Få][check]  | ![Få][check] |

I följande lista kan du konfigurera hanterad identitet för Azure App Service (i regioner där det finns tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-aktiverade Kubernetes

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Azure Arc-aktiverade Kubernetes [stöder för närvarande systemtilldelad identitet](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Det hanterade tjänst identitets certifikatet används av alla Azure Arc-aktiverade Kubernetes-agenter för kommunikation med Azure.

### <a name="azure-arc-enabled-servers"></a>Azure Arc-aktiverade servrar

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Alla Azure Arc-aktiverade servrar har en tilldelad identitet. Du kan inte inaktivera eller ändra systemets tilldelade identitet på en Azure Arc-aktiverad server. Se följande resurser för att lära dig mer om hur du använder hanterade identiteter på Azure Arc-aktiverade servrar:

- [Autentisera mot Azure-resurser med ARC-aktiverade servrar](../../azure-arc/servers/managed-identity-authentication.md)
- [Använda en hanterad identitet med ARC-aktiverade servrar](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande dokument för att konfigurera om en hanterad identitet om du har flyttat din prenumeration till en ny klient organisation:
* [Reparera ett trasigt automanage-konto](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att använda en hanterad identitet med [Azure-skisser](../../governance/blueprints/overview.md):

- [Azure Portal skiss tilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API skiss tilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


### <a name="azure-communication-services"></a>Azure Communication Services

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Linux: för hands version<br>Windows: inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Instances (i regioner där det finns tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Container Registry uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure-datautforskaren

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Data Factory v2 (i regioner där det finns):

- [Azure-portalen](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [GRÄNSSNITTET](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för digitala Digital-ID: t (i regioner där det finns tillgängligt):

- [Azure-portalen](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig  | Inte tillgänglig  | Inte tillgänglig |

### <a name="azure-firewall-policy"></a>Princip för Azure-brandvägg

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig  | Inte tillgänglig  | Inte tillgänglig |

### <a name="azure-functions"></a>Azure Functions

Hanterad identitets typ |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Få][check]  | ![Få][check]  | ![Få][check]  |

I följande lista kan du konfigurera hanterad identitet för Azure Functions (i regioner där det finns tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure IoT Hub (i regioner där det är tillgängligt):

- [Azure-portalen](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt i regionen där Azure import export-tjänsten är tillgänglig | Förhandsgranskning | Tillgänglig | Tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


Mer information finns i [använda hanterade identiteter i Azure Kubernetes-tjänsten](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics-kluster

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |

Mer information finns i [så här fungerar identitet i Azure Monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Få][check] |


I följande lista kan du konfigurera hanterad identitet för Azure Logic Apps (i regioner där det finns tillgängligt):

- [Azure-portalen](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-mall](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgängligt | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Mer information finns i [använda hanterade identiteter med Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

I följande lista kan du konfigurera hanterad identitet för Azure Policy (i regioner där det finns tillgängligt):

- [Azure-portalen](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Azure Resource Manager-mallar](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Hanterad identitet för Service Fabric program](../../service-fabric/concepts-managed-identity.md) är tillgänglig i alla regioner.

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | Inte tillgängligt | Inte tillgängligt | inte tillgängligt |
| Användartilldelad | ![Få][check] | Inte tillgängligt | Inte tillgängligt |Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:

- [Azure Resource Manager-mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | Inte tillgängligt | Inte tillgängligt | ![Få][check] |
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |


Mer information finns i [så här aktiverar du systemtilldelad hanterad identitet för Azure våren Cloud Application](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt i den region där Azure Stack Edge-tjänsten är tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |
| Användartilldelad | ![Få][check] | ![Få][check] | ![Få][check] | ![Få][check] |

I följande lista kan du konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure-SDK:er](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Image Builder för Azure VM

| Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Användartilldelad | [Tillgängligt i regioner som stöds](../../virtual-machines/image-builder-overview.md#regions) | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

Information om hur du konfigurerar hanterad identitet för Azure VM Image Builder (i regioner där tillgänglig) finns i [Översikt över Image Builder](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR Service

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

I följande lista kan du konfigurera hanterad identitet för Azure SignalR service (i regioner där det finns):

- [Azure Resource Manager-mall](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure-resursflyttare

Hanterad identitets typ | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Tillgängligt i de regioner där Azure Resource Personaling-tjänsten är tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande dokument för att använda Azure Resource superflytt:

- [Azure-resursflyttare](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klient tjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure Portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via Azure Resource Manager mall](../../role-based-access-control/role-assignments-template.md)

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Få][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Få][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Få][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Få][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Få][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Få][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Få][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-sql"></a>Azure SQL

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Få][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Få][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Få][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Få][check] |

### <a name="azure-data-explorer"></a>Azure-datautforskaren

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Få][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland | `https://<account>.<region>.kusto.cloudapi.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Få][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Få][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-service-bus"></a>Azure Service Bus

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Få][check] |
| Azure Government |  | ![Få][check] |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobbar och köer

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Få][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Få][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Få][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Få][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Få][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Få][check] |

> [!Note]
> Microsoft Power BI [stöder också hanterade identiteter](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Få"
