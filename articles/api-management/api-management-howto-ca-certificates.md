---
title: Lägg till ett anpassat CA-certifikat – Azure API Management | Microsoft Docs
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
ms.openlocfilehash: 124bc053aa2c6e59e205bb6f33a9a96190799499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93102045"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>Så här lägger du till ett anpassat CA-certifikat i Azure API Management

Med Azure API Management kan du installera CA-certifikat på datorn i betrodda rot-och mellanliggande certifikat arkiv. Den här funktionen ska användas om dina tjänster kräver ett anpassat CA-certifikat.

Artikeln visar hur du hanterar CA-certifikat för en Azure API Management-tjänstinstans i Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>Ladda upp ett CA-certifikat

![Lägg till CA-certifikat](media/api-management-howto-ca-certificates/00.png)

Följ stegen nedan för att ladda upp ett nytt CA-certifikat. Om du inte har skapat en API Management tjänst instans ännu går du till självstudien [skapa en API Management tjänst instans](get-started-create-service-instance.md).

1. Gå till Azure API Management Service-instansen i Azure Portal.

2. Välj **ca-certifikat** på menyn.

3. Klicka på knappen **+ Lägg till**.  

    ![Skärm bild som visar knappen + Lägg till för att lägga till ett CA-certifikat.](media/api-management-howto-ca-certificates/01.png)  

4. Bläddra efter certifikatet och bestäm certifikat arkivet. Det krävs bara att den offentliga nyckeln behövs, så lösen ordet krävs inte.

    ![Skärm bild som visar hur du bläddrar efter certifikatet.](media/api-management-howto-ca-certificates/02.png)  

5. Klicka på **Spara**. Den här åtgärden kan ta några minuter.

    ![Skärm bild som visar hur du sparar certifikatet.](media/api-management-howto-ca-certificates/03.png)  

> [!NOTE]
> Du kan ladda upp ett CA-certifikat med hjälp av `New-AzApiManagementSystemCertificate` PowerShell-kommandot.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Ta bort ett klient certifikat

Ta bort ett certifikat genom att klicka på snabb menyn **...** och välj **ta bort** bredvid certifikatet.

![Ta bort CA-certifikat](media/api-management-howto-ca-certificates/04.png)  

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
