---
title: Azure Storage Explorer support livs cykel | Microsoft Docs
description: Översikt över support policyn och livs cykeln för Azure Storage Explorer
services: storage
author: MRayermannMSFT
manager: jinglouMSFT
ms.service: storage
ms.topic: article
ms.date: 07/10/2020
ms.author: marayerm
ms.openlocfilehash: dc89631208c6a72c4b48d49260f584149d6c4260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97679944"
---
# <a name="azure-storage-explorer-support-lifecycle-and-policy"></a>Azure Storage Explorer support livs cykel och policy

Det här dokumentet täcker support livs cykeln och policyn för Azure Storage Explorer.

## <a name="update-schedule-and-process"></a>Uppdatera schema och process

Azure Storage Explorer lanseras fyra till sex gånger per år. Microsoft kan också kringgå det här schemat för release-korrigeringar för kritiska problem.

Storage Explorer söker efter uppdateringar varje timme och laddar ned dem när de är tillgängliga. Användar godkännande krävs för att starta installations processen. Om användarna väljer att uppdatera vid en annan tidpunkt kan de manuellt söka efter uppdateringar. På Windows och Linux kan användare söka efter uppdateringar genom att välja **Sök efter uppdateringar** på **Hjälp** -menyn. I macOS kan du **söka efter uppdateringar** på **menyn app**. Användarna kan också direkt gå till sidan [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) hämtning för att ladda ned och installera den senaste versionen.

Vi rekommenderar starkt att du alltid använder de senaste versionerna av Storage Explorer. Om ett problem hindrar dig från att uppdatera till den senaste versionen av Storage Explorer, kan du öppna ett problem på vår [GitHub](https://github.com/microsoft/AzureStorageExplorer).

## <a name="support-lifecycle"></a>Support livs cykel

Storage Explorer regleras av den [moderna livs cykel policyn](https://support.microsoft.com/help/30881/modern-lifecycle-policy). Det förväntas att användarna ska ha installerat Storage Explorer. Med dagens datum säkerställer du att användarna har de senaste funktionerna, prestanda förbättringarna, säkerheten och tjänstens tillförlitlighet.

Från och med version 1.14.1 betraktas alla Storage Explorer-versioner som är större än 12 månader gamla som stöd. Alla versioner innan 1.14.1 kommer att anses som support från den 14 juli 2021. Versioner som inte längre stöds är inte längre garanterat för att fungera fullständigt som de är utformade och förväntade. För en lista över alla versioner, publicerings datumet och deras slut för ande datum, se [versioner](#releases).

Från och med version 1.13.0 visas en avisering i appen när en version är ungefär en månad bort från supporten. Aviseringen uppmanar användarna att uppdatera till den senaste versionen av Storage Explorer. När en version saknar stöd visas aviseringen i appen på varje start.

## <a name="releases"></a>Versioner

I den här tabellen beskrivs utgivnings datumet och slutet på support datumet för varje version av Azure Storage Explorer.

| Storage Explorer version  | Utgivningsdatum       | Sista support datum |
|:-------------------------:|:------------------:|:-------------------:|
| v-1.17.0                   | 15 december 2020  | 15 december 2021   |
| v-1.16.0                   | 10 november 2020  | 10 november 2021   |
| v-1.15.1                   | 2 september 2020  | 2 september 2021   |
| v-1.15.0                   | 27 augusti 2020    | 27 augusti 2021     |
| v-1.14.2                   | 16 juli 2020      | 16 juli 2021       |
| v-1.14.1                   | 14 juli 2020      | 14 juli 2021       |
| v-1.14.0                   | 24 juni 2020      | 14 juli 2021       |
| v-1.13.1                   | 18 maj 2020       | 14 juli 2021       |
| v-1.13.0                   | Den 1 maj 2020        | 14 juli 2021       |
| v-1.12.0                   | 16 januari 2020   | 14 juli 2021       |
| v-1.11.2                   | 17 december 2019  | 14 juli 2021       |
| v-1.11.1                   | 20 november 2019  | 14 juli 2021       |
| v-1.11.0                   | Den 4 november 2019   | 14 juli 2021       |
| v-1.10.1                   | 19 september 2019 | 14 juli 2021       |
| v-1.10.0                   | 12 september 2019 | 14 juli 2021       |
| v-1.9.0                    | Den 1 juli 2019       | 14 juli 2021       |
| v-1.8.1                    | 10 maj 2019       | 14 juli 2021       |
| v-1.8.0                    | 2 maj 2019        | 14 juli 2021       |
| v-1.7.0                    | 5 mars 2019      | 14 juli 2021       |
| v-1.6.2                    | 8 januari 2019    | 14 juli 2021       |
| v-1.6.1                    | 18 december 2018  | 14 juli 2021       |
| v-1.6.0                    | 4 december 2018   | 14 juli 2021       |
| v-1.5.0                    | 29 oktober 2018   | 14 juli 2021       |
| v-1.4.4                    | 15 oktober 2018   | 14 juli 2021       |
| v-1.4.2                    | September 24, 2018 | 14 juli 2021       |
| v 1.4.1                    | 28 augusti 2018    | 14 juli 2021       |
| v-1.3.1                    | 11 juli 2018      | 14 juli 2021       |
| v-1.2.0                    | 12 juni 2018      | 14 juli 2021       |
| v-1.1.0                    | 9 maj 2018        | 14 juli 2021       |
| v-1.0.0 (och äldre)        | 16 april 2018     | 14 juli 2021       |
