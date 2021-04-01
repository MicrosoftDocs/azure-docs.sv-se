---
title: Livs cykeln för team data science process
description: TDSP (Team data science process) tillhandahåller en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ed54fda744978ac43bbffc6a70d6b331e85dc5e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305604"
---
# <a name="the-team-data-science-process-lifecycle"></a>Livs cykeln för team data science process

TDSP (Team data science process) tillhandahåller en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de fullständiga stegen som slutfört projekt. Om du använder en annan data vetenskaps livs cykel, till exempel en process som bygger på flera bransch standarder för Data utvinning [(skarp DM)](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), kunskaps identifiering i databaser [(KDD)](https://wikipedia.org/wiki/Data_mining#Process)eller din organisations egen anpassade process, kan du fortfarande använda den uppgiftsbaserade TDSP. 

Den här livs cykeln är utformad för data vetenskaps projekt som är avsedda att levereras som en del av intelligenta program. Dessa program distribuerar Machine Learning-eller artificiell Intelligence-modeller för förutsägelse analys. Undersökande data – vetenskaps projekt och Improvised analys projekt kan också dra nytta av användningen av den här processen. Men för dessa projekt kanske vissa av stegen som beskrivs här inte behövs. 

## <a name="five-lifecycle-stages"></a>Fem livs cykel steg

TDSP-livscykeln består av fem huvud steg som körs iterativt. Dessa steg omfattar:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


TDSP-livscykeln modelleras som en sekvens av upprepade steg som ger vägledning om de uppgifter som behövs för att använda förutsägande modeller. Du distribuerar de förutsägelse modeller i produktions miljön som du planerar att använda för att bygga smarta program. Målet med den här process livs cykeln är att fortsätta att flytta ett data vetenskaps projekt till en klar slut punkt för engagemang. Data vetenskap är en övning för forskning och identifiering. Möjligheten att kommunicera uppgifter till ditt team och dina kunder genom att använda en väldefinierad uppsättning artefakter som använder standardiserade mallar hjälper till att undvika fel. Med hjälp av dessa mallar ökar risken för att ett komplext data vetenskaps projekt slutförs.

För varje steg tillhandahåller vi följande information:

   * **Mål**: de speciella målen.
   * **Gör så här**: en översikt över de olika aktiviteterna och rikt linjerna för hur du utför dem.
   * **Artefakter**: slut produkter och support för att skapa dem.

## <a name="next-steps"></a>Nästa steg

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för särskilda scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning](./index.yml).