---
title: Använd Azure Portal för att hantera Kundhanterade nycklar för Azure Data Box
description: Lär dig hur du använder Azure Portal för att skapa och hantera kund hanterade nycklar med Azure Key Vault för en Azure Data Box. Med Kundhanterade nycklar kan du skapa, rotera, inaktivera och återkalla åtkomst kontroller.
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 11/19/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: e6b588ddea5bf4b4c92e89d9cebb37b09b9a86af
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791552"
---
# <a name="use-customer-managed-keys-in-azure-key-vault-for-azure-data-box"></a>Använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box

Azure Data Box skyddar enhetens upplåsnings nyckel (även kallat enhetens lösen ord), som används för att låsa en enhet via en krypterings nyckel. Som standard är den här krypterings nyckeln en Microsoft-hanterad nyckel. Du kan använda en kundhanterad nyckel för ytterligare kontroll.

Användning av en kundhanterad nyckel påverkar inte hur data på enheten krypteras. Den påverkar bara hur enhetens upplåsnings nyckel krypteras.

Använd en kundhanterad nyckel när du skapar din beställning för att behålla den här kontroll nivån i hela order processen. Mer information finns i [Självstudier: beställ Azure Data Box](data-box-deploy-ordered.md).

Den här artikeln visar hur du aktiverar en kundhanterad nyckel för din befintliga Data Box-enhets ordning i [Azure Portal](https://portal.azure.com/). Du lär dig hur du ändrar nyckel valvet, nyckeln, versionen eller identiteten för den aktuella Kundhanterade nyckeln eller växlar tillbaka till med hjälp av en Microsoft-hanterad nyckel.

Den här artikeln gäller Azure Data Box-och Azure Data Box Heavy-enheter.

## <a name="requirements"></a>Krav

Den Kundhanterade nyckeln för en Data Box-enhet order måste uppfylla följande krav:

- Nyckeln måste skapas och lagras i ett Azure Key Vault som har **mjuk borttagning** och **inte rensas** aktiverat. Mer information finns i [Vad är en Azure Key Vault?](../key-vault/general/overview.md). Du kan skapa ett nyckel valv och en nyckel när du skapar eller uppdaterar din beställning.

- Nyckeln måste vara en RSA-nyckel på 2048 storlek eller större.

## <a name="enable-key"></a>Aktivera nyckel

Följ dessa steg om du vill aktivera en kundhanterad nyckel för din befintliga Data Box-enhet ordning i Azure Portal:

1. Gå till **översikts** skärmen för din data Box-enhets ordning.

    ![Översikts skärm i en Data Box-enhet order-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-1.png)

2. Gå till **inställningar > kryptering** och välj **kund hanterad nyckel**. Välj sedan **Välj en nyckel och ett nyckel valv**.

    ![Välj alternativet för kundhanterad nyckel kryptering](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3.png)

   Din prenumeration fylls i automatiskt på skärmen **Välj nyckel från Azure Key Vault** .

 3. För **Key Vault** kan du välja ett befintligt nyckel valv i list rutan eller välja **Skapa nytt** och skapa ett nytt nyckel valv.

     ![Nyckel valv alternativ när du väljer en kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-3-a.png)

     Om du vill skapa ett nytt nyckel valv anger du prenumerationen, resurs gruppen, nyckel valvets namn och annan information på skärmen **Skapa nytt nyckel valv** . I **återställnings alternativ** ser du till att **mjuk borttagning** och **rensnings skydd** är aktiverat. Välj sedan **Granska + skapa**.

      ![Granska och skapa Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-4.png)

      Granska informationen för nyckel valvet och välj **skapa**. Vänta några minuter tills nyckel valvet har skapats.

       ![Skapa Azure Key Vault med dina inställningar](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-5.png)

4. På skärmen **Välj nyckel från Azure Key Vault** kan du välja en befintlig nyckel från nyckel valvet eller skapa en ny.

    ![Välj nyckel från Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6.png)

   Om du vill skapa en ny nyckel väljer du **Skapa ny**. Du måste använda en RSA-nyckel. Storleken kan vara 2048 eller högre.

    ![Skapa ny nyckel i Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-6-a.png)

    Ange ett namn för den nya nyckeln, godkänn de andra standardinställningarna och välj **skapa**. Du får ett meddelande om att en nyckel har skapats i ditt nyckel valv.

    ![Namn ny nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-7.png)

5. För **version** kan du välja en befintlig nyckel version i list rutan.

    ![Välj version för ny nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8.png)

    Om du vill generera en ny nyckel version väljer du **Skapa ny**.

    ![Öppna en dialog ruta för att skapa en ny nyckel version](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-a.png)

    Välj inställningar för den nya nyckel versionen och välj **skapa**.

    ![Skapa en ny nyckel version](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-b.png)

6. När du har valt nyckel valv, nyckel och nyckel version väljer du **Välj**.

    ![En nyckel i en Azure Key Vault](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-8-c.png)

    **Krypterings typs** inställningarna visar nyckel valvet och nyckeln som du har valt.

    ![Nyckel-och nyckel valv för en kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-9.png)

7. Välj den typ av identitet som ska användas för att hantera den Kundhanterade nyckeln för den här resursen. Du kan använda den **systemtilldelade** identiteten som genererades när du skapade ordern eller välja en tilldelad identitet.

    En användardefinierad identitet är en oberoende resurs som du kan använda för att hantera åtkomst till resurser. Mer information finns i [hanterade identitets typer](../active-directory/managed-identities-azure-resources/overview.md).

    ![Välj identitets typ](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-13.png)

    Om du vill tilldela en användar identitet väljer du **tilldelad användare**. Välj sedan **Välj en användar identitet** och välj den hanterade identitet som du vill använda.

    ![Välj en identitet som ska användas](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-14.png)

    Du kan inte skapa en ny användar identitet här. Om du vill ta reda på hur du skapar en sådan, se [skapa, lista, ta bort eller tilldela en roll till en användardefinierad hanterad identitet med hjälp av Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

    Den valda användar identiteten visas i inställningar för **krypterings typ** .

    ![En vald användar identitet visas i inställningar för krypterings typ](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-15.png)

 9. Välj **Spara** för att spara de uppdaterade inställningarna för **krypterings typ** .

     ![Spara din Kundhanterade nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-10.png)

    Nyckel-URL: en visas under **krypterings typ**.

    ![Kund hanterad nyckel-URL](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-11.png)<!--Probably need new screen from recent order. Can you provide one? I can't create an order using CMK with the subscription I'm using.-->

## <a name="change-key"></a>Ändra nyckel

Följ dessa steg om du vill ändra nyckel valvet, nyckeln och/eller nyckel versionen för den Kundhanterade nyckel som du använder för närvarande:

1. På skärmen **Översikt** för din data Box-enhet ordning går du till **Inställningar**  >  **kryptering** och klickar på **ändra nyckel**.

    ![Översikts skärm i en Data Box-enhet order med kundhanterad nyckel-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16.png)

2. Välj **Välj ett annat nyckel valv och nyckel**.

    ![Översikts skärm i en Data Box-enhet ordning väljer du ett annat alternativ för nyckel-och nyckel valv](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-16-a.png)

3. På skärmen **Välj nyckel från Key Vault** visas prenumerationen men inget nyckel valv, nyckel eller nyckel version. Du kan göra följande ändringar:

   - Välj en annan nyckel från samma nyckel valv. Du måste välja nyckel valvet innan du väljer nyckeln och versionen.

   - Välj ett annat nyckel valv och tilldela en ny nyckel.

   - Ändra versionen för den aktuella nyckeln.
   
    När du är klar med ändringarna väljer du **Välj**.

    ![Välj krypterings alternativ-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17.png)

4. Välj **Spara**.

    ![Spara uppdaterade krypterings inställningar-1](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="change-identity"></a>Ändra identitet

Följ dessa steg om du vill ändra den identitet som används för att hantera åtkomst till den Kundhanterade nyckeln för den här beställningen:

1. På skärmen **Översikt** för din slutförda data Box-enhets ordning går du till **Inställningar**  >  **kryptering**.

2. Gör någon av följande ändringar:

     - Om du vill ändra till en annan användar identitet klickar du på **Välj en annan användar identitet**. Välj sedan en annan identitet i panelen på höger sida av skärmen och välj **Välj**.

       ![Alternativ för att ändra den tilldelade identiteten för en kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-18.png)

   - Om du vill växla till den systemtilldelade identitet som genererades när du skapade ordningen väljer du **system tilldelad** av **Välj typ av identitet**.

     ![Alternativ för att ändra till ett system som tilldelats en kundhanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-19.png)

3. Välj **Spara**.

    ![Spara uppdaterade krypterings inställningar-2](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-17-a.png)

## <a name="use-microsoft-managed-key"></a>Använd Microsoft-hanterad nyckel

Följ dessa steg om du vill ändra från att använda en kundhanterad nyckel till den hanterade nyckeln från Microsoft för din beställning:

1. På skärmen **Översikt** för din slutförda data Box-enhets ordning går du till **Inställningar**  >  **kryptering**.

2. Välj **Microsoft Managed Key**( **Välj typ**).

    ![Översikts skärm i en Data Box-enhet beställning – 5](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-20.png)

3. Välj **Spara**.

    ![Spara uppdaterade krypterings inställningar för en Microsoft-hanterad nyckel](./media/data-box-customer-managed-encryption-key-portal/customer-managed-key-21.png)

## <a name="troubleshoot-errors"></a>Felsöka fel

Om du får fel som rör din Kundhanterade nyckel kan du använda följande tabell för att felsöka.

| Felkod| Felinformation| Återställnings bara?|
|-------------|--------------|---------|
| SsemUserErrorEncryptionKeyDisabled| Det gick inte att hämta nyckeln eftersom den hanterade nyckeln för kund är inaktive rad.| Ja, genom att aktivera nyckel versionen.|
| SsemUserErrorEncryptionKeyExpired| Det gick inte att hämta nyckeln eftersom den hanterade nyckeln för kund har upphört att gälla.| Ja, genom att aktivera nyckel versionen.|
| SsemUserErrorKeyDetailsNotFound| Det gick inte att hämta nyckeln eftersom kundens hanterade nyckel inte hittades.| Om du har tagit bort nyckel valvet kan du inte återställa den Kundhanterade nyckeln.  Om du har migrerat nyckel valvet till en annan klient organisation läser du [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../key-vault/general/move-subscription.md). Om du har tagit bort nyckel valvet:<ol><li>Ja, om det är i rensnings skydds tiden, med hjälp av stegen i [återställa ett nyckel valv](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).</li><li>Nej, om det ligger utanför rensnings skyddets varaktighet.</li></ol><br>Om nyckel valvet har genomgått en klient migrering, ja, kan den återställas med hjälp av något av stegen nedan: <ol><li>Återställ Key Vault tillbaka till den gamla klienten.</li><li>Ange `Identity = None` och ange värdet tillbaka till `Identity = SystemAssigned` . Detta tar bort och återskapar identiteten när den nya identiteten har skapats. Aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip.</li></ol> |
| SsemUserErrorKeyVaultBadRequestException | Tillämpade en kundhanterad nyckel men nyckel åtkomsten har inte beviljats eller har återkallats, eller så går det inte att komma åt Key Vault på grund av att brand väggen har Aktiver ATS | Lägg till den identitet som valts i nyckel valvet för att ge åtkomst till kundens hanterade nyckel. Om nyckel valvet har aktive rad brand vägg, byter du till en tilldelad identitet och lägger sedan till en kundhanterad nyckel. Mer information finns i så här [aktiverar du nyckeln](#enable-key). |
| SsemUserErrorKeyVaultDetailsNotFound| Det gick inte att hämta nyckeln eftersom det inte gick att hitta det associerade nyckel valvet för kundens hanterade nyckel. | Om du har tagit bort nyckel valvet kan du inte återställa den Kundhanterade nyckeln.  Om du har migrerat nyckel valvet till en annan klient organisation läser du [ändra ett klient-ID för Key Vault efter att prenumerationen har flyttats](../key-vault/general/move-subscription.md). Om du har tagit bort nyckel valvet:<ol><li>Ja, om det är i rensnings skydds tiden, med hjälp av stegen i [återställa ett nyckel valv](../key-vault/general/key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell).</li><li>Nej, om det ligger utanför rensnings skyddets varaktighet.</li></ol><br>Om nyckel valvet har genomgått en klient migrering, ja, kan den återställas med hjälp av något av stegen nedan: <ol><li>Återställ Key Vault tillbaka till den gamla klienten.</li><li>Ange `Identity = None` och ange värdet tillbaka till `Identity = SystemAssigned` . Detta tar bort och återskapar identiteten när den nya identiteten har skapats. Aktivera `Get` , `Wrap` och `Unwrap` behörighet till den nya identiteten i nyckel valvets åtkomst princip.</li></ol> |
| SsemUserErrorSystemAssignedIdentityAbsent  | Det gick inte att hämta nyckeln eftersom kundens hanterade nyckel inte hittades.| Ja, kontrol lera om: <ol><li>Key Vault har fortfarande MSI i åtkomst principen.</li><li>Identiteten är av typen System tilldelad.</li><li>Aktivera get-, wrap-och unwrap-behörigheter till identiteten i nyckel valvets åtkomst princip.</li></ol>|
| SsemUserErrorUserAssignedLimitReached | Det gick inte att lägga till en ny användare som tilldelats identitet eftersom du har nått gränsen för det totala antalet användare som tilldelats identiteter som kan läggas till. | Försök igen med färre användar identiteter eller ta bort vissa användare tilldelade identiteter från resursen innan du försöker igen. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Åtkomst åtgärden för hanterad identitet misslyckades. <br> Obs! det här är för scenariot när prenumerationen flyttas till en annan klient. Kunden måste flytta identiteten manuellt till en ny klient. PFA mail för mer information. | Flytta den identitet som valts till den nya klient organisation som prenumerationen finns under. Mer information finns i så här [aktiverar du nyckeln](#enable-key). |
| SsemUserErrorKekUserIdentityNotFound | Tillämpade en kundhanterad nyckel men den tilldelade användaren identiteten som har åtkomst till nyckeln hittades inte i Active Directory. <br> Obs: Detta gäller när användar identiteten tas bort från Azure.| Försök att lägga till en annan användardefinierad identitet som valts i ditt nyckel valv för att ge åtkomst till kundens hanterade nyckel. Mer information finns i så här [aktiverar du nyckeln](#enable-key). |
| SsemUserErrorUserAssignedIdentityAbsent | Det gick inte att hämta nyckeln eftersom kundens hanterade nyckel inte hittades. | Det gick inte att få åtkomst till kundens hanterade nyckel. Antingen tas den tilldelade identiteten (UAI) som är associerad med nyckeln bort eller så har UAI-typen ändrats. |
| SsemUserErrorCrossTenantIdentityAccessForbidden | Åtkomst åtgärden för hanterad identitet misslyckades. <br> Obs! det här är för scenariot när prenumerationen flyttas till en annan klient. Kunden måste flytta identiteten manuellt till en ny klient. PFA mail för mer information. | Försök att lägga till en annan användardefinierad identitet som valts i ditt nyckel valv för att ge åtkomst till kundens hanterade nyckel. Mer information finns i så här [aktiverar du nyckeln](#enable-key).|
| SsemUserErrorKeyVaultBadRequestException | Tillämpade en kundhanterad nyckel men nyckel åtkomsten har inte beviljats eller har återkallats, eller så går det inte att komma åt Key Vault på grund av att brand väggen har Aktiver ATS | Lägg till den identitet som valts i nyckel valvet för att ge åtkomst till kundens hanterade nyckel. Om nyckel valvet har aktive rad brand vägg, byter du till en tilldelad identitet och lägger sedan till en kundhanterad nyckel. Mer information finns i så här [aktiverar du nyckeln](#enable-key). |
| Allmänt fel  | Det gick inte att hämta nyckeln.| Detta är ett allmänt fel. Kontakta Microsoft Support för att felsöka felet och fastställa nästa steg.|

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../key-vault/general/overview.md)
- [Snabbstart: Ställ in och hämta en hemlighet från Azure Key Vault med hjälp av Azure-portalen](../key-vault/secrets/quick-create-portal.md)
