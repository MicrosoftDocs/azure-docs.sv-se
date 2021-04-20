---
title: Flera IP-adresser för virtuella Azure-datorer – portal | Microsoft Docs
description: Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med hjälp av Azure Portal | Resource Manager.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 050d3ac23562e7822d186a16675d03c1b9dc670b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739214"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Tilldela flera IP-adresser till virtuella datorer med hjälp av Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> Den här artikeln förklarar hur du skapar en virtuell dator (VM) via Azure Resource Manager med hjälp av Azure Portal. Flera IP-adresser kan inte tilldelas till resurser som skapats via den klassiska distributionsmodellen. Mer information om Azure-distributionsmodeller finns i artikeln [Förstå distributionsmodeller.](../azure-resource-manager/management/deployment-models.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Skapa en virtuell dator med flera IP-adresser

Om du vill skapa en virtuell dator med flera IP-adresser eller en statisk privat IP-adress måste du skapa den med Hjälp av PowerShell eller Azure CLI. Om du vill veta hur du gör klickar du på Alternativen för PowerShell eller CLI överst i den här artikeln. Du kan skapa en virtuell dator med en enda dynamisk privat IP-adress och (om du vill) en enskild offentlig IP-adress. Använd portalen genom att följa stegen i artiklarna Skapa en [virtuell Windows-dator](../virtual-machines/windows/quick-create-portal.md) eller [Skapa en virtuell Linux-dator.](../virtual-machines/linux/quick-create-portal.md) När du har skapat den virtuella datorn kan du ändra IP-adresstypen från dynamisk till statisk och lägga till ytterligare IP-adresser med hjälp av portalen genom att följa stegen i avsnittet Lägg till [IP-adresser](#add) till en virtuell dator i den här artikeln.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Lägga till IP-adresser till en virtuell dator

Du kan lägga till privata och offentliga IP-adresser i ett Azure-nätverksgränssnitt genom att utföra följande steg. Exemplen i följande avsnitt förutsätter att du redan har en virtuell dator med de tre IP-konfigurationer som beskrivs i [scenariot](#scenario), men det är inte obligatoriskt.

### <a name="core-steps"></a><a name="coreadd"></a>Viktiga steg

1. Bläddra till Azure Portal på https://portal.azure.com och logga in på den om det behövs.
2. I portalen klickar du på **Fler tjänster** > skriver *virtuella* datorer i filterrutan och klickar sedan på **Virtuella datorer.**
3. I fönstret **Virtuella datorer klickar** du på den virtuella dator som du vill lägga till IP-adresser till. Gå till **fliken** Nätverk. Klicka **på Nätverksgränssnitt** på sidan. Som du ser i bilden nedan: 


    ![Lägg till en offentlig IP-adress till en virtuell dator](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. I fönstret **Nätverksgränssnitt** klickar du på **IP-konfigurationerna**.

5. I fönstret som visas för det nätverkskort som du har valt klickar du på **IP-konfigurationer.** Klicka **på** Lägg till, slutför stegen i något av avsnitten som följer, baserat på vilken typ av IP-adress du vill lägga till och klicka sedan på **OK.** 

### <a name="add-a-private-ip-address"></a>Lägga till en privat IP-adress

Utför följande steg för att lägga till en ny privat IP-adress:

1. Slutför stegen i avsnittet [Kärnsteg i](#coreadd) den här artikeln och kontrollera att du befinner dig i avsnittet **IP-konfigurationer** i VM-nätverksgränssnittet.  Granska undernätet som visas som standard (till exempel 10.0.0.0/24).
2. Klicka på **Lägg till**. I fönstret **Lägg till IP-konfiguration** som visas skapar du en IP-konfiguration med namnet *IPConfig-4* med en ny statisk privat IP-adress genom att välja ett nytt nummer för den sista oktetten och sedan klicka på **OK.**   (För undernätet 10.0.0.0/24 är *exempel-IP-adressen 10.0.0.7*.)

    > [!NOTE]
    > När du lägger till en statisk IP-adress måste du ange en oanvänd, giltig adress i undernätet som nätverkskortet är anslutet till. Om den adress du väljer inte är tillgänglig visar portalen ett X för IP-adressen och du måste välja en annan.

3. När du klickar på OK stängs fönstret och den nya IP-konfigurationen visas. Stäng fönstret Lägg till **IP-konfiguration genom att klicka på** **OK.**
4. Du kan klicka på **Lägg till** för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser.
5. Lägg till de privata IP-adresserna i operativsystemet för den virtuella datorn genom att slutföra stegen i avsnittet Lägg till [IP-adresser](#os-config) till ett operativsystem för en virtuell dator i den här artikeln.

### <a name="add-a-public-ip-address"></a>Lägga till en offentlig IP-adress

En offentlig IP-adress läggs till genom att en offentlig IP-adressresurs associeras med antingen en ny IP-konfiguration eller en befintlig IP-konfiguration.

> [!NOTE]
> Offentliga IP-adresser har en nominell avgift. Mer information om priser för IP-adresser finns på prissättningssidan [för IP-adresser.](https://azure.microsoft.com/pricing/details/ip-addresses) Det finns en gräns för hur många offentliga IP-adresser som kan användas i en prenumeration. Mer information om gränserna finns i artikeln om [Azure-begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Skapa en offentlig IP-adressresurs

En offentlig IP-adress är en inställning för en offentlig IP-adressresurs. Om du har en offentlig IP-adressresurs som för närvarande inte är associerad med en IP-konfiguration som du vill koppla till en IP-konfiguration hoppar du över följande steg och slutför stegen i något av avsnitten som följer efter behov. Om du inte har någon tillgänglig offentlig IP-adressresurs utför du följande steg för att skapa en:

1. Bläddra till Azure Portal på https://portal.azure.com och logga in på den om det behövs.
3. I portalen klickar du på **Skapa en offentlig**  >  **IP-adress**  >  **för ett nätverk.**
4. I fönstret Skapa offentlig **IP-adress** som visas anger du ett Namn, väljer en tilldelningstyp för **IP-adress,** en prenumeration, en resursgrupp och en plats och klickar sedan på **Skapa** enligt följande bild:  

    ![Skapa en offentlig IP-adressresurs](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Slutför stegen i något av de avsnitt som följer för att associera den offentliga IP-adressresursen med en IP-konfiguration.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Associera den offentliga IP-adressresursen med en ny IP-konfiguration

1. Slutför stegen i avsnittet [Kärnsteg i](#coreadd) den här artikeln.
2. Klicka på **Lägg till**. I fönstret **Lägg till IP-konfiguration** som visas skapar du en IP-konfiguration med *namnet IPConfig-4.* Aktivera den **offentliga IP-adressen** och välj en befintlig, tillgänglig offentlig IP-adressresurs i **fönstret Välj offentlig IP-adress** som visas.

    När du har valt den offentliga IP-adressresursen klickar du **på OK** så stängs fönstret. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att slutföra stegen i avsnittet Skapa en [offentlig IP-adressresurs](#create-public-ip) i den här artikeln. 

3. Granska den nya IP-konfigurationen. Även om en privat IP-adress inte uttryckligen tilldelades tilldelades en automatiskt till IP-konfigurationen, eftersom alla IP-konfigurationer måste ha en privat IP-adress.
4. Du kan klicka på **Lägg till** för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser.
5. Lägg till den privata IP-adressen i operativsystemet för den virtuella datorn genom att slutföra stegen för ditt operativsystem i avsnittet Lägg till [IP-adresser](#os-config) till ett operativsystem för en virtuell dator i den här artikeln. Lägg inte till den offentliga IP-adressen i operativsystemet.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Associera den offentliga IP-adressresursen med en befintlig IP-konfiguration

1. Slutför stegen i avsnittet [Kärnsteg i](#coreadd) den här artikeln.
2. Klicka på den IP-konfiguration som du vill lägga till resursen för den offentliga IP-adressen till.
3. I fönstret IPConfig som visas klickar du på **IP-adress**.
4. I fönstret **Välj offentlig IP-adress** som visas väljer du en offentlig IP-adress.
5. Klicka **på** Spara så stängs fönstren. Om du inte har en befintlig offentlig IP-adress kan du skapa en genom att följa stegen i avsnittet Skapa en [offentlig IP-adressresurs](#create-public-ip) i den här artikeln.
3. Granska den nya IP-konfigurationen.
4. Du kan klicka på **Lägg till** för att lägga till ytterligare IP-konfigurationer eller stänga alla öppna blad för att slutföra tillägg av IP-adresser. Lägg inte till den offentliga IP-adressen i operativsystemet.

> [!NOTE]
> När du har ändrat IP-adresskonfigurationen måste du starta om den virtuella datorn för att ändringarna ska börja gälla på den virtuella datorn.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
