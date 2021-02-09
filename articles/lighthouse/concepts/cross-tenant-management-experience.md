---
title: Miljöer för hantering av flera klienter
description: Azure-delegerad resurs hantering möjliggör hantering av flera innehavare.
ms.date: 02/08/2021
ms.topic: conceptual
ms.openlocfilehash: b76723c9ea94581561f5bdb04ea1fd8335da77b7
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981093"
---
# <a name="cross-tenant-management-experiences"></a>Miljöer för hantering av flera klienter

Som tjänst leverantör kan du använda Azure- [Lighthouse](../overview.md) för att hantera resurser för flera kunder i din egen Azure Active Directory (Azure AD)-klient. Många aktiviteter och tjänster kan utföras över hanterade klienter med hjälp av [Azure-delegerad resurs hantering](../concepts/azure-delegated-resource-management.md).

> [!TIP]
> Azure-delegerad resurs hantering kan också användas [inom ett företag som har flera Azure AD-klienter för](enterprise.md) att förenkla administrationen av flera innehavare.

## <a name="understanding-tenants-and-delegation"></a>Förstå innehavare och delegering

En Azure AD-klient är en representation av en organisation. Det är en dedikerad instans av Azure AD som en organisation tar emot när de skapar en relation med Microsoft genom att registrera sig för Azure, Microsoft 365 eller andra tjänster. Varje Azure AD-klient är distinkt och åtskild från andra Azure AD-klienter och har ett eget klient-ID (ett GUID). Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

För att kunna hantera Azure-resurser för en kund måste tjänst leverantörer ofta logga in på Azure Portal med ett konto som är kopplat till den kundens klient organisation, vilket kräver att en administratör i kundens klient organisation skapar och hanterar användar konton för tjänst leverantören.

Med Azure Lighthouse anger onboarding-processen användare inom tjänst leverantörens klient organisation som kommer att kunna arbeta med delegerade prenumerationer och resurs grupper i kundens klient organisation. Dessa användare kan sedan logga in på Azure Portal med sina egna autentiseringsuppgifter. I Azure Portal kan de hantera resurser som hör till alla kunder som de har åtkomst till. Detta kan göras genom att gå till sidan [Mina kunder](../how-to/view-manage-customers.md) i Azure Portal eller genom att arbeta direkt inom kontexten för kundens prenumeration, antingen i Azure Portal eller via API: er.

Azure Lighthouse ger större flexibilitet för att hantera resurser för flera kunder utan att behöva logga in på olika konton i olika klienter. En tjänst leverantör kan till exempel ha två kunder med olika ansvars områden och åtkomst nivåer. Med Azure Lighthouse kan behöriga användare logga in på tjänst leverantörens klient för att få åtkomst till dessa resurser.

