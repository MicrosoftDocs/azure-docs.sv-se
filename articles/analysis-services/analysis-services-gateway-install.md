---
title: Installera lokal datagateway för Azure Analysis Services | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar en lokal datagateway för att ansluta till lokala datakällor från en Azure Analysis Services server.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43e5b64d06a6ec145876798b2e0da6499ab94bfc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769237"
---
# <a name="install-and-configure-an-on-premises-data-gateway"></a>Installera och konfigurera lokal datagateway

En lokal datagateway krävs när en eller flera Azure Analysis Services i samma region ansluter till lokala datakällor.  Även om den gateway som du installerar är samma som används av andra tjänster som Power BI, Power Apps och Logic Apps, finns det några ytterligare steg som du måste utföra när du installerar för Azure Analysis Services. Den här installationsartikeln är specifik **för Azure Analysis Services**. 

Mer information om hur Azure Analysis Services fungerar med gatewayen finns [i Ansluta till lokala datakällor.](analysis-services-gateway.md) Mer information om avancerade installationsscenarier och gatewayen i allmänhet finns [i dokumentationen om lokala datagatewayer.](/data-integration/gateway/service-gateway-onprem)

## <a name="prerequisites"></a>Förutsättningar

**Minimikrav:**

* .NET 4.5 Framework
* 64-bitars version Windows 8/Windows Server 2012 R2 (eller senare)

**Rekommenderas:**

* 8 CPU-kärnor
* 8 GB minne
* 64-bitars version Windows 8/Windows Server 2012 R2 (eller senare)

**Viktiga överväganden:**

