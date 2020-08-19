---
title: Anslut syslog-data till Azure Sentinel | Microsoft Docs
description: Anslut alla datorer och installationer som stöder Syslog till Azure Sentinel genom att använda en agent på en Linux-dator mellan-installationen och kontroll enheten. 
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 7670d00a2dd25961a51d18c50c102e0f92b30975
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566156"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Samla in data från Linux-baserade källor med syslog

Du kan strömma händelser från Linux-baserade, syslog-stödjande datorer eller-enheter till Azure Sentinel, med hjälp av Log Analytics agent för Linux (tidigare kallat OMS-agenten). Du kan göra detta för alla datorer som gör att du kan installera Log Analytics agenten direkt på datorn. Datorns interna syslog-daemon samlar in lokala händelser av de angivna typerna och vidarebefordrar dem lokalt till agenten, som kommer att strömma dem till din Log Analytics-arbetsyta.

> [!NOTE]
> - Om din installation stöder **common Event format (CEF) över syslog**samlas en mer fullständig data uppsättning och data parsas i samlingen. Du bör välja det här alternativet och följa anvisningarna i [ansluta din externa lösning med CEF](connect-common-event-format.md).
>
> - Log Analytics stöder insamling av meddelanden som skickats av **rsyslog** **-eller syslog-ng-** daemon, där rsyslog är standardvärdet. Standard syslog-daemonen på version 5 av Red Hat Enterprise Linux (RHEL), CentOS och Oracle Linux version (**sysklog**) stöds inte för händelse insamling i syslog. Om du vill samla in syslog-data från den här versionen av dessa distributioner ska rsyslog daemon installeras och konfigureras för att ersätta sysklog.

## <a name="how-it-works"></a>Så här fungerar det

**Syslog** är ett händelse loggnings protokoll som är gemensamt för Linux. När **Log Analytics agent för Linux** är installerad på din virtuella dator eller enhet konfigurerar installations rutinen lokalt syslog-daemon för att vidarebefordra meddelanden till agenten på TCP-port 25224. Agenten skickar sedan meddelandet till din Log Analytics-arbetsyta via HTTPS, där det parsas till en händelse logg post i syslog-tabellen i **Azure Sentinel >-loggar**.

Mer information finns i [syslog-datakällor i Azure Monitor](../azure-monitor/platform/data-sources-syslog.md).

## <a name="configure-syslog-collection"></a>Konfigurera syslog-samling

### <a name="configure-your-linux-machine-or-appliance"></a>Konfigurera din Linux-dator eller-apparat

1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan **syslog** -anslutningsprogrammet.

1. På bladet **syslog** väljer du **Öppna kopplings sida**.

1. Installera Linux-agenten. Under **Välj var du vill installera agenten:**
    
    **För en virtuell Azure Linux-dator:**
      
    1. Välj **Installera agent på virtuell Azure Linux-dator**.
    
    1. Klicka på länken **Ladda ned & installera agent för virtuella Azure Linux-datorer >** . 
    
    1. På bladet **virtuella datorer** klickar du på en virtuell dator för att installera agenten på och klickar sedan på **Anslut**. Upprepa det här steget för varje virtuell dator som du vill ansluta.
    
    **För alla andra Linux-datorer:**

    1. Välj **Installera agent på en dator som inte är en Azure Linux-dator**

    1. Klicka på länken **Ladda ned & installera agent för datorer som inte är Azure Linux-datorer >** . 

    1. På bladet **hantering av agenter** klickar du på fliken **Linux-servrar** och kopierar sedan kommandot för att **Ladda ned och integrera agent för Linux** och köra det på Linux-datorn. 
    
   > [!NOTE]
   > Se till att konfigurera säkerhets inställningar för de här datorerna enligt din organisations säkerhets princip. Du kan till exempel konfigurera nätverks inställningarna så att de överensstämmer med organisationens nätverks säkerhets princip och ändra portarna och protokollen i daemonen så att de överensstämmer med säkerhets kraven.

### <a name="configure-the-log-analytics-agent"></a>Konfigurera Log Analytics agent

1. Längst ned på bladet syslog-koppling klickar du på länken **Öppna konfiguration av avancerade inställningar >på arbets ytan ** .

