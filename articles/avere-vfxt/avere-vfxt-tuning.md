---
title: Aver vFXT-kluster justering – Azure
description: Lär dig mer om den anpassade justeringen för vFXT-kluster i aver vFXT för Azure som du kan göra med en support representant.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 5d9f81c9438cb992f81bd3e6319532d67db75552
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272390"
---
# <a name="cluster-tuning"></a>Klusterjustering

De flesta vFXT-kluster kan dra nytta av anpassade prestanda inställningar. De här inställningarna hjälper klustret att fungera bäst med specifika arbets flöden, data uppsättningar och verktyg.

Den här anpassningen bör göras med hjälp från en support representant, eftersom den kan omfatta konfigurations funktioner som inte är tillgängliga från den Avera kontroll panelen.

I det här avsnittet beskrivs några av de anpassade justeringarna som kan göras.

## <a name="general-optimizations"></a>Allmänna optimeringar

Dessa ändringar kan vara rekommenderade baserat på data uppsättnings kvaliteter eller arbets flödes format.

* Om arbets belastningen är skrivbar, ökar du storleken på skrivcache från standardvärdet 20%.
* Om data uppsättningen omfattar många små filer ökar du antalet filer för klustrets cacheminne.
* Om arbetet innebär att kopiera eller flytta data mellan två databaser, justera antalet trådar som används för att flytta data:
  * För att öka hastigheten kan du öka antalet parallella trådar som används.
  * Om Server delens lagrings volym blir överbelastad kan du behöva minska antalet parallella trådar som används.
* Om klustret cachelagrar data för en kärn post som använder NFSv4 ACL: er aktiverar du cachelagring av åtkomst läge för att effektivisera Filauktorisering för vissa klienter.

## <a name="cloud-nas-or-cloud-gateway-optimizations"></a>Moln-NAS eller moln-Gateway-optimeringar

I ett Cloud NAS-eller gateway-scenario tillhandahåller vFXT-klustret NAS-typ åtkomst till en moln behållare. Om du vill dra nytta av högre data hastigheter mellan vFXT-klustret och moln lagringen kan du rekommendera att ändra inställningarna till mer aggressivt push-data till lagrings volymen från cachen. Ett exempel:

* Öka antalet TCP-anslutningar mellan klustret och lagrings behållaren

## <a name="cloud-bursting-or-hybrid-wan-optimizations"></a>Cloud bursting eller hybrid WAN-optimeringar

I ett scenario med moln överföring eller i ett scenario med hybrid optimering i molnet tillhandahåller vFXT-klustret integrering mellan molnet och den lokala maskin varu lagringen. Dessa ändringar kan vara till hjälp:

* Öka antalet TCP-anslutningar som tillåts mellan klustret och kärn filen
* Aktivera inställningen för WAN-optimering för fjärrcore-filer (den här inställningen kan användas för fjärranslutna filer på plats eller en moln kärna som sparas i en annan Azure-region.)
* Öka buffertstorleken för TCP-socket<sup>*</sup>
* Aktivera inställningen "Always Forward" för att minska redundanta cachelagrade filer<sup>*</sup>

<sup>*</sup>Dessa justeringar kanske inte gäller för alla system, beroende på arbets belastning och prestanda behov.

## <a name="help-optimizing-your-avere-vfxt-for-azure"></a>Hjälp till att optimera ditt AVERT vFXT för Azure

Om du vill kontakta support personalen om dessa optimeringar använder du proceduren som beskrivs i [få hjälp med systemet](avere-vfxt-open-ticket.md).
