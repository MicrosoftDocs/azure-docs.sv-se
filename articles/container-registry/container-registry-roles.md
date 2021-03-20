---
title: Register roller och behörigheter
description: Använd rollbaserad åtkomst kontroll i Azure (Azure RBAC) och identitets-och åtkomst hantering (IAM) för att ge detaljerade behörigheter till resurser i ett Azure Container Registry.
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 097ccf89caf63d2a504d072cf04c2b534a57a031
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92207962"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Azure Container Registry roller och behörigheter

Tjänsten Azure Container Registry stöder en uppsättning [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md) som ger olika behörighets nivåer till ett Azure Container Registry. Använd [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/index.yml) för att tilldela särskilda behörigheter till användare, tjänst huvud namn eller andra identiteter som behöver samverka med ett register, till exempel för att hämta eller skicka behållar avbildningar. Du kan också definiera [anpassade roller](#custom-roles) med detaljerade behörigheter till ett register för olika åtgärder.

| Roll/behörighet       | [Åtkomst till Resource Manager](#access-resource-manager) | [Skapa/ta bort registret](#create-and-delete-registry) | [Push-avbildning](#push-image) | [Hämta bild](#pull-image) | [Ta bort avbildnings data](#delete-image-data) | [Ändra principer](#change-policies) |   [Signera bilder](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Ägare | X | X | X | X | X | X |  |  
| Deltagare | X | X | X |  X | X | X |  |  
| Läsare | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="assign-roles"></a>Tilldela roller

Se [steg för att lägga till en roll tilldelning](../role-based-access-control/role-assignments-steps.md) för avancerade steg för att lägga till en roll tilldelning till en befintlig användare, grupp, tjänstens huvud namn eller hanterad identitet. Du kan använda Azure Portal, Azure CLI eller andra Azure-verktyg.

När du skapar ett huvud namn för tjänsten konfigurerar du också dess åtkomst och behörighet till Azure-resurser, till exempel ett behållar register. Ett exempel skript som använder Azure CLI finns i [Azure Container Registry autentisering med tjänstens huvud namn](container-registry-auth-service-principal.md#create-a-service-principal).

## <a name="differentiate-users-and-services"></a>Särskilja användare och tjänster

När du använder en viss tids period är det en bra idé att tillhandahålla den mest begränsade uppsättningen behörigheter för en person, eller tjänst, för att utföra en uppgift. Följande behörighets uppsättningar representerar en uppsättning funktioner som kan användas av människor och konsol lösa tjänster.

### <a name="cicd-solutions"></a>CI/CD-lösningar

När du automatiserar `docker build` kommandon från CI/CD-lösningar behöver du `docker push` funktioner. För dessa sammanhang rekommenderar vi att du tilldelar **AcrPush** -rollen. Den här rollen, till skillnad från rollen bredare **deltagare** , förhindrar att kontot utför andra register åtgärder eller använder Azure Resource Manager.

### <a name="container-host-nodes"></a>Noder i container värden

Noder som kör dina behållare behöver inte heller **AcrPull** -rollen, men behöver inte **läsa** in funktioner.

### <a name="visual-studio-code-docker-extension"></a>Tillägg för Visual Studio Code Docker

För verktyg som Visual Studio Code [Docker-tillägget](https://code.visualstudio.com/docs/azure/docker)krävs ytterligare resurs leverantörs åtkomst för att lista tillgängliga Azure Container register. I det här fallet ger du användarna åtkomst till **läsaren** eller **deltagar** rollen. De här rollerna tillåter `docker pull` , `docker push` ,, `az acr list` `az acr build` och andra funktioner. 

## <a name="access-resource-manager"></a>Åtkomst till Resource Manager

Azure Resource Manager åtkomst krävs för Azure Portal-och register hantering med [Azure CLI](/cli/azure/). Om du till exempel vill hämta en lista över register med hjälp av `az acr list` kommandot behöver du den här behörighets uppsättningen. 

## <a name="create-and-delete-registry"></a>Skapa och ta bort registret

Möjlighet att skapa och ta bort Azure-behållar register.

## <a name="push-image"></a>Push-avbildning

Möjligheten till `docker push` en bild, eller skicka en annan [artefakt som stöds](container-registry-image-formats.md) , till exempel ett Helm-diagram, till ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten. 

## <a name="pull-image"></a>Hämta bild

Möjligheten till `docker pull` en icke-karantän avbildning, eller hämta en annan [artefakt som stöds](container-registry-image-formats.md) , till exempel ett Helm-diagram, från ett register. Kräver [autentisering](container-registry-authentication.md) med registret med den auktoriserade identiteten.

## <a name="delete-image-data"></a>Ta bort avbildnings data

Möjligheten att [ta bort behållar avbildningar](container-registry-delete.md)eller ta bort andra [artefakter som stöds](container-registry-image-formats.md) , till exempel Helm-diagram, från ett register.

## <a name="change-policies"></a>Ändra principer

Möjlighet att konfigurera principer i ett register. Principerna omfattar avbildnings rensning, aktivering av karantän och avbildnings signering.

## <a name="sign-images"></a>Signera bilder

Möjlighet att signera bilder, som vanligt vis tilldelas till en automatiserad process, som använder ett huvud namn för tjänsten. Den här behörigheten kombineras vanligt vis med [push-avbildning](#push-image) för att tillåta att en betrodd avbildning överförs till ett register. Mer information finns [i innehålls förtroende i Azure Container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Anpassade roller

Precis som med andra Azure-resurser kan du skapa [anpassade roller](../role-based-access-control/custom-roles.md) med detaljerade behörigheter att Azure Container Registry. Tilldela sedan de anpassade rollerna till användare, tjänstens huvud namn eller andra identiteter som behöver samverka med ett register. 

För att avgöra vilka behörigheter som ska gälla för en anpassad roll, se listan över Microsoft. ContainerRegistry- [åtgärder](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry), granska de tillåtna åtgärderna för de [inbyggda ACR-rollerna](../role-based-access-control/built-in-roles.md)eller kör följande kommando:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

För att definiera en anpassad roll, se [steg för att skapa en anpassad roll](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role).

> [!IMPORTANT]
> I en anpassad roll stöder Azure Container Registry för närvarande inte jokertecken som till exempel `Microsoft.ContainerRegistry/*` eller `Microsoft.ContainerRegistry/registries/*` som beviljar åtkomst till alla matchande åtgärder. Ange eventuella nödvändiga åtgärder individuellt i rollen.

### <a name="example-custom-role-to-import-images"></a>Exempel: anpassad roll för att importera bilder

Följande JSON definierar till exempel minimi åtgärderna för en anpassad roll som tillåter att [Importera avbildningar](container-registry-import-images.md) till ett register.

```json
{
   "assignableScopes": [
     "/subscriptions/<optional, but you can limit the visibility to one or more subscriptions>"
   ],
   "description": "Can import images to registry",
   "Name": "AcrImport",
   "permissions": [
     {
       "actions": [
         "Microsoft.ContainerRegistry/registries/push/write",
         "Microsoft.ContainerRegistry/registries/pull/read",
         "Microsoft.ContainerRegistry/registries/read",
         "Microsoft.ContainerRegistry/registries/importImage/action"
       ],
       "dataActions": [],
       "notActions": [],
       "notDataActions": []
     }
   ],
   "roleType": "CustomRole"
 }
```

Om du vill skapa eller uppdatera en anpassad roll med hjälp av JSON-beskrivningen använder du [Azure CLI](../role-based-access-control/custom-roles-cli.md), [Azure Resource Manager mall](../role-based-access-control/custom-roles-template.md), [Azure PowerShell](../role-based-access-control/custom-roles-powershell.md)eller andra Azure-verktyg. Lägg till eller ta bort roll tilldelningar för en anpassad roll på samma sätt som du hanterar roll tilldelningar för inbyggda Azure-roller.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om hur du tilldelar Azure-roller till en Azure-identitet med hjälp av [Azure Portal](../role-based-access-control/role-assignments-portal.md), [Azure CLI](../role-based-access-control/role-assignments-cli.md)eller andra Azure-verktyg.

* Lär dig mer om [autentiseringsalternativ](container-registry-authentication.md) för Azure Container Registry.

* Lär dig mer om hur du aktiverar [databasens begränsade behörigheter](container-registry-repository-scoped-permissions.md) (för hands version) i ett behållar register.
