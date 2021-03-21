---
title: Använd Azure CLI för att skapa en Azure AD-App och konfigurera den för att få åtkomst till Azure Media Services API | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure CLI för att skapa en Azure AD-App och konfigurerar den för att få åtkomst till Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6517cc8224a1a4e2e3319fde52fb621e4f7ab564
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016941"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Använd Azure CLI för att skapa en Azure AD-App och konfigurera den för åtkomst till Media Services-API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Det här avsnittet visar hur du använder Azure CLI för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvud namn för att få åtkomst till Azure Media Services resurser. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Mer information finns i [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Använd Azure Cloud Shell

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Starta Cloud Shell från det övre navigerings fönstret i portalen.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Mer information finns i [Översikt över Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Skapa en Azure AD-App och konfigurera åtkomst till media-kontot med Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Exempel:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

I det här exemplet är **omfånget** den fullständiga resurs Sök vägen för Media Services-kontot. **Omfattningen** kan dock finnas på vilken nivå som helst.

Det kan till exempel vara någon av följande nivåer:
 
* **Prenumerations** nivån.
* **Resurs grupps** nivå.
* **Resurs** nivån (till exempel ett medie konto).

Mer information finns i [skapa ett huvud namn för Azure-tjänsten med Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli)

Se även [lägga till eller ta bort Azure-roll tilldelningar med Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
