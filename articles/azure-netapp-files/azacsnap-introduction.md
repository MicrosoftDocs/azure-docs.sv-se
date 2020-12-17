---
title: Vad är Azure Application konsekvent ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller en introduktion till Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
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
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632827"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Vad är Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Azure Application enhetligt ögonblicks bild verktyget (AzAcSnap) är ett kommando rads verktyg som gör det möjligt att förenkla data skyddet för databaser från tredje part (SAP HANA) i Linux-miljöer (till exempel SUSE och RHEL).  

## <a name="benefits-of-using-azacsnap"></a>Fördelar med att använda AzAcSnap

AzAcSnap utnyttjar volym ögonblicks bilder och Replikerings funktioner i Azure NetApp Files och Azure stor instans.  Det ger följande fördelar:

- **Programkonsekvent data skydd** AzAcSnap är en centraliserad lösning för att säkerhetskopiera kritiska databasfiler. Den säkerställer databas konsekvens innan en ögonblicks bild av lagrings volymen utförs. Det innebär att lagrings volymens ögonblicks bild kan användas för databas återställning.
- **Databas katalog hantering** När du använder AzAcSnap med en databas som har en inbyggd säkerhets kopierings katalog hålls posterna i katalogen aktuella med lagrings ögonblicks bilder.  Den här funktionen gör att en databas administratör kan se säkerhets kopierings aktiviteten.
- **Ad hoc-volymens skydd** Den här funktionen är användbar för icke-databas volymer som inte behöver program offline innan en lagrings ögonblicks bild tas.  Exempel på detta är SAP HANA logg säkerhets kopierings volymer eller SAPTRANS volymer.
- **Kloning av lagrings volymer** Den här funktionen ger utrymmes effektiv lagrings volym kloner för utveckling och test.
- **Stöd för haveri beredskap** AzAcSnap använder lagrings volym replikering för att tillhandahålla alternativ för att återskapa replikerade programkonsekventa ögonblicks bilder på en fjärran sluten plats.

AzAcSnap är en enda binärfil.  Det behövs inga ytterligare agenter eller plugin-program för att interagera med databasen eller lagringen (Azure NetApp Files via Azure Resource Manager och Azure stor instans via SSH).  AzAcSnap måste installeras på ett system som har anslutning till databasen och lagringen.  Flexibiliteten i installationen och konfigurationen tillåter dock antingen en enkel centraliserad installation eller en fullständigt distribuerad installation med kopior installerade på varje databas installation.

## <a name="architecture-overview"></a>Översikt över arkitekturen

AzAcSnap kan installeras på samma värd som databasen (SAP HANA), eller så kan den installeras på ett centraliserat system.  Men det måste finnas en nätverks anslutning till databas servrarna och lagrings Server delen (Azure Resource Manager för Azure NetApp Files eller SSH för stor Azure-instans).

AzAcSnap är ett lätt program som vanligt vis körs från en extern schemaläggare.  På de flesta Linux-system är den här åtgärden `cron` , vilket är vad dokumentationen kommer att fokusera på.  Men Scheduler kan vara ett alternativt verktyg så länge det kan importera `azacsnap` användarens gränssnitts profil.  Om du importerar användarens miljö inställningar ser du till att fil Sök vägar och behörigheter initieras korrekt.

## <a name="command-synopsis"></a>Kommando Sammanfattning

Det allmänna formatet för kommandona är följande: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Kommando alternativ

Kommando alternativen är följande med kommandona som huvud punkter och associerade under kommandon som indragna punkter:

- **`-h`** tillhandahåller utökad kommando rads hjälp med exempel på AzAcSnap-användning.
- **`-c configure`** Det här kommandot innehåller ett interaktivt Q&ett format gränssnitt för att skapa eller ändra `azacsnap` konfigurations filen (standard = `azacsnap.json` ).
  - **`--configuration new`** kommer att skapa en ny konfigurations fil.
  - **`--configuration edit`** en befintlig konfigurations fil redige ras.
  - referera till [Konfigurera kommando referens](azacsnap-cmd-ref-configure.md).
- **`-c test`** verifierar konfigurations filen och testar anslutningen.
  - **`--test hana`**  testar anslutningen till SAP HANA-instansen.
  - **`--test storage`** testar kommunikationen med det underliggande lagrings gränssnittet genom att skapa en tillfällig lagrings ögonblicks bild på alla konfigurerade `data` volymer och sedan ta bort dem.
  - **`--test all`** utför både och- `hana` `storage` testerna i följd.
  - referera till [test kommando referens](azacsnap-cmd-ref-test.md).
- **`-c backup`** är det primära kommandot för att köra databas lagrings ögonblicks bilder för data (SAP HANA data volymer) & andra (till exempel delade, logg säkerhets kopior eller start) volymer.
  - **`--volume data`** för att ögonblicks bilder av alla volymer i `dataVolume` Stanza i konfigurations filen.
  - **`--volume other`** för att ögonblicks bilder av alla volymer i `otherVolume` Stanza i konfigurations filen.
  - referera till [kommando referens för säkerhets kopiering](azacsnap-cmd-ref-backup.md).
- **`-c details`** innehåller information om ögonblicks bilder eller replikering.
  - **`--details snapshots`** Innehåller en lista med grundläggande information om ögonblicks bilderna för varje volym som har kon figurer ATS.
  - **`--details replication`** Innehåller grundläggande information om replikeringsstatus från produktions platsen till katastrof återställnings platsen.
  - Se [kommando referens för information](azacsnap-cmd-ref-details.md).
- **`-c delete`** Det här kommandot tar bort en ögonblicks bild av lagring eller en uppsättning ögonblicks bilder. Du kan antingen använda det SAP HANA säkerhets kopierings-ID: t som finns i HANA Studio eller namn på lagrings ögonblicks bild. Säkerhets kopierings-ID: t är bara kopplat till `hana` ögonblicks bilderna, som skapas för data och delade volymer. Annars, om namnet på ögonblicks bilden anges, söker det efter alla ögonblicks bilder som matchar det angivna ögonblicks bild namnet.
  - Se [ta bort](azacsnap-cmd-ref-delete.md).
- **`-c restore`** innehåller två metoder för att återställa en ögonblicks bild till en volym genom att antingen skapa en ny volym baserat på ögonblicks bilden eller återställa en volym till ett förhands gransknings tillstånd.
  - **`--restore snaptovol`** Skapar en ny volym baserat på den senaste ögonblicks bilden på mål volymen.
  - **`-c restore --restore revertvolume`** Återställer mål volymen till ett tidigare tillstånd baserat på den senaste ögonblicks bilden.
  - referera till [restore Command-referens](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** Den valfria kommando rads parametern för att ange ett annat fil namn för JSON-konfigurationen.  Detta är särskilt användbart för att skapa en separat konfigurations fil per SID (t `--configfile H80.json` . ex.).

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Application enhetligt ögonblicks bild verktyget](azacsnap-get-started.md)
