---
title: Konfigurera Azure Application konsekvent ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för att köra Configure-kommandot för Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0875aae8bb9049fc96377c1c98efa7391211d08f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97632974"
---
# <a name="configure-azure-application-consistent-snapshot-tool-preview"></a>Konfigurera Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller en guide för att köra Configure-kommandot för Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="introduction"></a>Introduktion

Konfigurations filen kan skapas eller redige ras med hjälp av `azacsnap -c configure` kommandot.

## <a name="command-options"></a>Kommando alternativ

`-c configure`Kommandot har följande alternativ

- `--configuration new` för att skapa en ny konfigurations fil.

- `--configuration edit` för att redigera en befintlig konfigurations fil.

- `[--configfile <config filename>]` är en valfri parameter som tillåter anpassade konfigurations fil namn.

## <a name="configuration-file-for-snapshot-tools"></a>Konfigurations fil för verktyg för ögonblicks bild

Du kan skapa en konfigurations fil genom att köra `azacsnap -c configure --configuration new` .  Som standard är konfigurationens fil namn `azacsnap.json` .  Ett anpassat fil namn kan användas med `--configfile=` parametern (till exempel `--configfile=<customname>.json` ) följande exempel gäller för Azures stora instans konfiguration:

```bash
azacsnap -c configure --configuration new
```

```output
Building new config file
Add comment to config file (blank entry to exit adding comments):This is a new config file for `azacsnap`
Add comment to config file (blank entry to exit adding comments):
Add database to config? (y/n) [n]: y
HANA SID (for example, H80): H80
HANA Instance Number (for example, 00): 00
HANA HDB User Store Key (for example, `hdbuserstore List`): AZACSNAP
HANA Server's Address (hostname or IP address): testing01
Add ANF Storage to database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]: y
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]: y
Storage User Name (for example, clbackup25): clt1h80backup
Storage IP Address (for example, 192.168.1.30): 172.18.18.11
Storage Volume Name (for example, hana_data_h80_testing01_mnt00001_t250_vol): hana_data_h80_testing01_mnt00001_t020_vol
Add DATA Volume to HLI Storage section of Database section? (y/n) [n]:
Add OTHER Volume to HLI Storage section of Database section? (y/n) [n]:
Add HLI Storage to database section? (y/n) [n]:
Add database to config? (y/n) [n]:
Editing configuration complete, writing output to 'azacsnap.json'
```

## <a name="details-of-required-values"></a>Information om de värden som krävs

Följande avsnitt innehåller detaljerad information om de olika värden som krävs för konfigurations filen.

### <a name="sap-hana-values"></a>SAP HANA värden

När du lägger till en *databas* i konfigurationen, krävs följande värden:

- **Hana-serverns adress** = SAP HANA serverns värdnamn eller IP-adress.
- **Hana sid** = SAP HANA system-ID.
- **Hana-instans nummer** = SAP HANA instans numret.
- **Hana HDB User Store Key** = den SAP HANA användare som kon figurer ATS med behörigheter för att köra databas säkerhets kopior.

- Enskild nod: IP och värdnamn för noden
- HSR med STONITH: IP och värdnamn för noden
- Skala ut (N + N, N + M): aktuell huvudnod-IP och värdnamn
- HSR utan STONITH: IP och värdnamn för noden
- Flera SID på en nod: värdnamn och IP för noden som är värd för dessa sid: er

### <a name="azure-large-instance-hli-storage-values"></a>Lagrings värden för Azure Large Instance (HLI)

När du lägger till *HLI Storage* i ett databas avsnitt, krävs följande värden:

- **Lagrings användar namn** = det här värdet är det användar namn som används för att upprätta ssh-anslutningen till lagringen.
- **IP-adress för lagring** = adressen för lagrings systemet.
- **Lagrings volymens namn** = volym namnet på ögonblicks bilden.  Det här värdet kan bestämmas på flera sätt, kanske det enklaste är att prova följande kommando:

    ```bash
    grep nfs /etc/fstab | cut -f2 -d"/" | sort | uniq
    ```

    ```output
    hana_data_p40_soldub41_mnt00001_t020_vol
    hana_log_backups_p40_soldub41_t020_vol
    hana_log_p40_soldub41_mnt00001_t020_vol
    hana_shared_p40_soldub41_t020_vol
    ```

### <a name="azure-netapp-files-anf-storage-values"></a>Azure NetApp Files (ANF) lagrings värden

När du lägger till *ANF Storage* i ett databas avsnitt, krävs följande värden:

- **Tjänstens huvud namn för autentisering** = detta är den `authfile.json` fil som genereras i Cloud Shell när du konfigurerar kommunikation med Azure NetApp Files Storage.
- **Fullständigt ANF lagrings volym resurs-ID** = det fullständiga resurs-ID: t för den volym som ögonblicks bilden.  Detta kan hämtas från: Azure Portal – > ANF – > volym – > inställningar/egenskaper – > resurs-ID

## <a name="configuration-file-overview-azacsnapjson"></a>Översikt över konfigurations fil ( `azacsnap.json` )

I följande exempel `azacsnap.json` är konfigurerad med ett sid.

Parametervärdena måste anges till kundens specifika SAP HANAs miljö.
För **Azure Large instance** -systemet tillhandahålls den här informationen av Microsoft Service Management under onboarding/överlämnande-anropet och görs tillgänglig i en Excel-fil som tillhandahålls under överlämnande. Öppna en tjänstbegäran om du behöver ange den här informationen igen.

Följande är endast ett exempel, uppdatera alla värden efter behov.

```bash
cat azacsnap.json
```

```output
{
  "version": "5.0 Preview",
  "logPath": "./logs",
  "securityPath": "./security",
  "comments": [],
  "database": [
    {
      "hana": {
        "serverAddress": "sapprdhdb80",
        "sid": "H80",
        "instanceNumber": "00",
        "hdbUserStoreName": "SCADMIN",
        "savePointAbortWaitSeconds": 600,
        "hliStorage": [
          {
            "dataVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_data_h80_azsollabbl20a31_mnt00001_t210_vol"
              },
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_shared_h80_azsollabbl20a31_t210_vol"
              }
            ],
            "otherVolume": [
              {
                "backupName": "clt1h80backup",
                "ipAddress": "172.18.18.11",
                "volume": "hana_log_backups_h80_azsollabbl20a31_t210_vol"
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

> [!NOTE]
> För ett DR-scenario där säkerhets kopieringarna ska köras på DR-platsen, ska det HANA-servernamn som kon figurer ATS i DR-konfigurationsfilen (till exempel `DR.json` ) på Dr-platsen vara samma som namnet på produktions servern.

> [!NOTE]
> För en stor Azure-instans måste lagrings-IP-adressen finnas i samma undernät som serverpoolen. I det här fallet är vårt undernät för serverpoolen 172. arton. 18.0/24 och vår tilldelade IP-adress för lagring är 172.18.18.11.

## <a name="next-steps"></a>Nästa steg

- [Verktyget testa Azure Application konsekvent ögonblicks bild](azacsnap-cmd-ref-test.md)
