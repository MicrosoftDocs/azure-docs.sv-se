---
title: Miljöer för hantering av flera klienter
description: Azure-delegerad resurshantering möjliggör hantering mellan klientorganisation.
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 027d1d5e81d5a652a7e2d5441c40440c661f730f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778633"
---
# <a name="cross-tenant-management-experiences"></a>Miljöer för hantering av flera klienter

Som tjänstleverantör kan du använda [Azure Lighthouse](../overview.md) för att hantera resurser för flera kunder från din egen Azure Active Directory (Azure AD) klientorganisation. Många uppgifter och tjänster kan utföras över hanterade klienter med hjälp av [Azure-delegerad resurshantering.](../concepts/azure-delegated-resource-management.md)

> [!TIP]
> Azure-delegerad resurshantering kan också användas i ett företag som har flera Azure AD-klientorganisationsklienter för att förenkla administration mellan klientorganisationen. [](enterprise.md)

## <a name="understanding-tenants-and-delegation"></a>Förstå klienter och delegering

En Azure AD-klientorganisation är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation får när de skapar en relation med Microsoft genom att registrera sig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klientorganisation är separat från andra Azure AD-klienter och har sitt eget klient-ID (ett GUID). Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

För att kunna hantera Azure-resurser för en kund måste tjänstleverantörer vanligtvis logga in på Azure Portal med ett konto som är associerat med kundens klientorganisation, vilket kräver att en administratör i kundens klientorganisation skapar och hanterar användarkonton för tjänstleverantören.

Med Azure Lighthouse anger onboarding-processen användare i tjänstleverantörens klientorganisation som ska kunna arbeta med delegerade prenumerationer och resursgrupper i kundens klientorganisation. Dessa användare kan sedan logga in på Azure Portal med sina egna autentiseringsuppgifter. Inom Azure Portal kan de hantera resurser som hör till alla kunder som de har åtkomst till. Detta kan göras genom [](../how-to/view-manage-customers.md) att gå till sidan Mina kunder i Azure Portal eller genom att arbeta direkt i kontexten för kundens prenumeration, antingen i Azure Portal eller via API:er.

Azure Lighthouse ger större flexibilitet för att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. En tjänstleverantör kan till exempel ha två kunder med olika ansvarsområden och åtkomstnivåer. Med Azure Lighthouse kan behöriga användare logga in på tjänstleverantörens klientorganisation för att få åtkomst till dessa resurser.

