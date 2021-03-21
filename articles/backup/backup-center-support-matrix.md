---
title: Support mat ris för backup Center
description: Den här artikeln sammanfattar scenarier som stöds av backup Center för varje arbets belastnings typ
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: d6e5d34e201edda4fd1e9fda85f210fb88211e28
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102504515"
---
# <a name="support-matrix-for-backup-center"></a>Support mat ris för backup Center

Backup Center innehåller ett fönster med glas för företag som [styr, övervakar, hanterar och analyserar säkerhets kopior i stor skala](backup-center-overview.md). Den här artikeln sammanfattar scenarier som stöds av backup Center för varje arbets belastnings typ.

## <a name="supported-scenarios"></a>Scenarier som stöds

| **Kategori** | **Scenario**  | **Arbetsbelastningar som stöds**  | **Gränser** |
| -------------| ------------- | ----------------------- |------------|
| Övervakning   | Visa alla jobb | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | <li> 7 dagar tillgängligt jobb som är tillgängliga i kartongen. <br> <li> Varje filter/listruta stöder högst 1000 objekt. Så att backup Center kan användas för att övervaka högst 1000 prenumerationer och 1000-valv mellan klienter. |
| Övervakning | Visa alla säkerhets kopierings instanser | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Samma som ovan |
| Övervakning | Visa alla säkerhets kopierings principer | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Samma som ovan |
| Övervakning | Visa alla valv | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Samma som ovan |
| Åtgärder | Konfigurera säkerhetskopiering | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Azure VM backup](./backup-support-matrix-iaas.md) och [Azure Database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Åtgärder | Återställ säkerhets kopierings instans | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Azure VM backup](./backup-support-matrix-iaas.md) och [Azure Database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Åtgärder | Skapa valv | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Recovery Services Vault](./backup-support-matrix.md#vault-support) |
| Åtgärder | Skapa säkerhets kopierings princip | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Recovery Services Vault](./backup-support-matrix.md#vault-support) |
| Åtgärder | Kör säkerhets kopiering på begäran för en säkerhets kopierings instans | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Azure VM backup](./backup-support-matrix-iaas.md) och [Azure Database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Åtgärder | Stoppa säkerhets kopiering för en säkerhets kopierings instans | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server <br><br> <li> SQL i virtuell Azure-dator <br><br> <li> SAP HANA i virtuell Azure-dator <br><br> <li> Azure Files<br/><br/> <li>Azure-blobar<br/><br/> <li>Azure Managed Disks | Referera till support-matriser för [Azure VM backup](./backup-support-matrix-iaas.md) och [Azure Database for PostgreSQL Server Backup](backup-azure-database-postgresql.md#support-matrix) |
| Insikter | Visa säkerhets kopierings rapporter | <li> Virtuell Azure-dator <br><br> <li> SQL på virtuell Azure-dator <br><br> <li> SAP HANA på den virtuella Azure-datorn <br><br> <li> Azure Files <br><br> <li> System Center Data Protection Manager <br><br> <li> Azure Backup Agent (MARS) <br><br> <li> Azure Backup Server (MABS) | Läs om [vilka scenarier som stöds för säkerhets kopierings rapporter](./configure-reports.md#supported-scenarios) |
| Styrning | Visa och tilldela inbyggda och anpassade Azure-principer under kategorin säkerhets kopiering | Saknas | Saknas |
| Styrning | Visa data källor som inte har kon figurer ATS för säkerhets kopiering | <li> Virtuell Azure-dator <br><br> <li> Azure Database for PostgreSQL Server | Ej tillämpligt |

## <a name="unsupported-scenarios"></a>Scenarier som inte stöds

| **Kategori** | **Scenario**  |
|--------------|---------------|
| Övervakning | Visa aviseringar i skala |
| Åtgärder | Konfigurera valv inställningar i skala |
| Åtgärder | Kör återställnings jobb över flera regioner från backup Center |

## <a name="next-steps"></a>Nästa steg

* [Granska support mat ris för Azure Backup](./backup-support-matrix.md)
* [Granska support matrisen för säkerhets kopiering av virtuella Azure-datorer](./backup-support-matrix-iaas.md)
* [Granska support mat ris för Azure Database for PostgreSQL Server säkerhets kopiering](backup-azure-database-postgresql.md#support-matrix)