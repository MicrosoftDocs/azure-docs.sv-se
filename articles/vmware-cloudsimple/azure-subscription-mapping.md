---
title: Skapa resurspooler med Azure-prenumerations mappning
titleSuffix: Azure VMware Solution by CloudSimple
description: Beskriver hur du skapar resurspooler för ditt privata moln via Azure-prenumerations mappning
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 7be4a4c601d3f33972c1e52596ef623116dcadd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97897084"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Skapa resurspooler för ditt privata moln med Azure-prenumerations mappning
Med mappning av Azure-prenumeration kan du skapa resurspooler för ditt privata moln från tillgängliga vSphere-resurspooler. I CloudSimple-portalen kan du Visa och hantera Azure-prenumerationen för dina privata moln.

> [!NOTE]
> När du mappar en resurspool mappas även eventuella underordnade resurspooler. Det går inte att mappa en överordnad resurspool om några underordnade resurspooler redan har mappats.

1. [Få åtkomst till CloudSimple-portalen](access-cloudsimple-portal.md).
2. Öppna sidan **resurser** och välj **Mappning av Azure-prenumerationer**.  
3. Klicka på **Redigera Azure-prenumerations mappning**.  
4. Om du vill mappa tillgängliga resurspooler väljer du dem till vänster och klickar på pilen till höger. 
5. Om du vill ta bort mappningar markerar du dem till höger och klickar på pilen till vänster. 

    ![Azure-prenumerationer](media/resources-azure-mapping.png)

6. Klicka på **OK**.
