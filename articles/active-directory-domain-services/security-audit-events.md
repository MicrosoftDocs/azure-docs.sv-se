---
title: Aktivera säkerhets granskningar för Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du aktiverar säkerhets granskningar för att centralisera loggning av händelser för analys och aviseringar i Azure AD Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: caf46850b3d8d6946225575b8a9a732a90847482
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574135"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Aktivera säkerhets granskningar för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) säkerhets granskningar låter Azure Stream-säkerhetshändelser till riktade resurser. Dessa resurser omfattar Azure Storage, Azure Log Analytics-arbetsytor eller Azure Event Hub. När du har aktiverat säkerhets gransknings händelser skickar Azure AD DS alla granskade händelser för den valda kategorin till mål resursen.

Du kan arkivera händelser i Azure Storage och strömma händelser till SIEM-program (Security information and Event Management) (eller motsvarande) med hjälp av Azure Event Hubs eller utföra din egen analys och använda Azure Log Analytics-arbetsytor från Azure Portal.

> [!IMPORTANT]
> Azure AD DS-säkerhetsgranskningar är bara tillgängliga för Azure Resource Manager-baserade hanterade domäner. Information om hur du migrerar finns i [migrera Azure AD DS från den klassiska virtuella nätverks modellen till Resource Manager][migrate-azure-adds].

## <a name="security-audit-destinations"></a>Säkerhets gransknings mål

Du kan använda Azure Storage, Azure Event Hubs eller Azure-Log Analytics arbets ytor som mål resurs för Azure AD DS-säkerhetsgranskningar. Dessa mål kan kombineras. Du kan till exempel använda Azure Storage för att arkivera säkerhets gransknings händelser, men en Azure Log Analytics-arbetsyta för att analysera och rapportera informationen på kort sikt.

I följande tabell beskrivs scenarier för varje mål resurs typ.

> [!IMPORTANT]
> Du måste skapa mål resursen innan du aktiverar Azure AD DS-säkerhetsgranskningar. Du kan skapa dessa resurser med hjälp av Azure Portal, Azure PowerShell eller Azure CLI.

