---
title: Ansluta hybriddatorer till Azure i stor skala
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med Azure Arc-aktiverade servrar med hjälp av ett huvudnamn för tjänsten.
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4aad01fd6991c059b2cf891fd4f06ae83a78a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831609"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Ansluta hybriddatorer till Azure i stor skala

Du kan aktivera Azure Arc-aktiverade servrar för flera Windows- eller Linux-datorer i din miljö med flera flexibla alternativ beroende på dina krav. Med hjälp av mallskriptet som vi tillhandahåller kan du automatisera varje steg i installationen, inklusive att upprätta anslutningen till Azure Arc. Du måste dock köra skriptet interaktivt med ett konto som har förhöjd behörighet på måldatorn och i Azure.

Om du vill ansluta datorerna Azure Arc-aktiverade servrar kan du [](../../active-directory/develop/app-objects-and-service-principals.md) använda ett Azure Active Directory för tjänstens huvudnamn i stället för att använda din privilegierade identitet för att [interaktivt ansluta datorn](onboard-portal.md). Ett huvudnamn för tjänsten är en särskild begränsad hanteringsidentitet som endast beviljas den minsta behörighet som krävs för att ansluta datorer till Azure med hjälp av `azcmagent` kommandot . Det här är säkrare än att använda ett konto med högre privilegier, till exempel en innehavaradministratör, och följer våra metodtips för åtkomstkontrollsäkerhet. Tjänstens huvudnamn används endast under onboarding- och används inte för något annat ändamål.  

Installationsmetoderna för att installera och konfigurera Connected Machine-agenten kräver att den automatiserade metoden som du använder har administratörsbehörighet på datorerna. I Linux använder du rotkontot och i Windows som medlem i gruppen Lokala administratörer.

