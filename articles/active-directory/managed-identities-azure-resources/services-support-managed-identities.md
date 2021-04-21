---
title: Azure-tjänster som stöder hanterade identiteter – Azure AD
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
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771487"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Tjänster som stöder hanterade identiteter för Azure-resurser

Hanterade identiteter för Azure-resurser ger Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory. Med hjälp av en hanterad identitet kan du autentisera till alla tjänster som stöder Azure AD-autentisering utan att ha autentiseringsuppgifter i koden. Vi håller på att integrera hanterade identiteter för Azure-resurser och Azure AD-autentisering i Azure. Kom tillbaka ofta för uppdateringar.

> [!NOTE]
> Hanterade identiteter för Azure-resurser är det nya namnet på tjänsten som tidigare hade namnet Hanterad tjänstidentitet (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-tjänster som stöder hanterade identiteter för Azure-resurser

Följande Azure-tjänster stöder hanterade identiteter för Azure-resurser:


### <a name="azure-api-management"></a>Azure API Management

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

Se följande lista för att konfigurera hanterad identitet för Azure API Management (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgängligt][check]  | Inte tillgängligt  | ![Tillgängligt][check] |

Se följande lista för att konfigurera hanterad identitet för Azure App Configuration (i regioner där det är tillgängligt):

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgängligt][check]  | ![Tillgängligt][check]  | ![Tillgängligt][check] |

