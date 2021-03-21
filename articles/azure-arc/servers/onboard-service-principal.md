---
title: Ansluta hybrid datorer till Azure i stor skala
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med hjälp av Azure Arc-aktiverade servrar med ett huvud namn för tjänsten.
ms.date: 03/04/2021
ms.topic: conceptual
ms.openlocfilehash: c1ad3d4619896ff46db266789a17bfca80712e70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175948"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Ansluta hybrid datorer till Azure i stor skala

Du kan aktivera Azure Arc-aktiverade servrar för flera Windows-eller Linux-datorer i din miljö med flera flexibla alternativ beroende på dina behov. Med hjälp av det mall-skript som vi tillhandahåller kan du automatisera alla steg i installationen, inklusive att upprätta anslutningen till Azure-bågen. Du måste dock köra det här skriptet interaktivt med ett konto som har förhöjd behörighet på mål datorn och i Azure.

Om du vill ansluta datorerna till Azure Arc-aktiverade servrar kan du använda ett Azure Active Directory [tjänstens huvud namn](../../active-directory/develop/app-objects-and-service-principals.md) i stället för att använda din privilegierade identitet för att [ansluta datorn interaktivt](onboard-portal.md). Ett tjänst huvud namn är en särskild begränsad hanterings identitet som endast beviljas den lägsta behörighet som krävs för att ansluta datorer till Azure med hjälp av `azcmagent` kommandot. Detta är säkrare än att använda ett högre privilegierat konto, till exempel en innehavaradministratör, och följer våra bästa metoder för åtkomst kontroll. Tjänstens huvud namn används bara vid onboarding, det används inte i något annat syfte.  