1. På bladet **Avancerade inställningar** väljer du **data**  >  **syslog**. Lägg sedan till de anläggningar som ska samlas in av kopplingen.
    
    - Lägg till de anläggningar som syslog-apparaten innehåller i sina logg rubriker. 
    
    - Om du vill använda avvikande identifiering av SSH-inloggning med de data som du samlar in lägger du till **auth** och **authpriv**. Mer information finns i [följande avsnitt](#configure-the-syslog-connector-for-anomalous-ssh-login-detection) .

1. När du har lagt till alla funktioner som du vill övervaka och justerat eventuella allvarlighets grad alternativ för var och en, markerar du kryss rutan **Använd konfigurationen nedan för mina datorer**.

1. Välj **Spara**. 

1. Kontrol lera att du skickar de anläggningar som du har angett på din virtuella dator eller enhet.

1. Om du vill fråga syslog-loggdata i **loggar**skriver `Syslog` du i frågefönstret.

1. Du kan använda frågeparametrar som beskrivs i [använda funktioner i Azure Monitor logg frågor](../azure-monitor/log-query/functions.md) för att parsa syslog-meddelanden. Du kan sedan spara frågan som en ny Log Analytics-funktion och använda den som en ny datatyp.

> [!NOTE]
> **Använda samma dator för att vidarebefordra både vanliga syslog- *och* CEF-meddelanden**
>
>
> Du kan använda din befintliga [CEF log forwarder-dator](connect-cef-agent.md) för att samla in och vidarebefordra loggar från även vanliga syslog-källor. Du måste dock utföra följande steg för att undvika att skicka händelser i båda formaten till Azure Sentinel, eftersom det leder till duplicering av händelser.
>
>    Har redan konfigurerat [data insamling från dina CEF-källor](connect-common-event-format.md)och har konfigurerat Log Analytics-agenten enligt ovan:
>
> 1. På varje dator som skickar loggar i CEF-format måste du redigera syslog-konfigurationsfilen för att ta bort de anläggningar som används för att skicka CEF-meddelanden. På så sätt kommer inte de funktioner som skickas i CEF också att skickas i syslog. Mer information om hur du gör detta finns i [Konfigurera syslog på Linux-agenten](../azure-monitor/platform/data-sources-syslog.md#configure-syslog-on-linux-agent) .
>
> 1. Du måste köra följande kommando på de datorerna för att inaktivera synkroniseringen av agenten med syslog-konfigurationen i Azure Sentinel. Detta säkerställer att konfigurations ändringen du gjorde i föregående steg inte blir överskriven.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`


### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>Konfigurera syslog-anslutningen för identifiering av avvikande SSH-inloggning

> [!IMPORTANT]
> Identifiering av avvikande SSH-inloggning är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kan använda Machine Learning (ML) till syslog-data för att identifiera en SSH-inloggning (avvikande säker Shell). Scenarierna är:

- Omöjlig resa – när två lyckade inloggnings händelser inträffar från två platser som är omöjliga att uppnå inom ramen för de två inloggnings händelserna.
- Oväntad plats – platsen där en lyckad inloggnings händelse inträffade är misstänkt. Till exempel visas inte platsen nyligen.
 
Den här identifieringen kräver en speciell konfiguration av syslog-datakopplingen: 

1. I steg 5 i föregående procedur ser du till att både **auth** -och **authpriv** är markerade som anläggningar att övervaka. Behåll standardinställningarna för allvarlighets grad alternativen så att alla är markerade. Exempel:
    
    > [!div class="mx-imgBorder"]
    > ![Anläggningar som krävs för identifiering av avvikande SSH-inloggning](./media/connect-syslog/facilities-ssh-detection.png)

2. Tillåt att syslog-informationen samlas in tillräckligt med tid. Gå sedan till **Azure Sentinel-logs**och kopiera och klistra in följande fråga:
    
    ```console
    Syslog |  where Facility in ("authpriv","auth")| extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)| where isnotempty(c) | count 
    ```
    
    Ändra **tidsintervallet** vid behov och välj **Kör**.
    
    Om det resulterande antalet är noll bekräftar du konfigurationen av anslutningen och att de övervakade datorerna har lyckats med inloggnings aktiviteten för den tids period som du har angett för din fråga.
    
    Om det resulterande antalet är större än noll är dina syslog-data lämpliga för avvikande identifiering av SSH-inloggning. Du aktiverar den här identifieringen från **Analytics**  >   **rule templates**  >  **(för hands version) avvikande identifiering av SSH-inloggning**.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter syslog-lokala enheter till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.

