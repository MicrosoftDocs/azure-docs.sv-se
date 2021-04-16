---
title: Hantera lokala administratörer på Azure AD-anslutna enheter
description: Lär dig hur du tilldelar Azure-roller till den lokala administratörsgruppen för en Windows-enhet.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e0f92593d185890e34a1a5120093d68cf45484
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388423"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter

Om du vill hantera en Windows-enhet måste du vara medlem i den lokala administratörsgruppen. Som en del av Azure Active Directory (Azure AD)-anslutningsprocessen uppdaterar Azure AD medlemskapet för den här gruppen på en enhet. Du kan anpassa medlemskapsuppdateringen för att uppfylla dina affärsbehov. En medlemskapsuppdatering är till exempel användbar om du vill att supportpersonalen ska kunna utföra uppgifter som kräver administratörsbehörighet på en enhet.

Den här artikeln beskriver hur medlemskapsuppdateringen för lokala administratörer fungerar och hur du kan anpassa den under en Azure AD-anslutning. Innehållet i den här artikeln gäller inte för azure **AD-anslutna hybridenheter.**

## <a name="how-it-works"></a>Så här fungerar det

När du ansluter en Windows-enhet med Azure AD med hjälp av en Azure AD-anslutning lägger Azure AD till följande säkerhetsobjekt i den lokala administratörsgruppen på enheten:

- Den globala administratörsrollen för Azure AD
- Administratörsrollen för Azure AD-enhet 
- Användaren som utför Azure AD-anslutning   

Genom att lägga till Azure AD-roller i den lokala administratörsgruppen kan du uppdatera de användare som kan hantera en enhet när som helst i Azure AD utan att ändra något på enheten. Azure AD lägger också till azure AD-enhetsadministratörsrollen i den lokala administratörsgruppen för att stödja principen om minsta behörighet (PoLP). Förutom de globala administratörerna kan du även aktivera användare som bara har *tilldelats* rollen enhetsadministratör för att hantera en enhet. 

## <a name="manage-the-global-administrators-role"></a>Hantera rollen som global administratör

Information om hur du visar och uppdaterar medlemskapet för den globala administratörsrollen finns i:

