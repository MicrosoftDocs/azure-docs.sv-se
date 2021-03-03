---
title: Storlek på SAP HANA på Azure (stora instanser) | Microsoft Docs
description: Storlek på SAP HANA på Azure (stora instanser).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d49191abbba9c189672be4cd8bad4346e9689bf6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675417"
---
# <a name="sizing"></a>Storlekar

Storlek för den stora HANA-instansen skiljer sig från storlek för HANA i allmänhet. För befintliga och distribuerade system som du vill flytta från andra RDBMS till HANA tillhandahåller SAP ett antal rapporter som körs på dina befintliga SAP-system. Om databasen flyttas till HANA, kontrollerar de här rapporterna data och beräknar minnes kraven för HANA-instansen. Mer information om hur du kör dessa rapporter och skaffar de senaste korrigeringarna eller versionerna finns i följande SAP-anteckningar:

- [SAP-anteckning #1793345-storlek för SAP Suite på HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-anteckning #1872170-svit på HANA-och S/4 HANA-storleks rapport](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-anteckning #2121330 – vanliga frågor och svar: SAP BW på HANA storleks rapport](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-anteckning #1736976 storleks rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-anteckning #2296290-ny storleks rapport för BW på HANA](https://launchpad.support.sap.com/#/notes/2296290)

För gröna fält implementeringar kan SAP Quick Sizeer vara tillgängligt för att beräkna minnes kraven för implementeringen av SAP-programvara ovanpå HANA.

Minnes krav för HANA ökar när data volymen växer. Tänk på den aktuella minnes förbrukningen så att du kan förutsäga vad den kommer att vara i framtiden. Utifrån minnes kraven kan du mappa din efter frågan till en av de stora HANA-instanserna.

**Nästa steg**
- Se [onboarding-krav](hana-onboarding-requirements.md)