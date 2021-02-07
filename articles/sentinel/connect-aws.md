---
title: Anslut AWS CloudTrail till Azure Sentinel | Microsoft Docs
description: Använd AWS-anslutningen för att delegera Azure Sentinel-åtkomst till AWS-resurs loggar och skapa en förtroende relation mellan AWS CloudTrail och Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 3d24fa1ea046a860feb40d09a6d0a57c79371450
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807605"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Ansluta Azure Sentinel till AWS CloudTrail

Använd AWS-anslutningsprogrammet för att strömma dina AWS CloudTrail Management-händelser till Azure Sentinel. Den här anslutnings processen delegerar åtkomst för Azure Sentinel till dina AWS-resurs loggar och skapar en förtroende relation mellan AWS CloudTrail och Azure Sentinel. Detta görs på AWS genom att skapa en roll som ger behörighet till Azure Sentinel för att få åtkomst till dina AWS-loggar.

> [!NOTE]
> AWS-CloudTrail har [inbyggda begränsningar](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) i dess LOOKUPEVENTS-API. Den tillåter inte fler än två transaktioner per sekund (TPS) per konto och varje fråga kan returnera högst 50 poster. Om en enskild klientorganisation konstant genererar fler än 100 poster per sekund i en region kommer därför eftersläpningar och fördröjningar i datainmatningen att uppstå.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha Skriv behörighet på Azure Sentinel-arbetsytan.

> [!NOTE]
> Azure Sentinel samlar in CloudTrail hanterings händelser från alla regioner. Vi rekommenderar att du inte strömmar händelser från en region till en annan.

## <a name="connect-aws"></a>Ansluta AWS 


1. I Azure Sentinel väljer du **data kopplingar** och väljer sedan den **Amazon Web Services** raden i tabellen och i fönstret AWS till höger klickar du på **Öppna kopplings sida**.

1. Följ anvisningarna under **konfigurationen** med hjälp av följande steg.
 
1.  I Amazon Web Services-konsolen, under **säkerhet, identitet & efterlevnad**, väljer du **IAM**.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Välj **roller** och välj **skapa roll**.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Välj **ett annat AWS-konto.** I fältet **konto-ID** anger du det **ID för Microsoft-konto** (**123412341234**) som finns på anslutnings sidan för AWS på Azure Sentinel-portalen.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Se till att **extern ID** är markerat och ange sedan det externa ID (arbetsyte-ID) som finns på anslutnings sidan för AWS på Azure Sentinel-portalen.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Under **bifoga behörighets princip** väljer du **AWSCloudTrailReadOnlyAccess**.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Ange en tagg (valfritt).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Ange sedan ett **rollnamn** och välj knappen **skapa roll** .

    ![AWS7](./media/connect-aws/aws-7.png)

1.  I listan Roller väljer du den roll som du skapade.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Kopiera **rollens ARN**. I Azure Sentinel-portalen, i fönstret Amazon Web Services anslutning, klistrar du in det i fältet **roll som ska läggas** till och klickar på **Lägg till**.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Om du vill använda det relevanta schemat i Log Analytics för AWS-händelser söker du efter **AWSCloudTrail**.

    > [!IMPORTANT]
    > Från och med den 1 december 2020 har fältet **AwsRequestId** ersatts av fältet **AwsRequestId_** (Observera det tillagda understrecket). Data i det gamla **AwsRequestId** -fältet kommer att behållas genom slutet av kundens angivna data lagrings period.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter AWS-CloudTrail till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.
