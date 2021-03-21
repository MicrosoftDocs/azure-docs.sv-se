---
title: 'Gör så här: exportera anpassade kommandon till program som en tjänst för en fjärran sluten kunskaps tal'
titleSuffix: Azure Cognitive Services
description: I den här artikeln får du lära dig hur du exporterar anpassade kommandon till program som en färdighet
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: sausin
ms.openlocfilehash: 77ade17803a35491712ec6df70aed9eb7b4883eb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95025882"
---
# <a name="export-custom-commands-application-as-a-remote-skill"></a>Exportera anpassade kommandon program som en fjärrskicklighet

I den här artikeln får du lära dig hur du exporterar ett program för anpassade kommandon som en fjärrkunskap.

## <a name="prerequisites"></a>Förutsättningar
> [!div class="checklist"]
> * [Förståelse för bot Framework-kunskaper](/azure/bot-service/skills-conceptual)
> * [Förståelse för kunskaps manifest](https://aka.ms/speech/cc-skill-manifest)
> * [Så här anropar du en färdighet från en bot Framework-robot](/azure/bot-service/skills-about-skill-consumers)
> * Ett befintligt anpassat kommandon-program. Om du inte har några anpassade kommando program kan du prova med- [snabb start: skapa en röst assistent med anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="custom-commands-as-remote-skills"></a>Anpassade kommandon som fjärrfärdigheter
* Bot Framework-kunskaper är återanvändbara konversationer som bygger på konversations användning – så att du kan lägga till omfattande funktioner i en robot på några minuter. Läs mer på detta genom att gå till [robot Framework-kunskaper](https://microsoft.github.io/botframework-solutions/overview/skills/).
* Ett program med anpassade kommandon kan exporteras som en färdighet. Den här kunskapen kan sedan anropas via protokollet för fjärran sluten kunskap från en bot Framework-robot.

## <a name="configure-an-application-to-be-exposed-as-a-remote-skill"></a>Konfigurera ett program som ska exponeras som en fjär kunskap

### <a name="application-level-settings"></a>Inställningar för program nivå
1. I den vänstra panelen väljer du **Inställningar**  >  **fjärrfärdigheter**.
1. Ange **fjärrfärdigheter aktiverade** växla till på.

### <a name="authentication-to-skills"></a>Autentisering för kunskaper
1. Om du vill aktivera autentisering lägger du till Microsofts program-ID: n för bot Framework-robotar som du vill konfigurera för att anropa programmet för anpassade kommandon.
      > [!div class="mx-imgBorder"]
      > ![Lägg till ett MSA-ID i kompetensen](media/custom-commands/skill-add-msa-id.png)

1. Om du har lagt till minst en post i listan, aktive ras autentiseringen i programmet och endast de tillåtna robotar kan anropa programmet.
> [!TIP]
>  Om du vill inaktivera autentisering tar du bort alla Microsoft-programid: n från listan över tillåtna program. 

 ### <a name="enabledisable-commands-to-be-exposed-as-skills"></a>Aktivera/inaktivera kommandon som ska visas som färdigheter

Du kan välja vilka kommandon du vill exportera via fjärrkunskaper.

1. Om du vill visa ett kommando över kunskaper väljer du **aktivera ett nytt kommando** under kommandot **Aktivera kommandon för kunskaper**.
1. I list rutan väljer du kommandot som du vill lägga till.
1. Välj **Spara**.

### <a name="configure-triggering-utterances-for-commands"></a>Konfigurera Utlös ande av yttranden för kommandon
Anpassade kommandon använder exempel meningar som har kon figurer ATS för kommandona för att generera de kunskaper som utlöser yttranden. Dessa **Utlös ande yttranden** kommer att användas för att generera **dispatcher** -avsnittets [**kunskaps manifest**](https://microsoft.github.io/botframework-solutions/skills/handbook/manifest/).

Som författare kanske du vill kontrol lera vilka av dina **exempel meningar** som används för att generera yttranden för kunskaper.
1. Som standard ingår manifest filen i alla **exempel på Utlös ande exempel** från ett kommando.
1. Om du uttryckligen vill ta bort ett av de två exemplen väljer du **Redigera** ikon i avsnittet **kommandon från aktiverade kommandon för kunskaper** .
    > [!div class="mx-imgBorder"]
    > ![Redigera ett aktiverat kommando för kompetens](media/custom-commands/skill-edit-enabled-command.png)

1. I de exempel meningar som du vill utelämna, **högerklickar du på**  >  **inaktivera exempel mening**.
    > [!div class="mx-imgBorder"]
    > ![Inaktivera exempel](media/custom-commands/skill-disable-example-sentences.png)

1. Välj **Spara**.
1. Du kommer att märka att du inte kan lägga till ett nytt exempel i det här fönstret. Om du behöver göra det går du vidare till avsnittet för att avsluta inställningarna och väljer det relevanta kommandot från **kommandon** . Nu kan du lägga till den nya posten i avsnittet **exempel meningar** . Den här ändringen visas automatiskt i värdet för fjärr styrnings inställningar för kommandot.

> [!IMPORTANT]
> Om de befintliga exempel meningarna innehåller referenser till **sträng > katalog** data typ, kommer dessa meningar automatiskt att utelämnas från listan med de kunskaper som utlöser yttranden. 

## <a name="download-skill-manifest"></a>Hämta kunskaps manifest
1. När du har **publicerat** ditt program kan du ladda ned kunskaps manifest filen.
1. Använd kunskaps manifestet för att konfigurera din bot Framework Consumer bot att anropa i de anpassade kommandona.
> [!IMPORTANT]
> Du måste **publicera** programmet för anpassade kommandon för att kunna hämta kunskaps manifestet. </br>
> Om du har gjort **några ändringar** i programmet måste du dessutom publicera programmet igen för att de senaste ändringarna ska avspeglas i manifest filen.

> [!NOTE]
> Om du har problem med att publicera programmet och felet leder till kunskaper som utlöser yttranden, kontrollerar du om konfigurationen för de **aktiverade kommandona för kunskaper**. Vart och ett av de exponerade kommandona måste ha minst en giltig trigger uttryck.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Uppdatera ett kommando från klienten](./how-to-custom-commands-update-command-from-client.md)
