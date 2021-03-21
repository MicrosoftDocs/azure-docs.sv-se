---
title: Ansluta hybrid datorer till Azure från Windows administrations Center
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter datorer till Azure med hjälp av Azure Arc-aktiverade servrar från administrations Center för Windows.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 5fa7a61b1e3b22503377cbcbe308a82be89dac72
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100584771"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Ansluta hybrid datorer till Azure från Windows administrations Center

Du kan aktivera Azure Arc-aktiverade servrar för en eller flera Windows-datorer i din miljö genom att utföra en uppsättning steg manuellt. Du kan också använda [Windows administrations Center](/windows-server/manage/windows-admin-center/understand/what-is) för att distribuera den anslutna dator agenten och registrera dina lokala servrar utan att behöva utföra några steg utanför det här verktyget.

## <a name="prerequisites"></a>Förutsättningar

* Arc-aktiverade servrar – granska [förutsättningarna](agent-overview.md#prerequisites) och kontrol lera att din prenumeration, ditt Azure-konto och resurser uppfyller kraven.

* Windows administrations Center – granska kraven för att [förbereda din miljö](/windows-server/manage/windows-admin-center/deploy/prepare-environment) för att distribuera och [Konfigurera Azure-integrering ](/windows-server/manage/windows-admin-center/azure/azure-integration).

* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* De mål Windows-servrar som du vill hantera måste ha Internet anslutning för att få åtkomst till Azure.

### <a name="security"></a>Säkerhet

Den här distributions metoden kräver att du har administratörs behörighet på mål datorn eller servern för Windows för att installera och konfigurera agenten. Du måste också vara medlem i rollen Gateway- [**användare**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) .

## <a name="deploy"></a>Distribuera

Utför följande steg för att konfigurera Windows Server med ARC-aktiverade servrar.

1. Logga in på Windows administrations Center.

1. Från listan anslutning på sidan **Översikt** väljer du en server i listan för att ansluta till den i listan över anslutna Windows-servrar.

1. Välj **Azure Hybrid Services** i den vänstra rutan.

1. På sidan **Azure Hybrid Services** väljer du **identifiera Azure-tjänster**.

1. På sidan **identifiera Azure-tjänster** , under **utnyttja Azure-principer och lösningar för att hantera dina servrar med Azure-båge**, väljer du **Konfigurera**.

1. På sidan **Settings\Azure båge för servrar** , om du uppmanas att autentisera till Azure och välj sedan **Kom igång**.

1. På sidan **Anslut server till Azure** anger du följande:

    1. I list rutan **Azure-prenumeration** väljer du Azure-prenumerationen.
    1. För **resurs grupp** väljer du antingen **ny** för att skapa en ny resurs grupp eller under List rutan **resurs grupp** väljer du en befintlig resurs grupp för att registrera och hantera datorn från.
    1. I list rutan **region** väljer du den Azure-region där du vill lagra metadata för servrarna.
    1. Om datorn eller servern kommunicerar via en proxyserver för att ansluta till Internet väljer du alternativet **Använd proxyserver**. Ange IP-adressen för proxyservern eller namnet och port numret som datorn ska använda för att kommunicera med proxyservern.

1. Välj **Konfigurera** för att fortsätta med konfigurationen av Windows Server med Azure Arc-aktiverade servrar.

Windows Server kommer att ansluta till Azure, ladda ned den anslutna dator agenten, installera den och registrera dig för Azure Arc-aktiverade servrar. För att spåra förloppet väljer du **meddelanden** på menyn.

För att bekräfta installationen av den anslutna dator agenten i Windows administrations Center väljer du [**händelser**](/windows-server/manage/windows-admin-center/use/manage-servers#events) i rutan till vänster för att granska *MsiInstaller* -händelser i program händelse loggen.

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa datorn i [Azure Portal](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="En lyckad dator anslutning" border="false":::

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md)och mycket mer.

* Läs mer om den [Log Analytics agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill samla in operativ system och data för övervakning av arbets belastning, hantera dem med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).