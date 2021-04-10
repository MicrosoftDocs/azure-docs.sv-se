---
title: Flytta en Windows AWS EC2-instans till Azure
description: Flytta en Amazon Web Services (AWS) EC2 Windows-instansen till en virtuell Azure-dator.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550798"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Flytta en virtuell Windows-dator från Amazon Web Services (AWS) till en virtuell Azure-dator

Om du utvärderar virtuella Azure-datorer för att vara värd för dina arbets belastningar kan du exportera en befintlig Amazon Web Services (AWS) EC2 Windows VM instance och sedan ladda upp den virtuella hård disken (VHD) till Azure. När den virtuella hård disken har laddats upp kan du skapa en ny virtuell dator i Azure från den virtuella hård disken. 

Den här artikeln beskriver hur du flyttar en enskild virtuell dator från AWS till Azure. Om du vill flytta virtuella datorer från AWS till Azure i skala, se [migrera virtuella datorer i Amazon Web Services (AWS) till Azure med Azure Site Recovery](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn 
 
Du kan ladda upp både generaliserade och specialiserade virtuella hård diskar till Azure. Varje typ kräver att du förbereder den virtuella datorn innan du exporterar från AWS. 

- **Generaliserad virtuell hård disk** – en generaliserad virtuell hård disk har fått all personlig konto information borttagen med hjälp av Sysprep. Om du tänker använda den virtuella hård disken som en avbildning för att skapa nya virtuella datorer från bör du: 
 
    * [Förbered en virtuell Windows-dator](prepare-for-upload-vhd-image.md).  
    * Generalisera den virtuella datorn med hjälp av Sysprep.  

 
- **Specialiserad virtuell hård disk** – en specialiserad virtuell hård disk hanterar användar konton, program och andra tillstånds data från den ursprungliga virtuella datorn. Om du tänker använda den virtuella hård disken för att skapa en ny virtuell dator kontrollerar du att följande steg är slutförda.  
    * [Förbered en virtuell Windows-hårddisk att ladda upp till Azure](prepare-for-upload-vhd-image.md). Generalisera **inte** den virtuella datorn med Sysprep. 
    * Ta bort eventuella verktyg och agenter för gästautentisering som är installerade på den virtuella datorn (t. ex. VMware-verktyg). 
    * Se till att den virtuella datorn är konfigurerad för att hämta dess IP-adress och DNS-inställningar via DHCP. Detta säkerställer att servern erhåller en IP-adress i VNet när den startas.  


## <a name="export-and-download-the-vhd"></a>Exportera och ladda ned den virtuella hård disken 

Exportera EC2-instansen till en virtuell hård disk i en Amazon S3-Bucket. Följ stegen i artikeln för Amazon-dokumentation [som exporterar en instans som en virtuell dator med hjälp av import/export av virtuella datorer](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) och kör kommandot [create-instance-export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) för att exportera EC2-instansen till en VHD-fil. 

Den exporterade VHD-filen sparas i den Amazon S3-Bucket som du anger. Den grundläggande syntaxen för att exportera den virtuella hård disken är nedan. Ersätt bara platshållartexten \<brackets> med din information.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

När den virtuella hård disken har exporter ATS följer du anvisningarna i [Hur hämtar jag ett objekt från en S3-Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) för att ladda ned VHD-filen från S3-Bucket. 

> [!IMPORTANT]
> AWS debiterar avgifter för data överföring för att ladda ned den virtuella hård disken. Se [Amazon S3-prissättning](https://aws.amazon.com/s3/pricing/) för mer information.


## <a name="next-steps"></a>Nästa steg

Nu kan du ladda upp den virtuella hård disken till Azure och skapa en ny virtuell dator. 

- Om du körde Sysprep på källan för att **generalisera** den innan du exporterar, se [överför en generaliserad virtuell hård disk och Använd den för att skapa nya virtuella datorer i Azure](upload-generalized-managed.md)
- Om du inte körde Sysprep före exporten anses den virtuella hård disken vara **specialiserad**, se [Ladda upp en specialiserad virtuell hård disk till Azure och skapa en ny virtuell dator](create-vm-specialized.md)

 
