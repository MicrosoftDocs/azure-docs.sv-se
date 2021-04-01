---
title: Principer för att tagga resurser
description: Beskriver de Azure-principer som du kan tilldela för att säkerställa taggens kompatibilitet.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89293751"
---
# <a name="assign-policies-for-tag-compliance"></a>Tilldela principer för tagg-kompatibilitet

Du använder [Azure policy](../../governance/policy/overview.md) för att tillämpa taggnings regler och konventioner. Genom att skapa en princip undviker du syftet med resurser som distribueras till din prenumeration som inte har de förväntade taggarna för din organisation. I stället för att manuellt tillämpa taggar eller söka efter resurser som inte är kompatibla, skapar du en princip som automatiskt tillämpar de taggar som krävs under distributionen. Taggar kan nu också tillämpas på befintliga resurser med den nya [ändra](../../governance/policy/concepts/effects.md#modify) -effekt och en [reparations uppgift](../../governance/policy/how-to/remediate-resources.md). I följande avsnitt visas exempel principer för taggar.

## <a name="policies"></a>Principer

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om att tagga resurser finns i [använda taggar för att ordna dina Azure-resurser](tag-resources.md).
* Inte alla resurs typer stöder taggar. Information om hur du kan använda en tagg för en resurs typ finns i [tagga stöd för Azure-resurser](tag-support.md).
