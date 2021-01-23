---
title: Kom igång med Azure Application enhetligt verktyg för ögonblicks bilder för Azure NetApp Files | Microsoft Docs
description: Innehåller en guide för att installera Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736370"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Kom igång med Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Den här artikeln innehåller en guide för att installera Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Hämta verktyg för ögonblicks bilder

Vi rekommenderar att kunderna får den senaste versionen av [installations programmet för AzAcSnap](https://aka.ms/azacsnapdownload) från Microsoft.

Själv installations filen har en associerad [AzAcSnap](https://aka.ms/azacsnapdownloadsignature) som är signerad med Microsofts offentliga nyckel för att tillåta GPG verifiering av det nedladdade installations programmet.

När dessa nedladdningar har slutförts följer du stegen i den här hand boken för att installera.

### <a name="verifying-the-download"></a>Verifierar nedladdningen

Installations programmet, som kan hämtas enligt ovan, har en associerad PGP-signaturfil med `.asc` fil namns tillägget. Den här filen kan användas för att kontrol lera att installations programmet är verifierat som Microsoft-fil. Den offentliga Microsoft PGP-nyckeln som används för att signera Linux-paket finns här ( <https://packages.microsoft.com/keys/microsoft.asc> ) och har använts för att signera signatur filen.

Den offentliga nyckeln för Microsoft PGP kan importeras till en användares lokala på följande sätt:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Följande kommandon litar på den offentliga nyckeln för Microsoft PGP:

1. Visa en lista med nycklarna i butiken.
2. Redigera Microsoft-nyckeln.
3. Kontrol lera finger avtrycket med `fpr`
4. Signera nyckeln för att lita på den.

```bash
gpg --list-keys
```

Listade nycklar:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Utdata från interaktiv `gpg` session som signerar Microsoft offentlig nyckel:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Du kan kontrol lera PGP-signaturfil för installations programmet på följande sätt:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Mer information om hur du använder GPG finns i [Sekretess hand boken för GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Scenarier som stöds

Verktygen för ögonblicks bilder kan användas i följande scenarier.

- Enskild SID
- Flera SID
- HSR
- Utskalning
- MDC (endast en enskild klient stöds)
- Enskild container
- SUSE operativ system
- Operativ systemet RHEL
- SKU-TYP I
- SKU-TYP II

Se [scenarier som stöds för Hana-stora instanser](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Stöd mat ris för Snapshot från SAP

Följande matris tillhandahålls som en rikt linje för vilka versioner av SAP HANA som stöds av SAP för säkerhets kopiering av lagrings ögonblicks bilder.

| Databas versioner       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Databas för enskild behållare| √       | √      | -      | -      | -      | -      |
|MDC enskild klient        | -       | -      | √      | √      | √      | √      |
|MDC flera klienter     | -       | -      | -      | -      | -      | √      |
> √ = <small>stöds av SAP för ögonblicks bilder av lagring</small>

## <a name="important-things-to-remember"></a>Viktiga saker att komma ihåg

- Efter installationen av verktyg för ögonblicks bilder övervakar du kontinuerligt tillgängligt lagrings utrymme och tar sedan bort de gamla ögonblicks bilderna regelbundet för att undvika att fylla i lagring.
- Använd alltid de senaste verktygen för ögonblicks bilder.
- Använd samma version av verktyg för ögonblicks bilder över liggande.
- Testa ögonblicks bilds verktygen och bekanta dig med de parametrar som krävs och kommandots utdata innan du använder i produktions systemet.
- När du konfigurerar HANA-användaren för säkerhets kopiering (information nedan i det här dokumentet) måste du ställa in användaren för varje HANA-instans. Skapa ett SAP HANA användar konto för att komma åt HANA-instansen under SYSTEMDB (och inte i SID-databasen) för MDC. I den enskilda behållar miljön kan den konfigureras under klient databasen.
- Kunder måste ange den offentliga SSH-nyckeln för lagrings åtkomst. Den här åtgärden måste utföras en gång per nod och för varje användare under vilken kommandot körs.
- Antalet ögonblicks bilder per volym är begränsat till 250.
- Om du redigerar konfigurations filen manuellt ska du alltid använda en Linux-textredigerare, till exempel "vi" och inte Windows-redigerare som anteckningar. Fil formatet kan vara skadat i Windows-redigeraren.
  - Konfigurera `hdbuserstore` för SAP HANA användare att kommunicera med SAP HANA.
- För DR: ögonblicks bild verktyg måste testas på DR-noden innan DR konfigureras.
- Övervaka disk utrymme regelbundet hanteras automatiserad logg borttagning med alternativet för `--trim`   `azacsnap -c backup` för SAP HANA 2 och senare versioner.
- **Risken för ögonblicks bilder som inte tas** – verktyg för ögonblicks bilder kan bara samverka med noden i det SAP HANA system som anges i konfigurations filen.  Om den här noden blir otillgänglig finns det ingen mekanism för att automatiskt starta kommunikationen med en annan nod.  
  - För en **SAP HANA Scale-Out med vänte läge** är det vanligt att installera och konfigurera verktyg för ögonblicks bilder på huvud-noden. Men om huvudnoden blir otillgänglig tar noden vänte läge över huvud-nodens roll. I det här fallet bör implementerings teamet konfigurera ögonblicks bilds verktygen på båda noderna (huvud och fristående) för att undvika uteblivna ögonblicks bilder. Huvudnoden tar i det normala läget HANA-ögonblicksbilder som initierats av crontab, men efter redundansväxlingen av huvud noden måste dessa ögonblicks bilder köras från en annan nod, till exempel den nya huvudnoden (tidigare vänte läge). För att uppnå detta måste du ha installerat verktyget för vänte läge, lagrings kommunikation aktive rad, hdbuserstore konfigurerade, `azacsnap.json` konfigurerade och crontab-kommandon i förväg vid redundansväxlingen.
  - För ett **SAP HANA HSR ha** -scenario rekommenderar vi att du installerar, konfigurerar och schemalägger verktyg för ögonblicks bilder på båda (primära och sekundära) noder. Om den primära noden blir otillgänglig tas den sekundära noden över med ögonblicks bilder som tas på den sekundära. I normal tillstånd tar den primära noden HANA-ögonblicksbilder som initieras av crontab och den sekundära noden försöker ta ögonblicks bilder, men Miss lyckas eftersom den primära fungerar korrekt.  Men när den primära noden redundansväxlas, kommer dessa ögonblicks bilder att köras från den sekundära noden. För att uppnå det här resultatet måste den sekundära noden ha ögonblicks bild verktyget installerat, lagrings kommunikation aktive rad, `hdbuserstore` konfigurerad azacsnap.jspå konfigurerad och crontab aktive ras i förväg vid redundansväxlingen.

## <a name="guidance-provided-in-this-document"></a>Vägledning i det här dokumentet

Följande rikt linjer är tillgängliga för att illustrera användningen av verktyg för ögonblicks bilder.

### <a name="taking-snapshot-backups"></a>Ta säkerhets kopior av ögonblicks bilder

- [Vilka är kraven för ögonblicks bilden av lagringen](azacsnap-installation.md#prerequisites-for-installation)
  - [Aktivera kommunikation med lagring](azacsnap-installation.md#enable-communication-with-storage)
  - [Aktivera kommunikation med SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Så här tar du ögonblicks bilder manuellt](azacsnap-tips.md#take-snapshots-manually)
- [Konfigurera automatisk säkerhets kopiering av ögonblicks bilder](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Övervaka ögonblicks bilderna](azacsnap-tips.md#monitor-the-snapshots)
- [Hur tar jag bort en ögonblicks bild?](azacsnap-tips.md#delete-a-snapshot)
- [Så här återställer du en ögonblicks bild](azacsnap-tips.md#restore-a-snapshot)
- [Så här återställer du en `boot` ögonblicks bild](azacsnap-tips.md#restore-a-boot-snapshot)
- [Vad är viktiga fakta för att veta om ögonblicks bilderna](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Ögonblicks bilder testas för både enkel SID och flera SID.

### <a name="performing-disaster-recovery"></a>Utföra haveri beredskap

- [Vilka är kraven för DR-konfiguration](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Så här konfigurerar du en katastrof återställning](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Övervaka datareplikering från primär till DR-plats](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Hur gör jag en redundansväxling till en DR-plats?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Nästa steg

- [Installera Azure Application enhetligt ögonblicks bild verktyget](azacsnap-installation.md)