![Diagram över kund resurser som hanteras via en tjänst leverantörs klient.](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Stöd för API: er och hanterings verktyg

Du kan utföra hanterings uppgifter för delegerade resurser antingen direkt i portalen eller med hjälp av API: er och hanterings verktyg (till exempel Azure CLI och Azure PowerShell). Alla befintliga API: er kan användas när du arbetar med delegerade resurser, så länge som funktionerna stöds för hantering av flera innehavare och användaren har rätt behörigheter.

[Cmdleten Azure PowerShell get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) visar `HomeTenantId` och `ManagedByTenantIds` attributen för varje prenumeration, så att du kan identifiera om en returnerad prenumeration tillhör en hanterad klient eller till din hanterings klient.

På samma sätt visar Azure CLI-kommandon som [AZ Account List](/cli/azure/account#az-account-list) `homeTenantId` `managedByTenants` attributen och. Om du inte ser dessa värden när du använder Azure CLI kan du försöka rensa cacheminnet genom att köra `az account clear` följt av `az login --identity` .

I Azure-REST API innehåller kommandona [prenumerationer-get](/rest/api/resources/subscriptions/get) och [Subscription-List](/rest/api/resources/subscriptions/list) `ManagedByTenant` .

> [!NOTE]
> Utöver klient information som är relaterad till Azure-Lighthouse kan klienter som visas i dessa API: er även avspegla partner innehavare för Azure Databricks eller Azure-hanterade program.

Vi tillhandahåller också API: er som är speciella för att utföra Azure Lighthouse-uppgifter. Mer information finns i avsnittet **referens** .

## <a name="enhanced-services-and-scenarios"></a>Förbättrade tjänster och scenarier

De flesta uppgifter och tjänster kan utföras på delegerade resurser över hanterade klienter. Nedan visas några viktiga scenarier där hantering av flera innehavare kan vara särskilt effektivt.

[Azure-båge](../../azure-arc/index.yml):

- Hantera hybrid servrar i skala – [Azure Arc-aktiverade servrar](../../azure-arc/servers/overview.md):
  - [Hantera Windows Server-eller Linux-datorer utanför Azure som är anslutna](../../azure-arc/servers/onboard-portal.md) till delegerade prenumerationer och/eller resurs grupper i Azure
  - Hantera anslutna datorer med hjälp av Azure-konstruktioner, till exempel Azure Policy och taggning
  - Se till att samma uppsättning principer tillämpas på kunders hybrid miljöer
  - Använd Azure Security Center för att övervaka efterlevnad över kunders hybrid miljöer
- Hantera hybrid Kubernetes-kluster i skala – [Azure Arc Enabled Kubernetes (för hands version)](../../azure-arc/kubernetes/overview.md):
  - [Hantera Kubernetes-kluster som är anslutna](../../azure-arc/kubernetes/connect-cluster.md) till delegerade prenumerationer och/eller resurs grupper i Azure
  - [Använda GitOps](../../azure-arc/kubernetes/use-gitops-connected-cluster.md) för anslutna kluster
  - Tillämpa principer i anslutna kluster

[Azure Automation](../../automation/index.yml):

- Använd Automation-konton för att komma åt och arbeta med delegerade resurser

[Azure Backup](../../backup/index.yml):

- Säkerhetskopiera och återställa kund information [från lokala arbets belastningar, virtuella Azure-datorer, Azure-filresurser med mera](../..//backup/backup-overview.md#what-can-i-back-up)
- Använd [Backup Explorer](../../backup/monitor-azure-backup-with-backup-explorer.md) för att Visa användnings information om säkerhets kopierings objekt (inklusive Azure-resurser som ännu inte har kon figurer ATS för säkerhets kopiering) och övervaknings information (jobb och aviseringar) för delegerade prenumerationer. Backup Explorer är för närvarande endast tillgängligt för Azure VM-data.
- Använd [säkerhets kopierings rapporter](../../backup/configure-reports.md) över delegerade prenumerationer för att spåra historiska trender, analysera förbrukning av lagrings utrymme för säkerhets kopior och granska säkerhets kopieringar och återställning.

[Azure-ritningar](../../governance/blueprints/index.yml):

- Använd Azure-ritningar för att dirigera distributionen av resursfiler och andra artefakter (kräver [ytterligare åtkomst](https://www.wesleyhaakman.org/preparing-azure-lighthouse-customer-subscriptions-for-azure-blueprints/) för att förbereda kund prenumerationen)

[Azure Cost Management + fakturering](../../cost-management-billing/index.yml):

- Från hanterings klient organisationen kan CSP-partner Visa, hantera och analysera konsumtions kostnader (inte inklusive inköp) för kunder som följer Azure-planen. Kostnaden baseras på återförsäljarversionen och den Azure rollbaserad åtkomst kontroll (Azure RBAC) som partnern har för kundens prenumeration.

[Azure Key Vault](../../key-vault/general/index.yml):

- Skapa nyckel valv i kund klienter
- Använd en hanterad identitet för att skapa nyckel valv i kund klienter

[Azure Kubernetes service (AKS)](../../aks/index.yml):

- Hantera värdbaserade Kubernetes-miljöer och distribuera och hantera program i behållare inom kund klienter
- Distribuera och hantera kluster i kund klienter
-   Använd Azure Monitor för behållare för att övervaka prestanda mellan kund klienter

[Azure Migrate](../../migrate/index.yml):

- Skapa migreringsjobb i kund klienten och migrera virtuella datorer

[Azure Monitor](../../azure-monitor/index.yml):

- Visa aviseringar för delegerade prenumerationer med möjlighet att visa och uppdatera aviseringar över alla prenumerationer
- Visa aktivitets logg information för delegerade prenumerationer
- [Log Analytics](../../azure-monitor/platform/service-providers.md): fråga efter data från fjärrarbetsyta i flera klienter (Observera att Automation-konton som används för att komma åt data från arbets ytor i kund klienter måste skapas i samma klient organisation)
- [Skapa, Visa och hantera aktivitets logg aviseringar](../../azure-monitor/platform/alerts-activity-log.md) i kund klienter
- Skapa aviseringar i kund klienter som utlöser automatisering, till exempel Azure Automation runbooks eller Azure Functions, i hantera klient organisationen via Webhooks
- Skapa [diagnostiska inställningar](../..//azure-monitor/platform/diagnostic-settings.md) i kund klienter för att skicka resurs loggar till arbets ytor i hanterings klienten
- För SAP-arbetsbelastningar [övervakar du SAP Solutions-mått med en sammanställd vy över kund klienter](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/using-azure-lighthouse-and-azure-monitor-for-sap-solutions-to/ba-p/1537293)

[Azure-nätverk](../../networking/networking-overview.md):

- Distribuera och hantera [Azure Virtual Network](../../virtual-network/index.yml) och virtuella nätverks gränssnitts kort (virtuella nätverkskort) i hanterade klienter
- Distribuera och konfigurera [Azure-brandväggen](../../firewall/overview.md) för att skydda kunders Virtual Network resurser
- Hantera anslutnings tjänster som [Azure Virtual WAN](../../virtual-wan/virtual-wan-about.md), [ExpressRoute](../../expressroute/expressroute-introduction.md)och [VPN-gatewayer](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- Använd Azure-Lighthouse för att stödja viktiga scenarier för [Azure Network MSP-programmet](../../networking/networking-partners-msp.md)

[Azure policy](../../governance/policy/index.yml):

- Skapa och redigera princip definitioner i delegerade prenumerationer
- Tilldela kunddefinierade princip definitioner inom delegerade prenumerationer
- Kunder ser principer som skapats av tjänst leverantören tillsammans med eventuella principer som de har skapat själva
- Kan [åtgärda deployIfNotExists eller ändra tilldelningar i den hanterade klient organisationen](../how-to/deploy-policy-remediation.md)

[Azure-resurs diagram](../../governance/resource-graph/index.yml):

- Innehåller nu klient-ID: t i returnerade frågeresultat, så att du kan identifiera om en prenumeration tillhör en hanterad klient

[Azure Security Center](../../security-center/index.yml):

- Synlighet mellan klienter
  - Övervaka efterlevnad av säkerhets principer och garantera säkerhets täckning för alla klienters resurser
  - Kontinuerlig övervakning av efterlevnad över flera klienter i en enda vy
  - Övervaka, prioritering och prioritera åtgärds bara säkerhets rekommendationer med säker Poäng beräkning
- Hantering av säkerhets position över flera innehavare
  - Hantera säkerhetsprinciper
  - Vidta åtgärder för resurser som inte är kompatibla med åtgärds bara säkerhets rekommendationer
  - Samla in och lagra säkerhetsrelaterade data
- Hot identifiering och skydd mellan klienter
  - Identifiera hot över klient organisations resurser
  - Använd avancerade skydds kontroller som just-in-Time (JIT) VM-åtkomst
  - Härdning av nätverks säkerhets grupp konfigurationen med anpassad nätverks härdning
  - Se till att servrarna bara kör de program och processer som de ska vara med anpassningsbara program kontroller
  - Övervaka ändringar i viktiga filer och register poster med File Integrity Monitoring (FIM)
- Observera att hela prenumerationen måste delegeras till hanterings klienten. Azure Security Center scenarier stöds inte med delegerade resurs grupper

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md):

- Hantera Azure Sentinel-resurser [i kund klienter](../../sentinel/multiple-tenants-service-providers.md)
- [Spåra attacker och Visa säkerhets aviseringar över flera klienter](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)
- [Visa incidenter](../../sentinel/multiple-workspace-view.md) över flera Azure Sentinel-arbetsytor som sprids över klienter

[Azure Service Health](../../service-health/index.yml):

- Övervaka hälso tillståndet för kund resurser med Azure Resource Health
- Spåra hälsan hos de Azure-tjänster som används av dina kunder

[Azure Site Recovery](../../site-recovery/index.yml):

- Hantera katastrof återställnings alternativ för virtuella Azure-datorer i kund klienter (Observera att du inte kan använda `RunAs` konton för att kopiera VM-tillägg)

[Azure-Virtual Machines](../../virtual-machines/index.yml):

- Använd tillägg för virtuella datorer för att tillhandahålla konfigurations-och automatiserings uppgifter efter distributionen på virtuella Azure-datorer
- Använd startdiagnostik för att felsöka virtuella Azure-datorer
- Åtkomst till virtuella datorer med serie konsol
- Integrera virtuella datorer med Azure Key Vault för lösen ord, hemligheter eller kryptografiska nycklar för disk kryptering med hjälp av [hanterad identitet via princip](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret), och se till att hemligheter lagras i en Key Vault i hanterade klienter
- Observera att du inte kan använda Azure Active Directory för fjärrinloggning till virtuella datorer

Support förfrågningar:

- [Öppna support förfrågningar från **hjälp + support**](../../azure-portal/supportability/how-to-create-azure-support-request.md#getting-started) i Azure Portal för delegerade resurser (välja det support avtal som är tillgängligt för det delegerade omfånget)
- Använd [Azures kvot-API](/rest/api/reserved-vm-instances/quotaapi) för att visa och hantera Azures tjänst kvoter för delegerade kund resurser

## <a name="current-limitations"></a>Aktuella begränsningar

Tänk på följande nuvarande begränsningar i alla scenarier:

- Begär Anden som hanteras av Azure Resource Manager kan utföras med hjälp av Azure dataLighthouses. Åtgärds-URI: erna för dessa begär Anden börjar med `https://management.azure.com` . Begär Anden som hanteras av en instans av en resurs typ (till exempel Key Vault hemligheter åtkomst eller åtkomst till lagrings data) stöds dock inte med Azure-Lighthouse. Åtgärds-URI: erna för dessa begär Anden börjar vanligt vis med en adress som är unik för din instans, till exempel `https://myaccount.blob.core.windows.net` eller `https://mykeyvault.vault.azure.net/` . De sistnämnda är också vanliga data åtgärder i stället för hanterings åtgärder.
- Roll tilldelningar måste använda [inbyggda Azure-roller](../../role-based-access-control/built-in-roles.md). Alla inbyggda roller stöds för närvarande med Azure-delegerad resurs hantering förutom för ägare eller inbyggda roller med [`DataActions`](../../role-based-access-control/role-definitions.md#dataactions) behörighet. Rollen administratör för användar åtkomst stöds endast för begränsad användning i [tilldela roller till hanterade identiteter](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant).  Administratörs roller för anpassade roller och [klassiska prenumerationer](../../role-based-access-control/classic-administrators.md) stöds inte.
- Även om du kan publicera prenumerationer som använder Azure Databricks kan användare i hanterings klienten inte starta Azure Databricks arbets ytor i en delegerad prenumeration just nu.
- Även om du kan publicera prenumerationer och resurs grupper som har resurs lås, kommer dessa lås inte att förhindra att åtgärder utförs av användare i hanterings klienten. [Neka tilldelningar](../../role-based-access-control/deny-assignments.md) som skyddar systemhanterade resurser, t. ex. de som skapats av Azure-hanterade program eller Azure-skisser (systemtilldelade neka-tilldelningar), förhindrar användare i hanterings klienten från att fungera på dessa resurser. men vid den här tidpunkten kan användare i kund klienten inte skapa sina egna neka-tilldelningar (användar tilldelning neka tilldelningar).
- Delegering av prenumerationer i ett [nationellt moln](../../active-directory/develop/authentication-national-cloud.md) och det offentliga Azure-molnet, eller över två separata nationella moln, stöds inte.

## <a name="next-steps"></a>Nästa steg

- Publicera dina kunder till Azure Lighthouse, antingen genom att [använda Azure Resource Manager mallar](../how-to/onboard-customer.md) eller genom [att publicera ett privat eller offentligt hanterat tjänst erbjudande på Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Visa och hantera kunder](../how-to/view-manage-customers.md) genom att gå till **mina kunder** i Azure Portal.
