---
title: Ta bort en grupp-Azure Active Directory | Microsoft Docs
description: Anvisningar om hur du tar bort en grupp med hjälp av Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aed5f282ff7b5d573a6f8511f2fc4dbfd27135d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371825"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Ta bort en grupp med Azure Active Directory
Du kan ta bort en Azure Active Directory (Azure AD) grupp av valfritt antal orsaker, men vanligt vis är det att du:

- Felaktigt angett **grupp typen** till fel alternativ.

- Fel eller en dubblett av gruppen skapades av misstag. 

- Behöver inte längre gruppen.

## <a name="to-delete-a-group"></a>Ta bort en grupp
1. Logga in på [Azure-portalen](https://portal.azure.com) med ett Globalt administratörskonto för katalogen.

2. Välj **Azure Active Directory** och välj sedan **grupper**.

3. Från sidan **grupper – alla grupper** söker du efter och väljer den grupp som du vill ta bort. I de här stegen använder vi **MDM-princip – öst**.

    ![Sidan Groups-All grupper, grupp namn har marker ATS](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. På sidan **MDM-princip – östra översikt** och välj sedan **ta bort**.

    Gruppen tas bort från Azure Active Directory klient organisationen.

    ![MDM-princip – sidan för östra översikt, alternativet ta bort markerat](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Nästa steg

- Om du tar bort en grupp av misstag kan du skapa den igen. Mer information finns i [så här skapar du en grundläggande grupp och lägger till medlemmar](active-directory-groups-create-azure-portal.md).

- Om du tar bort en Microsoft 365 grupp av misstag kan du kanske återställa den. Mer information finns i [återställa en borttagen Office 365-grupp](../enterprise-users/groups-restore-deleted.md).
