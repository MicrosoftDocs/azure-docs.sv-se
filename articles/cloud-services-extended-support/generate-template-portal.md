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
ms.openlocfilehash: a4f206d68df3cd8dd4dd5b1b411d316e7aacde92
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077129"
---
# <a name="generate-arm-template-for-cloud-services-extended-support-using-the-azure-portal"></a>Skapa ARM-mall för Cloud Services (utökad support) med hjälp av Azure Portal

Den här artikeln förklarar hur du hämtar ARM-mallen och parameter filen från [Azure Portal](https://portal.azure.com) för din moln tjänst. ARM-mallen och parameter filen kan användas i distributioner via PowerShell för att skapa eller uppdatera en moln tjänst

## <a name="get-arm-template-via-portal"></a>Hämta ARM-mall via portalen

  1. Gå till Azure Portal och [skapa en ny moln tjänst](deploy-portal.md). Lägg till din moln tjänst konfiguration, paket-och definitions filer. 
    :::image type="content" source="media/deploy-portal-4.png" alt-text="Bild som visar avsnittet Ladda upp på fliken grundläggande vid skapandet.":::
  
  2. När alla fält har slutförts går du till fliken Granska och skapa för att verifiera distributions konfigurationen och klickar på **Hämta mall för automatisering** av moln tjänsten (utökad support).
    :::image type="content" source="media/download-template-portal-1.png" alt-text="Bilden visar hur du hämtar mallen under moln tjänsten (utökad support) på Azure Portal.":::
  
  3. Ladda ned mallen och parametern filer. 
    :::image type="content" source="media/generate-template-portal-3.png" alt-text="Bild som visar hämtning av mallfil på Azure Portal.":::
  
  4. Kopiera paketets SAS-URI och konfigurationens SAS-URI från fliken Granska och skapa och Lägg till dem i filen parameter.js. De här filerna kan nu användas för att skapa en ny moln tjänst via PowerShell.
    :::image type="content" source="media/download-template-portal-2.png" alt-text="Bild visar URL-parametrarna för paketets SAS-URI och konfigurations-SAS i Azure Portal.":::
  
## <a name="next-steps"></a>Nästa steg 
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md)
  
