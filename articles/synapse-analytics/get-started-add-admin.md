---
title: 'Självstudie: kom igång Lägg till en administratör'
description: I den här självstudien får du lära dig hur du lägger till en annan administrativ användare i din arbets yta.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 04/02/2021
ms.openlocfilehash: 8b854dfcff7dfb4d03038b542308b6f3ebb6d491
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553506"
---
# <a name="add-an-administrator-to-your-synapse-workspace"></a>Lägg till en administratör i din Synapse-arbetsyta

I den här självstudien får du lära dig hur du lägger till en administratör i din Synapse-arbetsyta. Den här användaren kommer att ha fullständig kontroll över arbets ytan.

## <a name="overview"></a>Översikt

Hittills i guiden kom igång har vi fokuserat på aktiviteter *som du* gör i arbets ytan. Eftersom du skapade arbets ytan i steg 1 är du administratör för Synapse-arbetsytan. Nu ska vi göra en annan användare Ryan ( `ryan@contoso.com` ) en administratör. När vi är klara kommer Ryan att kunna göra allt som du kan göra i arbets ytan.

## <a name="azure-rbac-owner-role-for-the-workspace"></a>Azure RBAC: ägar roll för arbets ytan

Tilldela `ryan@contoso.com` rollen Azure RBAC- **ägare** på arbets ytan.

1. Öppna den Azure Portal och öppna Synapse-arbetsytan.
1. Välj **Access Control (IAM)** på den vänstra sidan.
1. Lägg till `ryan@contoso.com` i **ägar** rollen. 
1. Klicka på **Spara**.
 
 
## <a name="synapse-rbac-synapse-administrator-role-for-the-workspace"></a>Synapse RBAC: Synapse-administratörs roll för arbets ytan

Tilldela till `ryan@contoso.com` SYNAPSE RBAC- **Synapse administratörs** roll på arbets ytan.

1. Öppna din arbets yta i Synapse Studio.
1. Klicka på **Hantera** på vänster sida för att öppna hantera hubben.
1. Under **säkerhet** klickar du på **åtkomst kontroll**.
1. Klicka på **Lägg till**.
1. Lämna **området** inställt på **arbets ytan**.
1. Lägg till `ryan@contoso.com` i **Administratörs rollen Synapse** . 
1. Klicka sedan på **Använd**.
 
## <a name="azure-rbac-role-assignments-on-the-primary-storage-account"></a>Azure RBAC: roll tilldelningar för det primära lagrings kontot

Tilldela till `ryan@contoso.com` **ägar** rollen på arbets ytans primära lagrings konto.
Tilldela till `ryan@contoso.com` **Azure Storage BLOB rollen data deltagare** på arbets ytans primära lagrings konto.

1. Öppna arbets ytans primära lagrings konto i Azure Portal.
1. Klicka på **Access Control (IAM)** på den vänstra sidan.
1. Lägg till `ryan@contoso.com` i **ägar** rollen. 
1. Lägg till `ryan@contoso.com` i rollen **Azure Storage BLOB data deltagare**

## <a name="dedicated-sql-pools-db_owner-role"></a>Dedikerade SQL-pooler: db_owner roll

Tilldela `ryan@contoso.com` **db_owner** för varje dedikerad SQL-pool i arbets ytan.

```
CREATE USER [ryan@contoso.com] FROM EXTERNAL PROVIDER; 
EXEC sp_addrolemember 'db_owner', 'ryan@contoso.com'
```

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Azure Synapse Analytics](get-started.md)
* [Skapa en arbetsyta](quickstart-create-workspace.md)
* [Använda en serverlös SQL-pool](quickstart-sql-on-demand.md)
