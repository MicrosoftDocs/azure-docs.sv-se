---
title: Så här migrerar du Azure Arc-aktiverade servrar över regioner
description: Lär dig hur du migrerar en Azure Arc-aktiverad server från en region till en annan.
ms.date: 02/10/2021
ms.topic: conceptual
ms.openlocfilehash: 251a347205d93af715add52db293d8000438df44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101650183"
---
# <a name="how-to-migrate-azure-arc-enabled-servers-across-regions"></a>Så här migrerar du Azure Arc-aktiverade servrar över regioner

Det finns scenarier där du vill flytta din befintliga Azure Arc-aktiverade Server från en region till en annan. Exempelvis har du realiserat datorn i fel region, för att förbättra hanterbarheten eller för att kunna ta hänsyn till styrnings orsaker.

Om du vill migrera en Azure Arc-aktiverad server från en Azure-region till en annan måste du avinstallera VM-tilläggen, ta bort resursen i Azure och återskapa den i den andra regionen. Innan du utför de här stegen bör du granska datorn för att kontrol lera vilka VM-tillägg som är installerade.

> [!NOTE]
> När de installerade tilläggen fortsätter att köras och utföra sin normala åtgärd när proceduren har slutförts kan du inte hantera dem. Om du försöker distribuera om tilläggen på datorn kan det uppstå oförutsägbara beteenden.

## <a name="move-machine-to-other-region"></a>Flytta datorn till en annan region

> [!NOTE]
> Under den här åtgärden resulterar det i stillestånds tid under migreringen.

1. Ta bort VM-tillägg som är installerade från [Azure Portal](manage-vm-extensions-portal.md#uninstall-extension), med hjälp av [Azure CLI](manage-vm-extensions-cli.md#remove-an-installed-extension)eller med [Azure PowerShell](manage-vm-extensions-powershell.md#remove-an-installed-extension).

2. Använd **azcmagent** -verktyget med från [kopplings](manage-agent.md#disconnect) parametern för att koppla bort datorn från Azure-bågen och ta bort dator resursen från Azure. Om du kopplar från datorn från Arc-aktiverade servrar tas inte den anslutna dator agenten bort, och du behöver inte ta bort agenten som en del av den här processen. Du kan köra det här manuellt när du är inloggad interaktivt eller automatiserar användningen av samma huvud namn för tjänsten som du använde för att publicera flera agenter eller med [en åtkomsttoken för Microsoft Identity Platform.](../../active-directory/develop/access-tokens.md) Om du inte använde ett huvud namn för tjänsten för att registrera datorn med Azure Arc-aktiverade servrar, kan du läsa följande [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) för att skapa ett huvud namn för tjänsten.

3. Registrera den anslutna dator agenten på nytt med ARC-aktiverade servrar i den andra regionen. Kör `azcmagent` verktyget med [Connect](manage-agent.md#connect) -parametern slutför det här steget.

4. Distribuera om de VM-tillägg som ursprungligen distribuerades till datorn från Arc-aktiverade servrar. Om du har distribuerat Azure Monitor for VMs-agenten (insikter) eller Log Analytics agent med hjälp av en Azure-princip distribueras agenterna om efter nästa [utvärderings cykel](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med VM](../../azure-monitor/vm/vminsights-enable-policy.md) -principen och mycket mer.