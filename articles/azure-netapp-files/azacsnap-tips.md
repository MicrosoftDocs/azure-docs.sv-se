---
title: Tips om hur du använder Azure Application enhetligt ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller tips och trick för att använda Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 6465acc0d4ce760e0bf89c73dace7c8c66d37c49
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869947"
---
# <a name="tips-and-tricks-for-using-azure-application-consistent-snapshot-tool-preview"></a>Tips och trick för att använda Azure Application enhetligt ögonblicks bilds verktyg (för hands version)

Den här artikeln innehåller tips och knep som kan vara till hjälp när du använder AzAcSnap.

## <a name="limit-service-principal-permissions"></a>Begränsa behörigheter för tjänstens huvud namn

Det kan vara nödvändigt att begränsa omfattningen för AzAcSnap-tjänstens huvud namn.  Granska [Azure RBAC-dokumentationen](../role-based-access-control/index.yml) för mer information om detaljerad åtkomst hantering av Azure-resurser.  

Följande är en exempel roll definition med minsta möjliga åtgärder som krävs för att AzAcSnap ska fungera.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```

För att återställnings alternativen ska fungera korrekt måste AzAcSnap-tjänstens huvud namn också kunna skapa volymer.  I det här fallet behöver roll definitionen ytterligare en åtgärd, och därför bör hela tjänstens huvud namn se ut som i följande exempel.

```bash
az role definition create --role-definition '{ \
  "Name": "Azure Application Consistent Snapshot tool", \
  "IsCustom": "true", \
  "Description": "Perform snapshots and restores on ANF volumes.", \
  "Actions": [ \
    "Microsoft.NetApp/*/read", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete", \
    "Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write" \
  ], \
  "NotActions": [], \
  "DataActions": [], \
  "NotDataActions": [], \
  "AssignableScopes": ["/subscriptions/<insert your subscription id>"] \
}'
```


## <a name="take-snapshots-manually"></a>Ta ögonblicks bilder manuellt

Innan du kör några säkerhets kopierings kommandon ( `azacsnap -c backup` ) kontrollerar du konfigurationen genom att köra test kommandona och kontrol lera att de har körts.  Korrekt körning av de här testerna `azacsnap` kan ha kunnat kommunicera med den installerade SAP HANA databasen och det underliggande lagrings systemet för SAP HANA på **Azures stora instanser** eller **Azure NetApp Files** system.

- `azacsnap -c test --test hana`
- `azacsnap -c test --test storage`

Kör sedan följande kommando för att göra en manuell säkerhets kopiering av databas ögonblicks bilder:

```bash
azacsnap -c backup --volume data --prefix hana_TEST --retention=1
```

## <a name="setup-automatic-snapshot-backup"></a>Konfigurera automatisk säkerhets kopiering av ögonblicks bilder

Det är vanligt att använda UNIX/Linux-system för `cron` att automatisera körning av kommandon i ett system. Standard praxis för verktyg för ögonblicks bilder är att konfigurera användarens `crontab` .

Ett exempel på en `crontab` som användaren kan använda för `azacsnap` att automatisera ögonblicks bilder finns nedan.

```output
MAILTO=""
# =============== TEST snapshot schedule ===============
# Data Volume Snapshots - taken every hour.
@hourly (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume data --prefix hana_TEST --retention=9)
# Other Volume Snapshots - taken every 5 minutes, excluding the top of the hour when hana snapshots taken
5,10,15,20,25,30,35,40,45,50,55 * * * * (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix logs_TEST --retention=9)
# Other Volume Snapshots - using an alternate config file to snapshot the boot volume daily.
@daily (. /home/azacsnap/.profile ; cd /home/azacsnap/bin ; azacsnap -c backup --volume other --prefix DailyBootVol --retention=7 --configfile boot-vol.json)
```

Förklaring av ovanstående crontab.

- `MAILTO=""`: genom att ha ett tomt värde förhindrar cron automatiskt att användaren försöker skicka användaren vid körning av crontab-posten eftersom den skulle bli en sannolik i den lokala användarens e-postfil.
- Kort versioner av tids inställningar för crontab-poster är själv för klar ande:
  - `@monthly` = Kör en gång i månaden, det vill säga "0 0 1 * *".
  - `@weekly`  = Kör en gång i veckan, det vill säga "0 0 * * 0".
  - `@daily`   = Kör en gång om dagen, det vill säga "0 0 * * *".
  - `@hourly`  = Kör en gång i timmen, det vill säga "0 * * * *".
- De första fem kolumnerna används för att ange tider, se kolumn exemplen nedan:
  - `0,15,30,45`: Var 15: e minut
  - `0-23`: Varje timme
  - `*` : Varje dag
  - `*` : Varje månad
  - `*` : Varje dag i veckan
- Kommando rad som ska köras inom hakparenteser ()
  - `. /home/azacsnap/.profile` = Hämta i användarens. profil för att konfigurera deras miljö, inklusive $PATH osv.
  - `cd /home/azacsnap/bin` = ändra körnings katalog till platsen "/Home/azacsnap/bin" där config-filerna finns.
  - `azacsnap -c .....` = det fullständiga azacsnap-kommandot körs, inklusive alla alternativ.

Ytterligare förklaring av cron och formatet för CRONTAB-filen här: <https://en.wikipedia.org/wiki/Cron>

> [!NOTE]
> Användarna ansvarar för att övervaka cron-jobben för att se till att ögonblicks bilder genereras korrekt.

## <a name="monitor-the-snapshots"></a>Övervaka ögonblicks bilderna

Följande villkor bör övervakas för att säkerställa ett felfritt system:

1. Tillgängligt disk utrymme. Ögonblicks bilder kommer långsamt att förbruka disk utrymme eftersom äldre disk block hålls kvar i ögonblicks bilden.
    1. Du kan automatisera hanteringen av disk utrymme genom att `--retention` använda `--trim` alternativen och för att automatiskt rensa gamla ögonblicks bilder och databasfiler.
1. Lyckad körning av verktyg för ögonblicks bilder
    1. Kontrol lera `*.result` om filen lyckades eller misslyckades för den senaste körningen av `azacsnap` .
    1. Sök `/var/log/messages` efter utdata från `azacsnap` kommandot.
1. Konsekvensen av ögonblicks bilderna genom att återställa dem till ett annat system med jämna mellanrum.

> [!NOTE]
> Om du vill visa information om ögonblicks bilder kör du kommandot `azacsnap -c details` .

## <a name="delete-a-snapshot"></a>Ta bort en ögonblicks bild

Om du vill ta bort en ögonblicks bild kör du kommandot `azacsnap -c delete` . Det går inte att ta bort ögonblicks bilder från operativ system nivån. Du måste använda rätt kommando ( `azacsnap -c delete` ) för att ta bort lagrings ögonblicks bilder.

> [!IMPORTANT]
> Vigilant när du tar bort en ögonblicks bild. När den har tagits bort är det **omöjligt** att återställa borttagna ögonblicks bilder.

## <a name="restore-a-snapshot"></a>Återställ en ögonblicksbild

En ögonblicks bild av lagrings volymen kan återställas till en ny volym ( `-c restore --restore snaptovol` ).  För en stor Azure-instans kan volymen återställas till en ögonblicks bild ( `-c restore --restore revertvolume` ).

> [!NOTE]
> **Inget** databas återställnings kommando har angetts.

En ögonblicks bild kan kopieras tillbaka till SAP HANA data områden, men SAP HANA får inte köras när en kopia görs ( `cp /hana/data/H80/mnt00001/.snapshot/hana_hourly.2020-06-17T113043.1586971Z/*` ).

För stor Azure-instans kan du kontakta Microsoft Operations-teamet genom att öppna en service förfrågan för att återställa en önskad ögonblicks bild från befintliga tillgängliga ögonblicks bilder. Du kan öppna en tjänstbegäran från Azure Portal: <https://portal.azure.com>

Om du bestämmer dig för att utföra redundansväxling av haveri beredskap `azacsnap -c restore --restore revertvolume` kommer kommandot på Dr-platsen automatiskt att göra de senaste `/hana/data` `/hana/logbackups` volym ögonblicks bilderna (och) tillgängliga för att möjliggöra en SAP HANA återställning. Använd det här kommandot med försiktighet när det bryter replikeringen mellan produktions-och DR-platser.

## <a name="set-up-snapshots-for-boot-volumes-only"></a>Konfigurera ögonblicks bilder för enbart start volymer

> [!IMPORTANT]
> Den här åtgärden gäller endast för en stor Azure-instans.

I vissa fall har kunder redan verktyg för att skydda SAP HANA och vill bara konfigurera start volym ögonblicks bilder.  I det här fallet är uppgiften förenklad och följande steg bör vidtas.

1. Slutför stegen 1-4 i för hands kraven för installation.
1. Aktivera kommunikation med lagring.
1. Hämta installations programmet för att installera verktyg för ögonblicks bilder.
1. Slutför installationen av verktyg för ögonblicks bilder.
1. Skapa en ny konfigurations fil enligt följande. Information om start volymen måste vara i OtherVolume-Stanza (användar poster i <span style="color:red">rött</span>):
    ```output
    > <span style="color:red">azacsnap -c configure --configuration new --configfile BootVolume.json</span>
    Building new config file
    Add comment to config file (blank entry to exit adding comments):<span style="color:red">Boot only config file.</span>
    Add comment to config file (blank entry to exit adding comments):
    Add database to config? (y/n) [n]: <span style="color:red">y</span>
    HANA SID (for example, H80): <span style="color:red">X</span>
    HANA Instance Number (for example, 00): <span style="color:red">X</span>
    HANA HDB User Store Key (for example, `hdbuserstore List`): <span style="color:red">X</span>
    HANA Server's Address (hostname or IP address): <span style="color:red">X</span>
    Add ANF Storage to database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]: <span style="color:red">y</span>
    Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]: <span style="color:red">y</span>
    Storage User Name (for example, clbackup25): <span style="color:red">shoasnap</span>
    Storage IP Address (for example, 192.168.1.30): <span style="color:red">10.1.1.10</span>
    Storage Volume Name (for example, hana_data_soldub41_t250_vol): <span style="color:red">t210_sles_boot_azsollabbl20a31_vol</span>
    Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
    Add HLI Storage to database section? (y/n) [n]:
    Add database to config? (y/n) [n]:

    Editing configuration complete, writing output to 'BootVolume.json'.
    ```
1. Kontrol lera konfigurations filen, se följande exempel:

    Använd `cat` kommandot för att visa innehållet i konfigurations filen:

    ```bash
    cat BootVolume.json
    ```

    ```output
    {
      "version": "5.0 Preview",
      "logPath": "./logs",
      "securityPath": "./security",
      "comments": [
        "Boot only config file."
      ],
      "database": [
        {
          "hana": {
            "serverAddress": "X",
            "sid": "X",
            "instanceNumber": "X",
            "hdbUserStoreName": "X",
            "savePointAbortWaitSeconds": 600,
            "hliStorage": [
              {
                "dataVolume": [],
                "otherVolume": [
                  {
                    "backupName": "shoasnap",
                    "ipAddress": "10.1.1.10",
                    "volume": "t210_sles_boot_azsollabbl20a31_vol"
                  }
                ]
              }
            ],
            "anfStorage": []
          }
        }
      ]
    }
    ```

1. Testa en säkerhets kopia av start volymen

    ```bash
    azacsnap -c backup --volume other --prefix TestBootVolume --retention 1 --configfile BootVolume.json
    ```

1. Kontrol lera att det finns i listan, och Lägg `--snapshotfilter` till alternativet för att begränsa ögonblicks bild listan som returneras.

    ```bash
    azacsnap -c details --snapshotfilter TestBootVolume --configfile BootVolume.json
    ```

    Kommandoutdata:
    ```output
    List snapshot details called with snapshotFilter 'TestBootVolume'
    #, Volume, Snapshot, Create Time, HANA Backup ID, Snapshot Size
    #1, t210_sles_boot_azsollabbl20a31_vol, TestBootVolume.2020-07-03T034651.7059085Z, "Fri Jul 03 03:48:24 2020", "otherVolume Backup|azacsnap version: 5.0 Preview (20200617.75879)", 200KB
    , t210_sles_boot_azsollabbl20a31_vol, , , Size used by Snapshots, 1.31GB
    ```

1. Konfigurera automatisk säkerhets kopiering av ögonblicks bilder.

> [!NOTE]
> Det krävs inte att konfigurera kommunikation med SAP HANA.

## <a name="restore-a-boot-snapshot"></a>Återställa en "Start"-ögonblicks bild

> [!IMPORTANT]
> Den här åtgärden gäller för endast i Azure stor instans.
> Servern kommer att återställas till den tidpunkt då ögonblicks bilden togs.

En boot-ögonblicksbild kan återställas på följande sätt:

1. Kunden måste stänga av servern.
1. När servern har stängts av måste kunden öppna en tjänstbegäran som innehåller det dator-ID och den ögonblicks bild som ska återställas.
    > Kunder kan öppna en tjänstbegäran från Azure Portal: <https://portal.azure.com>
1. Microsoft återställer operativ systemets LUN med det angivna dator-ID: t och ögonblicks bilden och startar sedan servern.
1. Kunden måste då bekräfta att servern har startats och är felfri.

Inga ytterligare steg att utföra efter återställningen.

## <a name="key-facts-to-know-about-snapshots"></a>Viktiga fakta för att veta om ögonblicks bilder

Nyckelattribut för lagrings volymens ögonblicks bilder:

- **Plats för ögonblicks bilder**: ögonblicks bilder kan hittas i en virtuell katalog ( `.snapshot` ) i volymen.  Se följande exempel för **stor Azure-instans**:
  - Databas `/hana/data/<SID>/mnt00001/.snapshot`
  - Resursen `/hana/shared/<SID>/.snapshot`
  - Transaktionslogg `/hana/logbackups/<SID>/.snapshot`
  - Start: Start-ögonblicksbilder för HLI är **inte synliga** från operativ system nivån, men kan listas med `azacsnap -c details` .

  > [!NOTE]
  >  `.snapshot` är en skrivskyddad dold *virtuell* mapp som tillhandahåller skrivskyddad åtkomst till ögonblicks bilderna.

- **Maximal ögonblicks bild:** Maskin varan kan hantera upp till 250 ögonblicks bilder per volym. Ögonblicks bild kommandot behåller maximalt antal ögonblicks bilder för prefixet baserat på den kvarhållna uppsättningen på kommando raden och tar bort den äldsta ögonblicks bilden om den går utanför det maximala antalet som ska behållas.
- **Namn på ögonblicks bild:** Namnet på ögonblicks bilden innehåller den prefix som kunden angav.
- **Storlek på ögonblicks bilden:** Beror på databas nivåns storlek/ändringar.
- **Plats för loggfil:** Loggfiler som genereras av kommandona matas in i mappar som definieras i JSON-konfigurationsfilen, som som standard är en undermapp under där kommandot körs (till exempel `./logs` ).

## <a name="next-steps"></a>Nästa steg

- [Felsöka](azacsnap-troubleshoot.md)
