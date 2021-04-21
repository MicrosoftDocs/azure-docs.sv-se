---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 592f62eaf1627733f8cf20460b57e3f474f17963
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800056"
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill skapa ett huvudnamn för tjänsten med åtkomst till containerregistret kör du följande [skript i Azure Cloud Shell](../articles/cloud-shell/overview.md) eller en lokal installation av Azure [CLI](/cli/azure/install-azure-cli). Skriptet är formaterat för Bash-gränssnittet.

Innan du kör skriptet uppdaterar du `ACR_NAME` variabeln med namnet på ditt containerregister. Värdet `SERVICE_PRINCIPAL_NAME` måste vara unikt inom din Azure Active Directory klientorganisation. Om du får felet " `'http://acr-service-principal' already exists.` " anger du ett annat namn för tjänstens huvudnamn.

Du kan också ändra värdet `--role` i kommandot az ad sp [create-for-rbac][az-ad-sp-create-for-rbac] om du vill bevilja olika behörigheter. En fullständig lista över roller finns i [ACR-roller och behörigheter.](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)

När du har kört skriptet noterar du ID och lösenord för tjänstens **huvudnamn.**  När du har fått autentiseringsuppgifterna kan du konfigurera dina program och tjänster så att de autentiserar till containerregistret som tjänstens huvudnamn.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Använda ett befintligt huvudnamn för tjänsten

Om du vill bevilja registeråtkomst till ett befintligt huvudnamn för tjänsten måste du tilldela en ny roll till tjänstens huvudnamn. Precis som när du skapar ett nytt huvudnamn för tjänsten kan du bland annat bevilja pull-, push- och pull-åtkomst och ägaråtkomst.

Följande skript använder kommandot [az role assignment create för][az-role-assignment-create] att bevilja *pull-behörigheter* till ett huvudnamn för tjänsten som du anger i `SERVICE_PRINCIPAL_ID` variabeln. Justera `--role` värdet om du vill bevilja en annan åtkomstnivå.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
