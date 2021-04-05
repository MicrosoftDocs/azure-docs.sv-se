---
title: Använd grupp namngivnings princip i Azure Active Directory | Microsoft Docs
description: Så här konfigurerar du namngivnings princip för Microsoft 365 grupper i Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00291907ddea75ba4507779d1226502dc8343aea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100365709"
---
# <a name="enforce-a-naming-policy-on-microsoft-365-groups-in-azure-active-directory"></a>Framtvinga en namngivnings princip på Microsoft 365 grupper i Azure Active Directory

Om du vill använda konsekventa namngivnings konventioner för Microsoft 365 grupper som skapats eller redigerats av dina användare skapar du en grupp namngivnings princip för dina organisationer i Azure Active Directory (Azure AD). Du kan till exempel använda namngivnings principen för att kommunicera funktionen i en grupp, medlemskap, geografisk region eller vem som skapade gruppen. Du kan också använda namngivnings principen för att hjälpa att kategorisera grupper i adress boken. Du kan använda principen för att blockera vissa ord från att användas i grupp namn och alias.

> [!IMPORTANT]
> Genom att använda namngivnings principen i Azure AD för Microsoft 365 grupper måste du ha men inte nödvändigt vis tilldela en Azure Active Directory Premium P1-licens eller Azure AD Basic EDU-licens för varje unik användare som är medlem i en eller flera Microsoft 365 grupper.

Namngivnings principen används för att skapa eller redigera grupper som skapats mellan arbets belastningar (till exempel Outlook, Microsoft Teams, SharePoint, Exchange eller Planner). Den används både för grupp namn och grupp-alias. Om du konfigurerar din namngivnings princip i Azure AD och du har en befintlig Exchange-grupp namngivnings princip tillämpas Azure AD-namngivnings principen i din organisation.

När en grupp namngivnings princip har kon figurer ATS tillämpas principen på nya Microsoft 365 grupper som skapats av slutanvändare. Namngivnings principen gäller inte för vissa katalog roller, t. ex. global administratör eller användar administratör (se nedan för en fullständig lista över roller som undantas från grupp namngivnings princip). För befintliga Microsoft 365s grupper tillämpas principen inte omedelbart vid tidpunkten för konfigurationen. När grupp ägaren redigerar grupp namnet för dessa grupper kommer namngivnings principen att tillämpas.

## <a name="naming-policy-features"></a>Funktioner i namngivnings princip

Du kan tillämpa namngivnings princip för grupper på två olika sätt:

- **Prefix-namngivnings princip för suffix** Du kan definiera prefix eller suffix som sedan läggs till automatiskt för att genomdriva en namngivnings konvention i dina grupper (till exempel i grupp namnet "GRP \_ Japan \_ My Group \_ Engineering", GRP \_ Japan \_ är prefixet och \_ teknik är suffixet). 

- **Anpassade blockerade ord** Du kan ladda upp en uppsättning blockerade ord som är särskilt för din organisation att blockeras i grupper som skapats av användare (till exempel "VD, löner, HR").

### <a name="prefix-suffix-naming-policy"></a>Prefix-namngivnings princip för suffix

Den allmänna strukturen i namngivnings konventionen är prefix [GroupName] suffix. Du kan definiera flera prefix och suffix, men du kan bara ha en instans av [GroupName] i inställningen. Prefixen eller suffixen kan vara antingen fasta strängar eller användarattribut, till exempel \[ avdelning \] som ersätts av den användare som skapar gruppen. Det totala antalet tecken som tillåts för prefix-och suffix-strängar, inklusive grupp namn är 53 tecken. 

Prefix och suffix kan innehålla specialtecken som stöds i grupp namn och grupp Ali Aset. Alla tecken i prefixet eller suffixet som inte stöds i grupp Ali Aset används fortfarande i grupp namnet, men tas bort från grupp Ali Aset. På grund av den här begränsningen kan prefix och suffix som tillämpas på grupp namnet skilja sig från de som tillämpas på grupp Ali Aset. 

#### <a name="fixed-strings"></a>Fasta strängar

