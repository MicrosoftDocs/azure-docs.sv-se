---
title: Automatisera Azure Analysis Services uppgifter med tjänstens huvud namn | Microsoft Docs
description: Lär dig hur du skapar ett huvud namn för tjänsten för att automatisera Azure Analysis Services administrativa uppgifter.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b04b9ababfe0e4c2a60d14044b9d3ee120837dc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491051"
---
# <a name="automation-with-service-principals"></a>Automatisering med tjänstens huvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. De är en unik typ av *användar identitet* med ett program-ID och ett lösen ord eller certifikat. Ett huvud namn för tjänsten har bara de behörigheter som krävs för att utföra uppgifter som definierats av de roller och behörigheter som det har tilldelats. 

I Analysis Services används tjänstens huvud namn med Azure Automation, PowerShell obevakat läge, anpassade klient program och webbappar för att automatisera vanliga uppgifter. Till exempel kan etablering av servrar, distribution av modeller, data uppdatering, skala upp/ned och pausa/återuppta kan automatiseras med hjälp av tjänstens huvud namn. Behörigheter tilldelas tjänstens huvud namn via roll medlemskap, ungefär som vanliga Azure AD UPN-konton.

Analysis Services stöder också åtgärder som utförs av hanterade identiteter med tjänstens huvud namn. Läs mer i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) och [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).    

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
 
Tjänstens huvud namn kan skapas i Azure Portal eller med hjälp av PowerShell. Mer information finns i:

[Skapa tjänstens huvudnamn – Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Lagra autentiseringsuppgifter och certifikat till gångar i Azure Automation

Autentiseringsuppgifter och certifikat för tjänstens huvud namn kan lagras på ett säkert sätt i Azure Automation för Runbook-åtgärder. Mer information finns i:

[Inloggnings till gångar i Azure Automation](../automation/shared-resources/credentials.md)   
[Certifikattillgångar i Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Lägg till tjänstens huvud namn i rollen Server administratör

Innan du kan använda ett huvud namn för tjänsten för Analysis Services server hanterings åtgärder måste du lägga till det i rollen Server administratörer. Tjänstens huvud namn måste läggas till direkt i Server administratörs rollen. Det går inte att lägga till ett huvud namn för tjänsten i en säkerhets grupp och sedan lägga till säkerhets gruppen till Server administratörs rollen. Mer information finns i [lägga till ett huvud namn för tjänsten i Server administratörs rollen](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Tjänstens huvud namn i anslutnings strängar

SPN-namn och lösen ord eller certifikat för tjänstens huvud namn kan användas i anslutnings strängar, ungefär som ett UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Använda modulen AZ. AnalysisServices

Använd cmdlet när du använder ett huvud namn för tjänsten för resurs hanterings åtgärder med modulen [AZ. AnalysisServices](/powershell/module/az.analysisservices) . `Connect-AzAccount` 

I följande exempel används appID och ett lösen ord för att utföra kontroll Plans åtgärder för synkronisering till skrivskyddade repliker och skala upp/ut:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Använda SQLServer-modulen

I följande exempel används appID och ett lösen ord för att utföra en uppdaterings åtgärd för modell databasen:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO och ADOMD 

När du ansluter med klientprogram och webbappar har [AMO- och ADOMD-klientbibliotek](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) version 15.0.2 och senare installerbara paket från NuGet stöd för tjänstens huvudnamn i anslutningssträngar med hjälp av följande syntax: `app:AppID` och lösenord eller `cert:thumbprint`. 

I följande exempel används `appID` och `password` för att utföra en uppdateringsåtgärd för modelldatabasen:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Nästa steg
[Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Uppdatera med Logic Apps](analysis-services-refresh-logic-app.md)  
[Uppdatera med Azure Automation](analysis-services-refresh-azure-automation.md)  
[Lägg till ett huvud namn för tjänsten i Server administratörs rollen](analysis-services-addservprinc-admins.md)  
[Automatisera Power BI Premium arbets ytan och data mängds aktiviteter med tjänstens huvud namn](/power-bi/admin/service-premium-service-principal)