---
title: Vanliga frågor och svar om virtuella Linux-datorer i Azure
description: Innehåller svar på några vanliga frågor om virtuella Linux-datorer som skapats med Resource Manager-modellen.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: d510fa35163a4c4a79352f5f35af9c384bf85304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104596322"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Vanliga frågor om Virtuella Linux-datorer
Den här artikeln beskriver några vanliga frågor om virtuella Linux-datorer som skapats i Azure med hjälp av distributions modellen Resource Manager. För Windows-versionen av det här avsnittet, se [vanliga frågor och svar om virtuella Windows-datorer](../windows/faq.md)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Mer information finns i [Linux på Azure-Endorsed distributioner](endorsed-distros.md)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje data disk kan vara upp till 32 767 GiB. Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](../sizes.md).

Azure Managed Disks är de rekommenderade disk lagrings erbjudanden som används med Azure Virtual Machines för beständig data lagring. Du kan använda flera Managed Disks för varje virtuell dator. Managed Disks erbjuder två typer av varaktiga lagrings alternativ: Premium och standard Managed Disks. Information om priser finns i [Managed disks priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure Storage-konton kan också tillhandahålla lagring för operativ system disken och alla data diskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur får jag åtkomst till min virtuella dator?
Upprätta en fjärr anslutning för att logga in på den virtuella datorn med hjälp av SSH (Secure Shell). Se anvisningarna för hur du ansluter [från Windows](ssh-from-windows.md) eller [från Linux och Mac](mac-create-ssh-keys.md). Som standard tillåter SSH högst 10 samtidiga anslutningar. Du kan öka detta antal genom att redigera konfigurationsfilen.

Om du har problem kan du läsa [FELSÖK SSH-anslutningar (Secure Shell)](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan jag använda den temporära disken (/dev/sdb1) för att lagra data?
Använd inte den temporära disken (/dev/sdb1) för att lagra data. Det finns bara för tillfällig lagring. Du riskerar att förlora data som inte kan återställas.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig virtuell Azure-dator?
Ja. Instruktioner finns i [så här skapar du en kopia av en virtuell Linux-dator i distributions modellen för Resource Manager](copy-vm.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte Kanada Central-och Kanada östra regioner genom Azure Resource Manager?
De två nya regionerna i Kanada Central och Kanada, öst registreras inte automatiskt för att skapa virtuella datorer för befintliga Azure-prenumerationer. Den här registreringen görs automatiskt när en virtuell dator distribueras via Azure Portal till någon annan region som använder Azure Resource Manager. När en virtuell dator har distribuerats till någon annan Azure-region bör de nya regionerna vara tillgängliga för efterföljande virtuella datorer.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till min virtuella dator när det har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas. Sedan kan du lägga till eller ta bort ett nätverkskort (om det inte är det sista NÄTVERKSKORTet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några dator namns krav?
Ja. Dator namnet får innehålla högst 64 tecken. Se [namn konventions regler och begränsningar](/azure/architecture/best-practices/resource-naming) för mer information om hur du namnger dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det några resurs grupp namns krav?
Ja. Resurs gruppens namn får innehålla högst 90 tecken. Se [namn konventions regler och begränsningar](/azure/architecture/best-practices/resource-naming) för mer information om resurs grupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är användar namns kraven när du skapar en virtuell dator?

Användar namn ska vara 1-32 tecken långt.

Följande användar namn är inte tillåtna:

- `1`
- `123`
- `a`
- `actuser`
- `adm`
- `admin`
- `admin1`
- `admin2`
- `administrator`
- `aspnet`
- `backup`
- `console`
- `david`
- `guest`
- `john`
- `owner`
- `root`
- `server`
- `sql`
- `support_388945a0`
- `support`
- `sys`
- `test`
- `test1`
- `test2`
- `test3`
- `user`
- `user1`
- `user2`
- `user3`
- `user4`
- `user5`
- `video`


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösen ords kraven när du skapar en virtuell dator?

Det finns varierande krav på lösen ords längd, beroende på vilket verktyg du använder:
 - Portal – mellan 12-72 tecken
 - PowerShell – mellan 8-123 tecken
 - CLI – mellan 12-123 tecken
 - Azure Resource Manager (ARM) Mallar – 12-72 tecken och kontroll tecken tillåts inte
 

Lösen ord måste också uppfylla 3 av följande 4 komplexitets krav:

* Innehålla gemener
* Innehålla versaler
* Innehålla en siffra
* Innehålla ett specialtecken (Regex matchar [\W_])

Följande lösen ord är inte tillåtna:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@ $ $w 0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">PA $ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Ords!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>