---
title: Aviserings validering i Azure Security Center | Microsoft Docs
description: Lär dig hur du verifierar att dina säkerhets aviseringar är korrekt konfigurerade i Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 74323c63ab8985dee2391a546d82258dcb8d0114
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099530"
---
# <a name="alert-validation-in-azure-security-center"></a>Aviserings validering i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när ett hot mot dina resurser har identifierats. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center ger även rekommendationer för hur du kan åtgärda ett angrepp.
Mer information finns i [säkerhets aviseringar i Security Center](security-center-alerts-overview.md) och [Hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md)


## <a name="generate-sample-azure-defender-alerts"></a>Generera exempel på Azure Defender-aviseringar

Om du använder den nya funktionen för förhands gransknings aviseringar som beskrivs i [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md), kan du skapa exempel aviseringar på några få klick från sidan säkerhets aviseringar i Azure Portal.

Använd exempel aviseringar för att:

- utvärdera värdet och funktionerna i Azure Defender
- verifiera eventuella konfigurationer som du har gjort för dina säkerhets aviseringar (till exempel SIEM-integreringar, automatisering av arbets flöden och e-postaviseringar)

Så här skapar du exempel aviseringar:

1. I verktygsfältet på sidan Aviseringar väljer du **Skapa exempel aviseringar**. 
1. Välj prenumerationen.
1. Välj relevanta Azure Defender-planer som du vill visa aviseringar för. 
1. Välj **Skapa exempel aviseringar**.

    :::image type="content" source="media/security-center-alert-validation/create-sample-alerts-procedures.png" alt-text="Steg för att skapa exempel aviseringar i Azure Security Center":::
    
    Ett meddelande visas som låter dig veta att exempel aviseringarna skapas:

    :::image type="content" source="media/security-center-alert-validation/notification-sample-alerts-creation.png" alt-text="Meddelande om att exempel aviseringarna genereras.":::

    Efter några minuter visas aviseringarna på sidan säkerhets aviseringar. De visas också var som helst som du har konfigurerat för att ta emot Azure Security Center säkerhets aviseringar (anslutna Siem, e-postaviseringar och så vidare).

    :::image type="content" source="media/security-center-alert-validation/sample-alerts.png" alt-text="Exempel aviseringar i listan säkerhets aviseringar":::

    > [!TIP]
    > Aviseringarna är för simulerade resurser.

## <a name="simulate-alerts-on-your-azure-vms-windows"></a>Simulera aviseringar på dina virtuella Azure-datorer (Windows) <a name="validate-windows"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:

1. Kopiera en körbar fil (till exempel **calc.exe**) till datorns skriv bord eller någon annan katalog och Byt namn på den som **ASC_AlertTest_662jfi039N.exe**.
1. Öppna kommando tolken och kör den här filen med ett argument (bara ett falsk argument namn), till exempel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering ska visas.

> [!NOTE]
> När du granskar test aviseringen för Windows kontrollerar du att fält **argumenten granskning är aktive rad** är **Sant**. Om det är **falskt** måste du aktivera granskning av kommando rads argument. Använd följande kommando för att aktivera funktionen: 
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="simulate-alerts-on-your-azure-vms-linux"></a>Simulera aviseringar på dina virtuella Azure-datorer (Linux) <a name="validate-linux"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:
1. Kopiera en körbar fil till en lämplig plats och Byt namn på den till **./asc_alerttest_662jfi039n**, till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna kommando tolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering ska visas.


## <a name="simulate-alerts-on-kubernetes"></a>Simulera aviseringar på Kubernetes <a name="validate-kubernetes"></a>

Om du har integrerat Azure Kubernetes-tjänsten med Security Center kan du testa att aviseringarna fungerar med följande kubectl-kommando:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Mer information om att försvara dina Kubernetes-noder och-kluster finns i [Introduktion till Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Validera Azure Key Vault Threat Detection i Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar aviseringar och svarar på säkerhets incidenter i Security Center.
* [Förstå säkerhets aviseringar i Azure Security Center](./security-center-alerts-overview.md) – lär dig mer om de olika typerna av säkerhets aviseringar.