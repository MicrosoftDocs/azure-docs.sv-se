---
title: Använd Azure Portal för att konfigurera Kundhanterade nycklar för import/export-tjänsten
description: Lär dig hur du använder Azure Portal för att konfigurera Kundhanterade nycklar med Azure Key Vault för Azure import/export-tjänsten. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: storage
author: alkohli
ms.service: storage
ms.topic: how-to
ms.date: 01/14/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 71c322385c9b70eb46e1c478c1322752ee4561e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98706756"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-importexport-service"></a>Använda Kundhanterade nycklar i Azure Key Vault för import/export-tjänsten

Azure import/export skyddar de BitLocker-nycklar som används för att låsa enheterna via en krypterings nyckel. Som standard krypteras BitLocker-nycklar med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklarna kan du även tillhandahålla Kundhanterade nycklar.

Kundhanterade nycklar måste skapas och lagras i en Azure Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../key-vault/general/overview.md)

Den här artikeln visar hur du använder Kundhanterade nycklar med import/export-tjänsten i [Azure Portal](https://portal.azure.com/).

## <a name="prerequisites"></a>Krav

Innan du börjar ska du kontrollera att:

1. Du har skapat en import-eller export jobb enligt instruktionerna i:

    - [Skapa ett import jobb för blobbar](storage-import-export-data-to-blobs.md).
    - [Skapa ett import jobb för filer](storage-import-export-data-to-files.md).
    - [Skapa ett export jobb för blobbar](storage-import-export-data-from-blobs.md)

2. Du har en befintlig Azure Key Vault med en nyckel som du kan använda för att skydda din BitLocker-nyckel. Information om hur du skapar ett nyckel valv med hjälp av Azure Portal finns i [snabb start: skapa ett Azure Key Vault med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).

    - **Mjuk borttagning** och **Rensa inte** är inställda på din befintliga Key Vault. De här egenskaperna är inte aktiverade som standard. Om du vill aktivera dessa egenskaper kan du läsa avsnitten med rubriken **Aktivera mjuk borttagning** och **Aktivera rensnings skydd** i någon av följande artiklar:

        - [Använda mjuk borttagning med PowerShell](../key-vault/general/key-vault-recovery.md).
        - [Använda mjuk borttagning med CLI](../key-vault/general/key-vault-recovery.md).
    - Det befintliga nyckel valvet ska ha en RSA-nyckel på 2048 storlek eller mer. Mer information om nycklar finns i [om nycklar](../key-vault/keys/about-keys.md).
    - Nyckel valvet måste finnas i samma region som lagrings kontot för dina data.  
    - Om du inte har en befintlig Azure Key Vault kan du också skapa den i enlighet med följande avsnitt.

## <a name="enable-keys"></a>Aktivera nycklar

Det är valfritt att konfigurera kundhanterad nyckel för import/export-tjänsten. Som standard använder tjänsten import/export en Microsoft-hanterad nyckel för att skydda din BitLocker-nyckel. Följ dessa steg om du vill aktivera Kundhanterade nycklar i Azure Portal:

1. Gå till **översikts** bladet för ditt import jobb.
2. I den högra rutan väljer du **Välj hur dina BitLocker-nycklar ska krypteras**.

    ![Välj krypterings alternativ](./media/storage-import-export-encryption-key-portal/encryption-key-1.png)

3. På bladet **kryptering** kan du Visa och kopiera enhetens BitLocker-nyckel. Under **krypterings typ** kan du välja hur du vill skydda din BitLocker-nyckel. Som standard används en hanterad Microsoft-nyckel.

    ![Visa BitLocker-nyckel](./media/storage-import-export-encryption-key-portal/encryption-key-2.png)

4. Du kan välja att ange en kund hanterad nyckel. När du har valt kundens hanterade nyckel **väljer du Key Vault och en nyckel**.

    ![Välj kund hanterad nyckel](./media/storage-import-export-encryption-key-portal/encryption-key-3.png)

5. I bladet **Välj nyckel från Azure Key Vault** fylls prenumerationen i automatiskt. För **Key Vault** kan du välja ett befintligt nyckel valv i list rutan.

    ![Välj eller skapa Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-4.png)

6. Du kan också välja **Skapa nytt** för att skapa ett nytt nyckel valv. På **bladet skapa nyckel valv** anger du resurs gruppen och namnet på nyckel valvet. Acceptera alla andra standardvärden. Välj **Granska + skapa**.

    ![Skapa nytt Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-5.png)

7. Granska informationen som är kopplad till ditt nyckel valv och välj **skapa**. Vänta några minuter tills nyckel valvet har skapats.

    ![Skapa Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-6.png)

8. I **Välj nyckel från Azure Key Vault** kan du välja en nyckel i det befintliga nyckel valvet.

9. Om du har skapat ett nytt nyckel valv väljer du **Skapa nytt** för att skapa en nyckel. RSA-nyckelns storlek kan vara 2048 eller högre.

    ![Skapa ny nyckel i Azure Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-7.png)

    Om det mjuka borttagnings-och rensnings skyddet inte är aktiverat när du skapar nyckel valvet, uppdateras nyckel valvet för att aktivera mjuk borttagning och tömnings skydd.

10. Ange namnet på nyckeln, acceptera de andra standardinställningarna och välj **skapa**.

    ![Skapa ny nyckel](./media/storage-import-export-encryption-key-portal/encryption-key-8.png)

11. Välj **version** och välj sedan **Välj**. Du får ett meddelande om att en nyckel har skapats i ditt nyckel valv.

    ![Ny nyckel har skapats i Key Vault](./media/storage-import-export-encryption-key-portal/encryption-key-9.png)

På bladet **kryptering** kan du se nyckel valvet och nyckeln som valts för din kund hanterade nyckel.

> [!IMPORTANT]
> Du kan bara inaktivera Microsoft-hanterade nycklar och flytta till kundens hanterade nycklar i alla steg i import/export-jobbet. Du kan dock inte inaktivera kundens hanterade nyckel när du har skapat den.

## <a name="troubleshoot-customer-managed-key-errors"></a>Felsöka kund hanterade nyckel fel

Om du får fel som rör din kund hanterade nyckel kan du använda följande tabell för att felsöka:

| Felkod     |Information     | Återställnings bara?    |
|----------------|------------|-----------------|
| CmkErrorAccessRevoked | Åtkomst till kundens hanterade nyckel har återkallats.                                                       | Ja, kontrol lera om: <ol><li>Key Vault har fortfarande MSI i åtkomst principen.</li><li>Åtkomst principen har behörigheterna get, wrap och unwrap aktiverat.</li><li>Om nyckel valvet finns i ett VNet bakom brand väggen kontrollerar du om **Tillåt Microsoft-betrodda tjänster** är aktiverat.</li><li>Kontrol lera att MSI för jobb resursen har återställts till att `None` använda API: er.<br>Om ja, ange värdet tillbaka till `Identity = SystemAssigned` . Detta återskapar identiteten för jobb resursen.<br>När den nya identiteten har skapats, aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip</li></ol>                                                                                            |
| CmkErrorKeyDisabled      | Den kund hanterade nyckeln är inaktive rad.                                         | Ja, genom att aktivera nyckel versionen     |
| CmkErrorKeyNotFound      | Det går inte att hitta kundens hanterade nyckel. | Ja, om nyckeln har tagits bort men fortfarande ligger inom rensnings tiden, använder du [ångra borttagning av nyckel valv](/powershell/module/az.keyvault/undo-azkeyvaultkeyremoval).<br>Tilläggs <ol><li>Ja, om kunden har säkerhets kopie rad nyckeln och återställer den.</li><li>Nej, annars.</li></ol>
| CmkErrorVaultNotFound |Det går inte att hitta nyckel valvet för kundens hanterade nyckel. |   Om nyckel valvet har tagits bort:<ol><li>Ja, om det är i rensnings skydds tiden, med hjälp av stegen i [återställa ett nyckel valv](../key-vault/general/soft-delete-overview.md#key-vault-recovery).</li><li>Nej, om det ligger utanför rensnings skyddets varaktighet.</li></ol><br>Om nyckel valvet har migrerats till en annan klient, ja, kan det återställas med hjälp av något av stegen nedan:<ol><li>Återställ Key Vault tillbaka till den gamla klienten.</li><li>Ange `Identity = None` och ange värdet tillbaka till `Identity = SystemAssigned` . Detta tar bort och återskapar identiteten när den nya identiteten har skapats. Aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip.</li></ol>|

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault](../key-vault/general/overview.md)?