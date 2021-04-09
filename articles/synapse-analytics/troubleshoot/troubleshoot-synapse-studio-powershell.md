---
title: Felsöka Synapse Studio-anslutning
description: Felsöka Azure Synapse Studio-anslutning med PowerShell
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 6ff3d985bb24ec852bb5c6cfaedf295fd79a8247
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120367"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Felsök Synapse Studio-anslutning med PowerShell

Azure Synapse Studio är beroende av en uppsättning webb-API-slutpunkter för att fungera korrekt. Den här guiden hjälper dig att identifiera orsaker till anslutnings problem när du är:
- Konfigurera ditt lokala nätverk (till exempel ett nätverk bakom en företags brand vägg) för åtkomst till Azure Synapse Studio.
- problem med anslutningen med Azure Synapse Studio.

## <a name="prerequisite"></a>Förutsättning

* PowerShell 5,0 eller senare versioner av Windows, eller
* PowerShell Core 6,0 eller senare version på Windows eller Linux.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Högerklicka på följande länk och välj "Spara mål som":

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Alternativt kan du öppna länken direkt och spara den öppna skript filen. Spara inte adressen till länken ovan, eftersom den kan ändras i framtiden.

I Utforskaren högerklickar du på den nedladdade skript filen och väljer Kör med PowerShell.

![Kör Hämtad skript fil med PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

När du uppmanas till det anger du namnet på den Azure Synapse-arbetsyta som för närvarande har problem, eller om du vill testa anslutningen och trycka på RETUR.

![Ange namn på arbets yta](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Den diagnostiska sessionen kommer att startas. Vänta tills den är klar.

![Vänta tills diagnostiken har slutförts](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

I slutet visas en diagnos Sammanfattning. Om datorn inte kan ansluta till en eller flera slut punkter visas några förslag i avsnittet "Sammanfattning".

![Granska diagnostisk Sammanfattning](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Dessutom genereras en diagnostisk loggfil för sessionen i samma mapp som fel söknings skriptet. Dess plats visas i avsnittet "allmänna tips" ( `D:\TestAzureSynapse_2020....log` ). Du kan skicka den här filen till teknisk support om det behövs.

Om du är nätverks administratör och justerar brand Väggs konfigurationen för Azure Synapse Studio kan den tekniska informationen som visas ovanför avsnittet "Sammanfattning" hjälpa dig.

* Alla test objekt (begär Anden) som marker ATS med "lyckades" innebär att de har godkänt anslutnings test, oavsett HTTP-statuskod.
 För de misslyckade förfrågningarna visas orsaken i gult, till exempel `NamedResolutionFailure` eller `ConnectFailure` . Dessa orsaker kan hjälpa dig att ta reda på om det finns fel i konfigurationen med din nätverks miljö.


## <a name="next-steps"></a>Nästa steg
Om föregående steg inte hjälper till att lösa problemet kan du [skapa ett support ärende](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).