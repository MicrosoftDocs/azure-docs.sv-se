---
title: Vad är nytt med Azure Arc-aktiverade Server agenter
description: Den här artikeln innehåller viktig information om Azure Arc-aktiverade Server agenter. För många av de sammanfattade problemen finns det länkar till mer information.
ms.topic: conceptual
ms.date: 03/15/2021
ms.openlocfilehash: acf606ed1ad0f54c983b14a0141d0dc11e2c45d9
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470514"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Vad är nytt med Azure Arc-aktiverade Server agenter

Azure Arc-aktiverade servrar som är anslutna till dator agenten får fort löp ande förbättringar. För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

- De senaste versionerna
- Kända problem
- Felkorrigeringar

## <a name="march-2021"></a>Mars 2021

Version 1,4

## <a name="new-feature"></a>Ny funktion

- Stöd har lagts till för privata slut punkter.
- Expanderad lista över avslutnings koder för azcmagent.
- Agent konfigurations parametrar kan nu läsas från en fil med parametern--config.

## <a name="fixed"></a>Fast

Kontroll av nätverks slut punkter är nu snabbare.

## <a name="december-2020"></a>December 2020

Version: 1,3

### <a name="new-feature"></a>Ny funktion

Stöd har lagts till för Windows Server 2008 R2.

### <a name="fixed"></a>Fast

Löst problem med att förhindra att det anpassade skript tillägget på Linux installeras korrekt.

## <a name="november-2020"></a>November 2020

Version: 1,2

### <a name="fixed"></a>Fast

Löst problem där proxykonfigurationen kan försvinna efter uppgraderingen på RPM-baserade distributioner.

## <a name="october-2020"></a>Oktober 2020

Version: 1.1

### <a name="fixed"></a>Fast

- Fast proxy-skript för att hantera alternativ plats för en annan GC-enhets fil.
- GuestConfig-agentens tillförlitlighet ändras.
- Stöd för GuestConfig-agent för US Gov, Virginia region.
- GuestConfig agent Extension rapporterar meddelanden för att vara mer utförliga om det uppstår ett problem.

## <a name="september-2020"></a>September 2020

Version: 1,0 (allmän tillgänglighet)

### <a name="plan-for-change"></a>Planera för ändring

- Stöd för förhands gransknings agenter (alla versioner som är äldre än 1,0) tas bort i en framtida tjänst uppdatering.
- Stöd har tagits bort för återställnings slut punkt `.azure-automation.net` . Om du har en proxyserver måste du tillåta slut punkten `*.his.arc.azure.com` .
- Om den anslutna dator agenten är installerad på en virtuell dator som finns i Azure, kan VM-tillägg inte installeras eller ändras från resursen Arc-aktiverade servrar. Detta är för att undvika motstridiga tilläggs åtgärder som utförs från den virtuella datorns **Microsoft. Compute** -och **Microsoft. HybridCompute** -resurs. Använd **Microsoft. Compute** -resursen för datorn för alla förlängnings åtgärder.
- Namnet på gäst konfigurations processen har ändrats från *GCD* till *gcad* på Linux och *gcservice* till *gcarcservice* i Windows.

### <a name="new-feature"></a>Ny funktion

- Alternativet har lagts `azcmagent logs` till för att samla in information om support.
- Alternativet har lagts `azcmagent license` till för att Visa EULA.
- Alternativet har lagts `azcmagent show --json` till för tillstånd för utmatnings agent i lätt att tolkas.
- Flagga har lagts `azcmagent show` till i utdata för att ange om servern finns på en virtuell dator som finns i Azure.
- Alternativet har lagts `azcmagent disconnect --force-local-only` till för att tillåta återställning av lokalt agent tillstånd när det inte går att nå Azure-tjänsten.
- Alternativet har lagts `azcmagent connect --cloud` till för att stödja andra moln. I den här versionen stöds endast Azure av service i samband med agent versionen.
- Agenten har lokaliserats till Azure-språk som stöds.

### <a name="fixed"></a>Fast

- Förbättringar av anslutnings kontrollen.
- Korrigerat problem med proxyserverinställningar försvinner när du uppgraderar agenten på Linux.
- Lösta problem vid försök att installera agenten på en server som kör Windows Server 2012 R2.
- Förbättringar av ökad tillförlitlighet för tillägg

## <a name="august-2020"></a>Augusti 2020

Version: 0,11

- Den här versionen meddelade tidigare stöd för Ubuntu 20,04. Eftersom vissa Azure VM-tillägg inte stöder Ubuntu 20,04 tas stöd för den här versionen av Ubuntu bort.

- Tillförlitlighets förbättringar för tilläggs distributioner.

### <a name="known-issues"></a>Kända problem

Om du använder en äldre version av Linux-agenten och den har kon figurer ATS för att använda en proxyserver måste du konfigurera om inställningen för proxyservern efter uppgraderingen. Det gör du genom att köra `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Nästa steg

Innan du utvärderar eller aktiverar Arc-aktiverade servrar över flera hybrid datorer kan du läsa [Översikt över anslutna dator agenter](agent-overview.md) för att förstå krav, teknisk information om agenten och distributions metoder.