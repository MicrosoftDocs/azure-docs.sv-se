---
title: Viktiga ändringar som kommer till Azure Security Center
description: Kommande ändringar av Azure Security Center som du kan behöva känna till och för vilka du kan behöva planera
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475598"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

- [Kubernetes för arbets belastnings skydd kommer snart att lanseras för allmän tillgänglighet (GA)](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- [Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Kubernetes för arbets belastnings skydd kommer snart att lanseras för allmän tillgänglighet (GA)

**Beräknat datum för ändring:** Februari 2021

Kubernetes för arbets belastnings skydd som beskrivs i [skydda dina Kubernetes-arbetsbelastningar](kubernetes-workload-protections.md) är för närvarande en för hands version. Även om en rekommendation är i för hands version, återges inte en resurs som inte är felfri och ingår inte i beräkningarna av dina säkra poäng.

De här rekommendationerna kommer snart att lanseras för allmän tillgänglighet (GA) och *kommer* att ingå i Poäng beräkningen. Om du inte redan har reparerat dem kan det leda till en lätt inverkan på dina säkra poäng.

Åtgärda dem när det är möjligt (Lär dig hur du [reparerar rekommendationer i Azure Security Center](security-center-remediate-recommendations.md)).

Rekommendationerna för Kubernetes arbets belastnings skydd är:

- Azure Policy tillägg för Kubernetes bör installeras och aktive ras i klustren
- CPU-och minnes gränser för containern ska tillämpas
- Privilegierade behållare bör undvikas
- Oföränderligt (skrivskyddat) rot fil system ska tillämpas för behållare
- Container med behörighets eskalering bör undvikas
- Att köra behållare som rot användare bör undvikas
- Behållare som delar känsliga värd namn områden bör undvikas
- Minst privilegierade Linux-funktioner bör tillämpas för behållare
- Användningen av Pod HostPath-volym monteringar bör begränsas till en känd lista
- Behållare ska bara lyssna på tillåtna portar
- Tjänsterna ska bara lyssna på tillåtna portar
- Användning av värd nätverk och portar ska begränsas
- Åsidosättning eller inaktive ring av behållare AppArmor-profilen bör vara begränsad
- Behållar avbildningar ska endast distribueras från betrodda register             

Läs mer om de här rekommendationerna i [skydda dina Kubernetes-arbetsbelastningar](kubernetes-workload-protections.md).

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell 

**Beräknat datum för ändring:** Februari 2021

Följande två rekommendationer är schemalagda att bli föråldrade i februari 2021:

- **Datorerna måste startas om för att tillämpa system uppdateringar**. Detta kan leda till en lätt inverkan på dina säkra poäng.
- **Övervaknings agenten ska installeras på datorerna**. Den här rekommendationen gäller endast för lokala datorer och en del av dess logik överförs till en annan rekommendation, **Log Analytics agent hälso problem bör lösas på dina datorer**. Detta kan leda till en lätt inverkan på dina säkra poäng.

Vi rekommenderar att du kontrollerar dina kontinuerliga export-och arbets flödes automatiserings konfigurationer för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrument paneler och andra övervaknings verktyg som kan använda dem uppdateras i enlighet med detta.

Läs mer om de här rekommendationerna på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Den aktuella versionen av rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Därför:

- Rekommendationen kommer inte längre att påverka dina säkra Poäng
- Säkerhets kontrollen ("Använd data klassificering") kommer inte längre att påverka dina säkra Poäng
- Rekommendationens ID kommer också att ändras (för närvarande b0df6f56-862d-4730-8597-38c0fd4ebd59)



## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).
