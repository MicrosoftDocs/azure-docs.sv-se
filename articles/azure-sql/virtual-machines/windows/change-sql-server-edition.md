---
title: Förändringar på plats av SQL Server Edition
description: Lär dig hur du ändrar versionen av din SQL Server virtuella dator i Azure för att nedgradera för att minska kostnaderna eller uppgradera för att aktivera fler funktioner.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/14/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: b6ea6f947b2cd8d7d9c38a0e5e440ce5fd273bb7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358834"
---
# <a name="in-place-change-of-sql-server-edition-on-azure-vm"></a>Ändring på plats av SQL Server-utgåva på en virtuell Azure-dator
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

I den här artikeln beskrivs hur du ändrar versionen av SQL Server på en virtuell Windows-dator i Azure. 

Versionen av SQL Server bestäms av produkt nyckeln och anges under installations processen med hjälp av installations mediet. Versionen avgör vilka [funktioner](/sql/sql-server/editions-and-components-of-sql-server-2017) som är tillgängliga i SQL Server produkten. Du kan ändra SQL Server versionen med-installationsmedia och nedgradera för att minska kostnaderna eller uppgradera för att aktivera fler funktioner.

När utgåvan av SQL Server har ändrats internt till SQL Server VM måste du uppdatera egenskapen Edition för SQL Server i Azure Portal i fakturerings syfte. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra en direkt ändring av versionen av SQL Server behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQL Server VM på Windows](./create-sql-vm-portal.md) som registrerats med [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md).
- Konfigurera media med den **önskade versionen** av SQL Server. Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installations mediet från en Azure Marketplace SQL Server VM-avbildning som har önskad utgåva (vanligt vis finns i `C:\SQLServerFull` ). 


## <a name="upgrade-an-edition"></a>Uppgradera en utgåva

> [!WARNING]
> Om du uppgraderar versionen av SQL Server startas om tjänsten för SQL Server, tillsammans med associerade tjänster, till exempel Analysis Services-och R-tjänster. 

Om du vill uppgradera versionen av SQL Server hämtar du det SQL Server installations mediet för den önskade versionen av SQL Server och gör sedan följande:

1. Öppna Setup.exe från SQL Server-installations mediet. 
1. Gå till **Underhåll** och välj alternativet för **uppgradering av utgåva** . 

   ![Val för uppgradering av versionen av SQL Server](./media/change-sql-server-edition/edition-upgrade.png)

1. Välj **Nästa** tills du kommer till sidan **redo att uppgradera Edition** och välj sedan **Uppgradera**. Installations fönstret kan sluta svara några minuter medan ändringen börjar att fungera. En **fullständig** sida bekräftar att din versions uppgradering är klar. 

När den SQL Server versionen har uppgraderats ändrar du egenskapen utgåva för den SQL Server virtuella datorn i Azure Portal. Detta uppdaterar de metadata och faktureringar som är associerade med den här virtuella datorn.

## <a name="downgrade-an-edition"></a>Nedgradera en utgåva

Om du vill nedgradera versionen av SQL Server måste du avinstallera SQL Server helt och installera om den med önskad utgåva av installations mediet. 

> [!WARNING]
> Om du avinstallerar SQL Server kan ytterligare avbrott uppstå. 

Du kan nedgradera versionen av SQL Server genom att följa dessa steg:

1. Säkerhetskopiera alla databaser, inklusive system databaser. 
1. Flytta system databaser (Master, Model och msdb) till en ny plats. 
1. Avinstallera SQL Server och alla tillhör ande tjänster fullständigt. 
1. Starta om den virtuella datorn. 
1. Installera SQL Server med hjälp av mediet med den önskade versionen av SQL Server.
1. Installera de senaste Service Pack-versionerna och kumulativa uppdateringarna.  
1. Ersätt de nya system databaser som skapades under installationen med system databaserna som du tidigare flyttade till en annan plats. 

När SQL Servers versionen har nedgraderats ändrar du egenskapen utgåva för den SQL Server virtuella datorn i Azure Portal. Detta uppdaterar de metadata och faktureringar som är associerade med den här virtuella datorn.

## <a name="change-edition-in-portal"></a>Ändrings utgåva i portalen 

När du har ändrat versionen av SQL Server med hjälp av installations mediet och du har registrerat din SQL Server VM med [SQL IaaS agent-tillägget](sql-agent-extension-manually-register-single-vm.md)kan du sedan använda Azure Portal för att ändra egenskapen utgåva för SQL Server VM i fakturerings syfte. Det gör du på följande sätt: 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till din SQL Server virtuella dator resursen. 
1. Under **Inställningar** väljer du **Konfigurera**. Välj sedan önskad version av SQL Server i list rutan under **utgåva**. 

   ![Metadata för ändrings version](./media/change-sql-server-edition/edition-change-in-portal.png)

1. Granska varningen som säger att du först måste ändra SQL Server versionen och att egenskapen version måste vara samma SQL Server-utgåva. 
1. Välj **tillämpa** för att tillämpa dina ändringar i din version av metadata. 


## <a name="remarks"></a>Kommentarer

- Egenskapen version för SQL Server VM måste matcha versionen av SQL Server-instansen som installerats för alla SQL Server virtuella datorer, inklusive både betala per användning och hämta licenser.
- Om du släpper SQL Server VM-resursen går du tillbaka till avbildningens hårdkodade versions inställning.
- Möjligheten att ändra versionen är en funktion i SQL IaaS agent-tillägget. Genom att distribuera en Azure Marketplace-avbildning via Azure Portal registreras automatiskt en SQL Server VM med SQL IaaS agent-tillägget. Kunder som själv installerar SQL Server måste dock [registrera sina SQL Server VM](sql-agent-extension-manually-register-single-vm.md)manuellt.
- Om du lägger till en SQL Server VM i en tillgänglighets uppsättning måste den virtuella datorn skapas på nytt. Alla virtuella datorer som läggs till i en tillgänglighets uppsättning återgår till standard utgåvan och versionen måste ändras igen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](doc-changes-updates-release-notes.md)