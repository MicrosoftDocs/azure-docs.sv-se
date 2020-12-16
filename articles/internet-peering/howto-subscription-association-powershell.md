---
title: Koppla peer-ASN till Azure-prenumeration med hjälp av PowerShell
titleSuffix: Azure
description: Koppla peer-ASN till Azure-prenumeration med hjälp av PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 12/15/2020
ms.author: prmitiki
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b463293899dc2586404d68145943caff3105e89
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590196"
---
# <a name="associate-peer-asn-to-azure-subscription-using-powershell"></a>Koppla peer-ASN till Azure-prenumeration med hjälp av PowerShell

Innan du skickar in en peering-begäran bör du först associera ditt ASN med Azure-prenumerationen genom att följa stegen nedan.

Om du vill kan du slutföra den här guiden med hjälp av [portalen](howto-subscription-association-portal.md).

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Skapa PeerASN för att koppla ditt ASN med Azure-prenumeration

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="register-for-peering-resource-provider"></a>Registrera dig för peering Resource Provider
Registrera dig för peering Resource Provider i prenumerationen med hjälp av kommandot nedan. Om du inte kör det går det inte att komma åt de Azure-resurser som krävs för att konfigurera peering.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

Du kan kontrol lera registrerings statusen med hjälp av kommandona nedan:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Vänta tills *RegistrationState* har aktiverat "registrerad" innan du fortsätter. Det kan ta 5 till 30 minuter innan du kör kommandot.

### <a name="update-the-peer-information-associated-with-this-subscription"></a>Uppdatera peer-informationen som är associerad med den här prenumerationen

Nedan visas ett exempel på hur du uppdaterar peer-information.

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> – Namnet motsvarar resurs namnet och kan vara allt du väljer. -PeerName motsvarar dock företagets namn och måste vara så nära som möjligt för din PeeringDB-profil. Observera att värdet för-peerName endast stöder tecknen a-z, A-Z och blank steg.

En prenumeration kan ha flera ASN: er. Uppdatera peering-informationen för varje ASN. Se till att "name" är unikt för varje ASN.

Peer-datorer förväntas ha en fullständig och aktuell profil på [PeeringDB](https://www.peeringdb.com). Vi använder den här informationen under registreringen för att verifiera motpartens information, till exempel NOC information, teknisk kontakt information och deras närvaro vid peering-anläggningarna osv.

Observera att det faktiska prenumerations-ID: t visas i stället för **{subscriptionId}** i utdata ovan.

## <a name="view-status-of-a-peerasn"></a>Visa status för en PeerASN

Sök efter ASN-validerings tillstånd med hjälp av kommandot nedan:

```powershell
Get-AzPeerAsn
```

Nedan visas ett exempel på ett svar:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Vänta tills ValidationState för att aktivera "godkänd" innan du skickar en peering-begäran. Det kan ta upp till 12 timmar innan detta godkännande.

## <a name="modify-peerasn"></a>Ändra PeerAsn
Du kan ändra NOC kontakt information när som helst.

Nedan visas ett exempel:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## <a name="delete-peerasn"></a>Ta bort PeerAsn
Det finns för närvarande inte stöd för att ta bort en PeerASN. Om du behöver ta bort PeerASN kontaktar du [Microsoft-peering](mailto:peering@microsoft.com).

## <a name="next-steps"></a>Nästa steg

* [Skapa eller ändra en Direct-peering](howto-direct-powershell.md)
* [Konvertera en äldre Direct-peering till en Azure-resurs](howto-legacy-direct-powershell.md)
* [Skapa eller ändra Exchange-peering](howto-exchange-powershell.md)
* [Konvertera en äldre Exchange-peering till en Azure-resurs](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Ytterligare resurser

Mer information finns i [vanliga frågor och svar om Internet-peering](faqs.md)