* När du registrerar din gateway med Azure under installationen väljs standardregionen för din prenumeration. Du kan välja en annan prenumeration och region. Om du har servrar i mer än en region måste du installera en gateway för varje region. 
* Gatewayen kan inte installeras på en domänkontrollant.
* Endast en gateway kan installeras på en enda dator.
* Installera gatewayen på en dator som förblir på och inte för strömsparläge.
* Installera inte gatewayen på en dator med en trådlös anslutning till nätverket. Prestandan kan minskas.
* När du installerar gatewayen måste det användarkonto som du är inloggad på datorn med ha behörighet att logga in som tjänst. När installationen är klar använder den lokala datagatewaytjänsten KONTOT NT SERVICE\PBIEgwService för att logga in som en tjänst. Ett annat konto kan anges under installationen eller i Tjänster när installationen är klar. Se grupprincip tillåter både det konto som du är inloggad med när du installerar och det tjänstkonto som du väljer har inloggning som tjänstbehörighet.
* Logga in på Azure med ett konto i Azure AD för samma [klientorganisation](/previous-versions/azure/azure-services/jj573650(v=azure.100)#what-is-an-azure-ad-tenant) som den prenumeration som du registrerar gatewayen i. Azure B2B-konton (gäst) stöds inte vid installation och registrering av en gateway.
* Om datakällor finns på en Azure Virtual Network (VNet) måste du konfigurera [serveregenskapen AlwaysUseGateway.](analysis-services-vnet-gateway.md)

## <a name="download"></a>Ladda ned

 [Ladda ned gatewayen](https://go.microsoft.com/fwlink/?LinkId=820925&clcid=0x409)

## <a name="install"></a>Installera

1. Kör installationsprogrammet.

2. Välj **Lokal datagateway**.

   ![Välj](media/analysis-services-gateway-install/aas-gateway-installer-select.png)

2. Välj en plats, godkänn villkoren och klicka sedan på **Installera**.

   ![Installationsplats och licensvillkor](media/analysis-services-gateway-install/aas-gateway-installer-accept.png)

3. Logga in i Azure. Kontot måste finnas i klientorganisationens Azure Active Directory. Det här kontot används för gatewayadministratören. Azure B2B-konton (gäst) stöds inte vid installation och registrering av gatewayen.

   ![Logga in på Azure](media/analysis-services-gateway-install/aas-gateway-installer-account.png)

   > [!NOTE]
   > Om du loggar in med ett domänkonto mappas det till ditt organisationskonto i Azure AD. Ditt organisationskonto används som gatewayadministratör.

## <a name="register"></a>Registrera dig

För att kunna skapa en gatewayresurs i Azure måste du registrera den lokala instans som du installerade med gatewaymolntjänsten. 

1.  Välj **Registrera en ny gateway på den här datorn.**

    ![Skärmbild som visar alternativet Registrera en ny gateway på den här datorn.](media/analysis-services-gateway-install/aas-gateway-register-new.png)

2. Ange ett namn och en återställningsnyckel för din gateway. Som standard använder gatewayen din prenumerations standardregion. Om du behöver välja en annan region väljer du **Ändra region.**

    > [!IMPORTANT]
    > Spara återställningsnyckeln på en säker plats. Återställningsnyckeln krävs för att kunna ta över, migrera eller återställa en gateway. 

   ![Registrera dig](media/analysis-services-gateway-install/aas-gateway-register-name.png)


## <a name="create-an-azure-gateway-resource"></a>Skapa en Azure-gatewayresurs

När du har installerat och registrerat din gateway måste du skapa en gatewayresurs i Azure. Logga in på Azure med samma konto som du använde när du registrera gatewayen.

1. I Azure Portal klickar **du på Skapa en** resurs och söker sedan **efter Lokal datagateway** och klickar sedan på **Skapa.**

   ![Skapa en gatewayresurs](media/analysis-services-gateway-install/aas-gateway-new-azure-resource.png)

2. I **Skapa anslutningsgateway** anger du följande inställningar:

   * **Namn:** Ange ett namn för din gatewayresurs. 

   * **Prenumeration:** Välj den Azure-prenumeration som du vill associera med din gatewayresurs. 
   
     Standardprenumerationen baseras på det Azure-konto som du använde för att logga in.

   * **Resursgrupp**: Skapa en resursgrupp eller välj en befintlig resursgrupp.

   * **Plats:** Välj den region där du registrerade din gateway.

   * **Installationsnamn:** Om gatewayinstallationen inte redan är vald väljer du den gateway som du installerade på datorn och registrerade. 

     När du är klar klickar du på **Skapa**.

## <a name="connect-gateway-resource-to-server"></a>Ansluta gatewayresurs till server

> [!NOTE]
> Det finns inte stöd för att ansluta till en gatewayresurs i en annan prenumeration än din server i portalen, men du kan använda PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. I Azure Analysis Services serveröversikten klickar **du på Lokal datagateway**.

   ![Ansluta server till gateway](media/analysis-services-gateway-install/aas-gateway-connect-server.png)

2. I **Välj en lokal datagateway för att ansluta** väljer du din gatewayresurs och klickar sedan på Anslut vald **gateway.**

   ![Ansluta servern till gatewayresursen](media/analysis-services-gateway-install/aas-gateway-connect-resource.png)

    > [!NOTE]
    > Om din gateway inte visas i listan finns servern förmodligen inte i samma region som den region som du angav när du registrerar gatewayen.

    När anslutningen mellan servern och gatewayresursen lyckas visas Ansluten **i** statusen .


    ![Anslut servern till gatewayresursen](media/analysis-services-gateway-install/aas-gateway-connect-success.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Använd [Get-AzResource](/powershell/module/az.resources/get-azresource) för att hämta gatewayens ResourceID. Anslut sedan gatewayresursen till en befintlig eller ny server genom att ange **-GatewayResourceID** i [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) eller [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver).

Så här hämtar du gatewayresurs-ID:t:

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

Klart! Om du behöver öppna portar eller felsöka kan du läsa [Den lokala datagatewayen.](analysis-services-gateway.md)

## <a name="next-steps"></a>Nästa steg

* [Hantera Analysis Services](analysis-services-manage.md)   
* [Hämta data från Azure Analysis Services](analysis-services-connect.md)   
* [Använda gateway för datakällor på ett virtuellt Azure-nätverk](analysis-services-vnet-gateway.md)
