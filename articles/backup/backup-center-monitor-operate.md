---
title: Övervaka och arbeta med säkerhetskopieringar i Backup Center
description: Den här artikeln förklarar hur du övervakar och använder säkerhets kopieringar i skala med Backup Center
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 86b81110d6abeb1425e18ee45dfe65a96f69687d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102506171"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Övervaka och använda säkerhets kopieringar med Backup Center

Som administratör för säkerhets kopiering kan du använda Backup Center som ett enda fönster i glas för att övervaka dina jobb och säkerhets kopierings inventering på en daglig basis. Du kan också använda Backup Center för att utföra vanliga åtgärder, till exempel svara på begär Anden om säkerhets kopiering på begäran, återställa säkerhets kopior, skapa säkerhets kopierings principer och så vidare.

## <a name="supported-scenarios"></a>Scenarier som stöds

* Backup Center stöds för närvarande för säkerhets kopiering av virtuella Azure-datorer, SQL i Azure VM backup, SAP HANA i säkerhets kopiering av virtuella Azure-datorer, Azure Files säkerhets kopiering, Azure blobbar, Azure Managed Disks säkerhets kopiering och Azure Database for PostgreSQL Server säkerhets kopiering.
* Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="backup-instances"></a>Säkerhets kopierings instanser

Säkerhets kopierings Center möjliggör enkel sökning och identifiering av säkerhets kopierings instanser i din reserv fastighets reserv.

Genom att välja fliken **säkerhets kopierings instanser** i Backup Center kan du Visa information om alla säkerhets kopierings instanser som du har åtkomst till.

 Du kan visa följande information om var och en av dina säkerhets kopierings instanser:

* DataSource-prenumeration
* Data källa resurs grupp
* Senaste återställnings punkt
* Valv som är associerat med säkerhets kopierings instansen
* Skydds status

 Du kan också filtrera listan över säkerhets kopierings instanser i följande parametrar:

* DataSource-prenumeration
* Data källa resurs grupp
* Data källans plats
* DataSource-typ
* Valv
* Skydds status
* DataSource-Taggar

Genom att högerklicka på något av objekten i rutnätet kan du utföra åtgärder på den aktuella säkerhets kopierings instansen, till exempel navigera till resursen, utlösa säkerhets kopieringar på begäran och återställa eller stoppa säkerhets kopieringen.

![Säkerhets kopierings Center – instanser](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Säkerhets kopierings jobb

Med Backup Center kan du Visa detaljerad information om alla jobb som har skapats i reserv egendomen och vidta lämpliga åtgärder för att återställa jobb.

Om du väljer meny alternativet **säkerhetskopiera jobb** i Backup Center får du en översikt över alla dina jobb. Varje jobb innehåller följande information:

* Säkerhets kopierings instans kopplad till jobbet
* DataSource-prenumeration
* Data källa resurs grupp
* Data källans plats
* Jobb åtgärd
* Jobb status
* Jobbets starttid
* Jobb varaktighet

Genom att markera ett objekt i rutnätet kan du Visa mer information om det aktuella jobbet. Genom att högerklicka på ett objekt kan du navigera till resursen för att vidta nödvändiga åtgärder.

![Säkerhets kopierings Center – jobb](./media/backup-center-monitor-operate/backup-center-jobs.png)

Med hjälp av fliken **säkerhets kopierings jobb** kan du Visa jobb upp till de senaste sju dagarna. Om du vill visa äldre jobb använder du [säkerhets kopierings rapporter](backup-center-obtain-insights.md).

## <a name="vaults"></a>Valv

Om du väljer meny alternativet **valv** i Backup Center kan du se en lista över alla [Recovery Services-valv](backup-azure-recovery-services-vault-overview.md) och [säkerhets kopierings valv](backup-vault-overview.md) som du har åtkomst till. Du kan filtrera listan med följande parametrar:

* Valv prenumeration
* Valv resurs grupp
* Valvnamn
* DataSource-typ kopplad till principen

Genom att markera ett objekt i listan kan du navigera till ett angivet valv.

![Säkerhets kopierings Center – valv](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Säkerhets kopierings principer

Med Backup Center kan du Visa och redigera nyckelinformation för alla säkerhets kopierings principer.

Om du väljer meny alternativet **säkerhets kopierings principer** kan du Visa alla principer som du har skapat i din reserv egendom. Du kan filtrera listan efter valv prenumeration, resurs grupp, typ av data källa och valv. Genom att högerklicka på ett objekt i rutnätet kan du Visa associerade objekt för principen, redigera principen eller till och med ta bort den vid behov.

![Säkerhets kopierings Center – principer](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Nästa steg

* [Styr din reserv egendom](backup-center-govern-environment.md)
* [Utföra åtgärder med Backup Center](backup-center-actions.md)
* [Få insikter om dina säkerhets kopior](backup-center-obtain-insights.md)
