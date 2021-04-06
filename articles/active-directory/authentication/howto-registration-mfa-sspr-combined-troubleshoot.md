---
title: Felsöka kombinerad registrerings-Azure Active Directory
description: Felsöka Azure AD-Multi-Factor Authentication och en kombination av lösen ords återställning via självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: db87887fc2b51c7cb8cb300eb8e711d3ae9b6ac8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610797"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Felsöka kombinerad säkerhets informations registrering

Informationen i den här artikeln är avsedd att leda administratörer som har fel söknings problem som rapporteras av användare av den kombinerade registrerings upplevelsen.

## <a name="audit-logs"></a>Granskningsloggar

Händelserna som loggas för kombinerad registrering finns i tjänsten autentiseringsmetoder i gransknings loggarna för Azure AD.

![Azure AD audit logs-gränssnittet visar registrerings händelser](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

I följande tabell visas alla gransknings händelser som har genererats vid kombinerad registrering:

| Aktivitet | Status | Anledning | Beskrivning |
| --- | --- | --- | --- |
| Användaren registrerade all nödvändig säkerhets information | Klart | Användaren registrerade all nödvändig säkerhets information. | Den här händelsen inträffar när en användare har slutfört registreringen.|
| Användaren registrerade all nödvändig säkerhets information | Fel | Användaren avbröt registreringen av säkerhets information. | Den här händelsen inträffar när en användare avbryter registreringen från avbrotts läge.|
| Användarens registrerade säkerhets information | Klart | *Metod* för registrerad användare. | Den här händelsen inträffar när en användare registrerar en enskild metod. *Metoden* kan vara en Authenticator-app, telefon, e-post, säkerhets frågor, applösenord, alternativ telefon och så vidare.| 
| Användare granskad säkerhets information | Klart | Användaren har granskat säkerhets informationen. | Den här händelsen inträffar när en användare väljer **ser bra ut** på sidan granskning av säkerhets information.|
| Användare granskad säkerhets information | Fel | Användaren kunde inte granska säkerhets informationen. | Den här händelsen inträffar när en användare väljer **ser bra ut** på sidan granskning av säkerhets information men det går inte att utföra något på Server delen.|
| Användaren tog bort säkerhets information | Klart | *Metoden* User Deleted. | Den här händelsen inträffar när en användare tar bort en enskild metod. *Metoden* kan vara en Authenticator-app, telefon, e-post, säkerhets frågor, applösenord, alternativ telefon och så vidare.|
| Användaren tog bort säkerhets information | Fel | Användaren kunde inte ta bort *metoden*. | Den här händelsen inträffar när en användare försöker ta bort en metod men försöket Miss lyckas av någon anledning. *Metoden* kan vara en Authenticator-app, telefon, e-post, säkerhets frågor, applösenord, alternativ telefon och så vidare.|
| Användaren ändrade standard säkerhets information | Klart | Användaren ändrade standard säkerhets informationen för *metoden*. | Den här händelsen inträffar när en användare ändrar standard metoden. *Metoden* kan vara meddelandeautentisering, en kod från min Authenticator-app eller token, anropa + X XXXXXXXXXX, text A kod till + x xxxxxxxxx och så vidare.|
| Användaren ändrade standard säkerhets information | Fel | Användaren kunde inte ändra standard säkerhets informationen för *metoden*. | Den här händelsen inträffar när en användare försöker ändra standard metoden, men försöket Miss lyckas av någon anledning. *Metoden* kan vara meddelandeautentisering, en kod från min Authenticator-app eller token, anropa + X XXXXXXXXXX, text A kod till + x xxxxxxxxx och så vidare.|

## <a name="troubleshooting-interrupt-mode"></a>Fel sökning av avbrotts läge

| Symptom | Felsökningsanvisningar |
| --- | --- |
| Jag ser inte de metoder jag förväntade mig. | 1. kontrol lera om användaren har en administratörs roll för Azure AD. Om ja, se skillnaderna mellan SSPR admin-principen. <br> 2. avgör om användaren avbryts på grund av Multi-Factor Authentication tvångs registrering eller SSPR. Se [flödesschemat](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) under "kombinerade registrerings lägen" för att avgöra vilka metoder som ska visas. <br> 3. ta reda på hur nyligen Multi-Factor Authentication-eller SSPR-principen ändrades. Om ändringen var nyligen kan det ta lite tid innan den uppdaterade principen har spridits.|

## <a name="troubleshooting-manage-mode"></a>Fel sökning av hanterings läge

| Symptom | Felsökningsanvisningar |
| --- | --- |
| Jag har inte möjlighet att lägga till en viss metod. | 1. kontrol lera om metoden är aktive rad för Multi-Factor Authentication eller för SSPR. <br> 2. om metoden är aktive rad sparar du principerna igen och väntar 1-2 timmar innan du testar igen. <br> 3. om metoden är aktive rad kontrollerar du att användaren inte redan har konfigurerat det maximala antalet för den här metoden som de tillåts att konfigurera.|

## <a name="disable-combined-registration"></a>Inaktivera kombinerad registrering

När en användare registrerar ett telefonnummer och/eller en mobilapp i den nya kombinerade miljön, stämplar vår tjänst en uppsättning flaggor (StrongAuthenticationMethods) för dessa metoder för den användaren. Med den här funktionen kan användaren utföra Multi-Factor Authentication med dessa metoder när Multi-Factor Authentication krävs.

Om en administratör aktiverar för hands versionen registreras användarna genom den nya upplevelsen och administratören inaktiverar förhands granskningen. användare kan vara osäkert registrerade för Multi-Factor Authentication även.

Om en användare som har slutfört en kombinerad registrering går till den aktuella självbetjänings registrerings sidan för lösen ords återställning (SSPR) på [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) , uppmanas användaren att utföra Multi-Factor Authentication innan de kan komma åt sidan. Det här steget förväntas från en teknisk synpunkt, men det är nytt för användare som tidigare har registrerats för SSPR. Även om det här extra steget förbättrar användarens säkerhets position genom att tillhandahålla en annan säkerhets nivå kan administratörer vilja återställa sina användare så att de inte längre kan utföra Multi-Factor Authentication.  

### <a name="how-to-roll-back-users"></a>Återställa användare

Om du, som administratör, vill återställa en användares Multi-Factor Authentication inställningar, kan du använda PowerShell-skriptet som anges i nästa avsnitt. Skriptet tar bort egenskapen StrongAuthenticationMethods för en användares mobilapp och/eller telefonnummer. Om du kör det här skriptet för dina användare måste de registreras igen för Multi-Factor Authentication om de behöver det. Vi rekommenderar att du testar återställningen med en eller två användare innan du återställer alla berörda användare.

Stegen nedan hjälper dig att återställa en användare eller grupp av användare.

#### <a name="prerequisites"></a>Förutsättningar

1. Installera lämpliga Azure AD PowerShell-moduler. I ett PowerShell-fönster kör du följande kommandon för att installera modulerna:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Spara listan över berörda användar objekt-ID: n till datorn som en textfil med ett ID per rad. Anteckna filens plats.
1. Spara följande skript på datorn och anteckna var skriptet finns:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Återställning

Kör följande kommando i ett PowerShell-fönster och ange platser för skript och användar fil. Ange autentiseringsuppgifter för global administratör när du uppmanas till det. Skriptet kommer att resultera i resultatet av varje användar uppdaterings åtgärd.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Inaktivera den uppdaterade upplevelsen

Slutför de här stegen för att inaktivera den uppdaterade upplevelsen för dina användare:

1. Logga in på Azure Portal som användar administratör.
2. Gå till **Azure Active Directory**  >  **användar inställningar**  >  **Hantera inställningar för åtkomst panelens för hands versions funktioner**.
3. Under **användare kan använda för hands versions funktioner för att registrera och hantera säkerhets information**, ange Selector till **none** och välj **Spara**.

Användarna kommer inte längre att uppmanas att registrera sig genom att använda den uppdaterade versionen.

## <a name="next-steps"></a>Nästa steg

* [Lär dig mer om kombinerad registrering för självbetjäning för återställning av lösen ord och Azure AD Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
