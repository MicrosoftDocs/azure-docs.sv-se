---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 6ffc91a6e01cfb479a4090bcf13f15a7bd20095c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "71203931"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste auktorisera åtkomsten till ditt lagringskonto. Tillhandahåll autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot:

1. I [Azure Portal](https://portal.azure.com) går du till ditt lagrings konto.
1. I avsnittet **Inställningar** i översikten över lagrings kontot väljer du **åtkomst nycklar** för att Visa kontots åtkomst nycklar och anslutnings sträng.
1. Notera namnet på lagringskontot. Du behöver det för auktoriseringen.   
1. Hitta **nyckel** värdet under **KEY1** och välj **Kopiera** för att kopiera konto nyckeln.

    ![Skärmbild som visar hur du kopierar kontonyckeln från Azure-portalen](media/storage-copy-account-key-portal/portal-account-key.png)
