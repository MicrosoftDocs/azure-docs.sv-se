---
title: Skapa ARM-mall för Cloud Services (utökad support) med hjälp av Azure Portal
description: Skapa och hämta ARM-mall och parameter fil för Cloud Services (utökad support) med hjälp av Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 03/07/2021
ms.custom: ''
ms.openlocfilehash: 9d40bbd7e08d8d3869166827a22f3f08536532bb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104590712"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Skapa ARM-mall för Cloud Services (utökad support) med hjälp av Azure Portal

Den här artikeln beskriver hur du hämtar ARM-mallen och parameter filen från [Azure Portal](https://portal.azure.com) när moln tjänsten (utökad support) har distribuerats. ARM-mallen och parameter filen kan användas i framtida distributioner för att uppgradera eller uppdatera en moln tjänst (utökad support)

## <a name="get-arm-template-via-portal"></a>Hämta ARM-mall via portalen

  1. Gå till din resurs grupp och välj distributioner.
  :::image type="content" source="media/generate-template-portal-1.png" alt-text="Bilden visar hur du väljer distributioner under resurs grupp på Azure Portal.":::
  
  2. Välj din moln tjänst (utökad support) och klicka på mall.
  :::image type="content" source="media/generate-template-portal-2.png" alt-text="Bilden visar hur du väljer mall under moln tjänst (utökad support) på Azure Portal.":::
  
  3. Ladda ned mallen och parametern filer. Dessa kan användas för framtida distributioner via PowerShell.
  :::image type="content" source="media/generate-template-portal-3.png" alt-text="Bild som visar hämtning av mallfil på Azure Portal.":::
  
## <a name="next-steps"></a>Nästa steg 
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md)
  
