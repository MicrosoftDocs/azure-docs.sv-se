---
title: Distribuera en Azure Cloud Service (utökad support) – Azure Portal
description: Distribuera en Azure Cloud Service (utökad support) med hjälp av Azure Portal
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 306294cc654e46dbe8af80b25cb9c709071fad20
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166334"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Distribuera ett Azure-Cloud Services (utökad support) med hjälp av Azure Portal
Den här artikeln förklarar hur du använder Azure Portal för att skapa en distribution av en moln tjänst (utökad support). 

## <a name="before-you-begin"></a>Innan du börjar

Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support) och skapa de associerade resurserna. 

## <a name="deploy-a-cloud-services-extended-support"></a>Distribuera en Cloud Services (utökad support) 
1. Logga in på [Azure-portalen](https://portal.azure.com)

2.  Använd Sök fältet överst i Azure Portal, Sök efter och välj **Cloud Services (utökad support)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Bild visar bladet alla resurser i Azure Portal.":::
 
3.  I fönstret Cloud Services (utökad support) väljer du **skapa**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Bild som visar inköp av en moln tjänst från Marketplace.":::

4. Fönstret för att skapa Cloud Services (utökad support) öppnas i fliken **grundläggande** . 
    - Välj en prenumeration.
    - Välj en resurs grupp eller skapa en ny.
    - Ange önskat namn för distributionen av moln tjänsten (utökad support).
        - DNS-namnet på moln tjänsten är separat och anges av DNS-namn etiketten för den offentliga IP-adressen och kan ändras i avsnittet offentlig IP-adress på fliken konfiguration.
    -  Välj den region som du vill distribuera till.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Bild visar hem bladet Cloud Services (utökade support).":::

5. Lägg till din moln tjänst konfiguration, paket-och definitions filer. Du kan lägga till befintliga filer från Blob Storage eller ladda upp dem från den lokala datorn. Om du laddar upp från den lokala datorn kommer dessa att lagras i ett lagrings konto. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Bild som visar avsnittet Ladda upp på fliken grundläggande vid skapandet.":::

6. När alla fält har slutförts flyttar du till och slutför fliken **konfiguration** . 
    - Välj ett virtuellt nätverk som ska associeras med moln tjänsten eller skapa en ny. 
        - Distributioner av moln tjänster (utökad support) **måste** finnas i ett virtuellt nätverk. Det virtuella nätverket **måste** också refereras i tjänst konfigurations filen (. cscfg) i `NetworkConfiguration` avsnittet.
    - Välj en befintlig offentlig IP-adress som ska associeras med moln tjänsten eller skapa en ny.
        - Om du har **IP-ingångs slut punkter** definierade i din tjänst definitions fil (. csdef) måste du skapa en offentlig IP-adress för din moln tjänst. 
        - Cloud Services (utökad support) stöder endast det grundläggande IP-adress-SKU: n.
        - Om tjänst konfigurationen (. cscfg) innehåller en reserverad IP-adress måste du ange en **statisk** typ för den offentliga IP-adressen. 
        - Alternativt kan du tilldela ett DNS-namn för moln tjänst slut punkten genom att uppdatera egenskapen DNS-etikett för den offentliga IP-adress som är associerad med moln tjänsten.  
    - Valfritt Starta moln tjänsten. Välj Starta eller starta inte tjänsten direkt när du har skapat den.
    - Välj en Key Vault 
        - Key Vault krävs när du anger ett eller flera certifikat i tjänst konfigurations filen (. cscfg). När du väljer ett nyckel valv kommer vi försöka hitta de valda certifikaten från tjänst konfigurations filen (. cscfg) baserat på deras tumavtrycken. Om några certifikat saknas i ditt nyckel valv kan du ladda upp dem nu och klicka på **Uppdatera**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Bild visar konfigurations bladet i Azure Portal när du skapar en Cloud Services (utökad support).":::

7. När alla fält har slutförts går du till fliken **Granska och skapa** för att verifiera distributions konfigurationen och skapa din moln tjänst (utökad support).

## <a name="next-steps"></a>Nästa steg 
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)
