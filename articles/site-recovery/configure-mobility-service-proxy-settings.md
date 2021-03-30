---
title: Konfigurera proxyinställningar för mobilitets tjänsten för haveri beredskap i Azure till Azure | Microsoft Docs
description: Innehåller information om hur du konfigurerar mobilitets tjänsten när kunder använder en proxy i sin käll miljö.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133225"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Konfigurera proxyinställningar för mobilitets tjänsten för haveri beredskap i Azure till Azure

Den här artikeln innehåller vägledning om hur du anpassar nätverkskonfigurationer på den virtuella Azure-datorn (VM) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

Syftet med det här dokumentet är att tillhandahålla steg för att konfigurera proxyinställningar för Azure Site Recovery Mobility Service i scenariot för haveri beredskap i Azure till Azure. 

Proxyservrar är nätverks-gatewayer som tillåter/nekar nätverks anslutningar till slut punkter. Vanligt vis är en proxyserver en dator utanför klient datorn som försöker få åtkomst till nätverks slut punkter. En undantags lista gör att klienten kan upprätta anslutningar direkt till slut punkterna utan att gå via proxyn. Du kan välja att ange ett användar namn och lösen ord för en proxy av nätverks administratörer så att endast autentiserade klienter kan använda proxy. 

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveri beredskap för [det här scenariot](azure-to-azure-architecture.md).
Förstå [nätverks vägledningen](azure-to-azure-about-networking.md) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).
Se till att proxyservern är korrekt konfigurerad baserat på organisationens behov.

## <a name="configure-the-mobility-service"></a>Konfigurera mobilitets tjänsten

Mobilitets tjänsten stöder endast oautentiserade proxyservrar. Det finns två sätt att ange information om proxyn för kommunikation med Site Recovery slut punkter. 

### <a name="method-1-auto-detection"></a>Metod 1: automatisk identifiering

Mobilitets tjänsten identifierar automatiskt proxyinställningarna från miljö inställningar eller IE-inställningar (endast Windows) under aktivera replikering. 

- Windows OS: under aktivera replikering identifierar mobilitets tjänsten proxyinställningarna enligt konfigurationen i Internet Explorer för den lokala system användaren. Om du vill konfigurera proxy för lokalt system konto kan en administratör använda PsExec för att starta en kommando tolk och sedan Internet Explorer. 
- Windows OS: proxyinställningar konfigureras som miljövariabler http_proxy och no_proxy. 
- Linux OS: proxyinställningar konfigureras i/etc/profile eller/etc/Environment som miljövariabler http_proxy no_proxy. 
- De automatiskt identifierade proxyinställningarna sparas i ProxyInfo. conf för mobilitets tjänstens proxy. conf 
- Standard plats för ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft Azure Site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Metod 2: Ange anpassade inställningar för programproxy

I det här fallet tillhandahåller kunden anpassade programproxy-inställningar i mobilitets tjänstens konfigurations fil ProxyInfo. conf. Med den här metoden kan kunder bara tillhandahålla proxy för mobilitets tjänsten eller en annan proxy för Azure Site Recovery mobilitets tjänst än en proxy (eller ingen proxy) för resten av programmen på datorn.

## <a name="proxy-template"></a>Mall för proxy
ProxyInfo. conf innehåller följande mall [proxy] address = http://1.2.3.4 port = 5678 BypassList = hypervrecoverymanager. windowsazure. com, login. microsoftonline. com, blob. Core. Windows. net. BypassList har inte stöd för jokertecken som "*. windows.net", men om du ger windows.net är det tillräckligt tillräckligt för att kringgå. 

## <a name="next-steps"></a>Nästa steg:
- Läs [nätverks vägledning](./azure-to-azure-about-networking.md)  för replikering av virtuella Azure-datorer.
- Distribuera haveri beredskap genom att [Replikera virtuella Azure-datorer](./azure-to-azure-quickstart.md).
