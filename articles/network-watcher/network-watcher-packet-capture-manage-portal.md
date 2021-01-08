---
title: Hantera paket fångster – Azure Portal
titleSuffix: Azure Network Watcher
description: Lär dig hur du hanterar paket insamlings funktionen i Network Watcher att använda Azure Portal.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: d4112bd1d632ec21e2d8cb565027277eeb85452d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018267"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>Hantera paket fångster med Azure Network Watcher med hjälp av portalen

Med Network Watcher paket insamling kan du skapa avbildnings sessioner för att spåra trafik till och från en virtuell dator. Filter tillhandahålls för insamlingsbufferten för att se till att du bara fångar den trafik som du vill använda. Med paket fångst kan du diagnostisera nätverks avvikelser, både återaktivt och proaktivt. Andra användnings områden är insamling av nätverks statistik, insamling av information om nätverks intrång, fel sökning av klient server kommunikation och mycket mer. Att kunna fjärrutlös paket fångster, underlättar belastningen på att köra en paket registrering manuellt på en önskad virtuell dator, vilket sparar värdefull tid.

I den här artikeln lär du dig att starta, stoppa, ladda ned och ta bort en paket fångst. 

## <a name="before-you-begin"></a>Innan du börjar

Paket fångst kräver följande utgående TCP-anslutning:
- till det valda lagrings kontot via port 443
- till 169.254.169.254 över port 80
- till 168.63.129.16 över Port 8037

> [!NOTE]
> De portar som anges i de två sistnämnda fallen ovan är gemensamma för alla Network Watcher funktioner som involverar Network Watcher-tillägget och kan ibland ändras.


Om en nätverks säkerhets grupp är kopplad till nätverks gränssnittet eller undernät som nätverks gränssnittet finns i, kontrollerar du att det finns regler som tillåter föregående portar. På samma sätt kan tillägg av användardefinierade trafik vägar till nätverket förhindra anslutning till ovanstående IP-adresser och portar. Se till att de kan kontaktas. 

## <a name="start-a-packet-capture"></a>Starta en paket fångst

1. I webbläsaren navigerar du till [Azure Portal](https://portal.azure.com) och väljer **alla tjänster** och väljer sedan **Network Watcher** i **avsnittet nätverk**.
2. Välj **paket avbildning** under **diagnostikverktyg för nätverk**. Alla befintliga paket fångster visas, oavsett deras status.
3. Välj **Lägg till** för att skapa en paket fångst. Du kan välja värden för följande egenskaper:
   - **Prenumeration**: den prenumeration som den virtuella datorn som du vill skapa paket fångsten för finns i.
   - **Resurs grupp**: den virtuella datorns resurs grupp.
   - **Virtuell mål dator**: den virtuella dator som du vill skapa paket fångst för.
   - **Namn på paket fångst**: ett namn för paket fångsten.
   - **Lagrings konto eller fil**: Välj **lagrings konto**, **fil** eller både och. Om du väljer **fil** skrivs avbildningen till en sökväg i den virtuella datorn.
   - **Lokal fil Sök väg**: den lokala sökvägen på den virtuella datorn där paket fångsten ska sparas (endast giltig när *filen* har valts). Sökvägen måste vara en giltig sökväg. Om du använder en virtuell Linux-dator måste sökvägen börja med */var/Captures*.
   - **Lagrings konton**: Välj ett befintligt lagrings konto om du har valt *lagrings konto*. Det här alternativet är bara tillgängligt om du har valt **lagring**.
   
     > [!NOTE]
     > Premium Storage-konton stöds för närvarande inte för lagring av paket fångster.

   - **Högsta antal byte per paket**: antalet byte från varje paket som fångas. Om inget anges registreras alla byte.
   - **Maximalt antal byte per session**: det totala antalet byte som har fångats. När värdet har nåtts stoppas paket fångsten.
   - **Tids gräns (sekunder)**: tids gränsen innan paket fångsten stoppas. Standardvärdet är 18 000 sekunder.
   - Filtrering (valfritt). Välj **+ Lägg till filter**
     - **Protokoll**: protokollet som ska filtreras för infångade paket. De tillgängliga värdena är TCP, UDP och any.
     - **Lokal IP-adress**: filtrerar paket fångsten för paket där den lokala IP-adressen matchar det här värdet.
     - **Lokal port**: filtrerar paket fångsten för paket där den lokala porten matchar det här värdet.
     - **Fjärr-IP-adress**: filtrerar paket fångsten för paket där fjärr-IP-adressen matchar det här värdet.
     - **Fjärrport**: filtrerar paket fångsten för paket där fjärrporten matchar det här värdet.
    
     > [!NOTE]
     > Port-och IP-adress värden kan vara ett enda värde, ett intervall med värden eller ett intervall, till exempel 80-1024, för port. Du kan definiera så många filter som du behöver.

4. Välj **OK**.

När den tids gräns som har angetts för paket fångsten har gått ut stoppas paket fångsten och kan granskas. Du kan också stoppa en insamlings session manuellt.

> [!NOTE]
> Portalen automatiskt:
>  * Skapar en nätverks övervakare i samma region som den region som den valda virtuella datorn finns i, om regionen inte redan har en nätverks övervakare.
>  * Lägger till tillägget *AzureNetworkWatcherExtension* [Linux](../virtual-machines/extensions/network-watcher-linux.md) eller [Windows](../virtual-machines/extensions/network-watcher-windows.md) virtuell dator i den virtuella datorn om det inte redan är installerat.

## <a name="delete-a-packet-capture"></a>Ta bort en paket avbildning

1. I vyn paket fångst väljer du **...** på höger sida av paket fångsten eller högerklickar på en befintlig paket fångst och väljer **ta bort**.
2. Du uppmanas att bekräfta att du vill ta bort paket avbildningen. Välj **Ja**.

> [!NOTE]
> När du tar bort en paket insamling tas inte insamlings filen bort i lagrings kontot eller på den virtuella datorn.

## <a name="stop-a-packet-capture"></a>Stoppa en paket fångst

I vyn paket fångst väljer du **...** på höger sida av paket fångsten eller högerklickar på en befintlig paket fångst och väljer **stoppa**.

## <a name="download-a-packet-capture"></a>Ladda ned en paket avbildning

När din paket insamlings session har slutförts överförs infångstfilen till Blob Storage eller till en lokal fil på den virtuella datorn. Lagrings platsen för paket fångsten definieras när du skapar paket avbildningen. Ett användbart verktyg för att komma åt insamlingsfiler som sparas i ett lagrings konto är Microsoft Azure Storage Explorer, som du kan [Ladda ned](https://storageexplorer.com/).

Om ett lagrings konto anges sparas paket insamlings filer till ett lagrings konto på följande plats:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

Om du valde **filen** när du skapade avbildningen kan du Visa eller hämta filen från den sökväg som du konfigurerade på den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

- Information om hur du automatiserar paket fångster med aviseringar för virtuella datorer finns i [skapa en avisering Utlös ande paket avbildning](network-watcher-alert-triggered-packet-capture.md).
- Information om hur du tar reda på om en speciell trafik tillåts i eller från en virtuell dator finns i [diagnostisera ett problem med trafik filter för virtuella dator nätverk](diagnose-vm-network-traffic-filtering-problem.md).