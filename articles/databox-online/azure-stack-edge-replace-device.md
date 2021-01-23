---
title: Ersätt din Azure Stack Edge Pro-enhet | Microsoft Docs
description: Beskriver hur du får en ersättnings Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 91cb446da31f353162f90778d855056a9697d455
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726589"
---
# <a name="replace-your-azure-stack-edge-pro-device"></a>Ersätt din Azure Stack Edge Pro-enhet

Den här artikeln beskriver hur du ersätter din Azure Stack Edge Pro-enhet. En ersättnings enhet krävs när den befintliga enheten har ett maskin varu problem eller behöver uppgraderas. 


I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Öppna ett support ärende för maskin varu problem
> * Skapa en ny order för en ersättnings enhet i Azure Portal
> * Installera, aktivera ersättnings enheten
> * Returnera den ursprungliga enheten

## <a name="open-a-support-ticket"></a>Öppna ett support ärende

Om din befintliga enhet har ett maskin varu problem öppnar du ett support ärende genom att följa dessa steg:

1. Öppna ett support ärende med Microsoft Support som anger att du vill returnera enheten. Välj den **Azure Stack Edge Pro Hardware** problem typ och välj under typen **maskin varu problem** .  

    ![Öppna supportbegäran](media/azure-stack-edge-replace-device/open-support-ticket-1.png)  

2. En Microsoft Support-tekniker får kontakt med dig för att avgöra om en FRU-enhet (Field Replacement Unit) kan åtgärda problemet och är tillgängligt för den här instansen. Om ett FRU-objekt inte är tillgängligt eller om enheten behöver en maskin varu uppgradering, hjälper supporten dig att placera en ny order och returnera din gamla enhet.

## <a name="create-a-new-order"></a>Skapa en ny order

Skapa en ny resurs för aktivering av din ersättnings enhet genom att följa stegen i [skapa en ny resurs](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).

> [!NOTE]
> Det finns inte stöd för att aktivera en ersättnings enhet mot en befintlig resurs. Den nya resursen betraktas som en ny order. Du börjar med att fakturera 14 dagar efter att enheten levererats till dig.

## <a name="install-and-activate-the-replacement-device"></a>Installera och aktivera ersättnings enheten

Följ de här stegen för att installera och aktivera ersättnings enheten:

1. [Installera enheten](azure-stack-edge-deploy-install.md).
2. [Aktivera enheten](azure-stack-edge-deploy-connect-setup-activate.md) mot den nya resurs som du skapade tidigare.

## <a name="return-your-existing-device"></a>Returnera din befintliga enhet

Följ alla steg för att returnera den ursprungliga enheten:

1. [Radera data på enheten](azure-stack-edge-return-device.md#erase-data-from-the-device).
2. [Starta enhets retur](azure-stack-edge-return-device.md#initiate-device-return) för den ursprungliga enheten.
3. [Schemalägg hämtningen](azure-stack-edge-return-device.md#schedule-a-pickup).
4. När enheten har tagits emot från Microsoft kan du [ta bort resursen](azure-stack-edge-return-device.md#delete-the-resource) som är kopplad till den returnerade enheten.


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [returnerar en Azure Stack Edge Pro-enhet](azure-stack-edge-return-device.md).
