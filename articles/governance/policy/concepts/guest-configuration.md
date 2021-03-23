---
title: Lär dig att granska innehållet i virtuella datorer
description: Lär dig hur Azure Policy använder klienten för gäst konfiguration för att granska inställningar i virtuella datorer.
ms.date: 01/14/2021
ms.topic: conceptual
ms.openlocfilehash: a18f230c1b7b1eb2c953542e276127f4f47cbb39
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802530"
---
# <a name="understand-azure-policys-guest-configuration"></a>Om Azure Policys gästkonfiguration


Azure Policy kan granska inställningar i en dator, både för datorer som körs i Azure och [Arc-anslutna datorer](../../../azure-arc/servers/overview.md). Verifieringen utförs av gästkonfigurationstillägget och klienten. Tillägget kontrollerar inställningar via klienten, till exempel:

- Operativsystemets konfiguration
- Programkonfiguration eller förekomst
- Miljöinställningar

För närvarande har de flesta Azure Policy endast gransknings inställningar på datorn. De tillämpar inte konfigurationer. Undantaget är en inbyggd princip som [refereras nedan](#applying-configurations-using-guest-configuration).

[Det finns en video genom gång av det här dokumentet](https://youtu.be/Y6ryD3gTHOs).

## <a name="enable-guest-configuration"></a>Aktivera gäst konfiguration

Om du vill granska statusen för datorer i din miljö, inklusive datorer i Azure och Arc-anslutna datorer, kan du läsa följande information.

## <a name="resource-provider"></a>Resursprovider

Innan du kan använda gäst konfiguration måste du registrera resurs leverantören. Om tilldelningen av en princip för gäst konfiguration görs via portalen, eller om prenumerationen har registrerats i Azure Security Center, registreras resurs leverantören automatiskt. Du kan registrera dig manuellt via [portalen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure POWERSHELL](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)eller [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Distribuera krav för virtuella Azure-datorer

Om du vill granska inställningarna i en dator är ett [tillägg för virtuell dator](../../../virtual-machines/extensions/overview.md) aktiverat och datorn måste ha en Systemhanterad identitet. Tillägget hämtar tillämplig principtilldelning och motsvarande konfigurationsdefinition. Identiteten används för att autentisera datorn när den läser och skriver till gäst konfigurations tjänsten. Tillägget krävs inte för Arc-anslutna datorer eftersom det ingår i Arc Connected Machine agent.

> [!IMPORTANT]
> Gäst konfigurations tillägget och en hanterad identitet krävs för att granska virtuella Azure-datorer. Om du vill distribuera tillägget i skala tilldelar du följande princip initiativ:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Begränsningar som angetts för tillägget

Om du vill begränsa tillägget från att påverka program som körs på datorn får gäst konfigurationen inte överstiga mer än 5% CPU. Den här begränsningen finns för både inbyggda och anpassade definitioner. Detsamma gäller för gäst konfigurations tjänsten i Arc Connected Machine agent.

### <a name="validation-tools"></a>Verifierings verktyg

I datorn använder gäst konfigurations klienten lokala verktyg för att köra granskningen.

I följande tabell visas en lista över de lokala verktyg som används på varje operativ system som stöds. För inbyggt innehåll hanterar gäst konfigurationen inläsning av dessa verktyg automatiskt.

|Operativsystem|Validerings verktyg|Kommentarer|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| Sidan har lästs in till en mapp som endast används av Azure Policy. Är inte i konflikt med Windows PowerShell DSC. PowerShell-kärnan har inte lagts till i System Sök vägen.|
|Linux|[Chefs INSPEC](https://www.chef.io/inspec/)| Installerar chefs inspecens version 2.2.61 på standard platsen och läggs till i System Sök vägen. Beroenden för INSPEC-paketet inklusive ruby och python installeras också. |

### <a name="validation-frequency"></a>Validerings frekvens

Klienten för gäst konfiguration söker efter nya eller ändrade gäst tilldelningar var femte minut. När en gäst tilldelning har tagits emot, kontrol leras inställningarna för den konfigurationen på 15 minuters intervall. Resultat skickas till resurs leverantören för gäst konfigurationen när granskningen är klar. När en [utlösare](../how-to/get-compliance-data.md#evaluation-triggers) för princip utvärdering inträffar skrivs datorns tillstånd till resurs leverantören för gäst konfiguration. Den här uppdateringen gör att Azure Policy utvärdera Azure Resource Manager egenskaper. En utvärdering på begäran Azure Policy hämtar det senaste värdet från resurs leverantören för gäst konfigurationen. Den utlöser dock inte en ny granskning av konfigurationen på datorn. Statusen skrivs samtidigt till Azures resurs diagram.

## <a name="supported-client-types"></a>Klient typer som stöds

Princip definitioner för gäst konfiguration inkluderar nya versioner. Äldre versioner av operativ system som är tillgängliga på Azure Marketplace utesluts om gäst konfigurations klienten inte är kompatibel. I följande tabell visas en lista över operativ system som stöds på Azure-avbildningar:

|Publisher|Name|Versioner|
|-|-|-|
|Canonical|Ubuntu Server|14,04 – 20,04|
|Credativ|Debian|8 - 10|
|Microsoft|Windows Server|2012 – 2019|
|Microsoft|Windows-klient|Windows 10|
|OpenLogic|CentOS|7,3-8|
|Red Hat|Red Hat Enterprise Linux|7,4-8|
|SUSE|SLES|12 SP3-SP5, 15|

Anpassade avbildningar av virtuella datorer stöds av princip definitioner för gäst konfiguration så länge de är ett av operativ systemen i tabellen ovan.

## <a name="network-requirements"></a>Nätverkskrav

Virtuella datorer i Azure kan använda antingen det lokala nätverkskortet eller en privat länk för att kommunicera med gäst konfigurations tjänsten.

Azure Arc-datorer ansluter med den lokala nätverks infrastrukturen för att komma åt Azure-tjänster och rapportera kompatibilitetsstatus.

### <a name="communicate-over-virtual-networks-in-azure"></a>Kommunicera via virtuella nätverk i Azure

Virtuella datorer som använder virtuella nätverk för kommunikation kommer att kräva utgående åtkomst till Azure-datacenter på porten `443` . Om du använder ett privat virtuellt nätverk i Azure som inte tillåter utgående trafik, konfigurerar du undantag med regler för nätverks säkerhets grupper. Service tag-GuestAndHybridManagement kan användas för att referera till gäst konfigurations tjänsten.

### <a name="communicate-over-private-link-in-azure"></a>Kommunicera via privat länk i Azure

Virtuella datorer kan använda [privat länk](../../../private-link/private-link-overview.md) för kommunikation till gäst konfigurations tjänsten. Använd taggen med namnet `EnablePrivateNeworkGC` (utan "t" i nätverk) och värdet `TRUE` för att aktivera den här funktionen. Taggen kan tillämpas före eller efter princip definitioner för gäst konfiguration som tillämpas på datorn.

Trafiken dirigeras med hjälp av den [offentliga Azure-IP-adressen](../../../virtual-network/what-is-ip-address-168-63-129-16.md) för att upprätta en säker, autentiserad kanal med Azures plattforms resurser.

### <a name="azure-arc-connected-machines"></a>Azure Arc-anslutna datorer

Noder som finns utanför Azure och som är anslutna till Azure-bågen kräver anslutning till gäst konfigurations tjänsten. Information om nätverks-och proxy-krav som anges i [Azure Arc-dokumentationen](../../../azure-arc/servers/overview.md).

För att kunna kommunicera med resurs leverantören för gäst konfiguration i Azure måste datorer ha utgående åtkomst till Azure-datacenter på port **443**. Om ett nätverk i Azure inte tillåter utgående trafik konfigurerar du undantag med regler för [nätverks säkerhets grupper](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . [Service tag-](../../../virtual-network/service-tags-overview.md) GuestAndHybridManagement kan användas för att referera till gäst konfigurations tjänsten.

För Arc-anslutna servrar i privata data Center kan du tillåta trafik med följande mönster:

- Port: endast TCP 443 krävs för utgående Internet åtkomst
- Global URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Krav för hanterade identiteter

Princip definitioner i initiativet initiativ _distribuera krav för att aktivera principer för gäst konfiguration på virtuella datorer_ möjliggör en systemtilldelad hanterad identitet om den inte finns. Det finns två princip definitioner i det initiativ som hanterar skapandet av identitet. OM villkoren i princip definitionerna säkerställer rätt beteende baserat på dator resursens aktuella tillstånd i Azure.

Om datorn inte har några hanterade identiteter är den effektiva principen: [Lägg till systemtilldelad hanterad identitet för att aktivera gäst konfigurations tilldelningar på virtuella datorer utan identiteter](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Om datorn för närvarande har en användardefinierad system identitet blir den effektiva principen: [Lägg till systemtilldelad hanterad identitet för att aktivera gäst konfigurations tilldelningar på virtuella datorer med en tilldelad identitet](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Krav för konfigurations definition för gäst

Princip definitioner för gäst konfiguration använder **AuditIfNotExists** -effekter. När definitionen har tilldelats hanterar en backend-tjänst automatiskt livs cykeln för alla krav i `Microsoft.GuestConfiguration` Azure Resource Provider.

**AuditIfNotExists** princip definitioner returnerar inte några träffar förrän alla krav har uppfyllts på datorn. Kraven beskrivs i avsnittet [distribuera krav för Azure Virtual Machines](#deploy-requirements-for-azure-virtual-machines)

> [!IMPORTANT]
> I en tidigare version av gäst konfigurationen krävdes ett initiativ för att kombinera **DeployIfNoteExists** -och **AuditIfNotExists** -definitioner. **DeployIfNotExists** -definitioner krävs inte längre. Definitionerna och initiativen märks `[Deprecated]` men befintliga tilldelningar fortsätter att fungera. Mer information finns i blogg inlägget: [viktig ändring har lanserats för gransknings principer för gäst konfiguration](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

### <a name="what-is-a-guest-assignment"></a>Vad är en gäst tilldelning?

När en Azure Policy tilldelas, om den finns i kategorin "gäst konfiguration", finns det metadata som ingår för att beskriva en gäst tilldelning.
Du kan tänka på en gäst tilldelning som en länk mellan en dator och ett Azure Policy scenario.
Kodfragmentet nedan associerar till exempel Azure Windowss bas linje konfiguration med lägsta version `1.0.0` till alla datorer som omfattas av principen. Som standard utförs endast en granskning av datorn av gäst tilldelningen.

```json
"metadata": {
    "category": "Guest Configuration",
    "guestConfiguration": {
        "name": "AzureWindowsBaseline",
        "version": "1.*"
    }
//additional metadata properties exist
```

Gäst tilldelningar skapas automatiskt per dator av gäst konfigurations tjänsten. Resurstypen är `Microsoft.GuestConfiguration/guestConfigurationAssignments`.
Azure Policy använder egenskapen **complianceStatus** för gäst tilldelnings resursen för att rapportera kompatibilitetsstatus. Mer information finns i [Hämta efterlevnadsprinciper](../how-to/get-compliance-data.md).

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Granska operativ system inställningar efter bransch bas linjer

Ett initiativ i Azure Policy granskar inställningarna för operativ systemet efter en "bas linje". Definitionen, för _\[ hands versionen \] : Windows-datorer bör uppfylla kraven för Azures säkerhets bas linje_ innehåller en uppsättning regler som baseras på Active Directory Grupprincip.

De flesta av inställningarna är tillgängliga som parametrar. Med parametrar kan du anpassa vad som granskas.
Justera principen med dina krav eller mappa principen till information från tredje part, till exempel bransch regelverks standarder.

Vissa parametrar har stöd för ett heltals värde intervall. Till exempel kan inställningen högsta ålder för lösen ord granska inställningen gällande grupprincip. Ett "1, 70"-intervall bekräftar att användarna måste ändra sina lösen ord minst var 70 dag, men inte mindre än en dag.

Om du tilldelar principen med hjälp av en Azure Resource Manager-mall (ARM-mall) använder du en parameter fil för att hantera undantag. Checka in filerna till ett versions kontroll system, till exempel git. Kommentarer om fil ändringar ger bevis varför en tilldelning är ett undantag till det förväntade värdet.

#### <a name="applying-configurations-using-guest-configuration"></a>Använda konfigurationer med gäst konfiguration

Det är bara definitionen som _konfigurerar tids zonen på Windows-datorer_ som gör ändringar i datorn genom att konfigurera tids zonen. Anpassade princip definitioner för konfiguration av inställningar i datorer stöds inte.

När du tilldelar definitioner som börjar med _Konfigurera_ måste du också tilldela _krav för definitions distribution för att aktivera principen för gäst konfiguration på virtuella Windows-datorer_. Du kan kombinera dessa definitioner i ett initiativ om du väljer.

> [!NOTE]
> Den inbyggda tids zons principen är den enda definition som stöder konfiguration av inställningar i datorer och anpassade princip definitioner som konfigurerar inställningar i datorer som inte stöds.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Tilldela principer till datorer utanför Azure

De gransknings princip definitioner som är tillgängliga för gäst konfiguration är resurs typen **Microsoft. HybridCompute/Machines** . Alla datorer som har publicerats till [Azure-bågen för servrar](../../../azure-arc/servers/overview.md) som omfattas av princip tilldelningen ingår automatiskt.

## <a name="troubleshooting-guest-configuration"></a>Felsöka gäst konfiguration

Mer information om hur du felsöker gäst konfiguration finns [Azure policy fel sökning](../troubleshoot/general.md).

### <a name="multiple-assignments"></a>Flera tilldelningar

Princip definitioner för gäst konfiguration stöder för närvarande endast tilldelning av samma gäst tilldelning en gång per dator, även om princip tilldelningen använder olika parametrar.

### <a name="client-log-files"></a>Loggfiler för klienter

Gäst konfigurations tillägget skriver loggfiler till följande platser:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

### <a name="collecting-logs-remotely"></a>Samla in loggar via fjärr anslutning

Det första steget i fel sökning av konfigurationer eller moduler för gäst konfiguration bör vara att använda `Test-GuestConfigurationPackage` cmdleten genom att följa stegen hur du [skapar en anpassad princip för gäst konfigurations granskning för Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Om det inte lyckas kan insamling av klient loggar hjälpa till att diagnostisera problem.

#### <a name="windows"></a>Windows

Samla in information från loggfiler med [kommandot för körning av virtuell Azure-dator](../../../virtual-machines/windows/run-command.md). följande PowerShell-skript kan vara till hjälp.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Samla in information från loggfiler med [kommandot Azure VM Run](../../../virtual-machines/linux/run-command.md), det kan vara bra med följande exempel på bash-skript.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

### <a name="client-files"></a>Klient filer

Klienten för gäst konfiguration laddar ned innehålls paket till en dator och extraherar innehållet.
Du kan kontrol lera vilka innehåll som har hämtats och lagrats genom att visa mapparna nedan.

Windows: `c:\programdata\guestconfig\configurations`

Linux: `/var/lib/guestconfig/configurations`

## <a name="guest-configuration-samples"></a>Exempel på gäst konfiguration

Inbyggda princip exempel för gäst konfiguration finns på följande platser:

- [Inbyggda princip definitioner – gäst konfiguration](../samples/built-in-policies.md#guest-configuration)
- [Inbyggda initiativ – gäst konfiguration](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy exempel GitHub lagrings platsen](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

### <a name="video-overview"></a>Videoöversikt

Följande översikt över Azure Policy-gäst konfigurationen är från ITOps pratar 2021.

[Styrande bas linjer i hybrid Server miljöer med Azure Policy gäst konfiguration](https://techcommunity.microsoft.com/t5/itops-talk-blog/ops114-governing-baselines-in-hybrid-server-environments-using/ba-p/2109245)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du visar information om varje inställning från [vyn efterlevnad för gäst konfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](./definition-structure.md).
- Granska [Förstå policy-effekter](./effects.md).
- Lär dig att [program mässigt skapa principer](../how-to/programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](../how-to/get-compliance-data.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](../how-to/remediate-resources.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
