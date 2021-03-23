---
title: Felsöka Azure Application ett enhetligt ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller fel söknings innehåll för att använda Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 7ba5a05515284612e17d5aba4cc673c7e78f7ba1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869930"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Felsöka Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller fel söknings innehåll för att använda Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

Följande är vanliga problem som kan uppstå när du kör kommandona. Följ de lösnings instruktioner som nämns för att åtgärda problemet. Om du fortfarande stöter på ett problem öppnar du en tjänstbegäran från Azure Portal och tilldelar begäran till den SAP HANA stor instans kön för att Microsoft Support ska svara.

## <a name="log-files"></a>Loggfiler

En av de bästa informations källorna för fel sökning av fel med AzAcSnap är loggfilerna.  

### <a name="log-file-location"></a>Logg fils plats

Loggfilerna lagras i katalogen som kon figurer ATS per `logPath` parameter i konfigurations filen för AzAcSnap.  Standard konfigurations fil namnet är `azacsnap.json` och standardvärdet för `logPath` är, `"./logs"` vilket innebär att loggfilerna skrivs till `./logs` katalogen i förhållande till var `azacsnap` kommandot körs.  Om du gör `logPath` en absolut plats (t. ex. `/home/azacsnap/logs` ) ser du till att `azacsnap` loggarna visas `/home/azacsnap/logs` oavsett var `azacsnap` kommandot kördes.

### <a name="log-file-naming"></a>Logg fils namn

Logg fil namnet baseras på programmets namn (t. ex. `azacsnap` ), det kommando alternativ ( `-c` ) som används (t. ex.,, `backup` `test` `details` osv.) och konfigurations fil namnet (t. ex. standard = `azacsnap.json` ).  Så om du använder `-c backup` kommandot blir logg filen som standard `azacsnap-backup-azacsnap.log` och skrivs till katalogen som kon figurer ATS av `logPath` .  

Den här namngivnings konventionen upprättades för att tillåta flera konfigurationsfiler, en per databas, och garanterar att de associerade loggfilerna är lätta att hitta.  Om konfigurations filens fil namn är, är därför `SID.json` resultat filen när du använder `azacsnap -c backup --configfile SID.json` alternativen `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Resultat fil och syslog

`-c backup`Kommando alternativet AzAcSnap skriver ut till en `*.result` fil och system loggen ( `/var/log/messages` ) med hjälp av `logger` kommandot.  `*.result`Fil namnet har samma bas namn som [logg filen](#log-file-naming) och hamnar på samma [plats som logg filen](#log-file-location).  Det är en enkel rad utdatafil enligt följande exempel.

Exempel på utdata från `*.result` fil.
```output
Database # 1 (PR1) : completed ok
```

Exempel på utdata från `/var/log/messages` fil.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-azure-netapp-files"></a>Det gick inte att kommunicera med Azure NetApp Files

När du verifierar kommunikation med Azure NetApp Files kan kommunikationen Miss förfalla eller timeouta.  Kontrol lera att brand Väggs reglerna inte blockerar utgående trafik från systemet som kör AzAcSnap till följande adresser och TCP/IP-portar:-

- (https://) hantering. Azure. com: 443
- (https://) Login. microsoftonline. com: 443 

## <a name="failed-communication-with-sap-hana"></a>Det gick inte att kommunicera med SAP HANA

När du verifierar kommunikation med SAP HANA genom att köra ett test med `azacsnap -c test --test hana` och det ger följande fel:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Lösning:**

1. Kontrol lera konfigurations filen (till exempel `azacsnap.json` ) för varje Hana-instans för att säkerställa att SAP HANA databas värden är korrekta.
1. Försök att köra kommandot nedan för att kontrol lera om `hdbsql` kommandot finns i sökvägen och kan ansluta till den SAP HANA servern. I följande exempel visas rätt körning av kommandot och dess utdata.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    I det här exemplet `hdbsql` är kommandot inte i användarna `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    I det här exemplet `hdbsql` läggs kommandot tillfälligt till i användaren `$PATH` , men när körningen visar anslutnings nyckeln har inte ställts in korrekt med `hdbuserstore Set` kommandot (se komma igång guide för mer information):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > `$PATH`Uppdatera användarens fil permanent om du vill lägga till användarens `$HOME/.profile` filer

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`Platsen

När du konfigurerar kommunikation med SAP HANA `hdbuserstore` används programmet för att skapa inställningarna för säker kommunikation.  `hdbuserstore`Programmet finns vanligt vis under `/usr/sap/<SID>/SYS/exe/hdb/` eller `/usr/sap/hdbclient` .  Normalt lägger installations programmet till rätt plats till `azacsnap` användarens `$PATH` .

## <a name="failed-test-with-storage"></a>Det gick inte att testa med lagring

Kommandot `azacsnap -c test --test storage` slutförs inte korrekt.

### <a name="azure-large-instance"></a>Stor Azure-instans

Följande exempel körs `azacsnap` på SAP HANA på en stor Azure-instans:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Lösning:** Ovanstående fel visas vanligt vis när Azure stor Instance Storage-användare inte har åtkomst till den underliggande lagringen. Om du vill verifiera åtkomst till lagring med den angivna lagrings användaren kör du `ssh` kommandot för att verifiera kommunikationen med lagrings plattformen.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Ett exempel med förväntade utdata:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Det går inte att upprätta äktheten för värden "172.18.18.11 (172.18.18.11)"

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Lösning:** Välj inte Ja. Kontrol lera att din IP-adress för lagring är korrekt. Om det fortfarande finns ett problem bekräftar du lagrings-IP-adressen med Microsoft Operations-teamet.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Följande exempel körs `azacsnap` på en virtuell dator med hjälp av Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Lösning:**

1. Sök efter förekomsten av tjänstens huvud namns fil, `azureauth.json` som anges i `azacsnap.json` konfigurations filen.
1. Kontrol lera logg filen (till exempel `logs/azacsnap-test-azacsnap.log` ) för att se om tjänstens huvud namn ( `azureauth.json` ) har rätt innehåll.  Exempel från loggen på följande sätt:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Kontrol lera logg filen (till exempel `logs/azacsnap-test-azacsnap.log` ) för att se om tjänstens huvud namn ( `azureauth.json` ) har upphört att gälla. Exempel från loggen på följande sätt:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Nästa steg

- [Tips](azacsnap-tips.md)
