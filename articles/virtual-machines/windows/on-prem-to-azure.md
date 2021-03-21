---
title: Migrera från AWS och andra plattformar till Managed Disks i Azure
description: 'Skapa virtuella datorer i Azure med hjälp av VHD: er som laddats upp från andra moln som AWS eller andra virtualiseringsplattformen och dra nytta av Azure Managed Disks.'
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c817528ff26dd10112eaf69c7ad20f8fb5813695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550407"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrera från Amazon Web Services (AWS) och andra plattformar som ska Managed Disks i Azure

Du kan ladda upp VHD-filer från AWS eller lokala virtualiseringslösningar till Azure för att skapa virtuella datorer som utnyttjar Managed Disks. Azure Managed Disks tar bort behovet av att hantera lagrings konton för virtuella Azure IaaS-datorer. Du behöver bara ange vilken typ (Premium eller standard) och storleken på disken som du behöver, så skapar och hanterar Azure disken åt dig. 

Du kan ladda upp antingen generaliserade och specialiserade virtuella hård diskar. 
- **Generaliserad virtuell hård disk** : all personlig konto information har tagits bort med hjälp av Sysprep. 
- **Särskild virtuell hård disk** – hanterar användar konton, program och andra tillstånds data från den ursprungliga virtuella datorn. 

> [!IMPORTANT]
> Innan du laddar upp en virtuell hård disk till Azure bör du följa [förbereda en Windows-VHD eller VHDX för uppladdning till Azure](prepare-for-upload-vhd-image.md)
>
>


| Scenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Du har befintliga AWS EC2-instanser som du vill migrera till virtuella Azure-datorer med hanterade diskar                              | [Flytta en virtuell dator från Amazon Web Services (AWS) till Azure](aws-to-azure.md)                           |
| Du har en virtuell dator från en annan virtualiseringsteknik som du vill använda som en avbildning för att skapa flera virtuella Azure-datorer. | [Överför en generaliserad virtuell hård disk och Använd den för att skapa en ny virtuell dator i Azure](upload-generalized-managed.md) |
| Du har en unik anpassad virtuell dator som du vill återskapa i Azure.                                                      | [Ladda upp en specialiserad virtuell hård disk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Översikt över Managed Disks

Azure Managed Disks fören klar VM-hanteringen genom att ta bort behovet av att hantera lagrings konton. Managed Disks också dra nytta av bättre tillförlitlighet för virtuella datorer i en tillgänglighets uppsättning. Det garanterar att diskarna för olika virtuella datorer i en tillgänglighets uppsättning är tillräckligt isolerade från varandra för att undvika en enskild felpunkt. Den placerar automatiskt diskar med olika virtuella datorer i en tillgänglighets uppsättning i olika lagrings skalnings enheter (stämplar) som begränsar konsekvenserna av enskilda lagrings enhets enhets problem som orsakas av maskin-och program varu problem.
Utifrån dina behov kan du välja mellan fyra olika typer av lagrings alternativ. Information om tillgängliga disk typer finns i vår artikel [Välj en disktyp](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planera för migreringen till Managed Disks

Det här avsnittet hjälper dig att fatta det bästa beslutet om VM och disk typer.

Om du planerar att migrera från ohanterade diskar till hanterade diskar bör du vara medveten om att användare med rollen [virtuell dator deltagare](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) inte kommer att kunna ändra storleken på den virtuella datorn (eftersom de kan förkonverteras). Detta beror på att virtuella datorer med hanterade diskar kräver att användaren har behörigheten Microsoft. Compute/disks/Write på OS-diskarna.

### <a name="location"></a>Location

Välj en plats där Azure Managed Disks är tillgängliga. Om du migrerar till Premium-Managed Disks bör du också se till att Premium Storage är tillgängligt i den region där du planerar att migrera till. Se [Azure-tjänster efter region](https://azure.microsoft.com/regions/#services) för uppdaterad information om tillgängliga platser.

### <a name="vm-sizes"></a>VM-storlekar

Om du migrerar till Premium-Managed Disks måste du uppdatera storleken på den virtuella datorn till Premium Storage tillgänglig storlek i den region där den virtuella datorn finns. Granska de VM-storlekar som Premium Storage stöder. Specifikationerna för Azure VM-storlek anges i [storlekar för virtuella datorer](../sizes.md).
Granska prestanda egenskaperna för virtuella datorer som fungerar med Premium Storage och välj den lämpligaste VM-storlek som passar din arbets belastning bäst. Kontrol lera att det finns tillräckligt med bandbredd på den virtuella datorn för att köra disk trafiken.

### <a name="disk-sizes"></a>Disk storlekar

**Premium-Managed Disks**

Det finns sju typer av Premium-hanterade diskar som kan användas med den virtuella datorn och var och en har exakta IOPs-och data flödes gränser. Ta hänsyn till dessa begränsningar när du väljer Premium disk-typ för din virtuella dator baserat på programmets behov i förhållande till kapacitet, prestanda, skalbarhet och högsta belastning.

| Typ av Premium diskar  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Diskstorlek           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1 024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disk       | 120   | 240   | 500   | 1100  |2 300              | 5000              | 7 500              | 7 500              | 
| Dataflöde per disk | 25 MB per sekund  | 50 MB per sekund  | 100 MB per sekund | 125 MB per sekund |150 MB per sekund | 200 MB per sekund | 250 MB per sekund | 250 MB per sekund |

**Standard Managed Disks**

Det finns sju typer av standard-hanterade diskar som kan användas med den virtuella datorn. Var och en av dem har olika kapacitet men har samma IOPS och data flödes gränser. Välj vilken typ av standard Managed disks som baseras på programmets kapacitets behov.

| Disk av standardtyp  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Diskstorlek           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1 024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS per disk       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Dataflöde per disk | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund |60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 60 MB per sekund | 

### <a name="disk-caching-policy"></a>Princip för diskcachelagring 

**Premium-Managed Disks**

Som standard är diskcachelagring *-principen skrivskyddad* för alla Premium-datadiskarna och *Läs-och skriv* behörighet för den Premium-operativsystems disk som är ansluten till den virtuella datorn. Den här konfigurations inställningen rekommenderas för att uppnå optimala prestanda för ditt programs IOs. För skrivskyddade eller skrivskyddade data diskar (till exempel SQL Server loggfiler) inaktiverar du diskcachelagring så att du kan uppnå bättre program prestanda.

### <a name="pricing"></a>Priser

Läs [om prissättningen för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Priset för Premium-Managed Disks är detsamma som de Premium-ohanterade diskarna. Priser för standard Managed Disks skiljer sig dock från vanliga ohanterade diskar.


## <a name="next-steps"></a>Nästa steg

- Innan du laddar upp en virtuell hård disk till Azure bör du följa [förbereda en Windows-VHD eller VHDX för uppladdning till Azure](prepare-for-upload-vhd-image.md)