---
title: Skapa Azure Application Gateway anpassade felsidor
description: Den här artikeln visar hur du skapar Application Gateway anpassade felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/16/2019
ms.author: victorh
ms.openlocfilehash: 355caeb54f09797ae719f21401ceebb7d53d745a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592728"
---
# <a name="create-application-gateway-custom-error-pages"></a>Skapa Application Gateway anpassade fel sidor

Med Application Gateway kan du skapa anpassade felsidor i stället för att visa standardmässiga felsidor. Du kan använda din egen varumärkesanpassning och layout med hjälp av en anpassad felsida.

Du kan till exempel definiera en egen underhålls sida om ditt webb program inte går att komma åt. Du kan också skapa en obehörig åtkomst sida om en skadlig begäran skickas till ett webb program.

Anpassade felsidor stöds i följande två scenarier:

- **Sidan underhåll** – den här anpassade felsidan skickas i stället för en 502-Felaktig gateway-sida. Den visas när Application Gateway saknar Server del för att dirigera trafik till. Till exempel när det schemalagda underhållet är schemalagt eller om ett oförutsett problem påverkar backend-poolens åtkomst.
- **Sidan obehörig åtkomst** – den här anpassade felsidan skickas i stället för en otillåten åtkomst sida på 403. Den visas när Application Gateway WAF identifierar skadlig trafik och blockerar den.

Om ett fel härstammar från backend-servrarna skickas det vidare tillbaka till anroparen. Sidan anpassad fel visas inte. Application Gateway kan visa en anpassad felsida när en begäran inte kan komma åt Server delen.

Anpassade felsidor kan definieras på global nivå och på lyssnar nivån:

- **Global nivå** – sidan fel gäller för trafik för alla webb program som distribueras på programgatewayen.
- **Lyssnar nivå** -fel sidan används för trafik som tas emot på den lyssnaren.
- **Båda** – den anpassade felsidan som definierats på lyssnar nivån åsidosätter den uppsättning på global nivå.

Om du vill skapa en anpassad felsida måste du ha:

- en status kod för HTTP-svar.
- motsvarande plats för fel sidan. 
- en offentligt tillgänglig Azure Storage-BLOB för platsen.
- en fil av typen *. htm eller *. html. 

Fel sidans storlek måste vara mindre än 1 MB. Om det finns bilder som är länkade till felsidan, måste de vara antingen offentligt tillgängliga absoluta URL: er eller Base64-kodad bild infogad på sidan med anpassade fel. Relativa länkar med avbildningar på samma BLOB-plats stöds inte för närvarande. 

När du har angett en felsida laddar programgatewayen ned den från lagrings-BLOB-platsen och sparar den i den lokala Programgateway-cachen. Sedan betjänas sidan fel direkt från programgatewayen. Om du vill ändra en befintlig sida med anpassade fel måste du peka på en annan BLOB-plats i konfigurationen för programgatewayen. Programgatewayen kontrollerar inte regelbundet BLOB-platsen för att hämta nya versioner.

## <a name="portal-configuration"></a>Portal konfiguration

1. Navigera till Application Gateway i portalen och välj en Programgateway.

    ![Skärm bild som visar översikts sidan för en Application Gateway.](media/custom-error/ag-overview.png)
2. Klicka på **lyssnare** och navigera till en viss lyssnare där du vill ange en felsida.

    ![Application Gateway lyssnare](media/custom-error/ag-listener.png)
3. Konfigurera en anpassad felsida för ett 403 WAF-fel eller en 502 underhålls sida på lyssnar nivån.

    > [!NOTE]
    > Det finns för närvarande inte stöd för att skapa anpassade fel sidor på global nivå från Azure Portal.

4. Ange en offentligt tillgänglig BLOB-URL för en viss fel status kod och klicka på **Spara**. Application Gateway har nu kon figurer ATS med sidan för anpassade fel.

   ![Application Gateway felkoder](media/custom-error/ag-error-codes.png)

## <a name="azure-powershell-configuration"></a>Azure PowerShell-konfiguration

Du kan använda Azure PowerShell för att konfigurera en anpassad felsida. Till exempel en global anpassad felsida:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$updatedgateway = Add-AzApplicationGatewayCustomError -ApplicationGateway $appgw -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Eller på fel sidan på lyssnar nivån:

```powershell
$appgw   = Get-AzApplicationGateway -Name <app-gateway-name> -ResourceGroupName <resource-group-name>

$listener01 = Get-AzApplicationGatewayHttpListener -Name <listener-name> -ApplicationGateway $appgw

$updatedlistener = Add-AzApplicationGatewayHttpListenerCustomError -HttpListener $listener01 -StatusCode HttpStatus502 -CustomErrorPageUrl "http://<website-url>"
```

Mer information finns i [Add-AzApplicationGatewayCustomError](/powershell/module/az.network/add-azapplicationgatewaycustomerror) och [Add-AzApplicationGatewayHttpListenerCustomError](/powershell/module/az.network/add-azapplicationgatewayhttplistenercustomerror).

## <a name="next-steps"></a>Nästa steg

Information om Application Gateway diagnostik finns i [backend-hälsa, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).
