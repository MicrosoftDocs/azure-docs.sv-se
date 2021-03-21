---
title: Bevilja behörighet till hanterad identitet i Synapse-arbetsytan
description: En artikel som förklarar hur du konfigurerar behörigheter för hanterad identitet i Azure dataSynapses-arbetsytan.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 46fe27205a2c30d6cb2319bf620c6fe1ee34c31e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96459009"
---
# <a name="grant-permissions-to-workspace-managed-identity"></a>Bevilja behörighet till arbetsytans hanterade identitet

Den här artikeln lär dig hur du beviljar behörigheter till den hanterade identiteten i Azure datasynapses-arbetsytan. Behörigheter, i sin tur, tillåter åtkomst till dedikerade SQL-pooler i arbets ytan och ADLS Gen2 lagrings konto via Azure Portal.

>[!NOTE]
>Den här arbets ytans hanterade identitet kallas hanterad identitet genom resten av det här dokumentet.

## <a name="grant-managed-identity-permissions-to-the-dedicated-sql-pool"></a>Bevilja hanterade identitets behörigheter till den dedikerade SQL-poolen

Den hanterade identiteten ger behörighet till dedikerade SQL-pooler i arbets ytan. Med de behörigheter som har beviljats kan du dirigera pipeliner som utför dedikerade SQL-pool-relaterade aktiviteter. När du skapar en Azure dataSynapses-arbetsyta med hjälp av Azure Portal kan du bevilja behörighet för hanterad identitets kontroll på dedikerade SQL-pooler.

Välj **säkerhet** när du skapar din Azure dataSynapses-arbetsyta. Välj sedan **Tillåt pipelines (körs som arbets ytans tilldelade identitet) för att få åtkomst till SQL-pooler.**

![KONTROL lera behörighet för dedikerade SQL-pooler](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Ge hanterade identitets behörigheter till ADLS Gen2 lagrings konto

Ett ADLS Gen2 lagrings konto krävs för att skapa en Azure dataSynapses-arbetsyta. För att kunna starta Spark-pooler i Azure dataSynapses-arbetsytan behöver Azure Synapse-hanterad identitet rollen *Storage BLOB data Contributor* på det här lagrings kontot. Pipelining i Azure Synapse har också nytta av den här rollen.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Bevilja behörighet till hanterad identitet när arbets ytan skapas

Azure Synapse försöker tilldela rollen Storage BLOB data Contributor till den hanterade identiteten när du har skapat Azure Synapse-arbetsytan med Azure Portal. Du anger ADLS Gen2 lagrings konto information på fliken **grundläggande** .

![Fliken grundläggande i flöde för skapande av arbets ytor](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Välj ADLS Gen2 lagrings konto och filesystem i **konto namn** och **fil system namn**.

![Tillhandahålla en ADLS Gen2 lagrings konto information](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Om skaparen av arbets ytan också är **ägare** till ADLS Gen2 lagrings kontot, tilldelar Azure Synapse rollen *Storage BLOB data Contributor* till den hanterade identiteten. Följande meddelande visas under den lagrings konto information som du har angett.

![Slutförd tilldelning av BLOB för lagrings data deltagare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Om arbets ytans skapare inte är ägare till ADLS Gen2 lagrings kontot, tilldelar inte Azure Synapse rollen *Storage BLOB data Contributor* till den hanterade identiteten. Meddelandet som visas under information om lagrings kontot meddelar arbets ytans skapare att de inte har tillräckliga behörigheter för att ge rollen *Storage BLOB data Contributor* åtkomst till den hanterade identiteten.

![Misslyckad tilldelning av BLOB data för lagrings data deltagare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Som meddelande tillstånd kan du inte skapa Spark-pooler om inte *lagrings data deltagaren* tilldelas till den hanterade identiteten.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Bevilja behörighet till hanterad identitet efter att arbets ytan har skapats

Om du inte tilldelar den hanterade identiteten *till den* hanterade identiteten, tilldelar **ägaren** till ADLS Gen2 lagrings kontot manuellt rollen till identiteten. Med följande steg kan du utföra manuell tilldelning.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Steg 1: navigera till ADLS Gen2 lagrings konto i Azure Portal

I Azure Portal öppnar du ADLS Gen2 lagrings konto och väljer **Översikt** i det vänstra navigerings fältet. Du behöver bara tilldela rollen *Storage BLOB data Contributor* på nivån container eller fil system. Välj **behållare**.  
![Översikt över ADLS Gen2 lagrings konto](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Steg 2: Välj behållaren

Den hanterade identiteten ska ha data åtkomst till den behållare (fil system) som angavs när arbets ytan skapades. Du kan hitta den här behållaren eller fil systemet i Azure Portal. Öppna Azure dataSynapses-arbetsytan i Azure Portal och välj fliken **Översikt** i det vänstra navigerings fältet.
![ADLS Gen2 lagrings konto behållare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Välj samma behållare eller fil system för att bevilja rollen *Storage BLOB data Contributor* till den hanterade identiteten.
![Skärm bild som visar den behållare eller det fil system som du bör välja.](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Steg 3: navigera till åtkomst kontroll

Välj **Access Control (IAM)**.

![Åtkomst kontroll (IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Steg 4: Lägg till en ny roll tilldelning

Välj **+ Lägg till**.

![Lägg till ny roll tilldelning](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-azure-role"></a>Steg 5: Välj Azure-rollen

Välj rollen **Storage BLOB data Contributor** .

![Välj Azure-roll](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Steg 6: Välj säkerhets objekt för Azure AD

Välj **Azure AD-användare, grupp eller tjänstens huvud namn** från List rutan **tilldela åtkomst till** .

![Välj AAD säkerhets objekt](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Steg 7: Sök efter den hanterade identiteten

Namnet på den hanterade identiteten är också arbets ytans namn. Sök efter din hanterade identitet genom att ange namnet på Azure Synapse-arbetsytan i **Välj**. Du bör se den hanterade identiteten i listan.

![Hitta den hanterade identiteten](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Steg 8: Välj den hanterade identiteten

Välj den hanterade identiteten för de **valda medlemmarna**. Välj **Spara** för att lägga till roll tilldelningen.

![Välj den hanterade identiteten](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Steg 9: kontrol lera att rollen Storage BLOB data Contributor är tilldelad till den hanterade identiteten

Välj **Access Control (IAM)** och välj **roll tilldelningar**.

![Verifiera roll tilldelning](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Du bör se din hanterade identitet som anges under avsnittet **Storage BLOB data Contributor** med rollen *Storage BLOB data Contributor* kopplad till den. 
![ADLS Gen2 val av lagrings konto behållare](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Nästa steg

Läs mer om [hanterad identitet för arbets ytan](./synapse-workspace-managed-identity.md)
