---
title: Distribuera Azure SQL Edge på turbiner i en Contoso-vridnings grupp
description: I den här självstudien använder du Azure SQL Edge för aktiverings avkänning på turbinerna i en Contoso-lindnings grupp.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723481"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Använda Azure SQL Edge för att bygga smartare förnybara resurser

Den här Azure SQL Edge-demonstrationen är baserad på en förnybar förnybar energi, en lindning turbin-servergrupp som använder SQL DB Edge för data bearbetning av generatorn. 

Den här demon vägleder dig genom att lösa en avisering som aktive ras på grund av att Turbulence identifieras på enheten. Du tränar en modell och distribuerar den till SQL DB Edge som korrigerar den identifierade vindhastigheten och i slut änden optimerar strömförbrukningen.

Azure SQL Edge – förnybar energi demo video på Channel 9:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>Konfigurera demonstrationen på den lokala datorn
Git kommer att användas för att kopiera alla filer från demonstrationen till den lokala datorn. 

1. Installera git härifrån [här](https://git-scm.com/download).
2. Öppna en kommando tolk och navigera till en mapp där lagrings platsen ska laddas ned. 
3. Utfärda kommandot https://github.com/microsoft/sql-server-samples.git .
4. Gå till **SQL-Server-samples\samples\demos\azure-SQL-Edge-demos\Wind turbin-demon** på den plats där lagrings platsen ska klonas.
5. Följ anvisningarna i README.md för att konfigurera demo miljön och köra demon.