---
title: Automatisera Azure Analysis Services med tjänstens huvudnamn | Microsoft Docs
description: Lär dig hur du skapar ett huvudnamn för tjänsten för att Azure Analysis Services administrativa uppgifter.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04bc6ecd6e0a32e9234d07e995a7e012b17e3bbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769165"
---
# <a name="automation-with-service-principals"></a>Automatisering med tjänstens huvudnamn

Tjänstens huvudnamn är en programresurs för Azure Active Directory som du skapar i din klient för att utföra obevakade åtgärder på resurs- och tjänstnivå. De är en unik typ av *användaridentitet med* ett program-ID och lösenord eller certifikat. Tjänstens huvudnamn har endast de behörigheter som krävs för att utföra uppgifter som definieras av de roller och behörigheter som det har tilldelats. 

I Analysis Services används tjänstens huvudnamn med Azure Automation, obevakat PowerShell-läge, anpassade klientprogram och webbappar för att automatisera vanliga uppgifter. Till exempel kan etableringsservrar, distribution av modeller, datauppdatering, uppskalning/nedskalning och pausa/återuppta automatiseras med hjälp av tjänstens huvudnamn. Behörigheter tilldelas till tjänstens huvudnamn via rollmedlemskap, ungefär som vanliga Azure AD UPN-konton.

Analysis Services också stöd för åtgärder som utförs av hanterade identiteter med hjälp av tjänstens huvudnamn. Mer information finns i [Hanterade identiteter för Azure-resurser och](../active-directory/managed-identities-azure-resources/overview.md) [Azure-tjänster som stöder Azure AD-autentisering.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)    

## <a name="create-service-principals"></a>Skapa tjänsthuvudnamn
 
Tjänstens huvudnamn kan skapas i Azure Portal eller med hjälp av PowerShell. Mer information finns i:

[Skapa tjänstens huvudnamn – Azure-portalen](../active-directory/develop/howto-create-service-principal-portal.md)   
[Skapa tjänstens huvudnamn – PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Lagra autentiseringsuppgifter och certifikattillgångar i Azure Automation

Autentiseringsuppgifter för tjänstens huvudnamn och certifikat kan lagras på ett säkert Azure Automation för runbook-åtgärder. Mer information finns i:

[Autentiseringstillgångar i Azure Automation](../automation/shared-resources/credentials.md)   
[Certifikattillgångar i Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Lägga till tjänstens huvudnamn till serveradministratörsrollen

Innan du kan använda tjänstens huvudnamn Analysis Services serverhanteringsåtgärder måste du lägga till det i serveradministratörsrollen. Tjänstens huvudnamn måste läggas till direkt i serveradministratörsrollen. Det går inte att lägga till tjänstens huvudnamn i en säkerhetsgrupp och sedan lägga till säkerhetsgruppen i serveradministratörsrollen. Mer information finns i Lägga [till ett huvudnamn för tjänsten i serveradministratörsrollen](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Tjänstens huvudnamn i anslutningssträngar

AppID för tjänstens huvudnamn och lösenord eller certifikat kan användas i anslutningssträngar på ungefär samma sätt som ett UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Använda Az.AnalysisServices-modulen

När du använder tjänstens huvudnamn för resurshanteringsåtgärder med [modulen Az.AnalysisServices](/powershell/module/az.analysisservices)  använder du `Connect-AzAccount` cmdleten . 

I följande exempel används appID och ett lösenord för att utföra kontrollplansåtgärder för synkronisering till skrivskyddade repliker och skala upp/ut:

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

I följande exempel används appID och ett lösenord för att utföra en uppdateringsåtgärd för modelldatabasen:

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
[Lägga till ett huvudnamn för tjänsten i serveradministratörsrollen](analysis-services-addservprinc-admins.md)  
[Automatisera Power BI Premium och datauppsättningsuppgifter med tjänstens huvudnamn](/power-bi/admin/service-premium-service-principal)
