---
title: Tillägg för Cloud Services (utökad support)
description: Tillägg för Cloud Services (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220925"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Tillägg för Cloud Services (utökad support)

Tillägg är små program som ger konfigurations-och automatiserings åtgärder efter distributionen på roller. Du kan till exempel aktivera en anslutning till fjärr skrivbord i din roll under distributionen av moln tjänsten (utökad support) med hjälp av fjärr skrivbords tillägget.  

## <a name="remote-desktop-extension"></a>Fjärr skrivbords tillägg

Med fjärr skrivbord kan du komma åt Skriv bordet för en roll som körs i Azure. Du kan använda en anslutning till fjärr skrivbord för att felsöka och diagnostisera problem med programmet medan det körs.

Du kan aktivera en anslutning till fjärr skrivbord i din roll under utvecklingen genom att inkludera fjärr skrivbords-moduler i din tjänst definition eller via fjärr skrivbords tillägget. 

Mer information finns i [Konfigurera fjärr skrivbord från Azure Portal](enable-rdp.md)

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure-diagnostik-tillägg

Du kan övervaka viktiga prestanda mått för alla moln tjänster. Varje moln tjänst roll samlar in minimala data: CPU-användning, nätverks användning och disk användning. Om moln tjänsten har tillägget Microsoft. Azure. Diagnostics som tillämpas på en roll kan den rollen samla in ytterligare data punkter. 

Med grundläggande övervakning samplas prestanda räknar data från roll instanser och samlas in med 3 minuters intervall. Dessa grundläggande övervaknings data lagras inte i ditt lagrings konto och har ingen ytterligare kostnad kopplad till sig. 

Med avancerad övervakning samplas ytterligare mått och samlas in i intervall om 5 minuter, 1 timme och 12 timmar. De aggregerade data lagras i ett lagrings konto i tabeller och rensas efter 10 dagar. Det använda lagrings kontot har kon figurer ATS av rollen. Du kan använda olika lagrings konton för olika roller. 

Mer information finns i [använda Windows Azure Diagnostics-tillägget i Cloud Services (utökad support)](enable-wad.md)

## <a name="anti-malware-extension"></a>Tillägg för skydd mot skadlig kod
Ett Azure-program eller-tjänst kan aktivera och konfigurera Microsoft Antimalware för Azure Cloud Services med hjälp av PowerShell-cmdletar. Observera att Microsoft Antimalware är installerat i ett inaktiverat tillstånd i Cloud Services plattform som kör Windows Server 2012 R2 och äldre, vilket kräver en åtgärd av ett Azure-program för att aktivera det. För Windows Server 2016 och senare är Windows Defender aktiverat som standard, och därför kan dessa cmdletar användas för att konfigurera program mot skadlig kod.

Mer information finns i [lägga till Microsoft Antimalware till Azure Cloud service med hjälp av utökad support (CS-ES)](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)

Om du vill veta mer om Azure antimalware kan du gå [hit](https://docs.microsoft.com/azure/security/fundamentals/antimalware)



## <a name="next-steps"></a>Nästa steg 
- Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support).
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
