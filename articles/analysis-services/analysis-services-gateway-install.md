---
title: Installera lokal datagateway för Azure Analysis Services | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar en lokal datagateway för att ansluta till lokala data källor från en Azure Analysis Services-server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 64bd9e4a4cf78d2628e946af30c2d290ff002cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081152"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installera och konfigurera lokal datagateway

En lokal datagateway krävs när en eller flera Azure Analysis Services-servrar i samma region ansluter till lokala data källor.  Även om den gateway som du installerar är samma som används av andra tjänster som Power BI, Power Apps och Logic Apps när du installerar för Azure Analysis Services, finns det några ytterligare steg som du måste utföra. Den här installations artikeln är unik för **Azure Analysis Services**. 

Mer information om hur Azure Analysis Services fungerar med gatewayen finns i [ansluta till lokala data källor](analysis-services-gateway.md). Mer information om avancerade installations scenarier och gatewayen i allmänhet finns i [dokumentationen om lokala datagatewayer](/data-integration/gateway/service-gateway-onprem).

## <a name="prerequisites"></a>Förutsättningar

**Minimi krav:**

* .NET 4.5 Framework
* 64-bitars version av Windows 8/Windows Server 2012 R2 (eller senare)

**Rekommenderas**

* 8 CPU-kärnor
* 8 GB minne
* 64-bitars version av Windows 8/Windows Server 2012 R2 (eller senare)

**Viktiga överväganden:**

* När du registrerar din gateway med Azure är standard regionen för din prenumeration markerad under installationen. Du kan välja en annan prenumeration och region. Om du har servrar i fler än en region måste du installera en gateway för varje region. 
* Gatewayen kan inte installeras på en domänkontrollant.
* Det går bara att installera en gateway på en enda dator.
* Installera gatewayen på en dator som är påslagen och inte övergår i vilo läge.
* Installera inte gatewayen på en dator med en trådlös anslutning till nätverket. Prestanda kan minskas.
* När du installerar gatewayen måste det användar konto som du är inloggad på datorn med ha behörighet att logga in som tjänst. När installationen är klar använder den lokala datagateway-tjänsten NT SERVICE\PBIEgwService-kontot för att logga in som en tjänst. Ett annat konto kan anges under installationen eller i tjänster när installationen är klar. Se till att grupprincip inställningarna tillåter att både kontot du är inloggad med när du installerar och det tjänst konto du väljer har behörigheten Logga in som tjänst.
* Logga in på Azure med ett konto i Azure AD för samma [klient organisation](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) som den prenumeration som du registrerar gatewayen i. Konton för Azure B2B (gäst) stöds inte när du installerar och registrerar en gateway.
* Om data källor finns på ett Azure-Virtual Network (VNet) måste du konfigurera egenskapen [AlwaysUseGateway](analysis-services-vnet-gateway.md) Server.

## <a name="download"></a>Ladda ned

 [Hämta gatewayen](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installera

1. Kör installations programmet.

2. Välj **lokal datagateway**.

   ![Välj](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Välj en plats, Godkänn villkoren och klicka sedan på **Installera**.

   ![Installations plats och licens villkor](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Logga in i Azure. Kontot måste finnas i klientens Azure Active Directory. Det här kontot används för gateway-administratören. Azure B2B-konton (gäst) stöds inte när gatewayen installeras och registreras.

   ![Logga in på Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Om du loggar in med ett domän konto mappas det till ditt organisations konto i Azure AD. Ditt organisations konto används som gateway-administratör.

## <a name="register"></a>Registrera dig

För att kunna skapa en gateway-resurs i Azure måste du registrera den lokala instansen som du installerade med Gateway-moln tjänsten. 

1.  Välj **Registrera en ny gateway på den här datorn**.

    ![Skärm bild som markerar alternativet registrera en ny gateway på den här datorn.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Ange ett namn och en återställnings nyckel för din gateway. Som standard använder gatewayen din prenumerations standard region. Om du behöver välja en annan region väljer du **ändra region**.

    > [!IMPORTANT]
    > Spara återställnings nyckeln på en säker plats. Återställnings nyckeln krävs för att kunna ta över, migrera eller återställa en gateway. 

   ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Skapa en Azure gateway-resurs

När du har installerat och registrerat din gateway måste du skapa en gateway-resurs i Azure. Logga in på Azure med samma konto som du använde när du registrerade gatewayen.

1. Klicka på **skapa en resurs** i Azure Portal och Sök sedan efter **lokal datagateway** och klicka sedan på **skapa**.

   ![Skapa en gateway-resurs](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. I **skapa Gateway för anslutning** anger du följande inställningar:

   * **Namn**: Ange ett namn för din gateway-resurs. 

   * **Prenumeration**: Välj den Azure-prenumeration som du vill koppla till din gateway-resurs. 
   
     Standard prenumerationen baseras på det Azure-konto som du använde för att logga in.

   * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.

   * **Plats**: Välj den region som du registrerade din gateway i.

   * **Installations namn**: om inte Gateway-installationen redan är vald väljer du den gateway som du har installerat på datorn och registrerat. 

     När du är klar klickar du på **skapa**.

## <a name="connect-gateway-resource-to-server"></a>Anslut gateway-resurs till Server

> [!NOTE]
> Att ansluta till en gateway-resurs i en annan prenumeration från servern stöds inte i portalen, men stöds med PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klicka på **lokal datagateway** i Azure Analysis Services server översikt.

   ![Anslut server till gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. I **Välj en lokal datagateway att ansluta** väljer du din gateway-resurs och klickar sedan på **Anslut vald Gateway**.

   ![Anslut server till gateway-resurs](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Om din Gateway inte visas i listan, är servern förmodligen inte i samma region som den region som du angav när du registrerade gatewayen.

    När anslutningen mellan servern och gateway-resursen lyckas visas statusen **ansluten**.


    ![Anslutning av server till gateway-resurs lyckades](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd [Get-AzResource](/powershell/module/az.resources/get-azresource) för att hämta Gateway-ResourceID. Anslut sedan Gateway-resursen till en befintlig eller ny server genom att ange **-GatewayResourceID** i [set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) eller [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Hämta gatewayens resurs-ID:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforGateway -Environment "AzureCloud"
$GatewayResourceId = $(Get-AzResource -ResourceType "Microsoft.Web/connectionGateways" -Name $gatewayName).ResourceId  

```

Så här konfigurerar du en befintlig server:

```azurepowershell-interactive
Connect-AzAccount -Tenant $TenantId -Subscription $subscriptionIdforAzureAS -Environment "AzureCloud"
Set-AzAnalysisServicesServer -ResourceGroupName $RGName -Name $servername -GatewayResourceId $GatewayResourceId

```
---

Klart! Om du behöver öppna portar eller göra en fel sökning måste du ta en titt [på den lokala datagatewayen](analysis-services-gateway.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Analysis Services](analysis-services-manage.md)   
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)   
* [Använda gateway för datakällor på ett virtuellt Azure-nätverk](analysis-services-vnet-gateway.md)