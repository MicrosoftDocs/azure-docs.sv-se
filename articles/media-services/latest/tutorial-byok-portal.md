---
title: Använda Kundhanterade nycklar eller BYOK i Azure Portal
description: I den här självstudien använder du Azure Portal för att aktivera Kundhanterade nycklar eller ta med din egen nyckel (BYOK) med ett Azure Media Services lagrings konto.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 61df4b3424a4d73eca7a946c6bd8e5160fd6c529
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935383"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Självstudie: Använd Azure Portal för att använda Kundhanterade nycklar eller BYOK med Media Services

Med 2020-05-01-API: et kan du använda en kundhanterad RSA-nyckel med ett Azure Media Services-konto som har en Systemhanterad identitet. I den här självstudien beskrivs stegen i Azure Portal.

De tjänster som används är:

- Azure Storage
- Azure Key Vault
- Azure Media Services

I den här självstudien får du lära dig att använda Azure Portal för att:

> [!div class="checklist"]
> - Skapa en resursgrupp.
> - Skapa ett lagrings konto med en Systemhanterad identitet.
> - Skapa ett Media Services-konto med en Systemhanterad identitet.
> - Skapa ett nyckel valv för att lagra en kundhanterad RSA-nyckel.

## <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>System-hanterade nycklar

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> När du har skapat stegen för att skapa lagrings konto väljer du det systemhanterade Nyckel valet i avancerade inställningar.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> För följande lagrings krypterings steg väljer du det **Kundhanterade Nyckel valet**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Ändra nyckeln

Media Services identifierar automatiskt när nyckeln ändras. Valfritt: om du vill testa den här processen skapar du en annan nyckel version för samma nyckel. Media Services ska identifiera att nyckeln har ändrats.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda de resurser som du har skapat och *du inte vill fortsätta att fakturera*, tar du bort dem.

## <a name="next-steps"></a>Nästa steg

Gå till nästa artikel om du vill lära dig att:
> [!div class="nextstepaction"]
> [Koda en fjärrfil baserat på URL och strömma videon med REST](stream-files-tutorial-with-rest.md)