- [Visa alla medlemmar i en administratörsroll i Azure Active Directory](../roles/manage-roles-portal.md)
- [Tilldela administratörsroller till en användare i Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Hantera enhetsadministratörsrollen 

I Azure Portal kan du hantera enhetsadministratörsrollen på **sidan** Enheter. Så här öppnar **du sidan** Enheter:

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
1. Sök efter och välj *Azure Active Directory*.
1. I avsnittet **Hantera** klickar du på **Enheter**.
1. På sidan **Enheter** klickar du på **Enhetsinställningar.**

Om du vill ändra enhetsadministratörsrollen **konfigurerar du Ytterligare lokala administratörer på Azure AD-anslutna enheter.**  

![Ytterligare lokala administratörer](./media/assign-local-admin/10.png)

>[!NOTE]
> Det här alternativet kräver en Azure AD Premium klientorganisation. 

Enhetsadministratörer tilldelas till alla Azure AD-anslutna enheter. Du kan inte begränsa enhetsadministratörer till en specifik uppsättning enheter. Uppdatering av enhetsadministratörsrollen påverkar inte nödvändigtvis de berörda användarna omedelbart. På enheter där en användare redan är inloggad sker behörighetshöjningen *när båda* åtgärderna nedan inträffar:

- Upp till 4 timmar har passerat för Azure AD att utfärda en ny primär uppdateringstoken med rätt behörigheter. 
- Användaren loggar ut och loggar in igen, inte låser/låser upp, för att uppdatera sin profil.

>[!NOTE]
> Ovanstående åtgärder gäller inte för användare som inte har loggat in på relevant enhet tidigare. I det här fallet tillämpas administratörsbehörigheterna omedelbart efter den första inloggningen på enheten. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Hantera administratörsbehörigheter med hjälp av Azure AD-grupper (förhandsversion)

Från och Windows 10 version 2004 kan du använda Azure AD-grupper för att [](/windows/client-management/mdm/policy-csp-restrictedgroups) hantera administratörsbehörigheter på Azure AD-anslutna enheter med MDM-principen begränsade grupper. Med den här principen kan du tilldela enskilda användare eller Azure AD-grupper till den lokala administratörsgruppen på en Azure AD-ansluten enhet, vilket ger dig kornigheten att konfigurera distinkta administratörer för olika grupper av enheter. 

>[!NOTE]
> Från Windows 10 uppdatering 20H2 rekommenderar vi att du använder [principen Lokala användare](/windows/client-management/mdm/policy-csp-localusersandgroups) och grupper i stället för principen Begränsade grupper


För närvarande finns det inget användargränssnitt i Intune för att hantera dessa principer och de måste konfigureras med anpassade [OMA-URI-inställningar.](/mem/intune/configuration/custom-settings-windows-10) Några saker att tänka på när du använder någon av dessa principer: 

- För att lägga till Azure AD-grupper via principen krävs gruppens SID som kan hämtas genom att [köra Microsoft Graph-API:et för grupper](/graph/api/resources/group?view=graph-rest-beta). SID definieras av egenskapen i `securityIdentifier` API-svaret.
- När principen Begränsade grupper tillämpas tas alla aktuella medlemmar i gruppen som inte finns med i listan Medlemmar bort. Om du framtvingar den här principen med nya medlemmar eller grupper tas de befintliga administratörerna bort, nämligen användaren som anslöt enheten, rollen Enhetsadministratör Global administratör rollen från enheten. För att undvika att ta bort befintliga medlemmar måste du konfigurera dem som en del av listan Medlemmar i principen Begränsade grupper. Den här begränsningen åtgärdas om du använder principen Lokala användare och grupper som tillåter inkrementella uppdateringar av gruppmedlemskap
- Administratörsbehörigheter som använder båda principerna utvärderas endast för följande välkända grupper på en Windows 10-enhet – administratörer, användare, gäster, privilegierade användare, användare av fjärrskrivbord och fjärrhanteringsanvändare. 
- Hantering av lokala administratörer med hjälp av Azure AD-grupper gäller inte för Hybrid Azure AD-anslutna eller Azure AD-registrerade enheter.
- Principen begränsade grupper fanns före Windows 10 version 2004, men den hade inte stöd för Azure AD-grupper som medlemmar i en enhets lokala administratörsgrupp. 

## <a name="manage-regular-users"></a>Hantera vanliga användare

Som standard lägger Azure AD till den användare som utför Azure AD-anslutning till administratörsgruppen på enheten. Om du vill förhindra att vanliga användare blir lokala administratörer har du följande alternativ:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows Autopilot ger dig ett alternativ för att förhindra att den primära användaren som utför en koppling blir lokal administratör. Du kan åstadkomma detta genom att [skapa en Autopilot-profil](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Massregistrering](/intune/windows-bulk-enroll) – En Azure AD-koppling som utförs i kontexten för en massregistrering sker i kontexten för en automatiskt skapad användare. Användare som loggar in när en enhet har anslutits läggs inte till i administratörsgruppen.   

## <a name="manually-elevate-a-user-on-a-device"></a>Höja en användare manuellt på en enhet 

Förutom att använda Azure AD-anslutningsprocessen kan du även manuellt höja en vanlig användare så att den blir lokal administratör på en specifik enhet. Det här steget kräver att du redan är medlem i den lokala administratörsgruppen. 

Från och **med Windows 10 version 1709** kan du utföra den här uppgiften från Inställningar **-> Accounts -> Other users**. Välj **Lägg till en arbets- eller skolanvändare,** ange användarens UPN under Användarkonto **och** välj *Administratör* under **Kontotyp**  
 
Du kan också lägga till användare med hjälp av kommandotolken:

- Om dina klientanvändare synkroniseras från lokal Active Directory använder du `net localgroup administrators /add "Contoso\username"` .
- Om dina klientanvändare skapas i Azure AD använder du `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Överväganden 

Du kan inte tilldela grupper till enhetsadministratörsrollen, utan endast enskilda användare tillåts.

Enhetsadministratörer tilldelas till alla Azure AD-anslutna enheter. De kan inte vara begränsade till en specifik uppsättning enheter.

När du tar bort användare från enhetsadministratörsrollen har de fortfarande den lokala administratörsbehörigheten på en enhet så länge de är inloggade på den. Behörigheten återkallas vid nästa inloggning när en ny primär uppdateringstoken utfärdas. Återkallningen, som liknar behörighetshöjningen, kan ta upp till 4 timmar.

## <a name="next-steps"></a>Nästa steg

- Om du vill ha en översikt över hantering av enheter i Azure-portalen kan du läsa om att [hantera enheter med Azure-portalen](device-management-azure-portal.md)
- Mer information om enhetsbaserad villkorlig åtkomst finns i Konfigurera Azure Active Directory [för enhetsbaserad villkorlig åtkomst.](../conditional-access/require-managed-devices.md)