Du kan använda strängar för att göra det enklare att söka igenom och särskilja grupper i den globala adress listan och i de vänstra navigerings länkarna i grupp arbets belastningar. Några av de vanliga prefixen är nyckelord som "GRP \_ Name", " \# Name", " \_ Name"

#### <a name="user-attributes"></a>Användarattribut

Du kan använda attribut som kan hjälpa dig och dina användare att identifiera vilken avdelning, vilket kontor eller vilken region som gruppen skapades för. Om du till exempel definierar din namngivnings princip som `PrefixSuffixNamingRequirement = "GRP [GroupName] [Department]"` och `User’s department = Engineering` , kan ett framtvingat grupp namn vara "GRP My Group Engineering". Azure AD-attribut som stöds är \[ avdelning \] , \[ företag \] , \[ kontor \] , \[ StateOrProvince \] , \[ CountryorRegion \] , \[ title \] . Användarattribut som inte stöds behandlas som fasta strängar. till exempel " \[ post nummer \] ". Attribut för tillägg och anpassade attribut stöds inte.

Vi rekommenderar att du använder attribut som har ifyllda värden för alla användare i din organisation och inte använder attribut som har långa värden.

### <a name="custom-blocked-words"></a>Anpassade blockerade ord

En blockerad ord lista är en kommaavgränsad lista med fraser som ska blockeras i grupp namn och alias. Ingen under Strängs sökning utförs. En exakt matchning mellan grupp namnet och ett eller flera av de anpassade blockerade orden krävs för att utlösa ett fel. Under Strängs sökning utförs inte så att användarna kan använda vanliga ord som "Class" även om "lass" är ett blockerat ord.

Blockerade ord List regler:

- Blockerade ord är inte Skift läges känsliga.
- När en användare anger ett blockerat ord som en del av ett grupp namn visas ett fel meddelande med det blockerade ordet.
- Det finns inga begränsningar för tecknen på blockerade ord.
- Det finns en övre gräns på 5000 fraser som kan konfigureras i listan blockerade ord. 

### <a name="roles-and-permissions"></a>Roller och behörigheter

Om du vill konfigurera en namngivnings princip krävs en av följande roller:
- Global administratör
- Grupp administratör
- Katalog skrivare


De valda administratörerna kan undantas från dessa principer, i alla grupp arbets belastningar och slut punkter, så att de kan skapa grupper med hjälp av blockerade ord och med egna namngivnings konventioner. Följande är en lista över administratörs roller som är undantagna från grupp namngivnings principen.

- Global administratör
- Användaradministratör

## <a name="configure-naming-policy-in-azure-portal"></a>Konfigurera namngivnings princip i Azure Portal

