---
title: Ändra mål inställningarna när du flyttar virtuella Azure-datorer mellan regioner med Azure Resource-arbetskraft
description: Lär dig hur du ändrar mål inställningarna när du flyttar virtuella Azure-datorer mellan regioner med Azure Resource-arbetskraft.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 936a667948c888f3ca7c53eaa5be9cc97facf5f7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375365"
---
# <a name="modify-destination-settings"></a>Ändra målinställningar

I den här artikeln beskrivs hur du ändrar mål inställningarna när du flyttar resurser mellan Azure-regioner med [Azure Resource-arbetskraft](overview.md).


## <a name="modify-vm-settings"></a>Ändra inställningar för virtuella datorer

När du flyttar virtuella Azure-datorer och tillhör ande resurser kan du ändra mål inställningarna. 

- Vi rekommenderar att du bara ändrar mål inställningarna efter att flyttnings samlingen har verifierats.
- Vi rekommenderar att du ändrar inställningarna innan du förbereder resurserna, eftersom vissa mål egenskaper kanske inte är tillgängliga för redigering när förberedelsen är klar.

Ändå
- Om du flyttar käll resursen kan du vanligt vis ändra mål inställningarna tills du startar processen påbörja flyttning.
- Om du tilldelar en befintlig resurs i käll regionen kan du ändra mål inställningarna tills flyttningen är klar.

### <a name="settings-you-can-modify"></a>Inställningar som du kan ändra

Konfigurations inställningar som du kan ändra sammanfattas i tabellen.