Innan du börjar bör du granska kraven [och kontrollera](agent-overview.md#prerequisites) att din prenumeration och dina resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds.](overview.md#supported-regions) Läs även vår [planeringsguide i stor skala](plan-at-scale-deployment.md) för att förstå design- och distributionsvillkoren, samt våra rekommendationer för hantering och övervakning.  

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Skapa ett huvudnamn för tjänsten för registrering i stor skala

Du kan använda [Azure PowerShell](/powershell/azure/install-az-ps) för att skapa ett huvudnamn för tjänsten med cmdleten [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Eller så kan du följa stegen under Skapa [ett huvudnamn för tjänsten med hjälp Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) för att slutföra den här uppgiften.

> [!NOTE]
> Innan du skapar ett huvudnamn för tjänsten  måste  ditt konto vara medlem i rollen Ägare eller Administratör för användaråtkomst i den prenumeration som du vill använda för registrering. Om du inte har tillräcklig behörighet för att konfigurera rolltilldelningar kan tjänstens huvudnamn skapas, men det kan inte publicera datorer.
>

Utför följande steg för att skapa tjänstens huvudnamn med Hjälp av PowerShell.

1. Kör följande kommando. Du måste lagra utdata från cmdleten i en variabel, annars kan du inte hämta lösenordet [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) som behövs i ett senare steg.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Kör följande kommando för att hämta lösenordet `$sp` som lagras i variabeln:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Leta reda på lösenordsvärdet under fältets lösenord i **utdata och** kopiera det. Hitta även värdet under fältet **ApplicationId och** kopiera det också. Spara dem till senare på en säker plats. Om du glömmer eller förlorar lösenordet för tjänstens huvudnamn kan du återställa det med [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) hjälp av cmdleten .

Värdena från följande egenskaper används med parametrar som skickas till `azcmagent` :

* Värdet från egenskapen **ApplicationId** används för `--service-principal-id` parametervärdet
* Värdet från **lösenordsegenskapen** används för  `--service-principal-secret` parametern som används för att ansluta agenten.

> [!NOTE]
> Se till att använda **applicationId-egenskapen** för tjänstens huvudnamn, inte **id-egenskapen.**
>

Den **Azure Connected Machine onboarding-rollen** innehåller bara de behörigheter som krävs för att registrera en dator. Du kan tilldela behörigheten för tjänstens huvudnamn så att dess omfång kan innehålla en resursgrupp eller en prenumeration. Information om hur du lägger till [rolltilldelning finns i Tilldela Azure-roller med hjälp Azure Portal](../../role-based-access-control/role-assignments-portal.md) tilldela [Azure-roller med hjälp av Azure CLI.](../../role-based-access-control/role-assignments-cli.md)

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generera installationsskriptet från Azure Portal

Skriptet för att automatisera nedladdningen och installationen, samt för att upprätta anslutningen Azure Arc, är tillgängligt från Azure Portal. Utför följande steg för att slutföra processen:

1. Från webbläsaren går du till [Azure Portal](https://portal.azure.com).

1. På sidan **Servrar - Azure Arc** väljer du Lägg **till** längst upp till vänster.

1. På sidan **Välj en metod** väljer du panelen Lägg till flera **servrar** och sedan **Generera skript.**

1. På sidan **Generera skript** väljer du den prenumeration och resursgrupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras. Den här platsen kan vara samma eller en annan som resursgruppens plats.

1. På sidan **Förutsättningar granskar** du informationen och väljer sedan **Nästa: Resursinformation.**

1. På **sidan Resursinformation** anger du följande:

    1. I **listrutan** Resursgrupp väljer du den resursgrupp som datorn ska hanteras från.
    1. I **listrutan Region** väljer du den Azure-region där metadata för servrarna ska lagras.
    1. I **listrutan** Operativsystem väljer du det operativsystem som skriptet är konfigurerat att köras på.
    1. Om datorn kommunicerar via en proxyserver för att ansluta till Internet anger du proxyserverns IP-adress eller det namn och portnummer som datorn ska använda för att kommunicera med proxyservern. Ange värdet i formatet `http://<proxyURL>:<proxyport>` .
    1. Välj **Nästa: Autentisering**.

1. På sidan **Autentisering** går du till **listrutan för** tjänstens huvudnamn och väljer **Arc-for-servers**.  Välj sedan **Nästa: Taggar**.

1. På sidan **Taggar granskar**  du de föreslagna standardtaggarna för fysisk plats och anger ett värde, eller anger en eller flera **anpassade taggar** som stöd för dina standarder.

1. Välj **Nästa: Ladda ned och kör skriptet**.

1. På sidan **Ladda ned och kör skript** granskar du sammanfattningsinformationen och väljer sedan Ladda **ned.** Om du fortfarande behöver göra ändringar väljer du **Föregående**.

För Windows uppmanas du att spara `OnboardingScript.ps1` och för Linux på `OnboardingScript.sh` datorn.

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och ansluta till Azure

Med skriptmallen som skapades tidigare kan du installera och konfigurera connected machine-agenten på flera Linux- och Windows-hybriddatorer med hjälp av det automatiseringsverktyg som din organisation föredrar. Skriptet utför liknande steg som beskrivs i artikeln [Ansluta hybriddatorer till Azure Azure Portal](onboard-portal.md) artikeln. Skillnaden är i det sista steget, där du upprättar anslutningen till Azure Arc med kommandot `azcmagent` med hjälp av tjänstens huvudnamn.

Följande är de inställningar som du konfigurerar kommandot `azcmagent` för att använda för tjänstens huvudnamn.

* `service-principal-id` : Den unika identifierare (GUID) som representerar program-ID för tjänstens huvudnamn.
* `service-principal-secret` | Lösenordet för tjänstens huvudnamn.
* `tenant-id` : Den unika identifierare (GUID) som representerar din dedikerade instans av Azure AD.
* `subscription-id` : Prenumerations-ID :t (GUID) för din Azure-prenumeration som du vill ha datorerna i.
* `resource-group` : Resursgruppens namn där du vill att dina anslutna datorer ska tillhöra.
* `location`: Se [Azure-regioner som stöds.](overview.md#supported-regions) Den här platsen kan vara samma eller en annan som resursgruppens plats.
* `resource-name` : (*Valfritt*) Används för Azure-resursrepresentationen för din lokala dator. Om du inte anger det här värdet används datorns värdnamn.

Du kan läsa mer om `azcmagent` kommandoradsverktyget i [Referens för Azcmagent.](./manage-agent.md)

>[!NOTE]
>Skriptet Windows PowerShell endast stöd för körning från en 64-bitars version av Windows PowerShell.
>

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrollera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

![En lyckad serveranslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Felsökningsinformation finns i guiden Felsöka [Connected Machine-agenten.](troubleshoot-agent-onboard.md)

- Lär dig hur du hanterar din dator med [hjälp av Azure Policy,](../../governance/policy/overview.md)till exempel gästkonfiguration för virtuella [datorer,](../../governance/policy/concepts/guest-configuration.md)kontrollerar att datorn rapporterar till den förväntade Log Analytics-arbetsytan, aktiverar övervakning med Azure Monitor [med virtuella](../../azure-monitor/vm/vminsights-enable-policy.md)datorer och mycket mer.

- Läs mer om [Log Analytics-agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du vill samla in övervakningsdata för operativsystem och arbetsbelastningar med Azure Monitor for VMs, hantera dem med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering eller använda andra Azure-tjänster [som Azure Security Center](../../security-center/security-center-introduction.md).
