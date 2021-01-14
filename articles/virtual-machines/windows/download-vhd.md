---
title: Hämta en Windows-VHD från Azure
description: Ladda ned en virtuell Windows-hårddisk med Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5e6f7b461d205478d6a3fe3eecbbb933d0533ec9
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200523"
---
# <a name="download-a-windows-vhd-from-azure"></a>Hämta en Windows-VHD från Azure

I den här artikeln får du lära dig hur du hämtar en Windows virtuell hård disk fil (VHD) från Azure med hjälp av Azure Portal.

## <a name="optional-generalize-the-vm"></a>Valfritt: generalisera den virtuella datorn

Om du vill använda den virtuella hård disken som en [avbildning](tutorial-custom-images.md) för att skapa andra virtuella datorer bör du använda [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) för att generalisera operativ systemet. 

Om du vill använda den virtuella hård disken som en avbildning för att skapa andra virtuella datorer, generaliserar du den virtuella datorn.

1. Om du inte redan gjort det loggar du in på [Azure Portal](https://portal.azure.com/).
2. [Anslut till den virtuella datorn](connect-logon.md). 
3. Öppna kommando tolkens fönster som administratör på den virtuella datorn.
4. Ändra katalogen till *%WINDIR%\system32\sysprep* och kör sysprep.exe.
5. I dialog rutan system förberedelse verktyg väljer du **Skriv system out-of-Box Experience (OOBE)** och kontrollerar att **generalize** är markerat.
6. I avslutnings alternativ väljer du **shutdown** och klickar sedan på **OK**. 


## <a name="stop-the-vm"></a>Stoppa den virtuella datorn

En virtuell hård disk kan inte laddas ned från Azure om den är ansluten till en virtuell dator som körs. Du måste stoppa den virtuella datorn för att kunna ladda ned en virtuell hård disk. 

1. Klicka på **Virtual Machines** på menyn hubb i Azure Portal.
1. Välj den virtuella datorn i listan.
1. Klicka på **stoppa** på bladet för den virtuella datorn.


## <a name="generate-download-url"></a>Generera nedladdnings-URL

Om du vill hämta VHD-filen måste du generera en URL för [signatur för delad åtkomst (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) . När URL: en genereras tilldelas URL: en förfallo tid.

1. På sidan för den virtuella datorn klickar du på **diskar** på den vänstra menyn.
1. Välj operativ system disk för den virtuella datorn.
1. På sidan för disken väljer du **disk export** på den vänstra menyn.
1. Standard förfallo tiden för URL: en är *3600* sekunder. Öka till **36000** för Windows OS-diskar.
1. Klicka på **skapa URL**.

> [!NOTE]
> Förfallo tiden höjs från standardvärdet för att tillhandahålla tillräckligt med tid för att ladda ned den stora VHD-filen för ett Windows Server-operativsystem. Du kan vänta på att en VHD-fil som innehåller operativ systemet Windows Server ta flera timmar att ladda ned beroende på din anslutning. Om du hämtar en virtuell hård disk för en datadisk är standard tiden tillräckligt. 
> 
> 

## <a name="download-vhd"></a>Hämta VHD

1. Under den URL som genererades klickar du på Hämta VHD-filen.
1. Du kan behöva klicka på **Spara** i webbläsaren för att starta nedladdningen. Standard namnet för VHD-filen är *ABCD*.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [laddar upp en VHD-fil till Azure](upload-generalized-managed.md). 
- [Skapa hanterade diskar från ohanterade diskar i ett lagrings konto](attach-disk-ps.md).
- [Hantera Azure-diskar med PowerShell](tutorial-manage-data-disk.md).
