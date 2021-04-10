---
title: Säkerhetskopiering och återställning för Azure Database for PostgreSQL Hyperskala-servergrupper
description: Säkerhetskopiering och återställning för Azure Database for PostgreSQL Hyperskala-servergrupper
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101686707"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Säkerhetskopiera och återställa Azure Arc-aktiverade PostgreSQL för storskaliga Server grupper

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

När du säkerhetskopierar eller återställer en Azure-båge som är aktive rad PostgreSQL-Server grupp, säkerhets kopie ras och/eller återställs hela uppsättningen databaser på alla PostgreSQL-noder i Server gruppen.

## <a name="take-a-manual-full-backup"></a>Gör en manuell fullständig säkerhets kopiering

Kör följande kommando för att göra en fullständig säkerhets kopia av hela data-och loggmappen i Server gruppen:
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Plats:
- __namn__ anger namnet på en säkerhets kopia
- __Server namn__ anger en Server grupp
- __no-wait__ anger att kommando raden inte väntar på att säkerhets kopieringen ska slutföras för att du ska kunna fortsätta att använda detta kommando rads fönster

Det här kommandot koordinerar en distribuerad fullständig säkerhets kopiering över alla noder som utgör din Azure Arc-aktiverade PostgreSQL-Server grupp. Med andra ord säkerhets kopie ras alla data i koordinator-och arbetsnoderna.

Exempel:

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

När säkerhets kopieringen är klar returneras ID, namn, storlek, tillstånd och tidsstämpel för säkerhets kopian. Exempel:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` anger tids zonen för tiden då säkerhets kopieringen gjordes. I det här exemplet betyder "+ 00:00" UTC-tid (UTC + 00 timme 00 minuter).

> [!NOTE]
> Det är ännu inte möjligt att:
> - Schemalägga automatiska säkerhets kopieringar
> - Visa förloppet för en säkerhets kopia medan den tas

## <a name="list-backups"></a>Visa säkerhetskopior

Om du vill visa en lista över säkerhets kopior som är tillgängliga för återställning kör du följande kommando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Exempel:

```console
azdata arc postgres backup list --server-name postgres01
```

Returnerar utdata som:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

Kolumnen tidsstämpelkolumn anger tidpunkten i UTC då säkerhets kopieringen gjordes.

## <a name="restore-a-backup"></a>Återställ en säkerhetskopia
I det här avsnittet visar vi hur du gör en fullständig återställning eller en återställning av en tidpunkt. När du återställer en fullständig säkerhets kopia återställer du hela innehållet i säkerhets kopian. När du gör en tidpunkts återställning återställer du upp till den tidpunkt som du anger. Alla transaktioner som har utförts senare än den här tidpunkten återställs inte.

### <a name="restore-a-full-backup"></a>Återställa en fullständig säkerhets kopia
För att återställa hela innehållet i en säkerhets kopia kör du kommandot:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Plats:
- __säkerhets kopierings-ID__ är ID: t för säkerhets kopian som visas i listan över säkerhets kopieringar som visas ovan.
Detta koordinerar en distribuerad fullständig återställning över alla noder som utgör din Azure Arc-aktiverade PostgreSQL-Server grupp. Med andra ord återställs alla data i koordinator-och arbetsnoderna.

#### <a name="examples"></a>Exempel:

__Återställ Server gruppen postgres01 till sig själv:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Den här åtgärden stöds endast för PostgreSQL-version 12 och högre.

__Återställa Server gruppens postgres01 till en annan server grupp postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Den här åtgärden stöds för alla versioner av PostgreSQL som startar version 11. Mål server gruppen måste skapas före återställnings åtgärden, måste vara av samma konfiguration och måste använda samma säkerhets kopierings-PVC som käll Server gruppen.

När återställningen är klar returnerar den utdata som detta till kommando raden:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Det är ännu inte möjligt att:
> - Återställa en säkerhets kopia genom att ange dess namn
> - Visa förloppet för en återställnings åtgärd


### <a name="do-a-point-in-time-restore"></a>Gör en återställning vid ett tillfälle

Om du vill återställa en Server grupp till en viss tidpunkt kör du kommandot:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Om du vill läsa det allmänna formatet för RESTORE-kommandot kör du: `azdata arc postgres backup restore --help` .

Var `time` är tidpunkten för att återställa till. Ange antingen en tidstämpel eller en siffra och ett suffix ( `m` i minuter, i `h` timmar, `d` i dagar eller `w` i veckor). Till exempel `1.5h` går tillbaka 90 minuter.

#### <a name="examples"></a>Exempel:
__Gör en återställning till en tidpunkt för Server gruppen postgres01 till sig själv:__

Det går inte att utföra återställnings punkt för en Server grupp på egen hand.

__Gör en återställning av Server gruppen postgres01 till en annan server grupp postgres02 till en viss tidsstämpel:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

I det här exemplet återställs till Server gruppen postgres02 det tillstånd där Server grupp postgres01 skedde den 8 december 2020 vid 04:23:48.75 UTC. Observera att "+ 00" anger tids zonen för den tidpunkt som du anger. Om du inte anger en tidszon används den tidszon för klienten som du kör återställnings åtgärden från.

Exempel:
- `2020-12-08 04:23:48.751326+00` tolkas som `2020-12-08 04:23:48.751326` UTC
- Om du befinner dig i Pacific Standard Time Zone (PST = UTC + 08) `2020-12-08 04:23:48.751326` tolkas den som `2020-12-08 12:23:48.751326` UTC den här åtgärden stöds för alla versioner av postgresql som startar version 11. Mål server gruppen måste skapas före återställnings åtgärden och måste använda samma säkerhets kopierings-PVC som käll Server gruppen.


__Gör en återställning av Server gruppens postgres01 till en annan server grupp postgres02 till en viss tids period tidigare:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Det här exemplet återställer till Server gruppen postgres02 det tillstånd då Server grupp postgres01 var 22 minuter sedan.
Den här åtgärden stöds för alla versioner av PostgreSQL som startar version 11. Mål server gruppen måste skapas före återställnings åtgärden och måste använda samma säkerhets kopierings-PVC som käll Server gruppen.

> [!NOTE]
> Det är ännu inte möjligt att:
> - Visa förloppet för en återställnings åtgärd

## <a name="delete-backups"></a>Ta bort säkerhetskopior

Kvarhållning av säkerhets kopior kan inte anges som för hands version. Du kan dock manuellt ta bort säkerhets kopior som du inte behöver.
Det allmänna kommandot för att ta bort säkerhets kopior är:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

där:
- `--server-name` är namnet på den server grupp från vilken användaren vill ta bort en säkerhets kopia
- `--name` är namnet på den säkerhets kopia som ska tas bort
- `-id`är ID: t för den säkerhets kopia som ska tas bort

> [!NOTE]
> `--name` och `-id` är ömsesidigt uteslutande.

Exempel:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Du kan hämta namnet och ID: t för dina säkerhets kopieringar genom att köra säkerhets kopierings kommandot enligt beskrivningen i föregående stycke.

Om du vill ha mer information om kommandot Ta bort kör du:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att skala ut (lägga till arbetsnoder)](scale-out-postgresql-hyperscale-server-group.md) i Server gruppen
- Läs mer om att [skala upp eller ned (öka/minska minnes-och virtuella kärnor)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) i Server gruppen
