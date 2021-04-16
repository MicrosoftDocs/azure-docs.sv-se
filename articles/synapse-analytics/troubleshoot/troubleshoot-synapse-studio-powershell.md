---
title: Felsöka Synapse Studio-anslutning
description: Felsöka Azure Synapse Studio-anslutning med PowerShell
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 2bbdaef9268239005cdf5ea7fbee6734dadc8113
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566260"
---
# <a name="troubleshoot-synapse-studio-connectivity-with-powershell"></a>Felsöka Synapse Studio med PowerShell

Azure Synapse Studio är beroende av att en uppsättning webb-API-slutpunkter fungerar korrekt. Den här guiden hjälper dig att identifiera orsaker till anslutningsproblem när du:
- konfigurera ditt lokala nätverk (till exempel nätverket bakom en företagsbrandvägg) för åtkomst till Azure Synapse Studio.
- har anslutningsproblem med Azure Synapse Studio.

## <a name="prerequisite"></a>Förutsättning

* PowerShell 5.0 eller senare i Windows, eller
* PowerShell Core version 6.0 eller senare på Windows eller Linux.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Högerklicka på följande länk och välj Spara mål som:

- [Test-AzureSynapse.ps1](https://go.microsoft.com/fwlink/?linkid=2119734)

Du kan också öppna länken direkt och spara den öppna skriptfilen. Spara inte adressen till länken ovan eftersom den kan komma att ändras i framtiden.

Högerklicka på den nedladdade skriptfilen i Utforskaren och välj "Kör med PowerShell".

![Kör nedladdad skriptfil med PowerShell](media/troubleshooting-synapse-studio-powershell/run-with-powershell.png)

När du uppmanas till det anger Azure Synapse namnet på arbetsytan som för närvarande har problem eller som du vill testa för anslutning och trycker på Retur.

![Ange arbetsytans namn](media/troubleshooting-synapse-studio-powershell/enter-workspace-name.png)

Diagnostiksessionen startas. Vänta tills det är klart.

![Vänta tills diagnosen har slutförts](media/troubleshooting-synapse-studio-powershell/wait-for-diagnosis.png)

I slutändan visas en diagnossammanfattning. Om datorn inte kan ansluta till en eller flera slutpunkter visas några förslag i avsnittet Sammanfattning.

![Granska diagnostiksammanfattning](media/troubleshooting-synapse-studio-powershell/diagnosis-summary.png)

Dessutom genereras en diagnostikloggfil för den här sessionen i samma mapp som felsökningsskriptet. Platsen visas i avsnittet "Allmänna tips" ( `D:\TestAzureSynapse_2020....log` ). Du kan skicka filen till teknisk support om det behövs.

Om du är nätverksadministratör och finjusterar brandväggskonfigurationen för Azure Synapse Studio kan den tekniska informationen som visas ovanför avsnittet Sammanfattning vara till hjälp.

* Alla testobjekt (begäranden) som markerats med "Godkänt" innebär att de har klarat anslutningstesterna, oavsett HTTP-statuskod.
 För de misslyckade begärandena visas orsaken i gult, till exempel `NamedResolutionFailure` eller `ConnectFailure` . Dessa orsaker kan hjälpa dig att ta reda på om det finns felkonfigurationer med din nätverksmiljö.


## <a name="next-steps"></a>Nästa steg
Om de föregående stegen inte hjälper dig att lösa problemet skapar du [en supportbiljett.](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)