![Diagram som visar kundresurser som hanteras via en tjänstleverantörs klientorganisation.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Stöd för API:er och hanteringsverktyg

Du kan utföra hanteringsuppgifter på delegerade resurser antingen direkt i portalen eller med hjälp av API:er och hanteringsverktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API:er kan användas när du arbetar med delegerade resurser, så länge funktionen stöds för hantering mellan klientorganisationen och användaren har rätt behörigheter.

[Cmdleten Azure PowerShell Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) visar som standard för den `TenantId` hanterande klientorganisationen. Du kan använda attributen och för varje prenumeration, så att du kan identifiera om en returnerad prenumeration tillhör en hanterad `HomeTenantId` `ManagedByTenantIds` klient eller din hanterande klientorganisation.

På samma sätt visar Azure CLI-kommandon [som az account list](/cli/azure/account#az_account_list) `homeTenantId` `managedByTenants` attributen och . Om du inte ser dessa värden när du använder Azure CLI kan du prova att rensa cacheminnet genom att `az account clear` köra följt av `az login --identity` .

I Azure REST API kommandona [Subscriptions - Get](/rest/api/resources/subscriptions/get) och [Subscriptions – List](/rest/api/resources/subscriptions/list) `ManagedByTenant` .

> [!NOTE]
> Förutom information om klientorganisationen som rör Azure Lighthouse kan klienter som visas av dessa API:er även återspegla partnerklienter för Azure Databricks eller Azure-hanterade program.

Vi tillhandahåller även API:er som är specifika för att Azure Lighthouse uppgifter. Mer information finns i **avsnittet** Referens.

## <a name="enhanced-services-and-scenarios"></a>Förbättrade tjänster och scenarier

De flesta uppgifter och tjänster kan utföras på delegerade resurser över hanterade klientorganisationer. Nedan visas några av de viktigaste scenarierna där hantering mellan klientorganisationen kan vara särskilt effektivt.

[Azure Arc](../../azure-arc/index.yml):

- Hantera hybridservrar i stor skala [– Azure Arc-aktiverade servrar:](../../azure-arc/servers/overview.md)
  - [Hantera Windows Server- eller Linux-datorer utanför Azure som är anslutna](../../azure-arc/servers/onboard-portal.md) till delegerade prenumerationer och/eller resursgrupper i Azure
  - Hantera anslutna datorer med hjälp av Azure-konstruktioner, Azure Policy och taggning
  - Se till att samma uppsättning principer tillämpas i kundernas hybridmiljöer
  - Använd Azure Security Center för att övervaka efterlevnad i kundernas hybridmiljöer
- Hantera Kubernetes-hybridkluster i [stor skala Azure Arc Kubernetes (förhandsversion)](../../azure-arc/kubernetes/overview.md):
  - [Hantera Kubernetes-kluster som är anslutna till](../../azure-arc/kubernetes/quickstart-connect-cluster.md) delegerade prenumerationer och/eller resursgrupper i Azure
  - [Använda GitOps](../../azure-arc/kubernetes/tutorial-use-gitops-connected-cluster.md) för anslutna kluster
  - Framtvinga principer i anslutna kluster

[Azure Automation](../../automation/index.yml):

- Använda Automation-konton för att få åtkomst till och arbeta med delegerade resurser

[Azure Backup](../../backup/index.yml):

- Återställning av kunddata [från lokala arbetsbelastningar, virtuella Azure-datorer, Azure-filresurser med mera](../..//backup/backup-overview.md#what-can-i-back-up)
- Visa data för alla delegerade kundresurser i [Backup Center](../../backup/backup-center-overview.md)
- Använd följande [Backup-utforskaren](../../backup/monitor-azure-backup-with-backup-explorer.md) att visa driftsinformation om säkerhetskopieringsobjekt (inklusive Azure-resurser som ännu inte har konfigurerats för säkerhetskopiering) och övervakningsinformation (jobb och aviseringar) för delegerade prenumerationer. Den Backup-utforskaren är för närvarande endast tillgänglig för virtuella Azure-datordata.
- Använd [Backup-rapporter](../../backup/configure-reports.md) delegerade prenumerationer för att spåra historiska trender, analysera lagringsanvändning för säkerhetskopior och granska säkerhetskopieringar och återställningar.

[Azure Blueprints](../../governance/blueprints/index.yml):

- Använd Azure Blueprints för att samordna distributionen av resursmallar och andra artefakter (kräver ytterligare [åtkomst för](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) att förbereda kundprenumerationen)

[Azure Cost Management + Billing](../../cost-management-billing/index.yml):

- Från den hanterande klientorganisationen kan CSP-partner visa, hantera och analysera förbrukningskostnader före skatt (inte inklusive inköp) för kunder som omfattas av Azure-planen. Kostnaden baseras på återförsäljarpriser och azures rollbaserade åtkomstkontroll (Azure RBAC) som partnern har för kundens prenumeration. För närvarande kan du visa förbrukningskostnader till återförsäljarpriser för varje enskild kundprenumeration baserat på Azure RBAC-åtkomst.

[Azure Key Vault](../../key-vault/general/index.yml):

- Skapa nyckelvalv i kundklienter
- Använda en hanterad identitet för att skapa nyckelvalv i kundklienter

[Azure Kubernetes Service (AKS):](../../aks/index.yml)

- Hantera värdhanterade Kubernetes-miljöer och distribuera och hantera containerprogram i kundklienter
- Distribuera och hantera kluster i kundklienter
-   Använda Azure Monitor för containrar för att övervaka prestanda mellan kundklienter

[Azure Migrate](../../migrate/index.yml):

- Skapa migreringsprojekt i kundens klientorganisation och migrera virtuella datorer

[Azure Monitor](../../azure-monitor/index.yml):

- Visa aviseringar för delegerade prenumerationer, med möjlighet att visa och uppdatera aviseringar för alla prenumerationer
- Visa aktivitetslogginformation för delegerade prenumerationer
- [Log Analytics:](../../azure-monitor/logs/service-providers.md)Fråga efter data från fjärranslutna arbetsytor i flera klienter (observera att Automation-konton som används för att komma åt data från arbetsytor i kundklienter måste skapas i samma klient)
- [Skapa, visa och hantera aktivitetsloggaviseringar](../../azure-monitor/alerts/alerts-activity-log.md) i kundklienter
- Skapa aviseringar i kundklienter som utlöser automatisering, till exempel Azure Automation runbooks eller Azure Functions, i den hanterande klientorganisationen via webhooks
- Skapa [diagnostikinställningar i](../..//azure-monitor/essentials/diagnostic-settings.md) kundklienter för att skicka resursloggar till arbetsytor i den hanterande klientorganisationen
- För SAP-arbetsbelastningar [övervakar du SAP Solutions-mått med en aggregerad vy över kundklienter](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure-nätverkstjänster](../../networking/networking-overview.md):

- Distribuera och hantera [Azure Virtual Network](../../virtual-network/index.yml) och virtuella nätverkskort (vNIC) i hanterade klienter
- Distribuera och konfigurera [Azure Firewall](../../firewall/overview.md) att skydda kundernas Virtual Network resurser
- Hantera anslutningstjänster som [Azure Virtual WAN,](../../virtual-wan/virtual-wan-about.md) [ExpressRoute](../../expressroute/expressroute-introduction.md)och [VPN-gatewayer](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Använd Azure Lighthouse för att stödja viktiga scenarier för [Azure-nätverkstjänster MSP-programmet](../../networking/networking-partners-msp.md)

[Azure Policy](../../governance/policy/index.yml):

- Skapa och redigera principdefinitioner i delegerade prenumerationer
- Distribuera principdefinitioner och principtilldelningar över flera klienter
- Tilldela kunddefinierade principdefinitioner i delegerade prenumerationer
- Kunder ser principer som har redigerats av tjänstleverantören tillsammans med eventuella principer som de har skapat själva
- Kan [åtgärda deployIfNotExists eller ändra tilldelningar i den hanterade klientorganisationen](../how-to/deploy-policy-remediation.md)
- Observera att det för närvarande inte finns stöd för att visa efterlevnadsinformation för icke-kompatibla resurser i kundklienter

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Innehåller nu klientorganisations-ID:t i returnerade frågeresultat, så att du kan identifiera om en prenumeration tillhör en hanterad klientorganisation

[Azure Security Center](../../security-center/index.yml):

- Synlighet mellan klienter
  - Övervaka efterlevnad av säkerhetsprinciper och säkerställa säkerhetstäckning för alla klientorganisationsresurser
  - Kontinuerlig övervakning av regelefterlevnad över flera klienter i en enda vy
  - Övervaka, prioritera och prioritera säkerhetsrekommendationer med säkerhetspoängberäkning
- Säkerhetshållningshantering mellan klientorganisation
  - Hantera säkerhetsprinciper
  - Vidta åtgärder för resurser som inte följer gällande säkerhetsrekommendationer
  - Samla in och lagra säkerhetsrelaterade data
- Hotidentifiering och skydd mellan klientorganisationen
  - Identifiera hot mellan klientorganisationens resurser
  - Tillämpa avancerade hotskyddskontroller, till exempel jit-åtkomst (just-in-time) för virtuella datorer
  - Härda nätverkssäkerhetsgruppens konfiguration med anpassningsbar nätverkshärdning
  - Se till att servrarna endast kör de program och processer som de ska ha med anpassningsbara programkontroller
  - Övervaka ändringar av viktiga filer och registerposter med övervakning av filintegritet (FIM)
- Observera att hela prenumerationen måste delegeras till den hanterande klientorganisationen. Azure Security Center stöds inte med delegerade resursgrupper

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Hantera Azure Sentinel i [kundklienter](../../sentinel/multiple-tenants-service-providers.md)
- [Spåra attacker och visa säkerhetsaviseringar över flera klienter](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Visa incidenter](../../sentinel/multiple-workspace-view.md) över flera Azure Sentinel arbetsytor som är utspridda mellan klienter

[Azure Service Health](../../service-health/index.yml):

- Övervaka hälsotillståndet för kundresurser med Azure Resource Health
- Spåra hälsotillståndet för de Azure-tjänster som används av dina kunder

[Azure Site Recovery](../../site-recovery/index.yml):

- Hantera haveriberedskapsalternativ för virtuella Azure-datorer i kundklienter (observera att du inte kan använda konton `RunAs` för att kopiera VM-tillägg)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- Använda tillägg för virtuella datorer för att tillhandahålla konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer
- Använda startdiagnostik för att felsöka virtuella Azure-datorer
- Få åtkomst till virtuella datorer med seriekonsolen
- Integrera virtuella datorer med Azure Key Vault för lösenord, hemligheter eller kryptografiska nycklar för diskkryptering med hjälp av hanterad identitet via princip [,](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)vilket säkerställer att hemligheter lagras i en Key Vault i de hanterade klientorganisationen
- Observera att du inte kan använda Azure Active Directory för fjärrinloggning till virtuella datorer

Supportbegäranden:

- [Öppna supportbegäranden **från Hjälp + support**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) i Azure Portal för delegerade resurser (välja den supportplan som är tillgänglig för det delegerade omfånget)
- Använda [Azure Quota API för att](/rest/api/reserved-vm-instances/quotaapi) visa och hantera Azure-tjänstkvoter för delegerade kundresurser

## <a name="current-limitations"></a>Aktuella begränsningar

I alla scenarier bör du vara medveten om följande aktuella begränsningar:

- Begäranden som hanteras av Azure Resource Manager kan utföras med Azure Lighthouse. Åtgärds-URI:erna för dessa begäranden börjar med `https://management.azure.com` . Begäranden som hanteras av en instans av en resurstyp (till exempel Key Vault eller lagringsdataåtkomst) stöds dock inte med Azure Lighthouse. Åtgärds-URI:erna för dessa begäranden börjar vanligtvis med en adress som är unik för din instans, till exempel `https://myaccount.blob.core.windows.net` eller `https://mykeyvault.vault.azure.net/` . Det senare är vanligtvis även dataåtgärder i stället för hanteringsåtgärder.
- Rolltilldelningar måste använda [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md). Alla inbyggda roller stöds för närvarande med Azure-delegerad resurshantering förutom ägare eller inbyggda roller med [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) behörighet. Rollen Administratör för användaråtkomst stöds endast för begränsad användning vid [tilldelning av roller till hanterade identiteter.](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)  Anpassade roller och [administratörsroller för klassiska](../../role-based-access-control/classic-administrators.md) prenumerationer stöds inte.
- Du kan publicera prenumerationer som använder Azure Databricks, men användare i den hanterande klientorganisationen kan inte starta Azure Databricks arbetsytor i en delegerad prenumeration just nu.
- Du kan registrera prenumerationer och resursgrupper som har resurslås, men dessa lås hindrar inte åtgärder från att utföras av användare i den hanterande klientorganisationen. [Neka tilldelningar](../../role-based-access-control/deny-assignments.md) som skyddar system hanterade resurser, till exempel de som skapats av Azure-hanterade program eller Azure Blueprints (systemtilldelade tilldelningar), hindrar inte användare i den hanterande klientorganisationen från att agera på dessa resurser. För stunden kan dock användare i kundklientorganisationen inte skapa egna tilldelningsnekanden (tilldelningar av användartilldelad neka).
- Delegering av prenumerationer [över ett nationellt](../../active-directory/develop/authentication-national-cloud.md) moln och det offentliga Azure-molnet, eller mellan två separata nationella moln, stöds inte.

## <a name="next-steps"></a>Nästa steg

- Registrera dina kunder för Azure Lighthouse, antingen med [hjälp av Azure Resource Manager-mallar](../how-to/onboard-customer.md) eller genom att publicera ett privat eller offentligt hanterat tjänsterbjudande [till Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå **till Mina** kunder i Azure Portal.