---
title: Skapa och associera tjänst slut punkts principer – Azure Portal
titlesuffix: Azure Virtual Network
description: I den här artikeln lär du dig att konfigurera och associerade tjänst slut punkts principer med hjälp av Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004967"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Skapa, ändra eller ta bort tjänstens slut punkts princip med hjälp av Azure Portal

Med tjänst slut punkts principer kan du filtrera virtuell nätverks trafik till vissa Azure-resurser, över tjänst slut punkter. Om du inte är bekant med tjänstens slut punkts principer kan du läsa mer i [Översikt över tjänst slut punkts principer](virtual-network-service-endpoint-policies-overview.md) .

 I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en princip för tjänst slut punkt
> * Skapa en princip definition för tjänst slut punkt
> * Skapa ett virtuellt nätverk med ett undernät
> * Koppla en tjänst slut punkts princip till ett undernät

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Skapa en princip för tjänst slut punkt

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. I sökrutan skriver du "tjänstens slut punkts princip" och väljer **tjänst slut punkts princip** och väljer sedan **skapa**.

![Skapa tjänst slut punkts princip](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Ange eller Välj följande information i **grunderna** 

   - Prenumeration: Välj din prenumeration för principen
   - Resurs grupp: Välj **Skapa ny** och ange *myResourceGroup*
   - Namn: myEndpointPolicy
   - Plats: Central USA
 
   ![Skapa grundläggande information om tjänst slut punkts principer](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Välj **+ Lägg till** under **resurser** och ange eller Välj följande information i **Lägg till en resurs** -fönstret

   - Tjänst: endast **Microsoft. Storage** är tillgängligt med tjänst slut punkts principer
   - Omfång: Välj ett out-of- **Single-konto**, **alla konton i prenumerationen** och **alla konton i resurs gruppen**
   - Prenumeration: Välj din prenumeration för lagrings kontot. Princip-och lagrings konton kan finnas i olika prenumerationer.
   - Resurs grupp: Välj din resurs grupp. Obligatoriskt, om omfång anges som, "alla konton i resurs gruppen" eller "enskilt konto".  
   - Resurs: Välj Azure Storage resurs under den valda prenumerationen eller resurs gruppen
   - Klicka på knappen **Lägg till** längst ned för att slutföra tillägget av resursen

   ![Princip definition för tjänst slut punkt-resurs](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Lägg till fler resurser genom att upprepa stegen ovan efter behov

5. Valfritt: Ange eller Välj följande information i **taggar**:
   
   - Nyckel: Välj din nyckel för principen. T. ex. avd     
   - Värde: Ange värde paret för nyckeln. Till exempel: ekonomi

6. Välj **Granska + skapa**. Verifiera informationen och klicka på **skapa**. Klicka på **föregående** om du vill göra ytterligare ändringar. 

   ![Skapa slutlig validering för tjänst slut punkts princip](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Visa slut punkts principer 

1. I rutan *alla tjänster* i portalen börjar du skriva *tjänst slut punkts principer*. Välj **tjänst slut punkts principer**.
2. Under **prenumerationer** väljer du din prenumeration och resurs grupp, som du ser i följande bild

   ![Visa princip](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Välj principen och klicka på **princip definitioner** för att visa eller lägga till fler princip definitioner.

   ![Visa princip definitioner](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Välj **associerade undernät** för att visa de undernät som principen är associerad med. Följ anvisningarna i nästa steg om du inte har associerat något undernät ännu.

   ![Associerade undernät](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Koppla en princip till ett undernät

>[!WARNING] 
> Se till att alla resurser som nås från under nätet läggs till i princip definitionen innan du kopplar principen till det aktuella under nätet. När principen är associerad tillåts endast åtkomst till de resurser *som finns i listan* över tjänst slut punkter. 
>
> Se också till att inga hanterade Azure-tjänster finns i under nätet som associeras med tjänstens slut punkts princip

- Innan du kan koppla en princip till ett undernät måste du skapa ett virtuellt nätverk och ett undernät. Mer information om detta finns i artikeln [skapa en Virtual Network](./quick-create-portal.md) .

- När du har konfigurerat det virtuella nätverket och under nätet måste du konfigurera Virtual Network tjänstens slut punkter för Azure Storage. På bladet Virtual Network väljer du **tjänst slut punkter** och i nästa ruta väljer du **Microsoft. Storage** och under **undernät** väljer du önskat VNet eller undernät

- Nu kan du antingen välja att välja tjänstens slut punkts princip i list rutan i rutan ovan om du redan har skapat tjänst slut punkts principer innan du konfigurerar tjänst slut punkten för under nätet enligt nedan

    ![Associera undernät när du skapar tjänstens slut punkt](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- ELLER om du associerar tjänst slut punkts principer efter att tjänstens slut punkter redan har kon figurer ATS, kan du välja att koppla under nätet från bladet för tjänstens slut punkts princip genom att gå till rutan **associerade undernät** som visas nedan

    ![Associera undernät via SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Åtkomst till Azure Storage resurser i alla regioner begränsas enligt tjänst slut punkts principen från det här under nätet.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en tjänst slut punkts princip och kopplat den till ett undernät. Mer information om principer för tjänst slut punkter finns i [Översikt över tjänst slut punkts principer.](virtual-network-service-endpoint-policies-overview.md)
