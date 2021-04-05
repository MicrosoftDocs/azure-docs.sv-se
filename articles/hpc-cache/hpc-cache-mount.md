---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC cache-tjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 7f1d8d34d6351fc344fdb101ac8e9a96678df9d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91651436"
---
# <a name="mount-the-azure-hpc-cache"></a>Montera Azure HPC Cache

När cachen har skapats kan NFS-klienter komma åt den med ett enkelt `mount` kommando. Kommandot ansluter en speciell lagrings mål Sök väg på Azure HPC-cachen till en lokal katalog på klient datorn.

Monterings kommandot består av följande element:

* Ett av cachens monterings adresser (visas på sidan cache-översikt)
* En sökväg för virtuell namn område som du anger för lagrings målet (visas på sidan cache-namnområde)
* Den lokala sökväg som ska användas på klienten
* Kommando parametrar som optimerar framgången för den här typen av NFS-montering

Sidan **monterings instruktioner** för din cache samlar in informationen och de rekommenderade alternativen åt dig, och skapar ett prototyp monterings kommando som du kan kopiera. Läs [Använd verktyget Mount-instruktioner](#use-the-mount-instructions-utility) för mer information.

## <a name="prepare-clients"></a>Förbereda klienter

Se till att dina klienter kan montera Azure HPC-cachen genom att följa rikt linjerna i det här avsnittet.

### <a name="provide-network-access"></a>Ge nätverks åtkomst

Klient datorerna måste ha nätverks åtkomst till cachens virtuella nätverk och privata undernät.

Du kan till exempel skapa virtuella klient datorer i samma virtuella nätverk eller använda en slut punkt, gateway eller annan lösning i det virtuella nätverket för åtkomst från utanför. (Kom ihåg att inget annat än själva cachen ska finnas i cachens undernät.)

### <a name="install-utilities"></a>Installera verktyg

Installera rätt Linux-verktyg för att stödja NFS-monterings kommandot:

* För Red Hat Enterprise Linux eller SuSE: `sudo yum install -y nfs-utils`
* För Ubuntu eller Debian: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Skapa en lokal sökväg

Skapa en lokal katalog Sök väg på varje klient för att ansluta till cacheminnet. Skapa en sökväg för varje namn områdes Sök väg som du vill montera.

Exempel: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Sidan [monterings instruktioner](#use-the-mount-instructions-utility) i Azure Portal innehåller ett prototyp kommando som du kan kopiera.

När du ansluter klient datorn till cacheminnet associeras sökvägen med en sökväg till en virtuell namn rymd som representerar en export av lagrings målet. Skapa kataloger för var och en av de sökvägar för virtuella namn områden som klienten ska använda.

## <a name="use-the-mount-instructions-utility"></a>Använd verktyget monterings instruktioner

Du kan använda sidan **monterings instruktioner** i Azure Portal för att skapa ett kopierings Bart monterings kommando. Öppna sidan från avsnittet **Konfigurera** i vyn cache i portalen.

Innan du använder kommandot på en klient kontrollerar du att klienten uppfyller kraven och att den program vara som krävs för att använda NFS- `mount` kommandot enligt beskrivningen ovan i [förbereda klienter](#prepare-clients).

![skärm bild av en Azure HPC cache-instans i portalen med sidan Konfigurera > monterings instruktioner inläst](media/mount-instructions.png)

Följ den här proceduren för att skapa monterings kommandot.

1. Anpassa fältet för **klient Sök vägen** . Det här fältet innehåller ett exempel kommando som du kan använda för att skapa en lokal sökväg på klienten. Klienten får åtkomst till innehållet från Azure HPC cache lokalt i den här katalogen.

   Klicka på fältet och redigera kommandot så att det innehåller det katalog namn som du vill använda. Namnet visas i slutet av strängen efter `sudo mkdir -p`

   ![skärm bild av fältet för klient Sök vägen med markören placerad i slutet](media/mount-edit-client.png)

   När du har redigerat det här fältet uppdateras monterings kommandot längst ned på sidan med den nya klient Sök vägen.

1. Välj **cache-nätadressen** i listan. I den här menyn visas alla [klient monterings punkter](#find-mount-command-components)för cacheminnet.

   Utjämna klient belastningen över alla tillgängliga monterings adresser för bättre prestanda för cachelagring.

   ![skärm bild av fältet cache-Mount-adress med väljaren som visar tre IP-adresser att välja mellan](media/mount-select-ip.png)

1. Välj den **virtuella namn rymds-sökväg** som ska användas för klienten. Dessa sökvägar länkar till exporter i Server dels lagrings systemet.

   ![Skärm bild som visar fältet "sökväg till virtuell namn område" med Select Select Open.](media/mount-select-target.png)

   Du kan visa och ändra sökvägar för virtuella namn områden på sidan **namn områdes** Portal. Läs [Konfigurera den sammanställda namn rymden](add-namespace-paths.md) för att se hur.

   Om du vill veta mer om Azure HPC Caches funktioner för aggregerad namnrymd kan du läsa [planera den sammanställda namn rymden](hpc-cache-namespace.md).

1. **Monterings kommando** fältet i steg tre fylls i automatiskt med ett anpassat monterings kommando som använder monterings adress, sökväg för virtuellt namn område och klient Sök väg som du angett i föregående fält.

   Klicka på Kopiera symbol till höger om fältet för att automatiskt kopiera det till Urklipp.

   ![skärm bild av kommando fältet prototyp montering, som visar hov rings text för knappen Kopiera till Urklipp](media/mount-command-copy.png)

1. Använd det kopierade monterings kommandot på klient datorn för att ansluta det till Azure HPC-cachen. Du kan skicka kommandot direkt från klientens kommando rad eller inkludera kommandot Mount i ett skript eller en mall för klient installationen.

## <a name="understand-mount-command-syntax"></a>Förstå kommandoradssyntax för montering

Monterings kommandot har följande format:

> sudo montering {*alternativ*} *cache_mount_address*:/*namespace_path* *local_path*

Exempel:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

När det här kommandot har slutförts visas innehållet i lagrings exporten i ``hpccache`` katalogen på klienten.

### <a name="mount-command-options"></a>Monterings kommando alternativ

För en robust klient montering ska du överföra dessa inställningar och argument i monterings kommandot:

> montering-o hård, proto = TCP, mountproto = TCP, retry = 30 $ {CACHE_IP_ADDRESS}:/$ {NAMESPACE_PATH} $ {LOCAL_FILESYSTEM_MOUNT_POINT}

| Rekommenderade monterings kommando inställningar | Beskrivning |
--- | ---
``hard`` | Mjuka monteringar till Azure HPC cache är kopplade till program haverier och eventuell data förlust.
``proto=tcp`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=tcp`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=<value>`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

### <a name="find-mount-command-components"></a>Hitta monterings kommando komponenter

Om du vill skapa ett monterings kommando utan att använda sidan **monterings instruktioner** kan du hitta monterings adresserna på sidan cache- **Översikt** och Sök vägarna för virtuella namn områden på **namn områdes** sidan.

![skärm bild av översikts sidan för Azure HPC-instansen med en markerings ruta runt monterings adress listan längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Cachens cache-adresser motsvarar nätverks gränssnitten i cachens undernät. I en resurs grupp visas dessa nätverkskort med namn som slutar med `-cluster-nic-` och ett tal. Ändra inte eller ta bort dessa gränssnitt, annars blir cachen otillgänglig.

De virtuella namn Rymdernas sökvägar visas på sidan **namn rymds** inställningar.

![skärm bild av sidan med Portal inställningar >-namnområde med en markerings ruta runt den första kolumnen i tabellen: "sökväg till namnrymd"](media/view-namespace-paths.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagrings mål läser du [fylla i ny Azure Blob Storage](hpc-cache-ingest.md).