**Resurs** | **Alternativ** 
--- | --- | --- 
**VM-namn** | Alternativ:<br/><br/> -Skapa en ny virtuell dator med samma namn i mål regionen.<br/><br/> -Skapa en ny virtuell dator med ett annat namn i mål regionen.<br/><br/> – Använd en befintlig virtuell dator i mål regionen.<br/><br/> Om du skapar en ny virtuell dator, med undantag för de inställningar som du ändrar, tilldelas den nya virtuella mål datorn samma inställningar som källan.
**Tillgänglighets zon för virtuell dator** | Den tillgänglighets zon som den virtuella mål datorn kommer att placeras i. Välj **ej tillämpligt** om du inte vill ändra käll inställningarna eller om du inte vill placera den virtuella datorn i en tillgänglighets zon.
**VM-SKU** | Den [VM-typ](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (tillgänglig i mål regionen) som ska användas för den virtuella mål datorn.<br/><br/> Den valda virtuella mål datorn får inte vara mindre än den virtuella käll datorn.
* * Tillgänglighets uppsättning för virtuell dator | Tillgänglighets uppsättningen som den virtuella mål datorn kommer att placeras i. Välj **ej tillämpligt**  du vill inte ändra käll inställningarna eller om du inte vill placera den virtuella datorn i en tillgänglighets uppsättning.
**VM Key Vault** | Det associerade nyckel valvet när du aktiverar Azure Disk Encryption på en virtuell dator.
**Disk krypterings uppsättning** | Den associerade disk krypterings uppsättningen om den virtuella datorn använder en kundhanterad nyckel för kryptering på Server sidan.
**Resursgrupp** | Resurs gruppen där den virtuella mål datorn kommer att placeras.
**Nätverks resurser** | Alternativ för nätverks gränssnitt, virtuella nätverk (virtuella nätverk/) och nätverks säkerhets grupper/nätverks gränssnitt:<br/><br/> -Skapa en ny resurs med samma namn i mål regionen.<br/><br/> – Skapa en ny resurs med ett annat namn i mål regionen.<br/><br/> – Använd en befintlig nätverks resurs i mål regionen.<br/><br/> Om du skapar en ny mål resurs, med undantag för de inställningar som du ändrar, tilldelas samma inställningar som käll resursen.
**Offentligt IP-kontonamn, SKU och zon** | Anger namn, [SKU](../virtual-network/public-ip-addresses.md#sku)och [zon](../virtual-network/public-ip-addresses.md#standard) för offentliga standard-IP-adresser.<br/><br/> Om du vill att det ska vara en zon redundant, anger du **zonen är redundant**.
* * Belastnings Utjämnings namn, SKU och zon * * | Anger namn, SKU (Basic eller standard) och zon för belastningsutjämnaren.<br/><br/> Vi rekommenderar att du använder standard-sKU.<br/><br/> Om du vill att det ska vara zon redundant, anger du **zonen är redundant**.
**Resursberoenden** | Alternativ för varje beroende:<br/><br/>-Resursen använder käll beroende resurser som kommer att flyttas till mål regionen.<br/><br/> – Resursen använder olika beroende resurser som finns i mål regionen. I det här fallet kan du välja bland de liknande resurserna i mål regionen.

### <a name="edit-vm-destination-settings"></a>Redigera inställningar för virtuella dator mål

Om du inte vill ha beroende resurser från käll regionen till målet har du ett par andra alternativ:

- Skapa en ny resurs i mål regionen. Om du inte anger olika inställningar kommer den nya resursen att ha samma inställningar som käll resursen.
- Använd en befintlig resurs i mål regionen.

Det exakta beteendet beror på resurs typen. [Läs mer](modify-target-settings.md) om att ändra mål inställningarna.

Du ändrar mål inställningarna för en resurs med hjälp av **mål konfigurations** posten i resurs flyttnings samlingen. 

Ändra en inställning: 

1. Klicka på länken för resurs posten på sidan **över regioner** > **destinations konfiguration** .
2. I **konfigurations inställningar** kan du skapa en ny virtuell dator i mål regionen.
3. Tilldela en ny tillgänglighets zon, tillgänglighets uppsättning eller SKU till den virtuella mål datorn. **Tillgänglighets zon** och **SKU**.

Ändringar görs bara för den resurs som du redigerar. Du måste uppdatera alla beroende resurser separat.


## <a name="modify-sql-settings"></a>Ändra SQL-inställningar

När du flyttar Azure SQL Database-resurser kan du ändra mål inställningarna för flytten. 

- För SQL Database:
    - Vi rekommenderar att du ändrar inställningarna för mål konfigurationen innan du förbereder dem för flytt.
    - Du kan ändra inställningarna för mål databasen och zon redundans för databasen.
- För elastiska pooler:
    -  Du kan ändra mål konfigurationen när som helst innan du påbörjar flytten.
    - Du kan ändra målets elastiska pool och zon redundans för poolen. 

### <a name="sql-settings-you-can-modify"></a>SQL-inställningar du kan ändra

**Inställning** | **SQL-databas** | **Elastisk pool**
--- | --- | ---
**Namn** | Skapa en ny databas med samma namn i mål regionen.<br/><br/> Skapa en ny databas med ett annat namn i mål regionen.<br/><br/> Använd en befintlig databas i mål regionen. | Skapa en ny elastisk pool med samma namn i mål regionen.<br/><br/> Skapa en ny elastisk pool med ett annat namn i mål regionen.<br/><br/> Använd en befintlig elastisk pool i mål regionen.
**Zonredundans** | Om du vill flytta från en region som har stöd för zon redundans till en region som inte gör det, skriver du **disable** i zon inställningen.<br/><br/> Om du vill flytta från en region som inte har stöd för zon-redundans till en region som innehåller, skriver du **Enable** i zon inställningen. | Om du vill flytta från en region som har stöd för zon redundans till en region som inte gör det, skriver du **disable** i zon inställningen.<br/><br/> Om du vill flytta från en region som inte har stöd för zon-redundans till en region som innehåller, skriver du **Enable** i zon inställningen.

### <a name="edit-sql-destination-settings"></a>Redigera inställningar för SQL-mål

Du ändrar mål inställningarna för en Azure SQL Database resurs på följande sätt: 

1. Klicka på **mål konfigurations** posten för den resurs som du vill ändra i **flera regioner**.
2. I **konfigurations inställningar** anger du de mål inställningar som sammanfattas i tabellen ovan.

## <a name="next-steps"></a>Nästa steg

[Flytta en virtuell Azure-dator](tutorial-move-region-virtual-machines.md) till en annan region.