Se följande lista för att konfigurera hanterad identitet för Azure App Service (i regioner där det är tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc-aktiverade Kubernetes

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Azure Arc aktiverat Kubernetes stöder för [närvarande system tilldelad identitet](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Det hanterade tjänstidentitetscertifikatet används av alla Azure Arc Kubernetes-agenter för kommunikation med Azure.

### <a name="azure-arc-enabled-servers"></a>Azure Arc-aktiverade servrar

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Alla Azure Arc-aktiverade servrar har en system tilldelad identitet. Du kan inte inaktivera eller ändra den system tilldelade identiteten på en Azure Arc aktiverad server. Se följande resurser om du vill veta mer om hur du använder hanterade identiteter på Azure Arc aktiverade servrar:

- [Autentisera mot Azure-resurser med Arc-aktiverade servrar](../../azure-arc/servers/managed-identity-authentication.md)
- [Använda en hanterad identitet med Arc-aktiverade servrar](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande dokument för att konfigurera om en hanterad identitet om du har flyttat din prenumeration till en ny klientorganisation:
* [Reparera ett brutet automanagekonto](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att använda en hanterad identitet med [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure Portal – skisstilldelning](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API – skisstilldelning](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


### <a name="azure-communication-services"></a>Azure Communication Services

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


### <a name="azure-container-instances"></a>Azure Container Instances

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Linux: Förhandsversion<br>Windows: Inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Linux: Förhandsversion<br>Windows: Inte tillgängligt | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Container Instances (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-mall](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Container Registry-uppgifter (i regioner där det är tillgängligt):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Öppna Azure-datautforskaren

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Data Factory V2 (i regioner där det är tillgängligt):

- [Azure-portalen](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sdk](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Digital Twins (i regioner där det är tillgängligt):

- [Azure-portalen](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Hanterad identitetstyp |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig  | Inte tillgänglig  | Inte tillgänglig |

### <a name="azure-firewall-policy"></a>Azure Firewall princip

Hanterad identitetstyp |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig  | Inte tillgänglig  | Inte tillgänglig |

### <a name="azure-functions"></a>Azure Functions

Hanterad identitetstyp |Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgängligt][check]  | ![Tillgängligt][check]  | ![Tillgängligt][check]  |

Se följande lista för att konfigurera hanterad identitet för Azure Functions (i regioner där det är tillgängligt):

- [Azure-portalen](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-mall](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure IoT Hub (i regioner där det är tillgängligt):

- [Azure-portalen](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt i den region där Azure Import Export-tjänsten är tillgänglig | Förhandsgranskning | Tillgängligt | Tillgängligt |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |


Mer information finns i [Använda hanterade identiteter i Azure Kubernetes Service](../../aks/use-managed-identity.md).

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics-kluster

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |

Mer information finns i [hur identiteter fungerar i Azure Monitor](../../azure-monitor/logs/customer-managed-keys.md)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgänglig][check] | Inte tillgängligt | ![Tillgängligt][check] |


Se följande lista för att konfigurera hanterad identitet för Azure Logic Apps (i regioner där det är tillgängligt):

- [Azure-portalen](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-mall](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Inte tillgängligt | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Förhandsgranskning | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Mer information finns i [Använda hanterade identiteter med Azure Machine Learning](../../machine-learning/how-to-use-managed-identities.md).

### <a name="azure-policy"></a>Azure Policy

|Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande lista för att konfigurera hanterad identitet för Azure Policy (i regioner där det är tillgängligt):

- [Azure-portalen](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure Resource Manager-mallar](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Hanterad identitet för Service Fabric program](../../service-fabric/concepts-managed-identity.md) är tillgänglig i alla regioner.

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | Inte tillgängligt | Inte tillgängligt | inte tillgänglig |
| Användartilldelad | ![Tillgängligt][check] | Inte tillgängligt | Inte tillgängligt |Inte tillgängligt |

Se följande lista för att konfigurera hanterad identitet för Azure Service Fabric-program i alla regioner:

- [Azure Resource Manager-mall](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | Inte tillgängligt | Inte tillgängligt | ![Tillgängligt][check] |
| Användartilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |


Mer information finns i Aktivera [system tilldelad hanterad identitet för Azure Spring Cloud program](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md).

### <a name="azure-stack-edge"></a>Azure Stack Edge

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | --- | --- | --- | --- |
| Systemtilldelad | Tillgängligt i den region där Azure Stack Edge tjänst är tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |

Se följande lista för att konfigurera hanterad identitet för Azure Virtual Machine Scale Sets (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |
| Användartilldelad | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] | ![Tillgängligt][check] |

Se följande lista för att konfigurera hanterad identitet för Azure Virtual Machines (i regioner där det är tillgängligt):

- [Azure-portalen](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mallar](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure-SDK:er](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |
| Användartilldelad | [Tillgängligt i regioner som stöds](../../virtual-machines/image-builder-overview.md#regions) | Inte tillgängligt | Inte tillgängligt | Inte tillgängligt |

Information om hur du konfigurerar hanterade identiteter för Azure VM Image Builder (i regioner där det är tillgängligt) finns [i Image Builder översikt](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR Service

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |
| Användartilldelad | Förhandsgranskning | Förhandsgranskning | Inte tillgängligt | Förhandsgranskning |

Se följande lista för att konfigurera hanterad identitet för Azure SignalR Service (i regioner där det är tillgängligt):

- [Azure Resource Manager-mall](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure-resursflyttare

Hanterad identitetstyp | Alla allmänt tillgängliga<br>Globala Azure-regioner | Azure Government | Azure Tyskland | Azure Kina 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Systemtilldelad | Tillgängligt i de regioner där Azure Resource Mover tjänst är tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |
| Användartilldelad | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig | Inte tillgänglig |

Se följande dokument för att använda Azure Resource Mover:

- [Azure-resursflyttare](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-tjänster som stöder Azure AD-autentisering

Följande tjänster stöder Azure AD-autentisering och har testats med klienttjänster som använder hanterade identiteter för Azure-resurser.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Se följande lista för att konfigurera åtkomst till Azure Resource Manager:

- [Tilldela åtkomst via Azure Portal](howto-assign-access-portal.md)
- [Tilldela åtkomst via PowerShell](howto-assign-access-powershell.md)
- [Tilldela åtkomst via Azure CLI](howto-assign-access-CLI.md)
- [Tilldela åtkomst via en Azure Resource Manager mall](../../role-based-access-control/role-assignments-template.md)

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Tillgängligt][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Tillgängligt][check] |
| Azure Tyskland | `https://management.microsoftazure.de/` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://management.chinacloudapi.cn` | ![Tillgängligt][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Tillgängligt][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Tillgängligt][check] |
| Azure Tyskland |  `https://vault.microsoftazure.de` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://vault.azure.cn` | ![Tillgängligt][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Tillgängligt][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-sql"></a>Azure SQL

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Tillgängligt][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Tillgängligt][check] |
| Azure Tyskland | `https://database.cloudapi.de/` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://database.chinacloudapi.cn/` | ![Tillgängligt][check] |

### <a name="azure-data-explorer"></a>Öppna Azure-datautforskaren

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Tillgängligt][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Tillgängligt][check] |
| Azure Tyskland | `https://<account>.<region>.kusto.cloudapi.de` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Tillgängligt][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Tillgängligt][check] |
| Azure Government |  | Inte tillgängligt |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |

### <a name="azure-service-bus"></a>Azure Service Bus

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Tillgängligt][check] |
| Azure Government |  | ![Tillgängligt][check] |
| Azure Tyskland |   | Inte tillgängligt |
| Azure Kina 21Vianet |  | Inte tillgängligt |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobar och köer

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Tillgängligt][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Tillgängligt][check] |
| Azure Tyskland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Tillgängligt][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Moln | Resurs-ID | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Tillgängligt][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Tillgängligt][check] |
| Azure Tyskland | `https://*.asazure.cloudapi.de` | ![Tillgängligt][check] |
| Azure Kina 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Tillgängligt][check] |

> [!Note]
> Microsoft Power BI har även [stöd för hanterade identiteter](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Tillgängliga"