Installations metoderna för att installera och konfigurera den anslutna dator agenten kräver att den automatiserade metoden du använder har administratörs behörighet på datorerna. I Linux, med hjälp av rot kontot och Windows, som medlem i den lokala administratörs gruppen.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](agent-overview.md#prerequisites) resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds](overview.md#supported-regions). Se även vår [planerings guide](plan-at-scale-deployment.md) för utveckling och förståelse för design-och distributions kriterier samt våra rekommendationer för hantering och övervakning.  

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Skapa ett huvud namn för tjänsten för onboarding i skala

Du kan använda [Azure PowerShell](/powershell/azure/install-az-ps) för att skapa ett huvud namn för tjänsten med cmdleten [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . Du kan också följa stegen som visas under [skapa ett huvud namn för tjänsten med hjälp av Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) för att slutföra den här aktiviteten.

> [!NOTE]
> Innan du skapar ett huvud namn för tjänsten måste ditt konto vara medlem i rollen **ägare** eller **administratör för användar åtkomst** i den prenumeration som du vill använda för onboarding. Om du inte har behörighet att konfigurera roll tilldelningar kan tjänstens huvud namn skapas, men de kan inte publicera datorer.
>

Utför följande steg för att skapa tjänstens huvud namn med PowerShell.

1. Kör följande kommando. Du måste lagra utdata från [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdleten i en variabel, annars kommer du inte att kunna hämta lösen ordet som krävs i ett senare steg.

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

2. Kör följande kommando för att hämta det lösen ord som lagras i `$sp` variabeln:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. I utdata söker du efter lösen ordets värde under fältet **lösen ord** och kopierar det. Hitta även värdet under fältet **ApplicationId** och kopiera det också. Spara dem för senare på ett säkert ställe. Om du glömmer bort eller förlorar lösen ordet för tjänstens huvud namn kan du återställa det med hjälp av [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdleten.

Värdena från följande egenskaper används med parametrar som skickas till `azcmagent` :

* Värdet från egenskapen **ApplicationId** används för `--service-principal-id` parametervärdet
* Värdet från **lösen ords** egenskapen används för den parameter som  `--service-principal-secret` används för att ansluta agenten.

> [!NOTE]
> Se till att använda egenskapen **ApplicationId** för tjänstens huvud namn, inte egenskapen **ID** .
>

Den **Azure-anslutna dator onboarding** -rollen innehåller bara de behörigheter som krävs för att publicera en dator. Du kan tilldela tjänstens huvud namn behörighet att tillåta att dess omfång inkluderar en resurs grupp eller en prenumeration. Information om hur du lägger till roll tilldelning finns i [tilldela Azure-roller med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md) eller [tilldela Azure-roller med Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generera installations skriptet från Azure Portal

Skriptet för att automatisera nedladdningen och installationen, och för att upprätta anslutningen till Azure Arc, är tillgängligt från Azure Portal. Slutför processen genom att utföra följande steg:

1. Gå till [Azure Portal](https://portal.azure.com)i webbläsaren.

1. På sidan **servrar – Azure-båge** väljer du **Lägg till** längst upp till vänster.

1. På sidan **Välj en metod** väljer du panelen **Lägg till flera servrar** och väljer sedan **skapa skript**.

1. På sidan **skapa skript** väljer du den prenumeration och resurs grupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras. Den här platsen kan vara samma eller olika, som resurs gruppens plats.

1. På sidan **förutsättningar** granskar du informationen och väljer sedan **Nästa: resursinformation**.

1. På sidan **Resursinformation** anger du följande:

    1. I list rutan **resurs grupp** väljer du den resurs grupp som datorn ska hanteras från.
    1. I list rutan **region** väljer du den Azure-region där du vill lagra metadata för servrarna.
    1. I list rutan **operativ system** väljer du det operativ system som skriptet är konfigurerat för att köras på.
    1. Om datorn kommunicerar via en proxyserver för att ansluta till Internet anger du IP-adressen för proxyservern eller det namn och port nummer som datorn ska använda för att kommunicera med proxyservern. Ange värdet i formatet `http://<proxyURL>:<proxyport>` .
    1. Välj **Nästa: autentisering**.

1. På sidan **autentisering** under List rutan **tjänst huvud** väljer du **Arc-for-servers**.  Välj sedan **Nästa: Taggar**.

1. På sidan **taggar** granskar du standard **koderna för fysiska platser** och anger ett värde, eller så anger du en eller flera **anpassade taggar** som stöder dina standarder.

1. Välj **Nästa: Ladda ned och kör skript**.

1. På sidan **Ladda ned och kör skript** granskar du sammanfattnings informationen och väljer sedan **Hämta**. Om du fortfarande behöver göra ändringar väljer du **föregående**.

För Windows uppmanas du att spara `OnboardingScript.ps1` och för Linux `OnboardingScript.sh` till din dator.

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och Anslut till Azure

Genom att ta bort den skript mal len som skapats tidigare kan du installera och konfigurera den anslutna dator agenten på flera hybrid Linux-och Windows-datorer med hjälp av det önskade automatiserings verktyget för organisationer. Skriptet utför liknande steg som beskrivs i [ansluta hybrid datorer till Azure från Azure Portal](onboard-portal.md) artikeln. Skillnaden är i det sista steget, där du upprättar anslutningen till Azure-bågen med hjälp av `azcmagent` kommandot med hjälp av tjänstens huvud namn.

Följande är de inställningar som du konfigurerar `azcmagent` kommandot som ska användas för tjänstens huvud namn.

* `service-principal-id` : Den unika identifieraren (GUID) som representerar program-ID: t för tjänstens huvud namn.
* `service-principal-secret` | Lösen ordet för tjänstens huvud namn.
* `tenant-id` : Den unika identifieraren (GUID) som representerar din dedikerade instans av Azure AD.
* `subscription-id` : Prenumerations-ID (GUID) för den Azure-prenumeration som du vill ha datorerna i.
* `resource-group` : Namnet på den resurs grupp där du vill att dina anslutna datorer ska tillhöra.
* `location` : Se [Azure-regioner som stöds](overview.md#supported-regions). Den här platsen kan vara samma eller olika, som resurs gruppens plats.
* `resource-name` : (*Valfritt*) som används för Azures resurs åter givning av din lokala dator. Om du inte anger det här värdet används datorns värdnamn.

Du kan lära dig mer om `azcmagent` kommando rads verktyget genom att granska [Azcmagent-referensen](./manage-agent.md).

>[!NOTE]
>Windows PowerShell-skriptet stöder bara körning från en 64-bitars version av Windows PowerShell.
>

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

- Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), för t. ex. [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)för virtuella datorer, kontrol lera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/vm/vminsights-enable-policy.md)och mycket mer.

- Läs mer om den [Log Analytics agenten](../../azure-monitor/agents/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill samla in operativ system och data för arbets belastnings övervakning med Azure Monitor for VMs, hantera den med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering, eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).
