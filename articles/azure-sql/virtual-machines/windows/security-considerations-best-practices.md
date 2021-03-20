---
title: Säkerhets aspekter | Microsoft Docs
description: Det här avsnittet innehåller allmänna rikt linjer för att skydda SQL Server som körs i en virtuell Azure-dator.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 54010359f226fe02336f039e3dcbb98075e9b06a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97360092"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Säkerhetsöverväganden för SQL Server på Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Det här avsnittet innehåller allmänna rikt linjer för säkerhet som hjälper till att etablera säker åtkomst till SQL Server instanser på en virtuell Azure-dator (VM).

Azure följer flera bransch regler och standarder som gör att du kan skapa en kompatibel lösning med SQL Server som körs i en virtuell dator. Information om regelefterlevnad med Azure finns i [Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Kontrol lera åtkomst till den virtuella SQL-datorn

När du skapar en SQL Server virtuell dator bör du fundera över hur du noga kontrollerar vem som har åtkomst till datorn och SQL Server. I allmänhet bör du göra följande:

- Begränsa åtkomsten till SQL Server endast till de program och klienter som behöver det.
- Följ metod tips för att hantera användar konton och lösen ord.

Följande avsnitt innehåller förslag på hur du tänker på dessa punkter.

## <a name="secure-connections"></a>Säkra anslutningar

När du skapar en SQL Server virtuell dator med en galleri bild kan du med alternativet **SQL Server anslutning** kan du välja **lokalt (inne i VM)**, **privat (inom Virtual Network)** eller **offentlig (Internet)**.

![SQL Server anslutning](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

För bästa säkerhet väljer du det mest restriktiva alternativet för ditt scenario. Om du till exempel kör ett program som har åtkomst till SQL Server på samma virtuella dator är **lokalt** det säkraste alternativet. Om du kör ett Azure-program som kräver åtkomst till SQL Server, skyddas **privat** kommunikation enbart till SQL Server inom det angivna [virtuella Azure-nätverket](../../../virtual-network/virtual-networks-overview.md). Om du behöver **offentlig** (Internet) åtkomst till SQL Server VM ska du kontrol lera att du följer andra metod tips i det här avsnittet för att minska risken för angrepp.

De valda alternativen i portalen använder inkommande säkerhets regler i den virtuella datorns [nätverks säkerhets grupp](../../../active-directory/identity-protection/concept-identity-protection-security-overview.md) (NSG) för att tillåta eller neka nätverks trafik till den virtuella datorn. Du kan ändra eller skapa nya regler för inkommande NSG för att tillåta trafik till SQL Server porten (standard 1433). Du kan också ange vissa IP-adresser som tillåts att kommunicera via den här porten.

![Regler för nätverkssäkerhetsgrupp](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Förutom NSG-regler för att begränsa nätverks trafiken kan du också använda Windows-brandväggen på den virtuella datorn.

Om du använder slut punkter med den klassiska distributions modellen tar du bort alla slut punkter på den virtuella datorn om du inte använder dem. Instruktioner för hur du använder ACL: er med slut punkter finns i [Hantera ACL för en slut punkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). Detta är inte nödvändigt för virtuella datorer som använder Azure Resource Manager.

Till sist kan du aktivera krypterade anslutningar för instansen av SQL Server databas motorn på din virtuella Azure-dator. Konfigurera SQL Server-instansen med ett signerat certifikat. Mer information finns i [Aktivera krypterade anslutningar till databas motorn](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) och [syntaxen för anslutnings strängen](/dotnet/framework/data/adonet/connection-string-syntax).

## <a name="encryption"></a>Kryptering

Managed disks erbjuder Server-Side kryptering och Azure Disk Encryption. [Kryptering på Server sidan](../../../virtual-machines/disk-encryption.md) ger kryptering vid vila och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) använder antingen Bitlocker eller DM-Crypt-teknik och integrerar med Azure Key Vault för att kryptera både operativ systemet och data diskarna. 

## <a name="use-a-non-default-port"></a>Använd en port som inte är standard

Som standard lyssnar SQL Server på en känd port, 1433. För ökad säkerhet kan du konfigurera SQL Server att lyssna på en port som inte är standard, till exempel 1401. Om du etablerar en SQL Server Galleri avbildning i Azure Portal kan du ange den här porten i bladet **SQL Server inställningar** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Om du vill konfigurera detta efter etableringen har du två alternativ:

- För virtuella datorer i Resource Manager kan du välja **säkerhet** från [resursen för virtuella SQL-datorer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Detta ger ett alternativ för att ändra porten.

  ![TCP-port ändring i portalen](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- För klassiska virtuella datorer eller för SQL Server virtuella datorer som inte har tillhandahållits med portalen kan du konfigurera porten manuellt genom att fjärrans luta till den virtuella datorn. Konfigurations stegen finns i [Konfigurera en server för att lyssna på en angiven TCP-port](/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Om du använder den här manuella tekniken måste du också lägga till en regel för Windows-brandväggen för att tillåta inkommande trafik på TCP-porten.

> [!IMPORTANT]
> Att ange en port som inte är standard är en bra idé om din SQL Server Port är öppen för offentliga Internet anslutningar.

När SQL Server lyssnar på en port som inte är standard, måste du ange porten när du ansluter. Anta till exempel ett scenario där serverns IP-adress är 13.55.255.255 och SQL Server lyssnar på port 1401. Om du vill ansluta till SQL Server anger du `13.55.255.255,1401` anslutnings strängen.

## <a name="manage-accounts"></a>Hantera konton

Du vill inte att angripare ska kunna gissa konto namn eller lösen ord. Använd följande tips för att få hjälp:

- Skapa ett unikt lokalt administratörs konto som inte heter **administratör**.

- Använd komplexa starka lösen ord för alla dina konton. Mer information om hur du skapar ett starkt lösen ord finns i artikeln [skapa ett starkt lösen ord](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) .

- Som standard väljer Azure Windows-autentisering under SQL Server installation av virtuell dator. Därför är **sa** -inloggningen inaktive rad och ett lösen ord tilldelas av installations programmet. Vi rekommenderar att du inte använder eller aktiverar **sa** -inloggningen. Om du måste ha en SQL-inloggning kan du använda någon av följande strategier:

  - Skapa ett SQL-konto med ett unikt namn som har **sysadmin** -medlemskap. Du kan göra detta från portalen genom att aktivera **SQL-autentisering** under etableringen.

    > [!TIP] 
    > Om du inte aktiverar SQL-autentisering under etableringen måste du manuellt ändra autentiseringsläget till **SQL Server och Windows-autentiseringsläge**. Mer information finns i [ändra autentiseringsläge för Server](/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Om du måste använda **sa** -inloggningen aktiverar du inloggningen efter etableringen och tilldelar ett nytt starkt lösen ord.

## <a name="additional-best-practices"></a>Ytterligare metodtips

Utöver de metoder som beskrivs i det här avsnittet rekommenderar vi att du granskar och implementerar de rekommenderade säkerhets metoderna från både traditionella lokala säkerhets metoder, samt metod tips för virtuella datorer. 

Mer information om lokala säkerhets metoder finns i [säkerhets överväganden för en SQL Server-installation](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) och [Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Mer information om säkerhet för virtuella datorer finns i [säkerhets översikten för virtuella datorer](../../../security/fundamentals/virtual-machines-overview.md).


## <a name="next-steps"></a>Nästa steg

Om du också är intresse rad av bästa praxis kring prestanda, se [metod tips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md).

Andra avsnitt om att köra SQL Server i virtuella Azure-datorer finns [SQL Server på azure Virtual Machines-översikt](sql-server-on-azure-vm-iaas-what-is-overview.md). Om du har frågor om virtuella SQL Server-datorer kan du läsa [Vanliga frågor](frequently-asked-questions-faq.md).