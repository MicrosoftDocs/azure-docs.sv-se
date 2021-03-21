---
title: Fel sökning av Azure Internet Analyzer | Microsoft Docs
description: Fel söknings referensen för Azure Internet Analyzer.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: troubleshooting
ms.date: 12/04/2019
ms.author: dibotero
ms.openlocfilehash: fe173ec4d4f28444d43739ea2ed51e43021916b1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "84744365"
---
# <a name="azure-internet-analyzer-troubleshooting"></a>Fel sökning i Azure Internet Analyzer

Den här artikeln innehåller fel söknings steg för vanliga problem med Internet Analyzer.

## <a name="things-to-keep-in-mind"></a>Saker att tänka på
- Klient skriptet måste vara inbäddat i en **https** -webbplats. Mätningar samlas inte in om skriptet körs i en oformaterad text-(**http://**) eller lokal webbplats (**File://**).
- Mät data samlas endast in om Internet Analyzer-profilens klient skript har bäddats in i ett program som tar emot faktisk användar trafik. Syntetisk trafik (till exempel Azure WebApp-prestandatester) kör vanligt vis inte inbäddad JavaScript-kod, så inga mätningar genereras av den typen av trafik.

## <a name="azure-portal"></a>Azure Portal
**"Ett styrkort har inte skapats för den valda filter kombinationen" i avsnittet styrkort**
- Styrkort skapas per dag (i slutet av varje dag, UTC-tid).
- Styrkort skapas endast om fler än 100 mått samlades in för den valda filter kombinationen (test, tids period, land/region osv.).

**"Totalt antal mätningar" är noll för en eller båda slut punkterna i ett test**
- Tids serier och mätnings antal beräknas en gång i timmen, så du måste vänta minst den tiden för att nya Mät data ska visas.
- Internet Analyzer räknar endast lyckade mätningar (t. ex. HTTP 200-svar) för analys. Om en eller båda slut punkterna i ett test inte kan kommas åt eller returnerar en HTTP-kod som inte är 200, visas de med noll total mått.

## <a name="next-steps"></a>Nästa steg
Läs [vanliga frågor och svar om Internet Analyzer](internet-analyzer-faq.md)
