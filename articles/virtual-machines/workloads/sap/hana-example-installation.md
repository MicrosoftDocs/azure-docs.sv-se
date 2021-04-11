---
title: Så här installerar du HANA på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Installera HANA på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: hermannd
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 59ea41254c16f32f19a67e7811177a4667a85e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666696"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Installera HANA på SAP HANA på Azure (stora instanser)

Om du vill installera HANA på SAP HANA på Azure (stora instanser) måste du först göra följande:
- Du förser Microsoft med alla data som ska distribueras åt dig på en SAP HANA stor instans.
- Du får SAP HANA stor instans från Microsoft.
- Du skapar ett virtuellt Azure-nätverk som är anslutet till ditt lokala nätverk.
- Du ansluter ExpressRoute-kretsen för HANA-stora instanser till samma virtuella Azure-nätverk.
- Du installerar en virtuell Azure-dator som du använder som en hopp ruta för HANA-stora instanser.
- Du kan se till att du kan ansluta från rutan till en stor instans enhet för HANA och tvärtom.
- Du kontrollerar om alla nödvändiga paket och korrigeringar är installerade.
- Du läser SAP-anteckningar och dokumentation om HANA-installation på det operativ system som du använder. Se till att HANA-versionen av Choice stöds i operativ system versionen.

I nästa avsnitt visas ett exempel på hur du hämtar HANA-installations paketen till den virtuella datorn i hopp rutan. I det här fallet är operativ systemet Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Ladda ned installations bitar för SAP HANA
De stora HANA-instans enheterna är inte direkt anslutna till Internet. Du kan inte hämta installations paket direkt från SAP till den virtuella HANA-fjärrinstansen. I stället laddar du ned paketen till den virtuella hopp Box-datorn.

Du behöver en SAP S-användare eller en annan användare, vilket gör att du kan komma åt SAP Marketplace.

1. Logga in och gå till [SAP Service Marketplace](https://support.sap.com/en/index.html). Välj **Hämta program varu**  >  **installationer och uppgradera**  >  **enligt alfabetiskt index**. Välj **under H – SAP HANA Platform Edition**  >  **SAP HANA Platform Edition 2,0**-  >  **installation**. Hämta filerna som visas i följande skärm bild.

   ![Skärm bild av de filer som ska laddas ned](./media/hana-installation/image16_download_hana.PNG)

2. I det här exemplet har vi hämtat SAP HANA 2,0-installations paket. I den virtuella Azure-bygel-lådan, expanderar du de självextraherande arkiven till katalogen som visas nedan.

   ![Skärm bild av självextraherande arkivering](./media/hana-installation/image17_extract_hana.PNG)

3. När arkiven extraheras kopierar du katalogen som skapats av extraheringen (i det här fallet 51052030). Kopiera katalogen från volymen HANA stor instance Unit/Hana/Shared till en katalog som du har skapat.

   > [!Important]
   > Kopiera inte installations paketen till roten eller start-LUN eftersom utrymmet är begränsat och måste användas av andra processer.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Installera SAP HANA på den stora instans enheten HANA
Logga in som användar rot för att installera SAP HANA. Endast roten har tillräcklig behörighet för att installera SAP HANA. Ange behörigheter för den katalog som du kopierade till/Hana/Shared.

```
chmod –R 744 <Installation bits folder>
```

Om du vill installera SAP HANA med hjälp av installations programmet för grafiska användar gränssnitt måste gtk2-paketet installeras på HANA-stora instanser. Du kan kontrol lera om den är installerad genom att köra följande kommando:

```
rpm –qa | grep gtk2
```

(I senare steg visar vi SAP HANA-installationen med det grafiska användar gränssnittet.)

Gå till installations katalogen och navigera till under katalogen HDB_LCM_LINUX_X86_64. 

Från den katalogen startar du:

```
./hdblcmgui 
```
I det här läget går du igenom en sekvens med skärmar där du anger data för installationen. I det här exemplet installerar vi SAP HANA Database-servern och SAP HANA klient komponenterna. Vi väljer därför **SAP HANA Database**.

![Skärm bild av skärmen SAP HANA livs cykel hantering med SAP HANA databas vald](./media/hana-installation/image18_hana_selection.PNG)

På nästa skärm väljer du **Installera nytt system**.

![Skärm bild av skärmen SAP HANA livs cykel hantering med Installera nytt system valt](./media/hana-installation/image19_select_new.PNG)

Välj sedan bland flera ytterligare komponenter som du kan installera.

![Skärm bild av skärmen SAP HANA livs cykel hantering med lista över ytterligare komponenter](./media/hana-installation/image20_select_components.PNG)

Här väljer vi den SAP HANA klienten och SAP HANA Studio. Vi installerar även en upphöjd instans. Välj sedan **ett system med en värd**. 

![Skärm bild av skärmen SAP HANA livs cykel hantering med ett enda värd system valt](./media/hana-installation/image21_single_host.PNG)

Ange sedan vissa data.

![Skärm bild av skärmen SAP HANA livs cykel hantering med fält för system egenskaper för att definiera](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Som HANA-system-ID (SID) måste du ange samma SID som du tillhandahöll Microsoft när du beställde distributionen av HANA-stor instans. Att välja ett annat SID innebär att installationen Miss känner sig på grund av problem med åtkomst behörighet på de olika volymerna.

För installations Sök vägen använder du/Hana/Shared-katalogen. I nästa steg anger du platserna för HANA-datafilerna och HANA-loggfilerna.


![Skärm bild av skärmen SAP HANA livs cykel hantering med fält för data och logg områden](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> SID som du angav när du definierade system egenskaper (två skärmar sedan) ska matcha SID för monterings punkter. Om det finns ett matchnings fel går du tillbaka och justerar SID-värdet till det värde som du har på monterings punkterna.

I nästa steg granskar du värd namnet och korrigerar det slutligen. 

![Skärm bild av skärmen SAP HANA livs cykel hantering med värdnamn](./media/hana-installation/image24_review_host_name.PNG)

I nästa steg måste du också hämta data som du gav till Microsoft när du beställde distributionen HANA stor instans. 

![Skärm bild av SAP HANA livs cykel hantering med system administratörs fält för att definiera](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Ange samma **användar-ID** och **ID för användar gruppen** som du angav för Microsoft, när du beställer enhets distributionen. Annars Miss lyckas installationen av SAP HANA på den HANA-stora instans enheten.

De två följande skärmarna visas inte här. De gör det möjligt för dig att ange lösen ordet för systemanvändaren i SAP HANA-databasen och lösen ordet för sapadm-användaren. Den senare används för den SAP-värd agent som installeras som en del av SAP HANA databas instansen.

När du har definierat lösen ordet visas en bekräftelse skärm. kontrol lera alla data i listan och fortsätt med installationen. Du når en förlopps skärm som dokumenterar installations förloppet, t. ex. följande:

![Skärm bild av skärmen SAP HANA livs cykel hantering med installations förlopps indikatorer](./media/hana-installation/image27_show_progress.PNG)

När installationen är klar bör du se en skärm som är sådan:

![Skärm bild av skärmen SAP HANA livs cykel hantering, som visar att installationen har slutförts](./media/hana-installation/image28_install_finished.PNG)

SAP HANA-instansen bör nu vara igång och vara redo för användning. Du bör kunna ansluta till den från SAP HANA Studio. Se också till att du söker efter och tillämpar de senaste uppdateringarna.


## <a name="next-steps"></a>Nästa steg

- [SAP HANA – stora instanser hög tillgänglighet och haveri beredskap på Azure](hana-overview-high-availability-disaster-recovery.md)

