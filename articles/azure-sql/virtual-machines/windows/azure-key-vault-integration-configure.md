---
title: Integrera Key Vault med SQL Server på virtuella Windows-datorer i Azure (Resource Manager) | Microsoft Docs
description: Lär dig hur du automatiserar konfigurationen av SQL Server kryptering för användning med Azure Key Vault. I det här avsnittet beskrivs hur du använder Azure Key Vault integration med virtuella SQL-datorer som skapats med Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359905"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Konfigurera Azure Key Vault-integrering för SQL Server på virtuella Azure-datorer (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Det finns flera SQL Server krypterings funktioner, till exempel [transparent data kryptering (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), [CLE (Column Level Encryption)](/sql/t-sql/functions/cryptographic-functions-transact-sql)och [kryptering av säkerhets kopior](/sql/relational-databases/backup-restore/backup-encryption). Dessa krypterings former kräver att du hanterar och lagrar de kryptografiska nycklar som du använder för kryptering. Tjänsten Azure Key Vault är utformad för att förbättra säkerheten och hanteringen av dessa nycklar på en säker plats med hög tillgänglighet. Med [SQL Server-anslutning](https://www.microsoft.com/download/details.aspx?id=45344) kan SQL Server använda nycklarna från Azure Key Vault.

Om du kör SQL Server lokalt finns det steg du kan följa för att [få åtkomst till Azure Key Vault från din lokala SQL Server-instans](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server). Men för SQL Server på virtuella Azure-datorer kan du spara tid genom att använda funktionen för *Azure Key Vault-integrering* .

När den här funktionen är aktive rad installerar den automatiskt SQL Server-anslutning, konfigurerar EKM-providern för att få åtkomst till Azure Key Vault och skapar autentiseringsuppgiften så att du kan komma åt ditt valv. Om du har tittat på stegen i den tidigare nämnda dokumentationen kan du se att den här funktionen automatiserar steg 2 och 3. Det enda du behöver göra är att skapa nyckel valvet och nycklarna manuellt. Därifrån kommer hela installationen av din SQL Server VM att automatiseras. När den här funktionen har slutfört den här installationen kan du köra Transact-SQL-uttryck (T-SQL) för att börja kryptera dina databaser eller säkerhets kopior på vanligt sätt.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Version 1.0.4.0 av EKM-providern (Extensible Key Management) är installerad på SQL Server VM via [IaaS-tillägget (SQL Infrastructure as a Service)](./sql-server-iaas-agent-extension-automate-management.md). Uppgradering av SQL IaaS-tillägget kommer inte att uppdatera providerns version. Överväg att uppgradera EKM-providerns version manuellt om det behövs (till exempel vid migrering till en SQL-hanterad instans).


## <a name="enabling-and-configuring-key-vault-integration"></a>Aktivera och konfigurera Key Vault-integrering
Du kan aktivera Key Vault-integrering under etableringen eller konfigurera den för befintliga virtuella datorer.

### <a name="new-vms"></a>Nya virtuella datorer
Om du skapar en ny virtuell SQL-dator med Resource Manager kan du med hjälp av Azure Portal aktivera Azure Key Vault-integration. Azure Key Vault funktionen är bara tillgänglig för SQL Server för Enterprise, Developer och Evaluation.

![SQL Azure Key Vault-integrering](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

En detaljerad genom gång av etableringen finns i [etablera en virtuell SQL-dator i Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Befintliga virtuella datorer

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

För befintliga virtuella SQL-datorer öppnar du [resursen SQL Virtual Machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) och väljer **säkerhet** under **Inställningar**. Välj **Aktivera** för att aktivera Azure Key Vault-integrering. 

![SQL Key Vault-integrering för befintliga virtuella datorer](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

När du är klar väljer du knappen **tillämpa** längst ned på sidan **säkerhet** för att spara ändringarna.

> [!NOTE]
> Namnet på autentiseringsuppgifterna som vi skapade här kommer att mappas till en SQL-inloggning senare. Detta gör att SQL-inloggningen kan komma åt nyckel valvet. 


> [!NOTE]
> Du kan också konfigurera Key Vault-integrering med hjälp av en mall. Mer information finns i [Azure snabb starts mal len för Azure Key Vault-integrering](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]