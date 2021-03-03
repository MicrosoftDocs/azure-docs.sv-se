---
title: Anslut dina datorer som inte är Azure-datorer till Azure Security Center
description: Lär dig hur du ansluter datorer som inte är Azure-datorer till Security Center
author: memildin
ms.author: memildin
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
zone_pivot_groups: non-azure-machines
ms.openlocfilehash: b5dd06c148c53517214b47b39f083c99d0f37e0b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712206"
---
# <a name="connect-your-non-azure-machines-to-security-center"></a>Anslut dina datorer som inte är Azure-datorer till Security Center

Security Center kan övervaka säkerhets position för datorer som inte är Azure-datorer, men först måste du ansluta dem till Azure.

Du kan ansluta datorer som inte är Azure-datorer på något av följande sätt:

- Använda Azure Arc-aktiverade servrar (**rekommenderas**)
- Från Security Center sidor i Azure Portal (**komma igång** och **inventering**)

Var och en av dem beskrivs på den här sidan.

::: zone pivot="azure-arc"

## <a name="add-non-azure-machines-with-azure-arc"></a>Lägg till datorer som inte är Azure-datorer med Azure Arc

Azure Arc-aktiverade servrar är det bästa sättet att lägga till icke-Azure-datorer till Azure Security Center.

En dator med Azure Arc-aktiverade servrar blir en Azure-resurs och visas i Security Center med rekommendationer som dina andra Azure-resurser.

