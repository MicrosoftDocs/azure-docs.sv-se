---
title: ta med fil
description: ta med fil
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99569547"
---
Som standard kan standard fil resurser bara omfatta upp till 5 TiB, men du kan öka gränsen för delning till 100 TiB. Aktivera **stor fil resurs** på ditt lagrings konto om du vill öka gränsen för din resurs. Premium Storage-konton (*FileStorage* lagrings konton) har inte den stora fil resursens funktions flagga eftersom alla Premium-filresurser redan har Aktiver ATS för etablering upp till den fullständiga 100-TIB-kapaciteten.

Du kan bara aktivera stora fil resurser på lokalt redundanta eller redundanta standard lagrings konton. När du har aktiverat funktions flaggan stor fil resurs kan du inte ändra redundans nivån till Geo-redundant eller geo-Zone-redundant lagring.

Om du vill aktivera stora fil resurser på ett befintligt lagrings konto navigerar du till **fil resurser** i lagrings kontots innehålls förteckning.
På det här bladet väljer du **dela kapacitet**, ändrar resurs kapaciteten till **100 TIB** och väljer **Spara**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="En skärm bild av inställningen Aktivera stor fil resurs deltagande i Azure Portal." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Du kan också aktivera 100-TiB fil resurser via [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) PowerShell-cmdleten och [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) Azure CLI-kommandot. Detaljerade anvisningar om hur du aktiverar stora fil resurser finns i [Aktivera och skapa stora fil resurser](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Mer information om hur du skapar fil resurser på nya lagrings konton finns i [skapa en Azure-filresurs](../articles/storage/files/storage-how-to-create-file-share.md).