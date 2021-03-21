---
title: Montera klienter på Microsoft Azure FXT Edge-kluster
description: Hur NFS-klientdatorer kan montera Azure FXT Edge-filer hybrid Storage cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: fa1f9da2c60aaf4c552916d16c266e984bf08892
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92340520"
---
# <a name="tutorial-mount-the-cluster"></a>Självstudie: montera klustret

I den här självstudien lär du dig hur du monterar NFS-klienter till Azure FXT Edge-kluster. Klienter monterar de virtuella namn rymds Sök vägarna som du tilldelade när du La till backend-lagring.

Den här självstudien lär sig följande:

> [!div class="checklist"]
>
> * Strategier för belastnings Utjämnings klienter inom intervallet för IP-adresser som riktas mot klienter
> * Så här skapar du en monterings Sök väg från en klient som riktad IP-adress och namn områdes Knut
> * Vilka argument som ska användas i ett monterings kommando

Den här självstudien tar cirka 45 minuter att slutföra.

## <a name="steps-to-mount-the-cluster"></a>Steg för att montera klustret

Följ de här stegen för att ansluta klient datorer till ditt Azure FXT Edge-kluster.

1. Bestäm hur du ska belastningsutjämna klient trafik mellan klusternoderna. Läs [utjämning klient belastning](#balance-client-load)nedan för mer information.
1. Identifiera klustrets IP-adress och Knut punkts Sök väg för montering.
1. Ta reda på den klient-riktade sökvägen för monteringen.
1. Utfärda [monterings kommandot](#use-recommended-mount-command-options)med lämpliga argument.

## <a name="balance-client-load"></a>Utjämna klient belastning

För att balansera klient begär Anden mellan alla noder i klustret, bör du montera klienter till alla IP-adresser som klienten riktas mot. Det finns flera sätt att automatisera den här uppgiften.

Om du vill veta mer om resursallokering med DNS-belastning för klustret läser du [Konfigurera DNS för Azure FXT Edge-kluster](fxt-configure-network.md#configure-dns-for-load-balancing). Om du vill använda den här metoden måste du underhålla en DNS-server, som inte förklaras i dessa artiklar.

En enklare metod för små installationer är att använda ett skript för att tilldela IP-adresser inom intervallet vid klientens monterings tid.

Andra metoder för belastnings utjämning kan vara lämpliga för stora eller komplicerade system. Kontakta din Microsoft-representant eller öppna en [support förfrågan](fxt-support-ticket.md) om du behöver hjälp. (Azure Load Balancer stöds för närvarande *inte* med Azure FXT Edge-filer.)

## <a name="create-the-mount-command"></a>Skapa monterings kommandot

Från klienten ``mount`` mappar kommandot den virtuella servern (vserver) på Azure FXT Edge-klustret till en sökväg i det lokala fil systemet.

Formatet är ``mount <FXT cluster path> <local path> {options}``

Monterings kommandot innehåller tre element:

* kluster Sök väg – en kombination av sökväg för IP-adress och namespace-förgrening som beskrivs nedan
* lokal sökväg – sökvägen till klienten
* Monterings kommando alternativ – (anges i [Använd rekommenderade monterings kommando alternativ](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Skapa kluster Sök vägen

Kluster Sök vägen är en kombination av vserver *-IP-adressen* plus sökvägen till en *Knut punkt för namn områden*. Namn områdes knuten är en virtuell sökväg som du definierade när du [lade till lagrings systemet](fxt-add-storage.md#create-a-junction).

Om du till exempel använde ``/fxt/files`` som namn områdes Sök väg monterar klienterna *ip_address*:/FXT/Files till sin lokala monterings punkt.

![Dialog rutan Lägg till ny Knut punkt med/avere/Files i fältet namn områdes Sök väg](media/fxt-mount/fxt-junction-example.png)

IP-adressen är en av klientens IP-adresser som har definierats för vserver. Du hittar intervallet för klientbaserade IP-adresser på två platser i kluster kontroll panelen:

* **VServers** -tabell (fliken instrument panel) –

  ![Fliken instrument panel på kontroll panelen med fliken VServer markerad i data tabellen under grafen och avsnittet IP-adress inringat](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* Sidan **klient nätverks** inställningar-

  ![Inställningar > VServer > klient med en cirkel runt avsnittet adress intervall i tabellen för en viss vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Kombinera IP-adressen och namn områdets sökväg för att skapa kluster Sök vägen för monterings kommandot.

Exempel på klient monterings kommando: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Skapa den lokala sökvägen

Den lokala sökvägen för monterings kommandot är upp till dig. Du kan ange valfri Sök vägs struktur som du vill använda som en del av det virtuella namn området. Skapa ett namn område och en lokal sökväg som är lämplig för ditt klient arbets flöde.

Mer information om det klient-motstående namn området finns i Översikt över kluster konfigurations guidens [namn område](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Förutom Sök vägarna inkluderar du [kommando alternativen för montering](#use-recommended-mount-command-options) som beskrivs nedan när du monterar varje klient.

### <a name="use-recommended-mount-command-options"></a>Använd rekommenderade monterings kommando alternativ

För att säkerställa en sömlös klient montering måste du överföra dessa inställningar och argument i monterings kommandot:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Nödvändiga inställningar | Beskrivning |
--- | ---
``hard`` | Mjuka monteringar till Azure FXT Edge-klustret är kopplade till program fel och eventuell data förlust.
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=n`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

| Önskade inställningar  | Beskrivning |
--- | ---
``nointr``            | Om klienterna använder äldre OS-kernel (före 2008) som har stöd för det här alternativet använder du det. Alternativet "intr" är standardvärdet.

## <a name="next-steps"></a>Nästa steg

När du har monterat klienter kan du testa arbets flödet och komma igång med ditt kluster.

Om du behöver flytta data till en ny Cloud core-filer, kan du utnyttja cache-strukturen genom att använda Parallel data inmatning. Vissa strategier beskrivs i [Flytta data till ett vFXT-kluster](../avere-vfxt/avere-vfxt-data-ingest.md). (Aver vFXT for Azure är en molnbaserad produkt som använder teknik för cachelagring som är mycket likt Azure FXT Edge-filer.)

Läs [övervaka maskin varu status för Azure FXT Edge](fxt-monitor.md) -filer om du behöver felsöka eventuella maskin varu problem.