---
title: Viktig information för Azure Application enhetligt ögonblicks bilds verktyg för Azure NetApp Files | Microsoft Docs
description: Innehåller viktig information för Azure Application enhetligt ögonblicks bilds verktyg som du kan använda med Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105111479"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Viktig information för Azure Application enhetligt ögonblicks bild verktyget (förhands granskning)

Den här sidan visar större ändringar som gjorts i AzAcSnap för att tillhandahålla nya funktioner eller lösa fel.

## <a name="march-2021"></a>Mars – 2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Preview (version: 20210318.30771)

AzAcSnap v 5.0 Preview (build: 20210318.30771) har släppts med följande korrigeringar och förbättringar:

- Du har tagit bort behovet av att lägga till AZACSNAP-användaren i SAP HANA klient databaser, se avsnittet [Aktivera kommunikation med SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Korrigera för att tillåta en [återställning](azacsnap-cmd-ref-restore.md) med volymer som kon figurer ATS med manuella QoS.
- En mutex-kontroll har lagts till för att reglera SSH-anslutningar för stor Azure-instans.
- Korrigera installations programmet för att hantera Sök vägs namn med blank steg och andra relaterade problem.
- För att ge stöd till andra databas servrar ändrade den valfria parametern--hanasid till--dbsid.

Hämta den [senaste versionen](https://aka.ms/azacsnapdownload) av installations programmet och se hur du [kommer igång](azacsnap-get-started.md).

## <a name="next-steps"></a>Nästa steg

- [Kom igång med Azure Application enhetligt ögonblicks bild verktyget](azacsnap-get-started.md)
