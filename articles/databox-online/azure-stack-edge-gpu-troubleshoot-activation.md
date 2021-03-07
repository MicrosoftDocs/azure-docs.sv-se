---
title: Använd Azure Portal för att felsöka aktiverings fel som rör Azure Stack Edge Pro med GPU | Microsoft Docs
description: Beskriver hur du felsöker Azure Stack Edge Pro GPU-aktivering och viktiga valv-relaterade problem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 66e62bffe28cc10bd49e1456fdd6e2ac1faebf6e
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442153"
---
# <a name="troubleshoot-activation-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Felsök problem med aktivering på din Azure Stack Edge Pro GPU-enhet 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Den här artikeln beskriver hur du felsöker problem med aktivering på din Azure Stack Edge Pro GPU-enhet. 


## <a name="activation-errors"></a>Aktiverings fel

I följande tabell sammanfattas felen som rör enhets aktivering och motsvarande rekommenderade lösning.

| Fel meddelande| Rekommenderad lösning |
|------------------------------------------------------|--------------------------------------|
| Om den Azure Key Vault som används för aktivering tas bort innan enheten aktive ras med aktiverings nyckeln, visas det här felet. <br> ![Key Vault-fel 1](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-1.png)  | Om nyckel valvet har tagits bort kan du återställa nyckel valvet om valvet är i rensnings skyddets varaktighet. Följ stegen i [återställa ett nyckel valv](../key-vault/general/key-vault-recovery.md#list-recover-or-purge-soft-deleted-secrets-keys-and-certificates). <br>Om rensnings skyddets varaktighet har gått ut går det inte att återställa nyckel valvet. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Om Azure Key Vault tas bort när enheten har Aktiver ATS och du sedan försöker utföra en åtgärd som inbegriper kryptering, till exempel: **Lägg till användare**, **Lägg till resurs**, **Konfigurera beräkning** så får du det här felet. <br> ![Key Vault-fel 2](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-2.png)    | Om nyckel valvet har tagits bort kan du återställa nyckel valvet om valvet är i rensnings skyddets varaktighet. Följ stegen i återställa ett nyckel valv. <br>Om rensnings skyddets varaktighet har gått ut går det inte att återställa nyckel valvet. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Om kanalens integritets nyckel i Azure Key Vault tas bort och du sedan försöker utföra alla åtgärder som inbegriper kryptering, till exempel: **Lägg till användare**, **Lägg till resurs**, **Konfigurera beräkning** – du får det här felet. <br> ![Key Vault-fel 3](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-3.png) | Om kanal integritets nyckeln i nyckel valvet tas bort, men den fortfarande ligger inom rensnings tiden, följer du stegen i [ångra borttagning av Key Vault-nyckeln](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval). <br>Om rensnings skyddets varaktighet har förflutit, och om du har säkerhetskopierat, kan du återställa från säkerhets kopian annars kan du inte återställa nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Om genereringen av aktiverings nyckeln Miss lyckas på grund av ett fel visas det här felet. Mer information finns i meddelandet. <br> ![Key Vault-fel 4](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-4.png)   | Se till att de portar och URL: er som anges i [åtkomst Azure Key Vault bakom en brand](../key-vault/general/access-behind-firewall.md) vägg är öppna i brand väggen för att få åtkomst till Key Vault. Vänta några minuter och försök igen. Kontakta Microsoft Support om problemet kvarstår. |
| Om användaren har Läs behörighet får användaren inte skapa en aktiverings nyckel och detta fel visas. <br> ![Key Vault-fel 5](./media/azure-stack-edge-gpu-troubleshoot-activation/key-vault-error-5.png) | Det kan bero på att du inte har rätt åtkomst eller att  *Microsoft. nyckel valv* inte har registrerats.<li>Kontrol lera att du har ägar-eller deltagar åtkomst på resurs grupps nivån som används för din Azure Stack Edge-resurs.</li><li>Kontrol lera att Microsoft. nyckel valvets resurs leverantör har registrerats. Om du vill registrera en resurs leverantör går du till prenumerationen som används för Azure Stack Edge-resurs. Gå till **Resource providers**, Sök efter *Microsoft. nyckel valv* och välj och **Registrera**.</li> |

## <a name="next-steps"></a>Nästa steg

- Läs mer om hur du [felsöker enhets problem](azure-stack-edge-gpu-troubleshoot.md).
