---
title: Säkerhets härdning i AKS virtuella dator värdar
description: Lär dig mer om säkerhets härdningen i AKS VM Host OS
services: container-service
author: mlearned
ms.topic: article
ms.date: 09/11/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 84b826ce33b5395db5bd38e883b3a0fb3425725b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86244046"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Säkerhets härdning för AKS-nodens värd operativ system

Azure Kubernetes service (AKS) är en säker tjänst som är kompatibel med SOC-, ISO-, PCI DSS-och HIPAA-standarder. Den här artikeln beskriver säkerhets härdningen som tillämpas på AKS virtuella dator värdar. Mer information om AKS-säkerhet finns i [säkerhets begrepp för program och kluster i Azure Kubernetes service (AKS)](./concepts-security.md).

> [!Note]
> Det här dokumentet är begränsat till Linux-agenter i AKS.

AKS-kluster distribueras på virtuella värd datorer, som kör ett säkerhetsoptimerat operativ system som används för behållare som körs på AKS. Det här värd operativ systemet baseras på en **Ubuntu 16.04. LTS** -avbildning med ytterligare säkerhets härdning och optimeringar som tillämpas (se säkerhets härdnings information).

Målet för den säkerhetsbegränsade värd operativ systemet är att minska Angreppets yta och optimera för distribution av behållare på ett säkert sätt.

> [!Important]
> Det säkerhetshärdade operativ systemet är inte CIS-benchmark. Även om det finns överlappande av CIS-benchmarks, är målet inte att vara CIS-kompatibelt. Målet för att hantera OS-härdning är att konvergera på en säkerhets nivå som är konsekvent med Microsofts egna interna värd säkerhets standarder.

## <a name="security-hardening-features"></a>Säkerhets härdnings funktioner

* AKS tillhandahåller ett säkerhetsoptimerat värd-OS som standard. Det finns inget alternativ för att välja ett alternativt operativ system.

* Azure använder dagliga korrigeringar (inklusive säkerhets korrigeringar) för att AKS virtuella dator värdar. Vissa av de här korrigeringarna kräver en omstart, medan andra inte kommer. Du ansvarar för schemaläggning av AKS VM Host-omstarter efter behov. Vägledning om hur du automatiserar AKS korrigering finns i [korrigera AKS-noder](./node-updates-kured.md).

## <a name="what-is-configured"></a>Vad är konfigurerat

| CIS  | Gransknings Beskrivning|
|---|---|
| 1.1.1.1 |Se till att montering av cramfs-filsystem är inaktiverat|
| 1.1.1.2 |Se till att montering av freevxfs-filsystem är inaktiverat|
| 1.1.1.3 |Se till att montering av jffs2-filsystem är inaktiverat|
| 1.1.1.4 |Se till att montering av HFS-filsystem är inaktiverat|
| 1.1.1.5 |Se till att montering av HFS Plus fil system är inaktiverat|
|1.4.3 |Se till att autentisering krävs för enanvändarläge |
|1.7.1.2 |Kontrol lera att den lokala inloggnings varnings banderollen är korrekt konfigurerad |
|1.7.1.3 |Se till att den fjärranslutna inloggnings varningen är korrekt konfigurerad |
|1.7.1.5 |Se till att behörigheter för/etc/Issue har kon figurer ATS |
|1.7.1.6 |Se till att behörigheter för/etc/Issue.net har kon figurer ATS |
|2.1.5 |Se till att--streaming-Connection-Idle-timeout inte är inställt på 0 |
|3.1.2 |Se till att Packet Redirect skickas är inaktiverat |
|3.2.1 |Se till att källor som dirigerats inte godkänns |
|3.2.2 |Se till att ICMP-omdirigeringar inte accepteras |
|3.2.3 |Se till att säkra ICMP-omdirigeringar inte godkänns |
|3.2.4 |Se till att misstänkta paket loggas |
|3.3.1 |Se till att IPv6-router-annonsering inte accepteras |
|3.5.1 |Se till att DCCP har inaktiverats |
|3.5.2 |Se till att SCTP har inaktiverats |
|3.5.3 |Se till att RDS är inaktiverat |
|3.5.4 |Se till att TIPC har inaktiverats |
|4.2.1.2 |Se till att loggning har kon figurer ATS |
|5.1.2 |Se till att behörigheter för/etc/crontab har kon figurer ATS |
|5.2.4 |Se till att vidarebefordring av SSH-begäran om x11 är inaktiverat |
|5.2.5 |Se till att SSH-MaxAuthTries har angetts till 4 eller mindre |
|5.2.8 |Se till att SSH-rotnoden är inaktive rad |
|5.2.10 |Se till att SSH-PermitUserEnvironment är inaktiverat |
|5.2.11 |Se till att endast godkända algoritmer används |
|5.2.12 |Se till att tids gränsen för SSH-inaktivitet är konfigurerad |
|5.2.13 |Se till att SSH-LoginGraceTime är inställt på en minut eller mindre |
|5.2.15 |Se till att SSH-varningens banderoll har kon figurer ATS |
|5.3.1 |Se till att krav för att skapa lösen ord konfigureras |
|5.4.1.1 |Se till att lösen ordets giltighets tid är 90 dagar eller mindre |
|5.4.1.4 |Se till att det inaktiva lösen ords låset är 30 dagar eller mindre |
|5.4.4 |Se till att standard användaren umask är 027 eller mer restriktiv |
|5,6 |Se till att åtkomsten till Su-kommandot är begränsad|

## <a name="additional-notes"></a>Ytterligare information
 
* Vissa onödiga driv rutiner för kernel-moduler har inaktiverats i operativ systemet för att ytterligare minska risken för attack ytan.

* Det säkerhetshärdade operativ systemet är byggt och underhålls specifikt för AKS och stöds inte utanför AKS-plattformen.

## <a name="next-steps"></a>Nästa steg  

I följande artiklar finns mer information om AKS-säkerhet: 

[Azure Kubernetes Service (AKS)](./intro-kubernetes.md)

[AKS säkerhets aspekter ](./concepts-security.md)

[Metod tips för AKS ](./best-practices.md)
