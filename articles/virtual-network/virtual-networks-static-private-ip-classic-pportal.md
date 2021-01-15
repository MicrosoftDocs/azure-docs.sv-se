---
title: Konfigurera privata IP-adresser för virtuella datorer (klassisk) – Azure Portal | Microsoft Docs
description: Lär dig hur du konfigurerar privata IP-adresser för virtuella datorer (klassisk) med hjälp av Azure Portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222912"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurera privata IP-adresser för en virtuell dator (klassisk) med hjälp av Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver hur du gör om du använder den klassiska distributionsmodellen. Du kan också [hantera en statisk privat IP-adress i distributions modellen för Resource Manager](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

De exempel steg som följer förväntar sig en enkel miljö som redan har skapats. Om du vill köra stegen som de visas i det här dokumentet skapar du först test miljön som beskrivs i [skapa ett VNet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Så här anger du en statisk privat IP-adress när du skapar en virtuell dator
Utför följande steg för att skapa en virtuell dator med namnet *DNS01* i *klient delens* undernät med namnet *TestVNet* med en statisk privat IP-adress för *192.168.1.101*:

1. Navigera till https://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Välj **ny**  >  **Compute**  >  **Windows Server 2012 R2 Data Center**, Lägg märke till att listan **Välj en distributions modell** redan visas som **klassisk** och välj sedan **skapa**.
   
    ![Skärm bild som visar Azure Portal med panelen ny > Compute > Windows Server 2012 R2 Data Center markerat.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. Under **Skapa virtuell dator** anger du namnet på den virtuella dator som ska skapas (*DNS01* i scenariot), det lokala administratörs kontot och lösen ordet.
   
    ![Skärm bild som visar hur du skapar en virtuell dator genom att ange namnet på den virtuella datorn, det lokala administratörs användar namnet och lösen ordet.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Välj **valfria**  >  **nätverks**  >  **Virtual Network** för konfiguration och välj sedan **TestVNet**. Om **TestVNet** inte är tillgängligt kontrollerar du att du använder den *centrala amerikanska* platsen och har skapat test miljön som beskrivs i början av den här artikeln.
   
    ![Skärm bild som visar alternativet för valfri konfiguration > nätverks > Virtual Network > TestVNet markerat.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Under **nätverk** kontrollerar du att det valda under nätet är *FrontEnd* och väljer sedan **IP-adresser**, under **IP-adresstilldelning** , Välj **statiskt** och ange sedan *192.168.1.101* för **IP-adressen** som visas nedan.
   
    ![Skärm bild som markerar fältet IP-adresser där du anger den statiska IP-adressen.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Välj **OK** under **IP-adresser**, Välj **OK** under **nätverk** och välj sedan **OK** under **valfri konfiguration**.
7. Under **Skapa virtuell dator** väljer du **skapa**. Lägg märke till panelen nedan som visas på instrument panelen:
   
    ![Skärm bild som visar panelen skapa Windows Server 2012 R2 Data Center.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Hämta information om statisk privat IP-adress för en virtuell dator
Om du vill visa information om statisk privat IP-adress för den virtuella datorn som skapades med stegen ovan utför du stegen nedan.

1. Från Azure Portal väljer du **Bläddra bland alla**  >  **virtuella datorer (klassisk)**  >  **DNS01**  >  **alla inställningar**  >  **IP-adresser** och noterar IP-adresstilldelning och IP-adress som visas nedan.
   
    ![Skapa virtuell dator i Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Ta bort en statisk privat IP-adress från en virtuell dator

Under **IP-adresser** väljer du **dynamisk** till höger om **tilldelning av IP-adress**, väljer **Spara** och väljer sedan **Ja**, som du ser i följande bild:
   
![Skärm bild som visar hur du tar bort den statiska privata IP-adressen från en virtuell dator genom att välja dynamisk till höger om etiketten för IP-adresstilldelning.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Så här lägger du till en statisk privat IP-adress till en befintlig virtuell dator

1. Under **IP-adresser**, som visas tidigare, väljer du **statisk** till höger om **tilldelning av IP-adresser**.
2. Skriv *192.168.1.101* för **IP-adress**, Välj **Spara** och välj sedan **Ja**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ange IP-adresser inom operativ systemet

Vi rekommenderar att du inte statiskt tilldelar den privata IP-adress som tilldelats den virtuella Azure-datorn i operativ systemet på en virtuell dator, om det inte behövs. Om du manuellt ställer in den privata IP-adressen i operativ systemet måste du kontrol lera att den är samma adress som den privata IP-adress som tilldelats den virtuella Azure-datorn, eller så kan du förlora anslutningen till den virtuella datorn. Du bör aldrig manuellt tilldela den offentliga IP-adress som tilldelats till en virtuell Azure-dator i den virtuella datorns operativ system.

## <a name="next-steps"></a>Nästa steg
* Läs mer om [reserverade offentliga IP-](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) adresser.
* Lär dig mer om [offentliga IP-adresser på instans nivå (ILPIP)](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) .
* Läs [reserverad IP REST-API: er](/previous-versions/azure/reference/dn722420(v=azure.100)).