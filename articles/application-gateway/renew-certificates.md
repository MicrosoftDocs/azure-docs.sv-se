---
title: Förnya ett Azure Application-Gateway-certifikat
description: Lär dig hur du förnyar ett certifikat som är associerat med en Application Gateway-lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 01/20/2021
ms.author: victorh
ms.openlocfilehash: f0c06a94498f4d2481a6e953b959d766c60415fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98622188"
---
# <a name="renew-application-gateway-certificates"></a>Förnya Application Gateway certifikat

Vid något tillfälle måste du förnya dina certifikat om du har konfigurerat din Application Gateway för TLS/SSL-kryptering.

Du kan förnya ett certifikat som är associerat med en lyssnare med antingen Azure Portal, Azure PowerShell eller Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Om du vill förnya ett lyssnar certifikat från portalen navigerar du till dina Application Gateway-lyssnare. Välj den lyssnare som har ett certifikat som behöver förnyas och välj sedan **förnya eller redigera det valda certifikatet**.

:::image type="content" source="media/renew-certificate/ssl-cert.png" alt-text="Förnya certifikat":::

Överför ditt nya PFX-certifikat, ge det ett namn, ange lösen ordet och välj sedan **Spara**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill förnya certifikatet med Azure PowerShell använder du följande skript:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar TLS-avlastning med Azure Application Gateway finns i [Konfigurera TLS-avläsning](./create-ssl-portal.md)