1. Logga in på [administrations centret för Azure AD](https://aad.portal.azure.com) med ett grupp administratörs konto.
1. Välj **grupper** och välj sedan **namngivnings princip** för att öppna sidan namngivnings princip.

    ![Öppna sidan namngivnings princip i administrations centret](./media/groups-naming-policy/policy.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Visar eller redigerar prefix-suffixets namngivnings princip

1. På sidan **namngivnings princip** väljer du **grupp namngivnings princip**.
1. Du kan visa eller redigera nuvarande prefix eller suffix för namngivning individuellt genom att välja de attribut eller strängar som du vill tillämpa som en del av namngivnings principen.
1. Om du vill ta bort ett prefix eller suffix från listan väljer du prefixet eller suffixet och väljer sedan **ta bort**. Flera objekt kan tas bort samtidigt.
1. Spara ändringarna för den nya principen och börja gälla genom att välja **Spara**.

### <a name="edit-custom-blocked-words"></a>Redigera anpassade blockerade ord

1. På sidan **namngivnings princip** väljer du **blockerade ord**.

    ![Redigera och ladda upp blockerade ord lista för namngivnings princip](./media/groups-naming-policy/blockedwords.png)

1. Visa eller redigera den aktuella listan med anpassade blockerade ord genom att välja **Hämta**.
1. Ladda upp den nya listan med anpassade blockerade ord genom att välja fil ikonen.
1. Spara ändringarna för den nya principen och börja gälla genom att välja **Spara**.

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

Se till att avinstallera äldre versioner av Azure Active Directory PowerShell för Graph-modulen för Module for Windows PowerShell och installera [Azure Active Directory PowerShell för Graph – offentlig förhandsversion 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) innan du kör PowerShell-kommandon.

1. Öppna Windows PowerShell-appen som administratör.
2. Avinstallera en eventuell tidigare version av AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   ```

3. Installera den senaste versionen av AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```

   Om du tillfrågas om åtkomst till ett ej betrott lager, anger du **Y**. Det kan ta några minuter innan den nya modulen har installerats.

## <a name="configure-naming-policy-in-powershell"></a>Konfigurera namngivnings princip i PowerShell

1. Öppna ett Windows PowerShell-fönster på datorn. Du kan öppna den utan utökade privilegier.

1. Kör följande kommandon för att förbereda körningen av cmdletarna.
  
   ``` PowerShell
   Import-Module AzureADPreview
   Connect-AzureAD
   ```

   På sidan **Logga in på ditt konto** som öppnas anger du ditt administratörskonto och lösenord för att ansluta till tjänsten, och väljer **Logga in**.

1. Följ stegen i [Azure Active Directory-cmdletar för att konfigurera grupp inställningar](../enterprise-users/groups-settings-cmdlets.md) för att skapa grupp inställningar för den här organisationen.

### <a name="view-the-current-settings"></a>Visa de aktuella inställningarna

1. Hämta den aktuella namngivnings principen för att visa de aktuella inställningarna.
  
   ``` PowerShell
   $Setting = Get-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id
   ```
  
1. Visa de aktuella gruppinställningarna.
  
   ``` PowerShell
   $Setting.Values
   ```
  
### <a name="set-the-naming-policy-and-custom-blocked-words"></a>Ange namngivnings princip och anpassade blockerade ord

1. Ange gruppnamnsprefix och -suffix i Azure AD PowerShell. För att funktionen ska fungera korrekt måste [GroupName] inkluderas i inställningen.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =“GRP_[GroupName]_[Department]"
   ```
  
1. Ange de anpassade blockerade ord du vill begränsa. I följande exempel visas hur du kan lägga till dina egna anpassade ord.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=“Payroll,CEO,HR"
   ```
  
1. Spara inställningarna för den nya principen för att börja gälla, till exempel i följande exempel.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
  
Klart! Du har angett en namngivnings princip och lagt till dina blockerade ord.

## <a name="export-or-import-custom-blocked-words"></a>Exportera eller importera anpassade blockerade ord

Mer information finns i artikeln Azure Active Directory- [cmdletar för att konfigurera grupp inställningar](../enterprise-users/groups-settings-cmdlets.md).

Här är ett exempel på ett PowerShell-skript för att exportera flera blockerade ord:

``` PowerShell
$Words = (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value CustomBlockedWordsList -EQ 
Add-Content "c:\work\currentblockedwordslist.txt" -Value $words.value.Split(",").Replace("`"","")  
```

Här är ett exempel på PowerShell-skript för att importera flera blockerade ord:

``` PowerShell
$BadWords = Get-Content "C:\work\currentblockedwordslist.txt"
$BadWords = [string]::join(",", $BadWords)
$Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}
if ($Settings.Count -eq 0)
    {$Template = Get-AzureADDirectorySettingTemplate | Where-Object {$_.DisplayName -eq "Group.Unified"}
    $Settings = $Template.CreateDirectorySetting()
    New-AzureADDirectorySetting -DirectorySetting $Settings
    $Settings = Get-AzureADDirectorySetting | Where-Object {$_.DisplayName -eq "Group.Unified"}}
