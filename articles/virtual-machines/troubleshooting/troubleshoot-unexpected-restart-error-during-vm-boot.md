---
title: OS-start – datorn har startats om oväntat eller ett oväntat fel uppstod
description: Den här artikeln innehåller steg för att lösa problem där den virtuella datorn upplever en oväntad omstart eller fel vid installation av Windows.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 1d249a4e-71ba-475d-8461-31ff13e57811
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/22/2020
ms.author: v-mibufo
ms.openlocfilehash: d8d2ab2bb3f24e1faa4791ebdc1ce3852f6a790e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632698"
---
# <a name="os-start-up--computer-restarted-unexpectedly-or-encountered-an-unexpected-error"></a>OS-start – datorn har startats om oväntat eller ett oväntat fel uppstod

Den här artikeln innehåller steg för att lösa problem där den virtuella datorn (VM) upplever en oväntad omstart eller fel vid installation av Windows.

## <a name="symptom"></a>Symptom

När du använder [startdiagnostik](./boot-diagnostics.md) för att Visa skärm bilden för den virtuella datorn ser du att skärm bilden visar att Windows-installationen Miss känner av följande fel:

**Datorn startades om oväntat eller påträffades ett oväntat fel. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.**

![Ett fel uppstod när Windows-installationen pågår: datorn startade oväntat om eller påträffade ett oväntat fel. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.](./media/unexpected-restart-error-during-vm-boot/1.png)
 
![Fel vid start av installations programmet för Windows: datorn har oväntat startats om eller ett oväntat fel uppstod. Windows-installationen kan inte fortsätta. Installera Windows genom att klicka på OK för att starta om datorn och starta sedan om installationen.](./media/unexpected-restart-error-during-vm-boot/2.png)

## <a name="cause"></a>Orsak

Datorn försöker utföra en inledande start av en [generaliserad avbildning](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation), men påträffar problem på grund av en anpassad svarsfil (Unattend.xml) som bearbetas. **Anpassade svarsfiler stöds inte i Azure**. 

Svars filen är en särskild XML-fil som innehåller inställnings definitioner och värden för de konfigurations inställningar som du vill automatisera under installationen av en Windows Server-installation av operativ systemet. Konfigurations alternativen innehåller instruktioner för hur du partitionerar diskar, var du hittar Windows-avbildningen som ska installeras, vilka produkt nycklar som ska användas och andra kommandon som du vill köra.

Det går inte att använda anpassade svarsfiler i Azure. Den här situationen uppstår därför när en avbildning förbereds för användning i Azure, men du angav en anpassad Unattend.xml-fil med hjälp av **Sysprep** med en flagga som liknar följande kommando:

`sysprep /oobe /generalize /unattend:<your file’s name> /shutdown`

I Azure använder du alternativet för att **Ange OOBE (out-of-Box Experience)** i **GUI för system förberedelse verktyget** eller använder `sysprep /oobe` i stället för Unattend.xml-filen.

Det här problemet skapas oftast när du använder Sysprep med en lokal virtuell dator för att överföra en generaliserad virtuell dator till Azure. I det här fallet kanske du också är intresse rad av att ladda upp en generaliserad virtuell dator på ett korrekt sätt.

## <a name="solution"></a>Lösning

### <a name="do-not-use-unattendxml"></a>Använd inte Unattend.xml

> [!TIP]
> Om du har en ny säkerhets kopia av den virtuella datorn kan du försöka att [återställa den virtuella datorn från säkerhets kopian](../../backup/backup-azure-arm-restore-vms.md) för att åtgärda start problemet.

Åtgärda problemet genom att följa [Azure-vägledningen om hur du förbereder/fångar en avbildning](../windows/upload-generalized-managed.md) och förbereder en ny generaliserad avbildning. **Använd inte `/unattend:<your file’s name>` flagga** under Sysprep. Använd i stället bara flaggorna nedan:

`sysprep /oobe /generalize /shutdown`

- OOBE (out-of-Box-Experience) är den inställning som stöds för virtuella Azure-datorer.

Du kan också använda **GUI-verktyget för system förberedelse** för att utföra samma uppgift som kommandot ovan genom att välja de alternativ som visas nedan:

- Ange färdiga erfarenheter
- Generalisera
- Avstängning
 
![Fönstret system förberedelse verktyg med alternativen OOBE, generalize och shutdown markerat.](./media/unexpected-restart-error-during-vm-boot/3.png)
