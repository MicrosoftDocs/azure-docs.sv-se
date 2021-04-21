---
title: Lägga till ett anpassat CA-certifikat – Azure API Management | Microsoft Docs
description: Lär dig hur du lägger till ett anpassat CA-certifikat i Azure API Management. Du kan också se instruktioner för att ta bort ett certifikat.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 51719f23302bfaa036f99e88fcd440d97ca3bc02
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817836"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Så här lägger du till ett anpassat CA-certifikat i Azure API Management

Azure API Management kan installera CA-certifikat på datorn i de betrodda rotcertifikatarkiven och mellanliggande certifikatarkiv. Den här funktionen ska användas om dina tjänster kräver ett anpassat CA-certifikat.

Artikeln visar hur du hanterar CA-certifikat för en Azure API Management-tjänstinstans i Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Ladda upp ett CA-certifikat

![Lägga till CA-certifikat](media/api-management-howto-ca-certificates/00.png)

Följ stegen nedan för att ladda upp ett nytt CA-certifikat. Om du inte har skapat en API Management-tjänstinstans än kan du gå till [självstudien Skapa en API Management-tjänstinstans](get-started-create-service-instance.md).

1. Gå till din Azure API Management-tjänstinstans i Azure Portal.

2. Välj **CA-certifikat** på menyn.

3. Klicka på knappen **+ Lägg till**.  

    ![Skärmbild som visar knappen + Lägg till för att lägga till ett CA-certifikat.](media/api-management-howto-ca-certificates/01.png)  

4. Bläddra efter certifikatet och välj certifikatarkivet. Endast den offentliga nyckeln behövs, så lösenordet krävs inte.

    ![Skärmbild som visar hur du bläddrar efter certifikatet.](media/api-management-howto-ca-certificates/02.png)  

5. Klicka på **Spara**. Den här åtgärden kan ta några minuter.

    ![Skärmbild som visar hur du sparar certifikatet.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Du kan ladda upp ett CA-certifikat med `New-AzApiManagementSystemCertificate` powershell-kommandot.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ta bort ett klientcertifikat

Om du vill ta bort ett certifikat klickar du på **snabbmenyn ...** och **väljer Ta** bort bredvid certifikatet.

![Ta bort CA-certifikat](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
