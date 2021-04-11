---
title: Konfigurera Azure multifaktor-autentisering för Windows Virtual Desktop – Azure
description: Konfigurera Azure multifaktor-autentisering för ökad säkerhet i Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 7ebf38226ff725865104707a3f28e7ce51a86c31
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445660"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Aktivera Azure multifaktor-autentisering för Windows Virtual Desktop

>[!IMPORTANT]
> Om du besöker den här sidan från den klassiska Windows-dokumentationen (klassisk) ser du till att [återgå till Windows Virtual Desktop (klassisk)-dokumentationen](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) när du är klar.

Windows-klienten för virtuellt Windows-skrivbord är ett utmärkt alternativ för att integrera virtuella Windows-datorer med din lokala dator. Men när du konfigurerar ditt Windows-konto för virtuella skriv bord till Windows-klienten, finns det vissa mått som du måste vidta för att hålla dig trygg och dina användare.

När du först loggar in frågar klienten efter ditt användar namn, lösen ord och Azure multifaktor-autentisering. Efter det kommer klienten att komma ihåg din token från din Azure Active Directory (AD) företags program nästa gången du loggar in. När du väljer **kom ihåg mig** i prompten efter autentiseringsuppgifter för sessionens värd kan användarna logga in efter omstart av klienten utan att behöva ange sina autentiseringsuppgifter på annat sätt.

Även om det är praktiskt att komma ihåg autentiseringsuppgifterna, kan det också göra distributioner i företags scenarier eller personliga enheter mindre säkra. För att skydda dina användare kan du kontrol lera att klienten fortfarande ber om autentiseringsuppgifter för Azure multifaktor-autentisering oftare. I den här artikeln visas hur du konfigurerar principen för villkorlig åtkomst för Windows Virtual Desktop för att aktivera den här inställningen.

## <a name="prerequisites"></a>Förutsättningar

Det här behöver du för att komma igång:

- Tilldela användare en licens som innehåller Azure Active Directory Premium P1 eller P2.
- En Azure Active Directory grupp med dina användare tilldelade som grupp medlemmar.
- Aktivera Azure multifaktor-autentisering för alla dina användare. Mer information om hur du gör finns i [så här kräver du tvåstegsverifiering för en användare](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user).

> [!NOTE]
> Följande inställning gäller även för [webb klienten för virtuella Windows-datorer](https://rdweb.wvd.microsoft.com/arm/webclient/index.html).

## <a name="create-a-conditional-access-policy"></a>Skapa en princip för villkorlig åtkomst

Så här skapar du en princip för villkorlig åtkomst som kräver multifaktorautentisering vid anslutning till virtuella Windows-datorer:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller villkorlig åtkomst administratör.
2. Bläddra till **Azure Active Directory**  >  **säkerhet**  >  **villkorlig åtkomst**.
3. Välj **ny princip**.
4. Ge principen ett namn. Vi rekommenderar att organisationer skapar en meningsfull standard för namnen på deras principer.
5. Under **Tilldelningar** väljer du **Användare och grupper**.
6. Under **Inkludera** väljer du **Välj användare och grupper**  >  **användare och grupper** > väljer den grupp som du skapade i [krav](#prerequisites) stadiet.
7. Välj **Klar**.
8. Under **molnappar eller åtgärder**  >  **inkluderar** väljer du **Välj appar**.
9. Välj en av följande appar baserat på vilken version av Windows Virtual Desktop som du använder.
   
   - Om du använder Windows Virtual Desktop (klassisk) väljer du de här apparna:
       
       - **Windows Virtual Desktop** (app-ID 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Windows Virtual Desktop-klient** (app-ID fa4345a4-a730-4230-84a8-7d9651b86739), som gör att du kan ange principer för webb klienten
       
        Därefter går du vidare till steg 11.

   - Om du använder Windows Virtual Desktop väljer du den här appen i stället:
       
       -  **Windows Virtual Desktop** (app-ID 9cdead84-a844-4324-93f2-b2e6bb768d07)
       
        Sedan går du till steg 10.

   >[!IMPORTANT]
   > Välj inte appen som heter Windows Virtual Desktop Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63). Den här appen används bara för att hämta användar flödet och får inte ha multifaktorautentisering.
   > 
   > Om du använder Windows Virtual Desktop (klassisk), och om principen för villkorlig åtkomst blockerar all åtkomst och bara undantar Windows-ID: n för virtuella Skriv bords program, kan du åtgärda detta genom att lägga till app-ID-9cdead84-a844-4324-93f2-b2e6bb768d07 till principen. Om du inte lägger till det här app-ID: t blockeras feed-identifiering av klassiska Windows-resurser.

10. Gå till **villkor**  >  -**klient program** och välj sedan var du vill tillämpa principen på:
    
    - Välj **webbläsare** om du vill att principen ska tillämpas på webb klienten.
    - Välj **mobilappar och skriv bords klienter** om du vill tillämpa principen på andra klienter.
    - Markera båda kryss rutorna om du vill tillämpa principen på alla klienter.
   
    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan klient program. Användaren har markerat kryss rutan mobilappar och skriv bords klienter.](media/select-apply.png)

11. När du har valt din app väljer du **Välj** och väljer sedan **klar**.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av sidan molnappar eller åtgärder. Klient program för virtuella Windows-datorer och Windows-appar för virtuella skriv bord är markerade i rött.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >Om du vill hitta app-ID: t för den app som du vill välja går du till **företags program** och väljer **Microsoft-program** i den nedrullningsbara menyn program typ.

12. Under **åtkomst kontroller**  >  **tilldelar** väljer du **bevilja åtkomst**, **kräver Multi-Factor Authentication** och **väljer** sedan.
13. Under **Access Controls**  >  -**session** väljer du **inloggnings frekvens**, anger värdet till den tid som du vill ha mellan prompterna och väljer sedan **Välj**. Om du till exempel ställer in värdet på **1** och enheten till **timmar** kräver multifaktorautentisering autentisering om en anslutning startas en timme efter den sista.
14. Bekräfta inställningarna och ange **Aktivera princip** till **på**.
15. Välj **skapa** för att aktivera principen.

>[!NOTE]
>När du använder webb klienten för att logga in på Windows Virtual Desktop via webbläsaren, visar loggen klientens app-ID som a85cf173-4192-42F8-81fa-777a763e6e2c (Windows Virtual Desktop Client). Detta beror på att klient appen är internt länkad till Server App-ID: t där principen för villkorlig åtkomst har angetts. 

## <a name="next-steps"></a>Nästa steg

- [Läs mer om principer för villkorlig åtkomst](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [Läs mer om användar inloggnings frekvens](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
