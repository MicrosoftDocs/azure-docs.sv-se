---
title: Hantera lokala administratörer på Azure AD-anslutna enheter
description: Lär dig hur du tilldelar Azure-roller till den lokala gruppen Administratörer på en Windows-enhet.
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
ms.openlocfilehash: d482f21955b76e6b90523afe3b4933378c91d36e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107369"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>Hantera den lokala gruppen Administratörer på Azure AD-anslutna enheter

Om du vill hantera en Windows-enhet måste du vara medlem i den lokala gruppen Administratörer. Som en del av den Azure Active Directory (Azure AD) Join-processen uppdaterar Azure AD medlemskapet för den här gruppen på en enhet. Du kan anpassa medlemskaps uppdateringen så att den uppfyller dina affärs behov. En medlemskaps uppdatering är till exempel användbart om du vill att supportavdelningen ska kunna utföra uppgifter som kräver administratörs behörighet på en enhet.

I den här artikeln förklaras hur uppdateringen av lokala administratörer i medlemskap fungerar och hur du kan anpassa den under en Azure AD-anslutning. Innehållet i den här artikeln gäller inte för en **hybrid Azure AD-anslutna** enheter.

## <a name="how-it-works"></a>Så här fungerar det

När du ansluter en Windows-enhet med Azure AD med hjälp av en Azure AD-anslutning lägger Azure AD till följande säkerhets objekt i den lokala gruppen Administratörer på enheten:

- Rollen global administratör för Azure AD
- Administratörs rollen för Azure AD-enhet 
- Användaren som utför Azure AD Join   

Genom att lägga till Azure AD-roller i den lokala gruppen Administratörer kan du uppdatera användare som kan hantera en enhet när som helst i Azure AD utan att ändra något på enheten. Azure AD lägger också till rollen Azure AD-enhets administratör i den lokala gruppen Administratörer för att stödja principen om minsta behörighet (PoLP). Förutom de globala administratörerna kan du också aktivera användare som *endast* har tilldelats rollen enhets administratör för att hantera en enhet. 

## <a name="manage-the-global-administrators-role"></a>Hantera rollen som global administratör

Information om hur du visar och uppdaterar medlemskapet för den globala administratörs rollen finns i:

