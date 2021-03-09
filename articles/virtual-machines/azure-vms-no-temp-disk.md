---
title: Vanliga frågor och svar om Azure VM-storlekar utan lokal temporär disk
description: Den här artikeln innehåller svar på vanliga frågor och svar om Microsoft Azure VM-storlekar som inte har en lokal temporär disk.
author: brbell
ms.service: virtual-machines
ms.topic: conceptual
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 4dd078205989872179b0b2474974a29cf6b88dad
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102507848"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Azure VM-storlekar utan lokal temporär disk 
Den här artikeln innehåller svar på vanliga frågor och svar om Azure VM-storlekar som inte har en lokal temporär disk (dvs. ingen lokal temporär disk). Mer information om de här VM-storlekarna finns i [specifikationer för DV4 och Dsv4-serien (generell användning arbets belastningar)](dv4-dsv4-series.md) eller [specifikationer för Ev4 och Esv4-serien (minnesoptimerade arbets belastningar)](ev4-esv4-series.md).

## <a name="what-does-no-local-temp-disk-mean"></a>Vad betyder ingen lokal temporär disk? 
Traditionellt har vi haft VM-storlekar (t. ex. Standard_D2s_v3 Standard_E48_v3) som innehåller en liten lokal disk (t. ex. en D: Drive). Nu med de nya VM-storlekarna finns det ingen liten lokal disk längre. Du kan dock fortfarande bifoga Standard HDD Premium SSD eller Ultra SSD.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>Vad händer om jag fortfarande vill ha en lokal temporär disk?
Om din arbets belastning kräver en lokal temporär disk har vi också nya [Ddv4-och Ddsv4](ddv4-ddsv4-series.md) -eller [Edv4-och Edsv4](edv4-edsv4-series.md) VM-storlekar. De här storlekarna ger 50% större temporär disk jämfört med de tidigare v3-storlekarna.

> [!NOTE]
> Den lokala temporära disken är inte beständig. för att se till att dina data är beständiga använder du Standard HDD Premium SSD eller Ultra SSD alternativ. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Vad är skillnaden mellan de här nya VM-storlekarna och Generell användning dv3/Dsv3 eller de Minnesoptimerade Ev3/Esv3-storlekar som jag använder? 
| v3 VM-familjer med lokal temporär disk   | Nya V4-familjer med lokal temporär disk | Nya V4-familjer utan lokal temporär disk |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Kan jag ändra storlek på en VM-storlek som har en lokal temporär disk till en VM-storlek utan en lokal temporär disk?  
Nej. De enda kombinationer som tillåts för storleks ändring är: 

1. Virtuell dator (med lokal temporär disk) – > virtuell dator (med lokal temporär disk); särskilt 
2. Virtuell dator (utan lokal temporär disk) – > virtuell dator (utan lokal temporär disk). 

Om du är intresse rad av ett arbete runt, se nästa fråga.

> [!NOTE]
> Om en avbildning är beroende av resurs disken, eller om det finns en växlings fil eller en swapfile på den lokala Temp-disken, fungerar inte diskbaserade avbildningar. Använd till exempel alternativet med disk. 

## <a name="how-do-i-migrate-from-a-vm-size-with-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Hur gör jag för att migrera från en VM-storlek med en lokal temporär disk till en VM-storlek utan en lokal temporär disk?  
Du kan migrera genom att följa dessa steg: 

1. Anslut till den virtuella datorn som har en lokal temporär disk (till exempel en D: Drive) som lokal administratör.
2. Följ rikt linjerna i avsnittet "tillfälligt flytta pagefile.sys till C-enhet" i [Använd D: enheten som en data enhet på en virtuell Windows-dator](./windows/change-drive-letter.md) för att flytta växlings filen från den lokala tillfälliga disken (D: Drive) till enheten C:.

   > [!NOTE]
   > Följ rikt linjerna i avsnittet "tillfälligt flytta pagefile.sys till C-enhet" i Använd D: enheten som en data enhet på en virtuell Windows-dator för att flytta växlings filen från den lokala tillfälliga disken (D: Drive) till C: Drive. **Avvikelser från de steg som beskrivs leder till fel meddelandet – "det går inte att ändra storlek på den virtuella datorn eftersom det inte går att ändra storlek på resurs disk till icke-resursens VM-storlek och vice versa är inte tillåtet.**

3. Ta en ögonblicks bild av den virtuella datorn genom att följa stegen som beskrivs i [skapa en ögonblicks bild med hjälp av portalen eller Azure CLI](./linux/snapshot-copy-managed-disk.md). 
4. Använd ögonblicks bild för att skapa en ny disk lös virtuell dator (till exempel DV4, Dsv4, Ev4, Esv4-serien) genom att följa stegen som beskrivs i [skapa en virtuell dator från en ögonblicks bild med CLI](./scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot.md). 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Stöder de här VM-storlekarna både Linux-och Windows-operativsystem (OS)?
Ja.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Kommer detta att ta bort mina egna skript, anpassade avbildningar eller OS-avbildningar som har Scratch-filer eller växlingsfiler på en lokal temporär disk?
Om den anpassade OS-avbildningen pekar på den lokala Temp-disken kanske inte avbildningen fungerar korrekt med den här disk storleken.

## <a name="have-questions-or-feedback"></a>Har du några frågor eller feedback?
Fyll i [feedback-formuläret]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Nästa steg 
I det här dokumentet har du lärt dig mer om de vanligaste frågorna som rör virtuella Azure-datorer utan en lokal temporär disk. Mer information om de här VM-storlekarna finns i följande artiklar:

- [Specifikationer för DV4 och Dsv4-serien (Generell användning arbets belastning)](dv4-dsv4-series.md)
- [Specifikationer för Ev4 och Esv4-serien (Minnesoptimerade arbets belastning)](ev4-esv4-series.md)
