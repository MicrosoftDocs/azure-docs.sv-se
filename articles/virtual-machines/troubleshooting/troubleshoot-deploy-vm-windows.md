---
title: Felsöka distribution av problem med virtuella Windows-datorer i Azure | Microsoft Docs
description: Felsök distribution av problem med att distribuera virtuella Windows-datorer i Azure Resource Manager distributions modell.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 98a16e0a60ddf149e8f0e1a092051f3e98ea8225
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627070"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Felsöka problem med distribution av virtuella Windows-datorer i Azure

För att felsöka distributions problem med virtuella datorer i Azure kan du läsa om de vanligaste [problemen](#top-issues) med vanliga fel och lösningar.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

## <a name="top-issues"></a>Främsta problem
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klustret har inte stöd för den begärda virtuella dator storleken
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Gör om begäran med en mindre VM-storlek.
- Om det inte går att ändra storleken på den begärda virtuella datorn:
    - Stoppa alla virtuella datorer i tillgänglighets uppsättningen. Klicka på **resurs grupper** > resurs gruppen > **resurser** > din tillgänglighets uppsättning > **Virtual Machines** > den virtuella datorn > **stoppa**.
    - När alla virtuella datorer har stoppats skapar du den virtuella datorn i önskad storlek.
    - Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på Starta.


## <a name="the-cluster-does-not-have-free-resources"></a>Det finns inga lediga resurser för klustret
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Gör om begäran senare.
- Om den nya virtuella datorn kan ingå i en annan tillgänglighets uppsättning
    - Skapa en virtuell dator i en annan tillgänglighets uppsättning (i samma region).
    - Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Hur kan jag använda och distribuera en Windows-klient avbildning till Azure?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling/testnings scenarier om du har en lämplig prenumeration på Visual Studio (tidigare MSDN). Den här [artikeln](../windows/client-images.md) beskriver behörighets kraven för att köra Windows-klienten i Azure och använda Azure Gallery-avbildningar.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hur kan jag distribuera en virtuell dator med hjälp av Hybrid Use Benefit (hubb)?

Det finns ett par olika sätt att distribuera virtuella Windows-datorer med Azure Hybrid Use-förmånen.

För en Enterprise-avtal-prenumeration:

• Distribuera virtuella datorer från vissa Marketplace-avbildningar som är förkonfigurerade med Azure Hybrid Use Benefit.

För Enterprise-avtal:

• Ladda upp en anpassad virtuell dator och distribuera med hjälp av en Resource Manager-mall eller Azure PowerShell.

Mer information finns i följande resurser:

 - [Översikt över Azure Hybrid Use Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Vanliga frågor och svar](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Azure Hybrid Use Benefit för Windows Server och Windows-klienten](../windows/hybrid-use-benefit-licensing.md).

 - [Hur kan jag använda Hybrid Use-förmånen i Azure](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Hur gör jag för att aktivera min månads kredit för Visual Studio Enterprise (BizSpark)

Information om hur du aktiverar din månatliga kredit finns i den här [artikeln](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Hur lägger jag till Enterprise Dev/Test i Enterprise-avtal (EA) för att få åtkomst till klient avbildningar för Windows?

Möjligheten att skapa prenumerationer som baseras på Enterprise Dev/Test erbjudandet är begränsad till konto innehavare som har fått tillstånd att göra detta av en företags administratör. Konto ägaren skapar prenumerationer via Azures konto Portal och lägger sedan till aktiva Visual Studio-prenumeranter som medadministratörer. Så att de kan hantera och använda de resurser som krävs för utveckling och testning. Mer information finns i [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Mina driv rutiner saknas för den virtuella Windows N-serien

Instruktioner för att installera driv rutiner för Windows-baserade virtuella datorer finns [här](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Jag kan inte hitta en GPU-instans i min virtuella dator i N-serien

Om du vill dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera grafik driv rutiner på varje virtuell dator efter distributionen. Information om driv rutins installation finns [här](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Är virtuella datorer i N-serien tillgängliga i min region?

Du kan kontrol lera tillgängligheten från [tabellen produkter som är tillgängliga per region](https://azure.microsoft.com/regions/services)och prissättning [här](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Vilka klient avbildningar kan jag använda och distribuera i Azure och hur jag får dem?

Du kan använda Windows 7, Windows 8 eller Windows 10 i Azure för utveckling/testning-scenarier förutsatt att du har en lämplig Visual Studio-prenumeration (tidigare MSDN). 

- Windows 10-avbildningar är tillgängliga från Azure-galleriet inom [berättigade erbjudanden om utveckling/testning](../windows/client-images.md). 
- Visual Studio-prenumeranter inom vilken typ av erbjudande som helst kan också [förbereda och skapa](../windows/prepare-for-upload-vhd-image.md) en 64-bitars Windows 7-, Windows 8-eller Windows 10-avbildning och sedan [Ladda upp till Azure](../windows/upload-generalized-managed.md). Användningen är begränsad till utveckling/testning av aktiva Visual Studio-prenumeranter.

Den här [artikeln](../windows/client-images.md) beskriver behörighets kraven för att köra Windows-klienten i Azure och använda Azure Gallery-avbildningar.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Jag kan inte se den virtuella datorns storleks familj som jag vill när du ändrar storlek på den virtuella datorn.

När en virtuell dator körs distribueras den till en fysisk server. De fysiska servrarna i Azure-regionerna grupperas i kluster av gemensamt fysiskt maskin vara. Att ändra storlek på en virtuell dator som kräver att den virtuella datorn flyttas till olika maskin varu kluster är olika beroende på vilken distributions modell som användes för att distribuera den virtuella datorn.

- Virtuella datorer som distribueras i den klassiska distributions modellen måste moln tjänst distributionen tas bort och omdistribueras för att ändra de virtuella datorerna till en storlek i en annan storleks familj.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Virtuella datorer som distribueras i distributions modellen för Resource Manager måste du stoppa alla virtuella datorer i tillgänglighets uppsättningen innan du ändrar storleken på virtuella datorer i tillgänglighets uppsättningen.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Den angivna virtuella dator storleken stöds inte vid distribution i tillgänglighets uppsättning.

Välj en storlek som stöds i tillgänglighets uppsättningens kluster. Vi rekommenderar att du skapar en tillgänglighets uppsättning för att välja den största VM-storlek du tror att du behöver, och att den är din första distribution till tillgänglighets uppsättningen.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig klassisk virtuell dator i en tillgänglighets uppsättning?

Ja. Du kan lägga till en befintlig klassisk virtuell dator i en ny eller befintlig tillgänglighets uppsättning. Mer information finns i [lägga till en befintlig virtuell dator i en tillgänglighets uppsättning](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/).

Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