$Settings["CustomBlockedWordsList"] = $BadWords
Set-AzureADDirectorySetting -Id $Settings.Id -DirectorySetting $Settings 
```

## <a name="remove-the-naming-policy"></a>Ta bort namngivnings principen

### <a name="remove-the-naming-policy-using-azure-portal"></a>Ta bort namngivnings principen med Azure Portal

1. På sidan **namngivnings princip** väljer du **ta bort princip**.
1. När du har bekräftat borttagningen tas namngivnings principen bort, inklusive alla namngivnings principer för prefix och alla anpassade blockerade ord.

### <a name="remove-the-naming-policy-using-azure-ad-powershell"></a>Ta bort namngivnings principen med hjälp av Azure AD PowerShell

1. Ta bort gruppnamnsprefix och -suffix i Azure AD PowerShell.
  
   ``` PowerShell
   $Setting["PrefixSuffixNamingRequirement"] =""
   ```
  
1. Ta bort de anpassade spärrade orden.
  
   ``` PowerShell
   $Setting["CustomBlockedWordsList"]=""
   ```
  
1. Spara inställningarna.
  
   ``` PowerShell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```

## <a name="experience-across-microsoft-365-apps"></a>Erfarenheter mellan Microsoft 365-appar

När du har angett en grupp namngivnings princip i Azure AD, visas följande när en användare skapar en grupp i en Microsoft 365-app:

- En förhands granskning av namnet enligt din namngivnings princip (med prefix och suffix) så snart som användaren skriver i grupp namnet
- Om användaren anger blockerade ord visas ett fel meddelande så att de kan ta bort de blockerade orden.

Arbetsbelastning | Efterlevnad
----------- | -------------------------------
Azure Active Directory portaler | Azure AD-portalen och åtkomst panelens Portal visar namngivnings principens tvingande namn när användaren skriver i ett grupp namn när de skapar eller redigerar en grupp. När en användare anger ett anpassat blockerat ord visas ett fel meddelande med det blockerade ordet så att användaren kan ta bort det.
Outlook Web Access (OWA) | Outlook Web Access visar namnet på den namngivnings princip som tillämpas när användaren skriver ett grupp namn eller gruppalias. När en användare anger ett anpassat blockerat ord visas ett fel meddelande i användar gränssnittet tillsammans med det blockerade ordet så att användaren kan ta bort det.
Outlook-skrivbordet | Grupper som skapats i Outlook Desktop är kompatibla med namngivnings princip inställningarna. Outlook Desktop-appen visar inte för hands versionen av namnet på den tvingade gruppen och returnerar inte de anpassade blockerade Word-felen när användaren anger grupp namnet. Namngivnings principen tillämpas dock automatiskt när du skapar eller redigerar en grupp, och användarna ser fel meddelanden om det finns anpassade blockerade ord i grupp namnet eller aliaset.
Microsoft Teams | Microsoft Teams visar namnet på den grupp namngivnings princip som tillämpas när användaren anger ett grupp namn. När en användare anger ett anpassat blockerat ord visas ett fel meddelande tillsammans med det blockerade ordet så att användaren kan ta bort det.
SharePoint  |  SharePoint visar namnet på den namngivnings princip som tillämpas när användaren skriver ett plats namn eller en grupps e-postadress. När en användare anger ett anpassat blockerat ord visas ett fel meddelande, tillsammans med det blockerade ordet så att användaren kan ta bort det.
Microsoft Stream | Microsoft Stream visar namnet på den grupp namngivnings princip som tillämpas när användaren skriver ett grupp namn eller ett e-postalias för grupp. När en användare anger ett anpassat blockerat ord visas ett fel meddelande med det blockerade ordet så att användaren kan ta bort det.
Outlook iOS-och Android-app | Grupper som skapats i Outlook-appar är kompatibla med den konfigurerade namngivnings principen. Outlook Mobile-appen visar inte förhands granskningen av det tvingade namnet för namngivnings principen och returnerar inte anpassade blockerade Word-fel när användaren anger grupp namnet. Namngivnings principen tillämpas dock automatiskt när du klickar på Skapa/redigera och användare ser fel meddelanden om det finns anpassade blockerade ord i grupp namnet eller aliaset.
Gruppera mobilapp | Grupper som skapats i gruppmobilapp är kompatibla med namngivnings principen. Grupper mobilappar visar inte förhands granskningen av namngivnings principen och returnerar inte anpassade blockerade Word-fel när användaren anger grupp namnet. Men namngivnings principen tillämpas automatiskt när du skapar eller redigerar en grupp och användare visas med lämpliga fel om det finns anpassade blockerade ord i grupp namnet eller aliaset.
Planner | Planner är kompatibelt med namngivnings principen. Planner visar för hands versionen av namngivnings princip när du anger plan namnet. När en användare anger ett anpassat blockerat ord visas ett fel meddelande när du skapar planen.
Dynamics 365 för Customer Engagement | Dynamics 365 för kund engagemang är kompatibelt med namngivnings principen. Dynamics 365 visar namnet på namngivnings principen när användaren skriver ett grupp namn eller ett e-postalias för grupp. När användaren anger ett anpassat blockerat ord visas ett fel meddelande med det blockerade ordet så att användaren kan ta bort det.
SDS (School Data Sync) | Grupper som skapats via SDS följer namngivnings principen, men namngivnings principen tillämpas inte automatiskt. SDS-administratörer måste lägga till prefix och suffix till klass namn för vilka grupper måste skapas och sedan överföras till SDS. Det gick inte att skapa eller redigera grupp på annat sätt.
Klass rums app | Grupper som skapats i klass rums appen följer namngivnings principen, men namngivnings principen tillämpas inte automatiskt, och för hands versionen av namngivnings principen visas inte för användarna när du anger ett grupp namn i klass rummet. Användarna måste ange namnet på den tvingade klass rummets grupp med prefix och suffix. Annars Miss lyckas klass rums gruppen Skapa eller redigera med fel.
Power BI | Power BI-arbetsytor är kompatibla med namngivnings principen.    
Yammer | När en användare som är inloggad på Yammer med sitt Azure Active Directory konto skapar en grupp eller redigerar ett grupp namn, kommer grupp namnet att följa namngivnings principen. Detta gäller både för Microsoft 365 anslutna grupper och alla andra Yammer-grupper.<br>Om en Microsoft 365 ansluten grupp skapades innan namngivnings principen är på plats kommer grupp namnet inte automatiskt att följa namngivnings principerna. När en användare redigerar grupp namnet kommer de att uppmanas att lägga till prefixet och suffixet.
StaffHub  | StaffHub-team följer inte namngivnings principen, men den underliggande Microsoft 365s gruppen gör. StaffHub-teamets namn tillämpar inte prefix och suffix och söker inte efter anpassade blockerade ord. Men StaffHub tillämpar prefixen och suffixen och tar bort blockerade ord från den underliggande Microsoft 365s gruppen.
Exchange PowerShell | Exchange PowerShell-cmdletar är kompatibla med namngivnings principen. Användare får rätt fel meddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivnings principen i grupp namn och grupp Ali Aset (smek namn).
Azure Active Directory PowerShell-cmdletar | Azure Active Directory PowerShell-cmdletar är kompatibla med namngivnings principen. Användare får rätt fel meddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivnings konventionen i grupp namn och gruppalias.
Administrations Center för Exchange | Exchange administrations Center är kompatibelt med namngivnings principen. Användare får rätt fel meddelanden med föreslagna prefix och suffix och för anpassade blockerade ord om de inte följer namngivnings konventionen i grupp namn och alias.
Administrationscenter för Microsoft 365 | Microsoft 365 administrations Center är kompatibelt med namngivnings principen. När en användare skapar eller redigerar grupp namn tillämpas namngivnings principen automatiskt och användarna får rätt fel när de anger anpassade blockerade ord. Microsoft 365 administrations Center visar inte ännu en förhands granskning av namngivnings principen och returnerar inte anpassade blockerade Word-fel när användaren anger grupp namnet.

## <a name="next-steps"></a>Nästa steg

De här artiklarna innehåller ytterligare information om Azure AD-grupper.

- [Se befintliga grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Princip för förfallo datum för Microsoft 365 grupper](groups-lifecycle.md)
- [Hantera inställningar för en grupp](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Hantera medlemmar i en grupp](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Hantera medlemskap i en grupp](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Hantera dynamiska regler för användare i en grupp](groups-dynamic-membership.md)
