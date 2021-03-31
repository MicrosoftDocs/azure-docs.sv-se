---
title: Lägg till en intern support policy i ett labb i Azure DevTest Labs
description: Lär dig hur du publicerar en intern support policy till ett labb i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1c9920e6fe7fbfe2a8d0aeacb896150b342981b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480481"
---
# <a name="add-an-internal-support-statement-to-a-lab-in-azure-devtest-labs"></a>Lägg till en intern support policy i ett labb i Azure DevTest Labs

Med Azure DevTest Labs kan du anpassa ditt labb med en intern support instruktion som ger användare support information om labbet. Du kan till exempel ange kontakt information så att en användare vet hur man når intern support när de behöver hjälp med fel sökning eller åtkomst till resurser i labbet. Du kan också ange länkar till interna webbplatser eller vanliga frågor och svar som ditt team har åtkomst till innan support kontaktas.

En intern support policy är avsedd att låta dig anslå Lab-information som normalt inte ändras för ofta. För att meddela användare om labb information som är mer temporärt belastad – t. ex. nya uppdateringar av labb principer – se [publicera meddelande i ett labb](devtest-lab-announcements.md).

Du kan enkelt inaktivera eller redigera en support instruktion när den inte längre är tillämplig.

## <a name="steps-to-add-a-support-statement-to-an-existing-lab"></a>Steg för att lägga till ett support uttryck i ett befintligt labb

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Om det behövs väljer du **alla tjänster** och väljer sedan **DevTest Labs** i listan. (Ditt labb kanske redan visas på instrument panelen under **alla resurser**).
1. I listan med labb väljer du det labb som du vill lägga till ett support meddelande i.  
1. I labb **översikts** avsnittet väljer du **konfiguration och principer**.  

    ![Knappen konfiguration och principer](./media/devtest-lab-internal-support-message/devtestlab-config-and-policies.png)

1. Välj **intern support** till vänster under **Inställningar**.

    ![Knapp för intern support](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)

1. Om du vill skapa ett internt support meddelande för användarna i det här labbet ställer du in på **Ja**.

1. I fältet **support meddelande** anger du den interna support instruktion som du vill presentera för dina labb användare. Support meddelandet accepterar markdown. När du anger meddelande texten kan du Visa **förhands gransknings** ytan längst ned på skärmen för att se hur meddelandet visas för användarna.

    ![Intern support skärm för att skapa meddelandet.](./media/devtest-lab-internal-support-message/devtestlab-add-support-statement.png)


1. Välj **Spara** när ditt support meddelande är klart att publicera.

När du inte längre vill visa det här support meddelandet för labb användare går du tillbaka till sidan för **intern support** och **aktiverar** **Nej**.

## <a name="steps-for-users-to-view-the-support-message"></a>Steg för användare att visa support meddelandet

1. Välj ett labb från [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. I labb **översikts** avsnittet väljer du **internt stöd**.  

    ![Knapp för intern support](./media/devtest-lab-internal-support-message/devtestlab-internal-support.png)


1. Om ett support meddelande har publicerats kan användaren visa den från det interna support fönstret.

    ![Fönstret intern support som visar att stöd meddelandet har publicerats](./media/devtest-lab-internal-support-message/devtestlab-view-suport-statement.png)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
* Interna support rapporter används vanligt vis för att ge support information som inte ändras ofta. Du kan också lära dig att [publicera ett meddelande till ett labb](devtest-lab-announcements.md) för att informera användarna om tillfälliga ändringar eller uppdateringar av labbet.
* [Ange principer och scheman](devtest-lab-set-lab-policy.md) ger information om hur du kan tillämpa andra begränsningar och konventioner i din prenumeration med hjälp av anpassade principer.