- [Visa alla medlemmar i en administratörs roll i Azure Active Directory](../roles/manage-roles-portal.md)
- [Tilldela administratörsroller till en användare i Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Hantera rollen som enhets administratör 

I Azure Portal kan du hantera rollen som enhets administratör på sidan **enheter** . Så här öppnar du sidan **enheter** :

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
1. Sök efter och välj *Azure Active Directory*.
1. I avsnittet **Hantera** klickar du på **enheter**.
1. På sidan **enheter** klickar du på **enhets inställningar**.

Om du vill ändra rollen som enhets administratör konfigurerar du **ytterligare lokala administratörer på Azure AD-anslutna enheter**.  

![Ytterligare lokala administratörer](./media/assign-local-admin/10.png)

>[!NOTE]
> Det här alternativet kräver en Azure AD Premium klient. 

Enhets administratörer tilldelas till alla Azure AD-anslutna enheter. Du kan inte begränsa enhets administratörer till en speciell uppsättning enheter. Uppdatering av enhets administratörs rollen behöver inte nödvändigt vis påverka de berörda användarna direkt. På enheter där en användare redan har loggat in, sker privilegie höjning när *båda* åtgärderna nedan inträffar:

- Upp till 4 timmar har passerat för Azure AD för att utfärda en ny primär uppdateringstoken med rätt behörighet. 
- Användaren loggar ut och loggar in igen, inte låsa/låsa upp, för att uppdatera profilen.

>[!NOTE]
> Ovanstående åtgärder gäller inte för användare som inte har loggat in på relevant enhet tidigare. I det här fallet tillämpas administratörs behörigheterna direkt efter deras första inloggning på enheten. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Hantera administratörs behörigheter med Azure AD-grupper (för hands version)

>[!NOTE]
> Den här funktionen finns för närvarande som en förhandsversion.


Från och med Windows 10 2004 Update kan du använda Azure AD-grupper för att hantera administratörs behörighet på Azure AD-anslutna enheter med MDM-principen för [begränsade grupper](/windows/client-management/mdm/policy-csp-restrictedgroups) . Med den här principen kan du tilldela enskilda användare eller Azure AD-grupper till den lokala gruppen Administratörer på en Azure AD-ansluten enhet, vilket ger dig granularitet för att konfigurera distinkta administratörer för olika grupper av enheter. 

>[!NOTE]
> Starta Windows 10 20H2 Update, vi rekommenderar att du använder principer för [lokala användare och grupper](/windows/client-management/mdm/policy-csp-localusersandgroups) i stället för principen begränsade grupper


För närvarande finns det inget användar gränssnitt i Intune för att hantera dessa principer och de måste konfigureras med [anpassade OMA-URI-inställningar](/mem/intune/configuration/custom-settings-windows-10). Några saker att tänka på när du använder någon av dessa principer: 

- Att lägga till Azure AD-grupper via principen kräver gruppens SID som kan hämtas genom att köra [Microsoft Graph API för grupper](/graph/api/resources/group?view=graph-rest-beta). SID definieras av egenskapen `securityIdentifier` i API-svaret.
- När principen för begränsade grupper tillämpas, tas all aktuell medlem i gruppen som inte finns med i listan medlemmar bort. Detta innebär att den här principen med nya medlemmar eller grupper tar bort befintliga administratörer, nämligen användare som anslöt till enheten, rollen enhets administratör och rollen global administratör från enheten. För att undvika att ta bort befintliga medlemmar måste du konfigurera dem som en del av listan medlemmar i principen begränsade grupper. Den här begränsningen åtgärdas om du använder principen lokala användare och grupper som tillåter stegvisa uppdateringar av grupp medlemskap
- Administratörs behörighet med båda principerna utvärderas endast för följande välkända grupper på en Windows 10-enhet – administratörer, användare, gäster, privilegierade användare, användare av fjärr skrivbord och fjärrhantering. 
- Hantering av lokala administratörer med Azure AD-grupper kan inte användas för Hybrid Azure AD-anslutna eller registrerade Azure AD-enheter.
- Medan principen för begränsade grupper fanns före Windows 10 2004-uppdateringen har den inte stöd för Azure AD-grupper som medlemmar i en enhets lokala administratörs grupp. 

## <a name="manage-regular-users"></a>Hantera vanliga användare

Som standard lägger Azure AD till användaren som utför Azure AD-anslutning till gruppen Administratörer på enheten. Om du vill förhindra att vanliga användare blir lokala administratörer har du följande alternativ:

- [Windows autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) – Windows autopilot ger dig ett alternativ för att förhindra att den primära användaren utför en anslutning från att bli lokal administratör. Du kan göra detta genom att [skapa en autopilot-profil](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile).
- [Mass registrering](/intune/windows-bulk-enroll) – en Azure AD-anslutning som utförs i kontexten för en Mass registrering sker i kontexten för en automatiskt skapad användare. Användare som loggar in när en enhet har anslutits läggs inte till i gruppen Administratörer.   

## <a name="manually-elevate-a-user-on-a-device"></a>Öka manuellt en användare på en enhet 

Förutom att använda Azure AD Join-processen kan du också manuellt öka en vanlig användare så att den blir lokal administratör på en enskild enhet. Det här steget kräver att du redan är medlem i den lokala gruppen Administratörer. 

Från och med **Windows 10 1709** -versionen kan du utföra den här åtgärden från **Inställningar-> konton – > andra användare**. Välj **Lägg till en arbets-eller skol användare**, ange användarens UPN under **användar konto** och välj *administratör* under **Kontotyp**  
 
Dessutom kan du också lägga till användare med hjälp av kommando tolken:

- Om klient användarna synkroniseras från lokala Active Directory använder du `net localgroup administrators /add "Contoso\username"` .
- Om dina klient användare har skapats i Azure AD använder du `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Överväganden 

Du kan inte tilldela grupper till enhets administratörs rollen, endast enskilda användare tillåts.

Enhets administratörer tilldelas till alla Azure AD-anslutna enheter. De kan inte begränsas till en speciell uppsättning enheter.

När du tar bort användare från enhets administratörs rollen har de fortfarande den lokala administratörs behörigheten på en enhet så länge de är inloggade på den. Privilegiet återkallas vid nästa inloggning när en ny primär uppdateringstoken utfärdas. Detta återkallning, ungefär som privilegie höjning, kan ta upp till 4 timmar.

## <a name="next-steps"></a>Nästa steg

- Om du vill ha en översikt över hantering av enheter i Azure-portalen kan du läsa om att [hantera enheter med Azure-portalen](device-management-azure-portal.md)
- Mer information om enhets villkorliga åtkomst finns i [Konfigurera Azure Active Directory enhet-baserade principer för villkorlig åtkomst](../conditional-access/require-managed-devices.md).
