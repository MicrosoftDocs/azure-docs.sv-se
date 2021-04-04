---
title: Skapa en hanterad privat slut punkt för att ansluta till data källans resultat
description: I den här artikeln får du lära dig hur du skapar en hanterad privat slut punkt till dina data källor från en Azure Synapse-arbetsyta.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461316"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Skapa en hanterad privat slut punkt för din data Källa

I den här artikeln får du lära dig hur du skapar en hanterad privat slut punkt för din data källa i Azure. Mer information finns i [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Steg 1: öppna din Azure Synapse-arbetsyta i Azure Portal

Du kan skapa en hanterad privat slut punkt för din data Källa från Azure Synapse Studio. Välj fliken **Översikt** i Azure Portal och välj **Öppna** på kortet Open Synapse Studio i avsnittet komma igång.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Steg 2: gå till fliken hanterade virtuella nätverk i Synapse Studio

I Azure Synapse Studio väljer du fliken **Hantera** i det vänstra navigerings fältet. Välj **hanterade privata slut punkter** och välj sedan **+ ny**.
![Skapa en ny hanterad privat slut punkt](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Steg 3: Välj typ av data Källa

Välj typ av data källa. I det här fallet är mål data källan ett ADLS Gen2 konto. Välj **Fortsätt**.
![Välj en typ av mål data Källa](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Steg 4: Ange information om data källan

I nästa fönster anger du information om data källan. I det här exemplet skapar vi en hanterad privat slut punkt för ett ADLS Gen2-konto. Ange ett **namn** för den hanterade privata slut punkten. Ange en **Azure-prenumeration** och ett **lagrings konto namn**. Välj **Skapa**.
![Ange information om mål data Källa](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Steg 5: kontrol lera att den hanterade privata slut punkten har skapats

När du har skickat begäran visas dess status. För att kontrol lera att den hanterade privata slut punkten har skapats, kontrollerar du *etablerings statusen*. Du kan behöva vänta 1 minut och välja **Uppdatera** för att uppdatera etablerings statusen. Du kan se att den hanterade privata slut punkten till ADLS Gen2 kontot har skapats.

Du kan också se att *godkännande tillstånd* *väntar*. Ägaren till mål resursen kan godkänna eller neka anslutnings förfrågan för privat slut punkt. Om ägaren godkänner den privata slut punkts anslutningsbegäran, upprättas en privat länk. Om den nekas, upprättas ingen privat länk.
![Status för att skapa begäran om hanterad privat slut punkt](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [hanterade privata slut punkter](./synapse-workspace-managed-private-endpoints.md)