| Mål resurs | Scenario |
|:---|:---|
|Azure Storage| Det här målet ska användas när det primära behovet är att lagra säkerhets gransknings händelser för arkivering. Andra mål kan användas i arkiverings syfte, men dessa mål tillhandahåller funktioner utöver det primära behovet av arkivering. <br /><br />Innan du aktiverar säkerhets gransknings händelser i Azure AD DS måste du först [skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Det här målet ska användas när det primära behovet är att dela säkerhets gransknings händelser med ytterligare program vara, till exempel data analys program eller SIEM-programvara (Security information & Event Management).<br /><br />Innan du aktiverar säkerhets gransknings händelser i Azure AD DS [skapar du en Event Hub med hjälp av Azure Portal](../event-hubs/event-hubs-create.md)|
|Azure Log Analytics-arbetsyta| Det här målet ska användas när det primära behovet är att analysera och granska säkra granskningar från Azure Portal direkt.<br /><br />Innan du aktiverar säkerhets gransknings händelser i Azure AD DS [skapar du en Log Analytics arbets yta i Azure Portal.](../azure-monitor/logs/quick-create-workspace.md)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Aktivera säkerhets gransknings händelser med hjälp av Azure Portal

Slutför följande steg för att aktivera Azure AD DS-säkerhetsgransknings händelser med hjälp av Azure Portal.

> [!IMPORTANT]
> Granskningar av Azure AD DS-säkerhet är inte retroaktivt. Du kan inte hämta eller spela upp händelser tidigare. Azure AD DS kan bara skicka händelser som inträffar när säkerhets granskning har Aktiver ATS.

1. Logga in på Azure Portal på https://portal.azure.com.
1. Sök efter och välj **Azure AD Domain Services** överst i Azure Portal. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. I fönstret Azure AD DS väljer du **diagnostikinställningar** på vänster sida.
1. Ingen diagnostik konfigureras som standard. Kom igång genom att välja **Lägg till diagnostisk inställning**.

    ![Lägg till en diagnostisk inställning för Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Ange ett namn för diagnos konfigurationen, till exempel *aadds-Auditing*.

    Markera kryss rutan för det mål för säkerhets granskning som du vill använda. Du kan välja från ett Azure Storage konto, en Azure-händelsehubben eller en Log Analytics arbets yta. Dessa mål resurser måste redan finnas i din Azure-prenumeration. Du kan inte skapa mål resurserna i den här guiden.

    ![Aktivera det obligatoriska målet och typen av gransknings händelser som ska samlas in](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * Välj **Arkiv till ett lagrings konto** och välj sedan **Konfigurera**.
        * Välj den **prenumeration** och det **lagrings konto** som du vill använda för att arkivera säkerhets gransknings händelser.
        * När du är klar väljer du **OK**.
    * **Azure Event Hub**
        * Välj **Stream till en Event Hub** och välj sedan **Konfigurera**.
        * Välj **prenumerationen** och **Event Hub-namnområdet**. Om det behövs kan du även välja ett **namn på händelsehubben** och sedan **Event Hub-Principens namn**.
        * När du är klar väljer du **OK**.
    * **Azure logg analys arbets ytor**
        * Välj **Skicka till Log Analytics** och välj sedan den **prenumeration** och **Log Analytics arbets yta** som du vill använda för att lagra säkerhets gransknings händelser.

1. Välj de logg kategorier som du vill inkludera för den specifika mål resursen. Om du skickar gransknings händelser till ett Azure Storage konto kan du också konfigurera en bevarande princip som definierar antalet dagar som data ska bevaras. Standardvärdet *0* behåller alla data och roterar inte händelser efter en viss tids period.

    Du kan välja olika logg kategorier för varje mål resurs inom en enskild konfiguration. Med den här möjligheten kan du välja vilka loggar kategorier du vill behålla för Log Analytics och vilka loggar kategorier som du vill arkivera, till exempel.

1. När du är färdig väljer du **Spara** för att genomföra ändringarna. Mål resurserna börjar ta emot Azure AD DS säkerhets gransknings händelser strax efter att konfigurationen har sparats.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Aktivera säkerhets gransknings händelser med Azure PowerShell

Slutför följande steg för att aktivera Azure AD DS-säkerhetsgransknings händelser med Azure PowerShell. Om [det behövs installerar du först Azure PowerShell-modulen och ansluter till din Azure-prenumeration](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Granskningar av Azure AD DS-säkerhet är inte retroaktivt. Du kan inte hämta eller spela upp händelser tidigare. Azure AD DS kan bara skicka händelser som inträffar när säkerhets granskning har Aktiver ATS.

1. Autentisera till din Azure-prenumeration med hjälp av cmdleten [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) . När du uppmanas till det anger du dina autentiseringsuppgifter för kontot.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Skapa mål resursen för säkerhets gransknings händelser.

    * **Azure Storage**  -  [Skapa ett lagrings konto med hjälp av Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure Event Hub**  -  [Skapa en händelsehubben med Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Du kan också behöva använda cmdleten [New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) för att skapa en auktoriseringsregel som ger Azure AD DS-behörigheter till *namn området* för Event Hub. Auktoriseringsregeln måste innehålla rättigheterna **Hantera**, **Lyssna** och **Skicka** .

        > [!IMPORTANT]
        > Se till att du anger auktoriseringsregeln för Event Hub-namnområdet och inte själva händelsehubben.

    * **Azure logg analys arbets ytor**  -  [Skapa en Log Analytics arbets yta med Azure PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

1. Hämta resurs-ID för din Azure AD DS-hanterade domän med hjälp av cmdleten [Get-AzResource](/powershell/module/Az.Resources/Get-AzResource) . Skapa en variabel med namnet *$aadds. ResourceId* för att lagra värdet:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Konfigurera Azure Diagnostic-inställningarna med cmdleten [set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) för att använda mål resursen för Azure AD Domain Services säkerhets gransknings händelser. Variabeln $aadds i följande exempel *. ResourceId* används i föregående steg.

    * **Azure Storage** – Ersätt *storageAccountId* med ditt lagrings konto namn:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure Event Hub** – Ersätt *eventHubName* med namnet på händelsehubben och *eventHubRuleId* med ID: t för auktoriseringsregeln:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure logg analys arbets ytor** – Ersätt *workspaceId* med ID: t för arbets ytan Log Analytics:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Fråga och Visa säkerhets gransknings händelser med Azure Monitor

Med logg analys arbets ytor kan du Visa och analysera säkerhets gransknings händelser med hjälp av Azure Monitor och Kusto-frågespråket. Det här frågespråket är utformat för skrivskyddad användning som har funktioner för Power-analys med en lättläst syntax. Mer information om hur du kommer igång med Kusto finns i följande artiklar:

* [Dokumentation för Azure Monitor](../azure-monitor/index.yml)
* [Kom igång med Log Analytics i Azure Monitor](../azure-monitor/logs/log-analytics-tutorial.md)
* [Kom igång med loggfrågor i Azure Monitor](../azure-monitor/logs/get-started-queries.md)
* [Skapa och dela instrumentpaneler för Log Analytics-data](../azure-monitor/visualize/tutorial-logs-dashboards.md)

Följande exempel frågor kan användas för att börja analysera säkerhets gransknings händelser från Azure AD DS.

### <a name="sample-query-1"></a>Exempel fråga 1

Visa alla konto utelåsnings händelser under de senaste sju dagarna:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exempel fråga 2

Visa alla konto utelåsnings händelser (*4740*) mellan 3 juni 2020 kl. 09:00 och den 10 juni 2020 midnatt, sorteras stigande efter datum och tid:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-03 09:00) and TimeGenerated <= datetime(2020-06-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exempel fråga 3

Visa konto inloggnings händelser sju dagar sedan (från nu) för kontot med namnet användare:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exempel fråga 4

Visa konto inloggnings händelser sju dagar sedan från nu för kontot med namnet användare som försökte logga in med ett felaktigt lösen ord (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exempel fråga 5

Visa konto inloggnings händelser sju dagar sedan från nu för kontot med namnet användare som försökte logga in när kontot var utelåst (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exempel fråga 6

Visa antalet konto inloggnings händelser sju dagar sedan från nu för alla inloggnings försök som inträffat för alla utelåsta användare:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="audit-event-categories"></a>Granska händelse kategorier

Azure AD DS-säkerhetsgranskningar överensstämmer med traditionell granskning för traditionella AD DS-domänkontrollanter. I hybrid miljöer kan du återanvända befintliga gransknings mönster så att samma logik kan användas när händelserna analyseras. Beroende på vilket scenario du behöver för att felsöka eller analysera måste de olika gransknings händelse kategorierna vara riktade.

Följande kategorier av gransknings händelser är tillgängliga:

| Namn på gransknings kategori | Description |
|:---|:---|
| Konto inloggning|Granskningar försöker autentisera konto data på en domänkontrollant eller en lokal hanterare för konto säkerhet (SAM).</p>Princip inställningar för inloggning och utloggning och händelser spårar försök att komma åt en viss dator. Inställningar och händelser i den här kategorin fokuserar på den konto databas som används. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska verifiering av autentiseringsuppgifter](/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Granska Kerberos-autentiseringstjänst](/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Granska Kerberos-tjänstbiljettåtgärder](/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Granska andra inloggnings-och utloggnings händelser](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Kontohantering|Granskar ändringar av användar-och dator konton och grupper. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska hantering av programgrupp](/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Granska hantering av datorkonto](/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Granska hantering av distributionsgrupp](/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Granska annan konto hantering](/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Granska hantering av säkerhetsgrupp](/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Granska hantering av användarkonto](/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Informations spårning|Granskar aktiviteter för enskilda program och användare på den datorn och för att förstå hur en dator används. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska DPAPI-aktivitet](/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Granska PNP-aktivitet](/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Granska processgenerering](/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Granska processavslut](/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Granska RPC-händelser](/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Åtkomst till katalog tjänster|Granskningar försöker komma åt och ändra objekt i Active Directory Domain Services (AD DS). Dessa gransknings händelser loggas bara på domänkontrollanter. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska detaljerad replikering av katalogtjänst](/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Granska katalogtjänståtkomst](/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Granska ändringar av katalogtjänst](/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Granska replikering av katalogtjänst](/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|Granskningar försöker logga in på en dator interaktivt eller över ett nätverk. Dessa händelser är användbara för att spåra användar aktivitet och identifiera eventuella attacker på nätverks resurser. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska kontoutelåsning](/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Granska användar-/enhets anspråk](/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Granska utökat IPsec-läge](/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Medlemskap i gransknings grupp](/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Granska IPSec-huvudläge](/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Granska IPSec-snabbläge](/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Granska utloggning](/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Granska inloggning](/windows/security/threat-protection/auditing/audit-logon)</li><li>[Granska nätverksprincipserver](/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Granska andra inloggnings-och utloggnings händelser](/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Granska specialinloggning](/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Objekt åtkomst| Granskningar försöker komma åt vissa objekt eller typer av objekt i ett nätverk eller en dator. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska genererat program](/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Granska certifikattjänster](/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Granska detaljerad filresurs](/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Granska filresurs](/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Granska fil system](/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Granska anslutning för filterplattform](/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Granska ignorerat paket för filterplattform](/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Gransknings hanterings manipulering](/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Granska kernel-objekt](/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Granska andra objektåtkomsthändelser](/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Granska registret](/windows/security/threat-protection/auditing/audit-registry)</li><li>[Granska flyttbara lagrings enheter](/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Granska SAM](/windows/security/threat-protection/auditing/audit-sam)</li><li>[Granska mellanlagring av princip för central åtkomst](/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Princip ändring|Granskar ändringar av viktiga säkerhets principer på ett lokalt system eller nätverk. Principer upprättas vanligt vis av administratörer för att skydda nätverks resurser. Övervakning av ändringar eller försök att ändra dessa principer kan vara en viktig aspekt av säkerhets hanteringen för ett nätverk. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska ändring av granskningsprincip](/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Granska ändring av autentiseringsprincip](/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Granska ändring av auktoriseringsprincip](/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Granska ändring av filtreringsplattformsprincip](/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Granska ändring av MPSSVC-regelnivåprincip](/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Granska annan princip ändring](/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Privilegie rad användning| Granskar användningen av vissa behörigheter på ett eller flera system. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska användning av icke-känsliga privilegier](/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Granska användning av känsliga privilegier](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Granska andra händelser som rör privilegierad användning](/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Granskar ändringar på system nivå för en dator som inte ingår i andra kategorier och som har potentiella säkerhets risker. Den här kategorin omfattar följande under Kategorier:<ul><li>[Granska IPsec-drivrutin](/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Granska andra systemhändelser](/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Granska ändring av säkerhetsstatus](/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Granska utökning av säkerhetssystem](/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Granska systemintegritet](/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Händelse-ID per kategori

 Säkerhets granskningar i Azure AD DS registrerar följande händelse-ID: n när den särskilda åtgärden utlöser en gransknings bar händelse:

| Händelse kategori namn | Händelse-ID |
|:---|:---|
|Konto inloggnings säkerhet|4767, 4774, 4775, 4776, 4777|
|Konto hanterings säkerhet|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Informations spårnings säkerhet|Inget|
|Åtkomst säkerhet för DS|5136, 5137, 5138, 5139, 5141|
|Logon-Logoff säkerhet|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objekt åtkomst säkerhet|Inget|
|Princip ändrings säkerhet|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Privilegiet Använd säkerhet|4985|
|Systemsäkerhet|4612, 4621|

## <a name="next-steps"></a>Nästa steg

Detaljerad information om Kusto finns i följande artiklar:

* [Översikt](/azure/kusto/query/) över Kusto-frågespråket.
* [Kusto själv studie kurs](/azure/kusto/query/tutorial) som hjälper dig att bekanta dig med frågor.
* [Exempel frågor](/azure/kusto/query/samples) som hjälper dig att lära dig nya sätt att se dina data.
* Kusto [metod tips](/azure/kusto/query/best-practices) för att optimera dina frågor för framgång.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md