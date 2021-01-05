---
title: Konfigurera grupp inställningar med PowerShell – Azure AD | Microsoft Docs
description: Hantera inställningarna för grupper med hjälp av Azure Active Directory cmdlets
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
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c48e23de6832999b262283c0bf6664b4dfe88ee7
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97881573"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-cmdletar för att konfigurera gruppinställningar

Den här artikeln innehåller instruktioner för att använda PowerShell-cmdlets för Azure Active Directory (Azure AD) för att skapa och uppdatera grupper. Det här innehållet gäller endast för Microsoft 365 grupper (ibland kallade enhetliga grupper).

> [!IMPORTANT]
> Vissa inställningar kräver en licens för Azure Active Directory Premium P1. Mer information finns i tabellen [mall inställningar](#template-settings) .

Mer information om hur du hindrar icke-administratörer från att skapa säkerhets grupper får du genom att ange `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` enligt beskrivningen i [set-MSOLCompanySettings](/powershell/module/msonline/set-msolcompanysettings).

Inställningarna för Microsoft 365 grupper konfigureras med ett inställnings objekt och ett SettingsTemplate-objekt. Inlednings vis ser du inte några inställnings objekt i katalogen, eftersom din katalog är konfigurerad med standardinställningarna. Om du vill ändra standardinställningarna måste du skapa ett nytt inställnings objekt med hjälp av en mall för inställningar. Inställningar mallar definieras av Microsoft. Det finns flera olika inställnings mallar. Om du vill konfigurera Microsoft 365 grupp inställningar för din katalog använder du mallen "Group. Unified". Om du vill konfigurera Microsoft 365 grupp inställningar på en enskild grupp använder du mallen "Group. Unified. Guest". Den här mallen används för att hantera gäst åtkomst till en Microsoft 365s grupp. 

Cmdletarna är en del av modulen Azure Active Directory PowerShell V2. Instruktioner för hur du hämtar och installerar modulen på datorn finns i artikeln [Azure Active Directory PowerShell version 2](/powershell/azure/active-directory/overview). Du kan installera version 2 av modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-powershell-cmdlets"></a>Installera PowerShell-cmdletar

Se till att avinstallera en äldre version av Azure Active Directory PowerShell för Graph-modulen för Windows PowerShell och installera [Azure Active Directory PowerShell för för hands versionen av Graph-offentlig (senare än 2.0.0.137)](https://www.powershellgallery.com/packages/AzureADPreview) innan du kör PowerShell-kommandona.

1. Öppna Windows PowerShell-appen som administratör.
2. Avinstallera en eventuell tidigare version av AzureADPreview.
  
   ``` PowerShell
   Uninstall-Module AzureADPreview
   Uninstall-Module azuread
   ```

3. Installera den senaste versionen av AzureADPreview.
  
   ``` PowerShell
   Install-Module AzureADPreview
   ```
   
## <a name="create-settings-at-the-directory-level"></a>Skapa inställningar på katalog nivå
De här stegen skapar inställningar på katalog nivå, som gäller för alla Microsoft 365 grupper i katalogen. Get-AzureADDirectorySettingTemplate-cmdleten är endast tillgänglig i [för hands versionen av Azure AD PowerShell för Graph](https://www.powershellgallery.com/packages/AzureADPreview).

1. I DirectorySettings-cmdletar måste du ange ID: t för den SettingsTemplate som du vill använda. Om du inte känner till det här ID: t returnerar denna cmdlet listan över alla mallar för inställningar:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate

   ```
   Detta cmdlet-anrop returnerar alla tillgängliga mallar:
  
   ``` PowerShell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Microsoft 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Om du vill lägga till en URL för användnings rikt linjer måste du först hämta SettingsTemplate-objektet som definierar URL-värdet för användnings rikt linjer; det vill säga mallen Group. Unified:
  
   ```powershell
   $TemplateId = (Get-AzureADDirectorySettingTemplate | where { $_.DisplayName -eq "Group.Unified" }).Id
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value $TemplateId -EQ
   ```
3. Skapa sedan ett nytt inställnings objekt baserat på mallen:
  
   ```powershell
   $Setting = $Template.CreateDirectorySetting()
   ```  
4. Uppdatera sedan objektet Settings med ett nytt värde. De två exemplen nedan ändrar användnings rikt linje svärdet och aktiverar känslighets etiketter. Ange dessa eller andra inställningar i mallen efter behov:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   $Setting["EnableMIPLabels"] = "True"
   ```  
5. Använd sedan inställningen:
  
   ```powershell
   New-AzureADDirectorySetting -DirectorySetting $Setting
   ```
6. Du kan läsa värdena med:

   ```powershell
   $Setting.Values
   ```
   
## <a name="update-settings-at-the-directory-level"></a>Uppdatera inställningar på katalog nivå
Om du vill uppdatera värdet för UsageGuideLinesUrl i inställnings mal len läser du de aktuella inställningarna från Azure AD, annars kan vi behöva skriva över befintliga inställningar förutom UsageGuideLinesUrl.

1. Hämta de aktuella inställningarna från gruppen. Unified SettingsTemplate:
   
   ```powershell
   $Setting = Get-AzureADDirectorySetting | ? { $_.DisplayName -eq "Group.Unified"}
   ```  
2. Kontrol lera de aktuella inställningarna:
   
   ```powershell
   $Setting.Values
   ```
   
   Utdata:
   ```powershell
    Name                          Value
    ----                          -----
    EnableMIPLabels               True
    CustomBlockedWordsList
    EnableMSStandardBlockedWords  False
    ClassificationDescriptions
    DefaultClassification
    PrefixSuffixNamingRequirement
    AllowGuestsToBeGroupOwner     False
    AllowGuestsToAccessGroups     True
    GuestUsageGuidelinesUrl
    GroupCreationAllowedGroupId
    AllowToAddGuests              True
    UsageGuidelinesUrl            https://guideline.example.com
    ClassificationList
    EnableGroupCreation           True
    ```
3. Om du vill ta bort värdet för UsageGuideLinesUrl redigerar du URL: en som en tom sträng:
   
   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
4. Spara uppdatering till katalogen:
   
   ```powershell
   Set-AzureADDirectorySetting -Id $Setting.Id -DirectorySetting $Setting
   ```  

## <a name="template-settings"></a>Mal lin ställningar
Här är inställningarna som definierats i gruppen. Unified SettingsTemplate. Om inget annat anges kräver dessa funktioner en licens för Azure Active Directory Premium P1. 

| **Inställning** | **Beskrivning** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Typ: boolesk<li>Standard: sant |Flaggan anger om Microsoft 365 grupp skapas tillåts i katalogen av användare som inte är administratörer. Den här inställningen kräver inte en licens för Azure Active Directory Premium P1.|
|  <ul><li>GroupCreationAllowedGroupId<li>Typ: Sträng<li>Standard: "" |GUID för den säkerhets grupp för vilken medlemmarna får skapa Microsoft 365 grupper även när EnableGroupCreation = = false. |
|  <ul><li>UsageGuidelinesUrl<li>Typ: Sträng<li>Standard: "" |En länk till rikt linjerna för grupp användning. |
|  <ul><li>ClassificationDescriptions<li>Typ: Sträng<li>Standard: "" | En kommaavgränsad lista över klassificerings beskrivningar. Värdet för ClassificationDescriptions är endast giltigt i det här formatet:<br>$setting ["ClassificationDescriptions"] = "klassificering: Beskrivning, klassificering: Beskrivning"<br>där klassificeringen matchar en post i ClassificationList.<br>Den här inställningen gäller inte när EnableMIPLabels = = True.|
|  <ul><li>DefaultClassification<li>Typ: Sträng<li>Standard: "" | Klassificeringen som ska användas som standard klassificering för en grupp om inget har angetts.<br>Den här inställningen gäller inte när EnableMIPLabels = = True.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Typ: Sträng<li>Standard: "" | Sträng med en maximal längd på 64 tecken som definierar den namngivnings konvention som har kon figurer ATS för Microsoft 365 grupper. Mer information finns i [framtvinga en namngivnings princip för Microsoft 365 grupper](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Typ: Sträng<li>Standard: "" | Kommaavgränsad sträng med fraser som användarna inte får använda i grupp namn eller alias. Mer information finns i [framtvinga en namngivnings princip för Microsoft 365 grupper](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Typ: boolesk<li>Standard: "falskt" | Använd inte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Typ: boolesk<li>Standard: falskt | Booleskt värde som anger om en gäst användare kan vara ägare till grupper. |
|  <ul><li>AllowGuestsToAccessGroups<li>Typ: boolesk<li>Standard: sant | Booleskt värde som anger om en gäst användare kan ha åtkomst till Microsoft 365 grupp innehåll.  Den här inställningen kräver inte en licens för Azure Active Directory Premium P1.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Typ: Sträng<li>Standard: "" | URL-adressen till en länk till gäst användnings rikt linjerna. |
|  <ul><li>AllowToAddGuests<li>Typ: boolesk<li>Standard: sant | Ett booleskt värde som anger om gäster ska läggas till i katalogen eller inte. <br>Den här inställningen kan åsidosättas och bli skrivskyddad om *EnableMIPLabels* har värdet *True* och en gäst policy är associerad med den känslighets etikett som tilldelats gruppen.<br>Om inställningen AllowToAddGuests är inställd på falskt på organisations nivå ignoreras alla AllowToAddGuests-inställningar på grupp nivån. Om du vill aktivera gäst åtkomst endast för några få grupper måste du ange AllowToAddGuests till "på organisations nivå" och sedan inaktivera det för vissa grupper. |
|  <ul><li>ClassificationList<li>Typ: Sträng<li>Standard: "" | En kommaavgränsad lista över giltiga klassificerings värden som kan tillämpas på Microsoft 365 grupper. <br>Den här inställningen gäller inte när EnableMIPLabels = = True.|
|  <ul><li>EnableMIPLabels<li>Typ: boolesk<li>Standard: "falskt" |Flaggan som anger om känslighets etiketter som publiceras i Microsoft 365 Compliance Center kan användas för Microsoft 365 grupper. Mer information finns i [tilldela känslighets etiketter för Microsoft 365 grupper](groups-assign-sensitivity-labels.md). |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exempel: Konfigurera gäst princip för grupper på katalog nivå
1. Hämta alla mallar för inställningar:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Om du vill ange gäst policy för grupper på katalog nivå behöver du Group. Unified Template
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "62375ab9-6b52-47ed-826b-58e47e0e304b" -EQ
   ```
3. Skapa sedan ett nytt inställnings objekt baserat på mallen:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Uppdatera AllowToAddGuests-inställningen
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Använd sedan inställningen:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Du kan läsa värdena med:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Läsa inställningar på katalog nivå

Om du känner till namnet på den inställning som du vill hämta kan du använda nedanstående cmdlet för att hämta värdet för aktuella inställningar. I det här exemplet hämtar vi värdet för en inställning med namnet "UsageGuidelinesUrl". 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
De här stegen läser inställningar på katalog nivå, som gäller för alla Office-grupper i katalogen.

1. Läs alla befintliga katalog inställningar:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Denna cmdlet returnerar en lista över alla katalog inställningar:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Läs alla inställningar för en speciell grupp:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Läsa alla katalog inställnings värden för ett bestämt katalog inställnings objekt med hjälp av inställningar ID GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Den här cmdleten returnerar namn och värden i detta inställnings objekt för den här gruppen:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Ta bort inställningar på katalog nivå
Det här steget tar bort inställningar på katalog nivå, som gäller för alla Office-grupper i katalogen.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Skapa inställningar för en speciell grupp

1. Sök efter inställnings mal len "Groups. Unified. Guest"
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Microsoft 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Hämta mallobjektet för mallen Groups. Unified. Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate | where -Property Id -Value "08d542b9-071f-4e16-94b0-74abb372e3d9" -EQ
   ```
3. Skapa ett nytt inställnings objekt från mallen:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ställ in inställningen på det obligatoriska värdet:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Hämta ID: t för den grupp som du vill använda den här inställningen för:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Skapa den nya inställningen för den obligatoriska gruppen i katalogen:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Verifiera inställningarna genom att köra det här kommandot:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Uppdatera inställningar för en speciell grupp
1. Hämta ID för den grupp vars inställning du vill uppdatera:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Hämta inställningen för gruppen:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Uppdatera inställningen för gruppen efter behov, t. ex.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Hämta sedan ID: t för inställningen för den här gruppen:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Du får ett svar som liknar detta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Sedan kan du ange det nya värdet för den här inställningen:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Du kan läsa värdet för inställningen för att kontrol lera att det har uppdaterats korrekt:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet-syntax-referens
Du kan hitta mer Azure Active Directory PowerShell-dokumentation på [Azure Active Directory-cmdletar](/powershell/azure/active-directory/install-adv2).

## <a name="additional-reading"></a>Mer att läsa

* [Hantera åtkomst till resurser med Azure Active Directory grupper](../fundamentals/active-directory-manage-groups.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
