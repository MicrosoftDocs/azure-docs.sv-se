---
title: Vanliga frågor om SQL Server på Linux Virtual Machines | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om att köra SQL Server på Linux virtuella datorer.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.workload: iaas-sql-server
ms.date: 12/13/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: ad991974df30060e552d21a44d5796cd2ba165e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792556"
---
# <a name="frequently-asked-questions-for-sql-server-on-linux-virtual-machines"></a>Vanliga frågor och svar om SQL Server på Linux virtuella datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/frequently-asked-questions-faq.md)
> * [Linux](frequently-asked-questions-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna om att köra [SQL Server på Linux virtuella datorer](sql-server-on-linux-vm-what-is-iaas-overview.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Avbildningar

1. **Vilka SQL Server Galleri avbildningar för virtuella datorer är tillgängliga?**

   Azure upprätthåller virtuella dator avbildningar för alla stödda större versioner av SQL Server på alla utgåvor för både Linux och Windows. Mer information finns i den fullständiga listan med [virtuella Linux-avbildningar](sql-server-on-linux-vm-what-is-iaas-overview.md#create) och [Windows VM-avbildningar](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo).

1. **Uppdateras befintliga SQL Server Galleri avbildningar för virtuella datorer?**

   Varannan månad uppdateras SQL Server avbildningar i galleriet för virtuella datorer med de senaste Linux-och Windows-uppdateringarna. För Linux-avbildningar omfattar detta de senaste system uppdateringarna. För Windows-avbildningar inkluderar detta alla uppdateringar som har marker ATS som viktiga i Windows Update, inklusive viktiga SQL Server säkerhets uppdateringar och Service Pack. SQL Server kumulativa uppdateringar hanteras på olika sätt för Linux och Windows. SQL Server kumulativa uppdateringar för Linux ingår också i uppdateringen. Men för tillfället uppdateras inte virtuella Windows-datorer med SQL Server eller Windows Server kumulativa uppdateringar.

1. **Vilka relaterade SQL Server paket installeras också?**

   Om du vill se de SQL Server-paket som installeras som standard på SQL Server på Linux virtuella datorer, se [installerade paket](sql-server-on-linux-vm-what-is-iaas-overview.md#packages).

1. **Kan SQL Server avbildningar av virtuella datorer ta bort från galleriet?**

   Ja. Azure upprätthåller bara en bild per huvud version och utgåva. Till exempel, när en ny SQL Server service pack lanseras, lägger Azure till en ny avbildning till galleriet för den service pack. SQL Server avbildningen för föregående service pack tas omedelbart bort från Azure Portal. Men det är fortfarande tillgängligt för etablering från PowerShell under de kommande tre månaderna. Föregående service packs avbildning är inte tillgänglig längre efter tre månader. Den här borttagnings principen gäller även om en SQL Server-version inte stöds när den når slutet av livs cykeln.

## <a name="creation"></a>Skapa

1. **Hur gör jag för att skapa en virtuell Linux-dator med SQL Server?**

   Den enklaste lösningen är att skapa en virtuell Linux-dator som innehåller SQL Server. En själv studie kurs om hur du registrerar dig för Azure och skapar en SQL Server VM från portalen finns i [etablera en virtuell Linux-dator som kör SQL Server i Azure Portal](sql-vm-create-portal-quickstart.md). Du kan också välja att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad utgåva (utvecklare eller Express) eller genom att återanvända en lokal licens. Om du använder din egen licens måste du ha [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility).

1. **Varför kan jag inte etablera en RHEL-eller SLES-SQL Server VM med en Azure-prenumeration som har en utgifts gräns?**

   RHEL och SLES Virtual Machines kräver en prenumeration utan utgifts gräns och en verifierad betalnings metod (vanligt vis ett kredit kort) som är associerad med prenumerationen. Om du etablerar en RHEL-eller SLES-VM utan att ta bort utgifts gränsen, inaktive ras prenumerationen och alla virtuella datorer/tjänster stoppas. Om du använder det här läget för att återaktivera prenumerationen [tar du bort utgifts gränsen](https://account.windowsazure.com/subscriptions). Dina återstående krediter återställs för den aktuella fakturerings perioden, men en RHEL eller SLES VM Image-tillägg skickas till ditt kredit kort om du väljer att starta om och fortsätta att köra den.

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Skapa först en virtuell Linux OS-dator. Kör sedan [SQL Server installations stegen](/sql/linux/sql-server-linux-setup#platforms) för din Linux-distribution. Om du inte installerar någon av de fritt licensierade versionerna av SQL Server måste du också ha en SQL Server licens och [licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).

1. **Finns det BYOL virtuella Linux-dator avbildningar för SQL Server?**

   För närvarande finns det inga virtuella BYOL Linux-avbildningar för SQL Server. Du kan dock installera SQL Server manuellt på en virtuell Linux-dator som beskrivs i föregående frågor.

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Nej. Du kan inte växla från betala per sekund-licensiering till att använda din egen licens. Du måste skapa en ny virtuell Linux-dator, installera SQL Server och migrera dina data. Se föregående fråga om du vill ha mer information om hur du aktiverar din egen licens.

## <a name="administration"></a>Administration

1. **Kan jag hantera en virtuell Linux-dator som kör SQL Server med SQL Server Management Studio (SSMS)?**

   Ja, men SSMS är för närvarande ett Windows-verktyg. Du måste fjärrans luta från en Windows-dator för att kunna använda SSMS med virtuella Linux-datorer som kör SQL Server. Det nya [MSSQL-conf-](/sql/linux/sql-server-linux-configure-mssql-conf) verktyget kan utföra många administrativa uppgifter lokalt på Linux. En databas hanterings verktyg över plattformar finns i [Azure Data Studio](/sql/azure-data-studio/what-is).

1. **Kan jag ta bort SQL Server helt från en virtuell SQL Server-dator?**

   Ja, men du kommer att fortsätta att debiteras för din SQL Server VM enligt beskrivningen i [pris vägledningen för SQL Server virtuella Azure-datorer](../windows/pricing-guidance.md?toc=%2fazure%2fvirtual-machines%2flinux%2fsql%2ftoc.json). Om du inte längre behöver SQL Server kan du distribuera en ny virtuell dator och migrera data och program till den nya virtuella datorn. Sedan kan du ta bort den virtuella SQL Server-datorn.

## <a name="updating-and-patching"></a>Uppdatering och uppdatering

1. **Hur gör jag för att uppgradera till en ny version/utgåva av SQL Server på en virtuell Azure-dator?**

   För närvarande finns det ingen uppgradering på plats för SQL Server som körs på en virtuell dator i Azure. Skapa en ny virtuell Azure-dator med önskad SQL Server version/utgåva och migrera sedan databaserna till den nya servern med hjälp av [standard teknik för data migration](/sql/linux/sql-server-linux-migrate-overview).

## <a name="general"></a>Allmänt

1. **Stöds SQL Server lösningar med hög tillgänglighet på virtuella Azure-datorer?**

   Inte just nu. Always on-tillgänglighetsgrupper och redundanskluster kräver både en kluster lösning i Linux, till exempel pacemaker. Linux-distributioner som stöds för SQL Server har inte stöd för sina hög tillgänglighets tillägg i molnet.

## <a name="resources"></a>Resurser

**Virtuella Linux-datorer**:

* [Översikt över SQL Server på en virtuell Linux-dator](sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Etablera SQL Server på en virtuell Linux-dator](sql-vm-create-portal-quickstart.md)
* [SQL Server på Linux dokumentation](/sql/linux/sql-server-linux-overview)

**Virtuella Windows-datorer**:

* [Översikt över SQL Server på en virtuell Windows-dator](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Etablera SQL Server på en virtuell Windows-dator](../windows/sql-vm-create-portal-quickstart.md)
* [Vanliga frågor och svar (Windows)](../windows/frequently-asked-questions-faq.md)