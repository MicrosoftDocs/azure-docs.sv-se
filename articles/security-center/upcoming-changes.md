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
ms.openlocfilehash: 8ef0fedcbf7ce06063db22fb345a0711acddcb4d
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526389"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Viktiga kommande ändringar i Azure Security Center

> [!IMPORTANT]
> Informationen på den här sidan avser för hands versions produkter eller funktioner som kan ändras i stort sett innan de släpps kommersiellt. Microsoft lämnar inga åtaganden eller garantier, vare sig uttryckliga eller underförstådda, med avseende på den information som anges här.

På den här sidan får du lära dig om ändringar som planeras för Security Center. Den beskriver planerade ändringar av produkten som kan påverka saker som dina säkra poäng eller arbets flöden.

Om du letar efter den senaste versions informationen hittar du dem i [vad som är nytt i Azure Security Center](release-notes.md).


## <a name="planned-changes"></a>Planerade ändringar

- [Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [Förbättringar av SQL data klassificerings rekommendation](#enhancements-to-sql-data-classification-recommendation)

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>Två rekommendationer från "tillämpa system uppdateringar"-säkerhets kontrollen är inaktuell 

**Beräknat datum för ändring:** Februari 2021

Följande två rekommendationer är schemalagda att bli föråldrade i februari 2021:

- **Datorerna måste startas om för att tillämpa system uppdateringar**. Detta kan leda till en lätt inverkan på dina säkra poäng.
- **Övervaknings agenten ska installeras på datorerna**. Den här rekommendationen gäller endast för lokala datorer och en del av dess logik överförs till en annan rekommendation, **Log Analytics agent hälso problem bör lösas på dina datorer**. Detta kan leda till en lätt inverkan på dina säkra poäng.

Vi rekommenderar att du kontrollerar dina kontinuerliga export-och arbets flödes automatiserings konfigurationer för att se om dessa rekommendationer ingår i dem. Dessutom bör alla instrument paneler och andra övervaknings verktyg som kan använda dem uppdateras i enlighet med detta.

Läs mer om de här rekommendationerna på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).


### <a name="enhancements-to-sql-data-classification-recommendation"></a>Förbättringar av SQL data klassificerings rekommendation

**Beräknat datum för ändring:** K2 2021

Rekommendations **känsliga data i dina SQL-databaser bör klassificeras** i säkerhets kontrollen **Använd data klassificering** ersätts med en ny version som är bättre anpassad med Microsofts strategi för data klassificering. Som ett resultat kommer rekommendationens ID också att ändras (för närvarande b0df6f56-862d-4730-8597-38c0fd4ebd59).



## <a name="next-steps"></a>Nästa steg

För alla nyligen gjorda ändringar av produkten, se [Vad är nytt i Azure Security Center?](release-notes.md).
