---
title: Åtgärda problem med dynamiska grupp medlemskap – Azure AD | Microsoft Docs
description: Fel söknings tips för dynamiskt grupp medlemskap i Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41bfdf11bad28ab772b68839a5a7bf7776eb4dff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548111"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Felsöka och lösa problem med grupper

## <a name="troubleshooting-group-creation-issues"></a>Fel sökning av problem med grupp skapande

**Jag har inaktiverat skapande av säkerhets grupper i Azure Portal men grupper kan fortfarande skapas via PowerShell** **Användaren kan skapa säkerhets grupper i Azure Portals** -inställningen i Azure Portal kontrollerar om användare som inte är administratörer kan skapa säkerhets grupper i åtkomst panelen eller Azure Portal. Den styr inte skapandet av säkerhets grupper via PowerShell.

Så här inaktiverar du grupp skapande för användare som inte är administratörer i PowerShell:
1. Verifiera att användare som inte är administratörer får skapa grupper:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Om den returneras `UsersPermissionToCreateGroupsEnabled : True` kan användare som inte är administratörer skapa grupper. Så här inaktiverar du den här funktionen:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Jag har fått ett fel av högsta antal grupper som tillåts när du försökte skapa en dynamisk grupp i PowerShell**<br/>
Om du får ett meddelande i PowerShell som anger att det _maximala antalet tillåtna grupper för dynamiska grup principer har nåtts_ innebär det att du har nått Max gränsen för dynamiska grupper i din organisation. Det maximala antalet dynamiska grupper per organisation är 5 000.

Om du vill skapa nya dynamiska grupper måste du först ta bort vissa befintliga dynamiska grupper. Det finns inget sätt att öka gränsen.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Felsöka dynamiska medlemskap för grupper

**Jag har konfigurerat en regel för en grupp men inga medlemskap uppdateras i gruppen**<br/>
1. Verifiera värdena för användar-eller enhets attribut i regeln. Se till att det finns användare som uppfyller regeln. För enheter kontrollerar du enhetens egenskaper för att se till att alla synkroniserade attribut innehåller de förväntade värdena.<br/>
2. Kontrol lera status för medlemskaps bearbetning för att bekräfta att den är klar. Du kan kontrol lera [status för medlemskaps bearbetning](groups-create-rule.md#check-processing-status-for-a-rule) och senaste uppdaterade datum på **översikts** sidan för gruppen.

Om allting ser bra ut kan du ange en tid för att fylla i gruppen. Beroende på storleken på din Azure AD-organisation kan det ta upp till 24 timmar innan gruppen fylls för första gången eller efter en regel ändring.

**Jag har konfigurerat en regel, men nu tas befintliga medlemmar i regeln bort**<br/>Det här beteendet är förväntat. Befintliga medlemmar i gruppen tas bort när en regel aktive ras eller ändras. De användare som returneras från utvärderingen av regeln läggs till som medlemmar i gruppen.

**Jag ser inte medlemskaps ändringar direkt när jag lägger till eller ändrar en regel, varför inte?**<br/>Dedikerad utvärdering av medlemskap görs regelbundet i en asynkron bakgrunds process. Hur lång tid processen tar bestäms av antalet användare i din katalog och storleken på den grupp som skapas som ett resultat av regeln. Kataloger med ett litet antal användare ser vanligt vis grupp medlemskapen på mindre än några minuter. Kataloger med ett stort antal användare kan ta 30 minuter eller längre att fylla i.

**Hur kan jag tvinga gruppen att bearbetas nu?**<br/>
För närvarande finns det inget sätt att automatiskt utlösa gruppen som ska bearbetas på begäran. Du kan dock aktivera ombearbetningen manuellt genom att uppdatera medlemskaps regeln för att lägga till ett blank steg i slutet.  

**Jag påträffade ett fel vid regel bearbetning**<br/>I följande tabell visas vanliga regel fel för dynamiska medlemskap och hur du korrigerar dem.

| Fel i regel parser | Fel användning | Korrigerad användning |
| --- | --- | --- |
| Fel: attributet stöds inte. |(User. invalidProperty-EQ "value") |(User. Department-EQ "värde")<br/><br/>Kontrol lera att attributet finns i [listan över egenskaper som stöds](groups-dynamic-membership.md#supported-properties). |
| Fel: operatorn stöds inte för attribut. |(User. accountEnabled-contains true) |(User. accountEnabled-EQ true)<br/><br/>Operatorn som används stöds inte för egenskaps typen (i det här exemplet kan-det går inte att använda-contains i boolesk typ). Använd rätt operatorer för egenskaps typen. |
| Fel: fel vid frågekörning. | 1. (User. Department-EQ "Sales") (User. Department-EQ "Marketing")<br>2. (User. userPrincipalName-match "* @domain.ext ") | 1. operator saknas. Use-och or-eller-eller-eller två Join-predikat<br>(User. Department-EQ "Sales")-eller (User. Department-EQ "Marketing")<br>2. fel i reguljärt uttryck som används med-match<br>(User. userPrincipalName-match ". * @domain.ext ")<br>Alternativt kan du: (User. userPrincipalName-match " @domain.ext $") |

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller ytterligare information om Azure Active Directory.

* [Hantera åtkomst till resurser med Azure Active Directory grupper](../fundamentals/active-directory-manage-groups.md)
* [Programhantering i Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Vad är Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)