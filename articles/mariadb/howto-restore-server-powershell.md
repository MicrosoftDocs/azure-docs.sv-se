---
title: Säkerhets kopiering och återställning – Azure PowerShell-Azure Database for MariaDB
description: Lär dig hur du säkerhetskopierar och återställer en server i Azure Database for MariaDB genom att använda Azure PowerShell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 05/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0207be2c983fd986d5852403e36462d2d7d2cdda
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664859"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mariadb-server-using-powershell"></a>Säkerhetskopiera och återställa en Azure Database for MariaDB-server med hjälp av PowerShell

Azure Database for MariaDB servrar säkerhets kopie ras regelbundet för att aktivera återställnings funktioner. Med den här funktionen kan du återställa servern och alla dess databaser till en tidigare tidpunkt på en ny server.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktions guiden behöver du:

- [AZ PowerShell-modulen](/powershell/azure/install-az-ps) installeras lokalt eller [Azure Cloud Shell](https://shell.azure.com/) i webbläsaren
- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Även om PowerShell-modulen AZ. MariaDb är i för hands version måste du installera den separat från AZ PowerShell-modulen med hjälp av följande kommando: `Install-Module -Name Az.MariaDb -AllowPrerelease` .
> När AZ. MariaDb PowerShell-modulen är allmänt tillgänglig blir den en del av framtida versioner av AZ PowerShell-modulen som är tillgängliga internt inifrån Azure Cloud Shell.

Om du väljer att använda PowerShell lokalt ansluter du till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Ange konfiguration för säkerhets kopiering

När servern har skapats kan du välja mellan att konfigurera servern för antingen lokalt redundanta eller geografiskt redundanta säkerhets kopieringar.

> [!NOTE]
> När en server har skapats har den typ av redundans som den har, geografiskt redundant vs lokalt redundant, inte ändrats.

När du skapar en server via `New-AzMariaDbServer` kommandot, bestämmer **GeoRedundantBackup** -parametern säkerhets kopians redundans alternativ. Om **aktive rad** tas geo-redundanta säkerhets kopieringar. Eller om den är **inaktive rad** tas lokalt redundanta säkerhets kopieringar.

Kvarhållningsperioden för säkerhets kopior anges av parametern **BackupRetentionDay** .

Mer information om hur du anger dessa värden när du skapar en server finns i [skapa en Azure Database for MariaDB server med PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md).

Du kan ändra kvarhållningsperioden för säkerhets kopior för en server på följande sätt:

```azurepowershell-interactive
Update-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

I föregående exempel ändras kvarhållningsperioden för säkerhets kopior på mydemoserver till 10 dagar.

Kvarhållningsperioden för säkerhets kopior styr hur långt tillbaka en tidpunkts återställning kan hämtas, eftersom den baseras på tillgängliga säkerhets kopior. Återställning av tidpunkt för tidpunkt beskrivs ytterligare i nästa avsnitt.

## <a name="server-point-in-time-restore"></a>Återställning av Server-in-Time-återställning

Du kan återställa servern till en tidigare tidpunkt. Återställda data kopieras till en ny server och den befintliga servern lämnas oförändrad. Om en tabell t. ex. har släppts av misstag kan du återställa till den tid då bara den här minskningen ägde rum. Sedan kan du hämta den saknade tabellen och data från den återställda kopian av servern.

Använd PowerShell-cmdleten för att återställa servern `Restore-AzMariaDbServer` .

### <a name="run-the-restore-command"></a>Kör kommandot Restore

Om du vill återställa servern kör du följande exempel från PowerShell.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Parameter uppsättningen **PointInTimeRestore** för `Restore-AzMariaDbServer` cmdleten kräver följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Resurs gruppen där käll servern finns.  |
| Name | mydemoserver-restored | Namnet på den nya server som skapas med kommandot restore. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Välj en tidpunkt för återställning. Datumet och tiden måste finnas inom källserverns kvarhållningsperiod för säkerhetskopiering. Använd ISO8601 datum-och tids format. Du kan till exempel använda din egen lokala tidszon som **2020-03-13T05:59:00-08:00**. Du kan också använda formatet UTC-Zulu, till exempel **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Använd punkt-i-Time-läge för att återställa. |

När du återställer en server till en tidigare tidpunkt skapas en ny server. Den ursprungliga servern och dess databaser från den angivna tidpunkten kopieras till den nya servern.

Plats-och pris nivå värden för den återställda servern förblir samma som den ursprungliga servern.

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen startades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="geo-restore"></a>Geo-återställning

Om du har konfigurerat servern för geografiskt redundanta säkerhets kopieringar kan en ny server skapas från säkerhets kopian av den befintliga servern. Den nya servern kan skapas i vilken region som helst som Azure Database for MariaDB tillgänglig.

Om du vill skapa en server med hjälp av en Geo-redundant säkerhets kopia använder du `Restore-AzMariaDbServer` kommandot med parametern **UseGeoRestore** .

> [!NOTE]
> När en server först skapas kanske den inte är omedelbart tillgänglig för geo Restore. Det kan ta några timmar för nödvändiga metadata att fyllas i.

Om du vill använda geo-återställning av servern kör du följande exempel från PowerShell:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

I det här exemplet skapas en ny server med namnet **mydemoserver-långsiktig återställning** i regionen USA, östra som tillhör **myresourcegroup**. Det är en Generell användning, gen 5-Server med 8 virtuella kärnor. Servern skapas från den geo-redundanta säkerhets kopieringen av **mydemoserver**, även i resurs gruppen **myresourcegroup**.

Om du vill skapa den nya servern i en annan resurs grupp än den befintliga servern anger du det nya resurs grupps namnet med hjälp av parametern **ResourceGroupName** , som du ser i följande exempel:

```azurepowershell-interactive
Get-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMariaDbServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Parameter **uppsättningen för den här** `Restore-AzMariaDbServer` cmdleten måste ha följande parametrar:

| Inställning | Föreslaget värde | Beskrivning  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Namnet på den resurs grupp som den nya servern tillhör.|
|Name | mydemoserver – omåterställd | Namnet på den nya servern. |
|Location | USA, östra | Platsen för den nya servern. |
|UseGeoRestore | `<SwitchParameter>` | Använd geo-läge för att återställa. |

När du skapar en ny server med geo Restore ärver den samma lagrings storlek och pris nivå som käll servern om inte parametern **SKU** anges.

När återställnings processen har slutförts letar du reda på den nya servern och kontrollerar att data återställs som förväntat. Den nya servern har samma inloggnings namn och lösen ord för Server administratören som var giltiga för den befintliga servern vid den tidpunkt då återställningen startades. Du kan ändra lösen ordet från den nya serverns **översikts** sida.

Den nya servern som skapades under en återställning saknar de VNet-tjänstens slut punkter som fanns på den ursprungliga servern. Dessa regler måste konfigureras separat för den här nya servern. Brand Väggs regler från den ursprungliga servern återställs.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Så här genererar du en Azure Database for MariaDB anslutnings sträng med PowerShell](howto-connection-string-powershell.md)