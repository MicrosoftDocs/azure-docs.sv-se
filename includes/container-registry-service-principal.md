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
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244474"
---
## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Om du vill skapa ett huvud namn för tjänsten med åtkomst till ditt behållar register kör du följande skript i [Azure Cloud Shell](../articles/cloud-shell/overview.md) eller en lokal installation av [Azure CLI](/cli/azure/install-azure-cli). Skriptet är formaterat för bash-gränssnittet.

Uppdatera `ACR_NAME` variabeln med namnet på behållar registret innan du kör skriptet. `SERVICE_PRINCIPAL_NAME`Värdet måste vara unikt inom Azure Active Directory-klienten. Om du får ett " `'http://acr-service-principal' already exists.` "-fel anger du ett annat namn för tjänstens huvud namn.

Du kan också ändra `--role` värdet i kommandot [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] om du vill bevilja olika behörigheter. En fullständig lista över roller finns i [ACR roller och behörigheter](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

När du har kört skriptet bör du anteckna **ID** och **lösen ord** för tjänstens huvud namn. När du har angett autentiseringsuppgifterna kan du konfigurera dina program och tjänster för att autentisera till behållar registret som tjänstens huvud namn.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Använd ett befintligt huvud namn för tjänsten

Om du vill bevilja register åtkomst till ett befintligt huvud namn för tjänsten måste du tilldela en ny roll till tjänstens huvud namn. När du skapar ett nytt huvud namn för tjänsten kan du bland annat bevilja pull-, push-och pull-och ägar åtkomst.

Följande skript använder kommandot [AZ Role Assignment Create][az-role-assignment-create] för att ge *pull* -behörighet till ett huvud namn för tjänsten som du anger i `SERVICE_PRINCIPAL_ID` variabeln. Justera `--role` värdet om du vill bevilja en annan åtkomst nivå.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
