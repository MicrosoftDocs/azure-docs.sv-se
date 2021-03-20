---
title: Självstudie för åtkomst & program kontroller – Azure Security Center
description: I den här självstudien lär du dig hur du konfigurerar en just-in-Time-princip för VM-åtkomst och en princip för program kontroll.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/03/2018
ms.author: memildin
ms.openlocfilehash: cc88561f8882b1712648a261d1f0208a18bab26d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095484"
---
# <a name="tutorial-protect-your-resources-with-azure-security-center"></a>Självstudie: Skydda dina resurser ed Azure Security Center
Security Center begränsar din exponering för hot med kontroller för åtkomst och program för att blockera skadlig aktivitet. Just-in-Time (JIT)-åtkomsten för virtuella datorer minskar exponeringen för attacker genom att du kan neka beständiga åtkomst till virtuella datorer. Istället tillhandahåller du kontrollerad och granskad åtkomst till virtuella datorer enbart när det behövs. Anpassningsbara programkontroller hjälper till att skydda virtuella datorer mot skadlig programvara genom att kontrollera vilka program du kan köra på dina virtuella datorer. Security Center använder Machine Learning för att analysera processerna som körs på den virtuella datorn och hjälper dig att tillämpa Tillåt-List regler med hjälp av den här intelligensen.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst
> * Konfigurera en princip för programkontroll

## <a name="prerequisites"></a>Förutsättningar
Om du vill gå igenom de funktioner som beskrivs i den här självstudien måste du ha Azure Defender aktiverat. Det finns en kostnadsfri utvärderingsversion. Information om hur du uppgraderar finns i [Aktivera Azure Defender](enable-azure-defender.md).

## <a name="manage-vm-access"></a>Hantera åtkomst till virtuella datorer
JIT-åtkomst till virtuella datorer kan användas till att låsa inkommande trafik till dina virtuella Azure-datorer. Det här minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till de virtuella datorerna när du behöver.

Hanteringsportar behöver inte vara öppna hela tiden. De behöver endast vara öppna medan du är ansluten till den virtuella datorn för att exempelvis utföra hantering eller underhåll. När just-in-Time har Aktiver ATS använder Security Center regler för nätverks säkerhets grupper (NSG) som begränsar åtkomsten till hanterings portar så att de inte kan nås av angripare.

Följ rikt linjerna i [skydda dina hanterings portar med just-in-Time-åtkomst](security-center-just-in-time.md).

## <a name="harden-vms-against-malware"></a>Förstärka virtuella mot skadlig programvara
Anpassningsbara programkontroller hjälper dig att definiera en uppsättning program som ska tillåtas att köras på konfigurerade resursgrupper, vilket bland annat hjälper dig skydda dina virtuella datorer mot skadlig kod. Security Center använder Machine Learning för att analysera processerna som körs på den virtuella datorn och hjälper dig att tillämpa Tillåt-List regler med hjälp av den här intelligensen.

Följ anvisningarna i [använda anpassningsbara program kontroller för att minska dina datorers angrepps ytor](security-center-adaptive-application.md).

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du lärt dig att begränsa din exponering för hot genom att:

> [!div class="checklist"]
> * Konfigurera en just-in-Time-princip för VM-åtkomst för att tillhandahålla kontrollerad och granskad åtkomst till virtuella datorer endast vid behov
> * Konfigurera en princip för anpassningsbara programkontroller för att kontrollera vilka program som kan köras på din virtuella dator

Gå vidare till nästa kurs om du vill veta mer om att hantera säkerhetsincidenter.

> [!div class="nextstepaction"]
> [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)

<!--Image references-->
[1]: ./media/tutorial-protect-resources/just-in-time-vm-access.png
[2]: ./media/tutorial-protect-resources/add-port.png
[3]: ./media/tutorial-protect-resources/adaptive-application-control-options.png
[4]: ./media/tutorial-protect-resources/recommended-resource-groups.png