Dessutom tillhandahåller Azure Arc-aktiverade servrar förbättrade funktioner, till exempel möjligheten att aktivera principer för gäst konfiguration på datorn, distribuera Log Analytics-agenten som ett tillägg, förenkla distribution med andra Azure-tjänster med mera. En översikt över fördelarna finns i scenarier som [stöds](../azure-arc/servers/overview.md#supported-scenarios).

Läs mer om [Azure Arc-aktiverade servrar](../azure-arc/servers/overview.md).

**Så här distribuerar du Azure-bågen:**

- Följ anvisningarna i [snabb start: Anslut hybrid datorer med Azure Arc-aktiverade servrar](../azure-arc/servers/learn/quick-enable-hybrid-vm.md)för en dator.
- Om du vill ansluta flera datorer till en skala till Arc-aktiverade servrar, se [ansluta hybrid datorer till Azure i stor skala](../azure-arc/servers/onboard-service-principal.md)

> [!TIP]
> Om du registrerar datorer som körs på AWS hanterar Security Centers anslutningen för AWS transparent Azure Arc-distributionen åt dig. Läs mer i [ansluta dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md).

::: zone-end

::: zone pivot="azure-portal"

## <a name="add-non-azure-machines-from-the-azure-portal"></a>Lägg till datorer som inte är Azure-datorer från Azure Portal

1. Från Security Center menyn öppnar du sidan **komma igång** .
1. Välj fliken **Kom igång**.

    :::image type="content" source="./media/security-center-onboarding/onboarding-get-started-tab.png" alt-text="Fliken kom igång på sidan komma igång" lightbox="./media/security-center-onboarding/onboarding-get-started-tab.png":::

1. Under **Lägg till icke-Azure-servrar** väljer du **Konfigurera** .

    > [!TIP]
    > Du kan också öppna Lägg till datorer från sidan för att **lägga till icke-Azure-servrar** på sidan **lager** .
    > 
    > :::image type="content" source="./media/security-center-onboarding/onboard-inventory.png" alt-text="Lägga till icke-Azure-datorer från sidan till gångs inventering":::

    En lista över dina Log Analytics-arbetsytor visas. Om det är tillämpligt innehåller listan standardarbetsytan som har skapats för dig av Security Center när automatisk etablering aktiverades. Välj den här arbetsytan eller någon annan arbetsyta som du vill använda.

    Du kan lägga till datorer till en befintlig arbets yta eller skapa en ny arbets yta.

1. Du kan också skapa en ny arbets yta genom att välja  **Skapa ny arbets yta**.

1. I listan över arbets ytor väljer du **Lägg till servrar** för den aktuella arbets ytan.
    Sidan **hantering av agenter** visas.

    Härifrån väljer du den relevanta proceduren nedan beroende på vilken typ av datorer du registrerar:

    - [Publicera dina Azure Stack virtuella datorer](#onboard-your-azure-stack-vms)
    - [Publicera dina Linux-datorer](#onboard-your-linux-machines)
    - [Publicera dina Windows-datorer](#onboard-your-windows-machines)

### <a name="onboard-your-azure-stack-vms"></a>Publicera dina Azure Stack virtuella datorer

Om du vill lägga till Azure Stack virtuella datorer behöver du informationen på sidan **hantering av agenter** och konfigurera **Azure Monitor, uppdatering och konfigurations hantering** för virtuella datorer på de virtuella datorer som körs på din Azure Stack.

1. På sidan **hantering av agenter** kopierar du **arbets ytans ID** och **primär nyckel** till anteckningar.
1. Logga in på **Azure Stack** Portal och öppna sidan **virtuella datorer** .
1. Välj den virtuella dator som du vill skydda med Security Center.
    >[!TIP]
    > Information om hur du skapar en virtuell dator på Azure Stack finns i [den här snabb starten för virtuella Windows-datorer](/azure-stack/user/azure-stack-quick-windows-portal) eller [den här snabb starten för virtuella Linux-datorer](/azure-stack/user/azure-stack-quick-linux-portal).
1. Välj **Tillägg**. Listan över virtuella dator tillägg som är installerade på den här virtuella datorn visas.
1. Välj fliken **Lägg till** . Menyn **Ny resurs** visar en lista över tillgängliga tillägg för virtuella datorer.
1. Välj tillägget **Azure Monitor, uppdatering och konfigurations hantering** och välj **skapa**. Sidan **installera tillägg** konfiguration öppnas.
    >[!NOTE]
    > Om du inte ser tillägget **Azure Monitor, uppdatering och konfigurations hantering** som visas på din Marketplace, kan du kontakta din Azure Stack-operatör för att göra det tillgängligt.
1. På sidan **installera tillägg** -konfiguration klistrar du in **arbets ytans ID** och den **arbets ytans nyckel (primär nyckel)** som du kopierade i anteckningar i föregående steg.
1. När du har slutfört konfigurationen väljer du **OK**. Tilläggets status visas som **etableringen har slutförts**. Det kan ta upp till en timme innan den virtuella datorn visas i Security Center.

### <a name="onboard-your-linux-machines"></a>Publicera dina Linux-datorer

Om du vill lägga till Linux-datorer behöver du kommandot WGET från **hanterings** sidan för agenter.

1. På sidan **hantering av agenter** kopierar du kommandot **WGET** till Notepad. Spara den här filen på en plats som kan nås från din Linux-dator.
1. På Linux-datorn öppnar du filen med kommandot WGET. Markera hela innehållet och kopiera och klistra in det i en Terminal-konsol.
1. När installationen är klar kan du kontrol lera att *omsagent* har installerats genom att köra kommandot *pgrep* . Kommandot returnerar *omsagent* PID.
    Loggarna för agenten finns på: */var/opt/Microsoft/omsagent/ \<workspace id> /log/* det kan ta upp till 30 minuter innan den nya Linux-datorn visas i Security Center.

### <a name="onboard-your-windows-machines"></a>Publicera dina Windows-datorer

Om du vill lägga till Windows-datorer behöver du informationen på sidan **hantering av agenter** och för att ladda ned lämplig agent fil (32/64-bitars).
1. Välj den länk för att **ladda ned Windows-agent** som gäller för din typ av processor, så att du kan ladda ned installationsfilen.
1. På sidan **hantering av agenter** kopierar du **arbets ytans ID** och **primär nyckel** till anteckningar.
1. Kopiera den nedladdade installations filen till mål datorn och kör den.
1. Följ installations guiden (**Nästa**, **Jag accepterar** **Nästa**, **Nästa)**.
    1. På sidan **Azure Log Analytics** klistrar du in **arbets ytans ID** och den **arbets ytans nyckel (primär nyckel)** som du kopierade i anteckningar.
    1. Om datorn ska rapportera till en Log Analytics arbets yta i Azure Government molnet väljer du **Azure amerikanska myndigheter** i list rutan **Azure-moln** .
    1. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten väljer du **Avancerat** och anger URL och portnummer för proxyservern.
    1. När du har angett alla konfigurations inställningar väljer du **Nästa**.
    1. På sidan **klar att installera** granskar du de inställningar som ska tillämpas och väljer **Installera**.
    1. På sidan **konfigurationen har slutförts** väljer du **Slutför**.

När du är klar visas **Microsoft Monitoring Agent** på **kontroll panelen**. Du kan granska konfigurationen där och bekräfta att agenten är ansluten.

Mer information om hur du installerar och konfigurerar agenten finns i [ansluta Windows-datorer](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard).

::: zone-end

## <a name="verifying"></a>Verifiera

Grattis! Nu kan du se dina Azure-och icke-Azure-datorer tillsammans på en och samma plats. Öppna [sidan Asset Inventory](asset-inventory.md) och filtrera till relevanta resurs typer. Dessa ikoner särskiljer typerna:

  ![ASC-ikon för icke-Azure-dator](./media/quick-onboard-linux-computer/security-center-monitoring-icon1.png) Icke-Azure-dator

  ![ASC-ikon för Azure Machine](./media/quick-onboard-linux-computer/security-center-monitoring-icon2.png) Azure VM

  ![ASC-ikon för Azure båg Server](./media/quick-onboard-linux-computer/arc-enabled-machine-icon.png) Azure Arc-aktiverad server

## <a name="next-steps"></a>Nästa steg

Den här sidan visar hur du lägger till datorer som inte är Azure-datorer till Azure Security Center. Om du vill övervaka deras status använder du inventerings verktygen som beskrivs på följande sida:

- [Utforska och hantera dina resurser med till gångs inventering](asset-inventory.md)