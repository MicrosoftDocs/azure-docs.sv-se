---
title: 'Skapa en Azure skydds-värd: Portal'
description: I den här artikeln lär du dig hur du skapar en Azure skydds-värd, som tillhandahåller RDP/SSH-anslutning till alla virtuella datorer i ett virtuellt nätverk.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 04/24/2020
ms.author: cherylmc
ms.openlocfilehash: 1e0bc9a17a12afac6d91867b19aba0f90b7f6edb
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88270758"
---
# <a name="create-an-azure-bastion-host-using-the-portal"></a>Skapa en Azure skydds-värd med portalen

Den här artikeln visar hur du skapar en Azure skydds-värd med hjälp av Azure Portal. När du etablerar Azure skydds-tjänsten i det virtuella nätverket är den sömlösa RDP/SSH-upplevelsen tillgänglig för alla virtuella datorer i samma virtuella nätverk. Azure skydds-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

Du kan skapa en ny skydds-värd resurs genom att ange alla inställningar manuellt eller genom att använda de inställningar som motsvarar en befintlig virtuell dator. Om du vill skapa en skydds-värd med hjälp av inställningarna för virtuella datorer, se [snabb starts](quickstart-host-portal.md) artikeln. Du kan också använda [Azure PowerShell](bastion-create-host-powershell.md) för att skapa en Azure skydds-värd.

## <a name="before-you-begin"></a>Innan du börjar

Skydds finns i följande offentliga Azure-regioner:

[!INCLUDE [available regions](../../includes/bastion-regions-include.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Skapa en skydds-värd

Det här avsnittet hjälper dig att skapa en ny Azure skydds-resurs från Azure Portal.

1. I menyn i [Azure-portalen](https://portal.azure.com) eller på sidan **Start** väljer du **Skapa en resurs**.

1. På sidan **ny** i fältet *Sök på Marketplace skriver du* **skydds**och klickar sedan på **RETUR** för att komma till Sök resultaten.

1. Klicka på **skydds**i resultatet. Kontrol lera att utgivaren är *Microsoft* och att kategorin är *nätverk*.

1. På sidan **skydds** klickar du på **skapa** för att öppna sidan **skapa en skydds** .

1. På sidan **skapa en skydds** konfigurerar du en ny skydds-resurs. Ange konfigurations inställningarna för skydds-resursen.

    ![skapa en skydds](./media/bastion-create-host-portal/settings.png)

    * **Prenumeration**: den Azure-prenumeration som du vill använda för att skapa en ny skydds-resurs.
    * **Resurs grupp**: Azure-resurs gruppen där den nya skydds-resursen ska skapas i. Om du inte har en befintlig resurs grupp kan du skapa en ny.
    * **Namn**: namnet på den nya skydds-resursen
    * **Region**: den offentliga Azure-region som resursen ska skapas i.
    * **Virtuellt nätverk**: det virtuella nätverk där skydds-resursen ska skapas i. Du kan skapa ett nytt virtuellt nätverk i portalen under den här processen eller använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kontrollerar du att det befintliga virtuella nätverket har tillräckligt med ledigt adress utrymme för att uppfylla skydds-undernätets krav.
    * **Undernät**: under nätet i det virtuella nätverket där den nya skydds-värden ska distribueras. Under nätet är dedicerat till skydds-värden och måste namnges som **AzureBastionSubnet**. Det här under nätet måste vara minst/27 eller större.
    
       **AzureBastionSubnet** har inte stöd för [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md#custom-routes), men stöder [nätverks säkerhets grupper](bastion-nsg.md).
    * **Offentlig IP-adress**: den offentliga IP-adressen för den skydds-resurs som RDP/SSH kommer att få åtkomst till (via port 443). Skapa en ny offentlig IP-adress eller Använd en befintlig. Den offentliga IP-adressen måste finnas i samma region som den skydds-resurs som du skapar.
    * **Namn på offentlig IP-adress**: namnet på den offentliga IP-adressresursen.
    * **SKU för offentlig IP-adress**: den här inställningen fylls i automatiskt **som standard.** Azure skydds använder/stöder bara standard-offentliga IP SKU.
    * **Tilldelning**: den här inställningen är förifylld som standard som **statisk**.

1. När du är klar med att ange inställningarna klickar du på **Granska + skapa**. Detta validerar värdena. När verifieringen är klar kan du börja skapa processen.
1. På sidan **skapa en skydds** klickar du på **skapa**.
1. Ett meddelande visas där du vet att distributionen pågår. Statusen visas på den här sidan när resurserna skapas. Det tar ungefär 5 minuter för skydds-resursen att skapas och distribueras.

## <a name="next-steps"></a>Nästa steg

* Mer information finns i [vanliga frågor och svar om skydds](bastion-faq.md) .

* Information om hur du använder nätverks säkerhets grupper med Azure skydds-undernätet finns i [arbeta med NSG: er](bastion-nsg.md).
