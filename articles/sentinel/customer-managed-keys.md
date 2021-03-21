---
title: Konfigurera Kundhanterade nycklar i Azure Sentinel | Microsoft Docs
description: Lär dig hur du konfigurerar Kundhanterade nycklar (CMK) i Azure Sentinel.
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
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: a1c2754fcae5768c6b87d6280fc882acd46d9a0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585337"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Konfigurera Azure Sentinel – kundhanterad nyckel

Den här artikeln innehåller bakgrunds information och steg för att konfigurera en kundhanterad nyckel (CMK) för Azure Sentinel. CMK gör att alla data som sparas eller skickas till Azure Sentinel krypteras i alla relevanta lagrings resurser med en Azure Key Vault nyckel som skapats eller ägs av dig.

> [!NOTE]
> - Azure Sentinel CMK-funktionen tillhandahålls endast till **nya kunder**.
>
> - Åtkomst till den här funktionen styrs av Azure Feature Registration. Du kan begära åtkomst genom att kontakta azuresentinelCMK@microsoft.com . Väntande begär Anden kommer att godkännas enligt den tillgängliga kapaciteten.
>
> - CMK-funktionen är bara tillgänglig för kunder som skickar 1 TB per dag eller mer. Du får information om ytterligare priser när du använder Microsoft för att etablera CMK i din Azure-prenumeration. Läs mer om [Log Analytics prissättning](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Så här fungerar CMK 

Azure Sentinel-lösningen använder flera lagrings resurser för logg insamling och-funktioner, inklusive Log Analytics och andra. Som en del av konfigurationen av Azure Sentinel-CMK måste du också konfigurera CMK-inställningarna på de relaterade lagrings resurserna. Data som sparas i andra lagrings resurser än Log Analytics kommer också att krypteras.

Läs mer om [CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> Om du aktiverar CMK på Azure Sentinel, aktive ras inte alla offentliga förhands gransknings funktioner som inte stöder CMK.

## <a name="enable-cmk"></a>Aktivera CMK 

Följ dessa steg om du vill etablera CMK: 

1.  Skapa en Azure Key Vault och en lagrings nyckel.

2.  Aktivera CMK på din Log Analytics-arbetsyta.

3.  Registrera dig för Cosmos DB.

4.  Lägg till en åtkomst princip till Azure Key Vault-instansen.

5.  Aktivera CMK i Azure Sentinel.

6.  Aktivera Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>STEG 1: skapa en Azure Key Vault och lagra nyckel

1.  [Skapa Azure Key Vault resurs](/azure-stack/user/azure-stack-key-vault-manage-portal)och generera eller importera sedan en nyckel som ska användas för data kryptering.
    > [!NOTE]
    >  Azure Key Vault måste konfigureras som återställnings Bart för att skydda nyckeln och åtkomsten.

1.  [Aktivera återställnings alternativ:](../key-vault/general/key-vault-recovery.md)

    -   Se till att [mjuk borttagning](../key-vault/general/soft-delete-overview.md) är aktiverat.

    -   Aktivera [rensnings skyddet](../key-vault/general/soft-delete-overview.md#purge-protection) för att skydda mot framtvingad borttagning av hemligheten/valvet även efter mjuk borttagning.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>STEG 2: Aktivera CMK på arbets ytan Log Analytics

Följ instruktionerna i [Azure Monitor kundhanterad nyckel konfiguration](../azure-monitor/logs/customer-managed-keys.md) för att skapa en CMK-arbetsyta som ska användas som Azure Sentinel-arbetsyta i följande steg.

### <a name="step-3-register-for-cosmos-db"></a>STEG 3: registrera dig för Cosmos DB

Azure Sentinel fungerar med Cosmos DB som en ytterligare lagrings resurs. Se till att registrera till Cosmos DB.

[Registrera Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) Resource Provider för din Azure-prenumeration genom att följa Cosmos DB-instruktionen.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>STEG 4: Lägg till en åtkomst princip till Azure Key Vault-instansen

Se till att lägga till åtkomst från Cosmos DB till Azure Key Vault-instansen. Följ Cosmos DB instruktionen för att [lägga till en åtkomst princip till Azure Key Vault-instansen](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) med Azure Cosmos DB huvud konto.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>STEG 5: Aktivera CMK i Azure Sentinel

Azure Sentinel CMK-funktionen tillhandahålls till nya kunder endast efter att ha fått åtkomst direkt från Azures produkt grupp. Använd dina kontakter på Microsoft för att få godkännande från Azure Sentinel-teamet för att aktivera CMK i din lösning.

När du har godkänt godkännande uppmanas du att ange följande information för att aktivera funktionen CMK.

-  Arbetsyte-ID som du vill aktivera CMK för

-  Key Vault-URL: kopiera nyckelns "nyckel-ID" uppåt till det sista snedstrecket:  
    

    ![nyckel identifierare](./media/customer-managed-keys/key-identifier.png)

    Azure Sentinel-teamet aktiverar funktionen Azure Sentinel CMK för din angivna arbets yta.

-  Verifiering från produkt teamet för Azure Sentinel som du har godkänt för att använda den här funktionen. Du måste ha det här innan du fortsätter.

### <a name="step-6-enable-azure-sentinel"></a>STEG 6: Aktivera Azure Sentinel


Gå till Azure Portal och aktivera Azure Sentinel på arbets ytan där du konfigurerar CMK. Mer information finns i [Azure Sentinel onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Återkalla eller ta bort nyckel krypterings nyckel


I händelse av att en användare återkallar nyckel krypterings nyckeln, antingen genom att ta bort eller ta bort åtkomst för Azure Sentinel, inom en timme, kommer Azure Sentinel att bearbeta ändringen och beter sig som om data inte längre är tillgängliga. I det här läget kommer alla utförda åtgärder som använder permanenta lagrings resurser, till exempel data inmatning, beständiga konfigurations ändringar och incident skapande att förhindras. Tidigare lagrade data tas inte bort, men kommer inte att vara tillgängliga. Otillgängliga data regleras av data bevarande principen och tas bort i enlighet med principen.

Den enda åtgärd som är möjlig efter att krypterings nyckeln har återkallats eller tagits bort är ta bort konto.

Om åtkomsten återställs efter återkallning kommer Azure Sentinel att återställa åtkomsten till data inom en timme.

Om du vill veta mer om hur det fungerar i Azure Monitor, se [Azure Monitor CMK-återkallning](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Rotation av nyckel krypterings nyckel


Azure Sentinel och Log Analytics stöd för nyckel rotation. När en användare utför nyckel rotation i Key Vault stöder Azure Sentinel den nya nyckeln inom en timme.

I Key Vault kan du utföra nyckel rotation genom att skapa en ny version av nyckeln:

![nyckel rotation](./media/customer-managed-keys/key-rotation.png)

Du kan inaktivera den tidigare versionen av nyckeln efter 24 timmar, eller efter Azure Key Vault gransknings loggarna visar inte längre aktiviteter som använder den tidigare versionen.

Om du använder samma nyckel i Azure Sentinel och i Log Analytics är det nödvändigt att utföra nyckel rotationen. du måste uttryckligen uppdatera kluster resursen i Log Analytics med den nya Azure Key Vault nyckel versionen. Mer information finns i [Azure Monitor CMK rotation](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du konfigurerar en kundhanterad nyckel i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](./tutorial-detect-threats-built-in.md).
- [Använd arbets böcker](tutorial-monitor-your-data.md) för att övervaka dina data.