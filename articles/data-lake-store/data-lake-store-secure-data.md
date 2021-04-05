---
title: Skydda data som lagras i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig hur du skyddar data i Azure Data Lake Storage Gen1 att använda grupper och åtkomst kontrol listor
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ca35e65f-3986-4f1b-bf93-9af6066bb716
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: ac7666f4c4e68d24499f9c097dc9bd021d270355
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370703"
---
# <a name="securing-data-stored-in-azure-data-lake-storage-gen1"></a>Skydda data som lagras i Azure Data Lake Storage Gen1
Att skydda data i Azure Data Lake Storage Gen1 är en metod i tre steg.  Både Azure rollbaserad åtkomst kontroll (Azure RBAC) och åtkomst kontrol listor (ACL) måste anges för att ge åtkomst till data för användare och säkerhets grupper.

1. Börja med att skapa säkerhets grupper i Azure Active Directory (Azure AD). Dessa säkerhets grupper används för att implementera Azure-rollbaserad åtkomst kontroll (Azure RBAC) i Azure Portal. Mer information finns i [Azure RBAC](../role-based-access-control/role-assignments-portal.md).
2. Tilldela Azure AD-säkerhetsgrupper till Data Lake Storage Gen1-kontot. Detta styr åtkomsten till Data Lake Storage Gen1 kontot från portalen och hanterings åtgärderna från portalen eller API: erna.
3. Tilldela Azure AD-säkerhetsgrupper som åtkomst kontrol listor (ACL: er) i Data Lake Storage Gen1 fil system.
4. Dessutom kan du också ange ett IP-adressintervall för klienter som kan komma åt data i Data Lake Storage Gen1.

Den här artikeln innehåller anvisningar om hur du använder Azure Portal för att utföra ovanstående uppgifter. För detaljerad information om hur Data Lake Storage Gen1 implementerar säkerhet på konto-och data nivån, se [säkerhet i Azure Data Lake Storage gen1](data-lake-store-security-overview.md). Detaljerad information om hur ACL: er implementeras i Data Lake Storage Gen1 finns i [Översikt över Access Control i data Lake Storage gen1](data-lake-store-access-control.md).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett data Lake Storage gen1 konto**. Instruktioner för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage gen1](data-lake-store-get-started-portal.md)

## <a name="create-security-groups-in-azure-active-directory"></a>Skapa säkerhets grupper i Azure Active Directory
Instruktioner för hur du skapar Azure AD-säkerhetsgrupper och hur du lägger till användare i gruppen finns i [hantera säkerhets grupper i Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

> [!NOTE] 
> Du kan lägga till både användare och andra grupper i en grupp i Azure AD med hjälp av Azure Portal. Men för att lägga till ett huvud namn för tjänsten i en grupp använder du [Azure Ads PowerShell-modul](../active-directory/enterprise-users/groups-settings-v2-cmdlets.md).
> 
> ```powershell
> # Get the desired group and service principal and identify the correct object IDs
> Get-AzureADGroup -SearchString "<group name>"
> Get-AzureADServicePrincipal -SearchString "<SPI name>"
> 
> # Add the service principal to the group
> Add-AzureADGroupMember -ObjectId <Group object ID> -RefObjectId <SPI object ID>
> ```
 
## <a name="assign-users-or-security-groups-to-data-lake-storage-gen1-accounts"></a>Tilldela användare eller säkerhets grupper till Data Lake Storage Gen1 konton
När du tilldelar användare eller säkerhets grupper till Data Lake Storage Gen1 konton styr du åtkomsten till hanterings åtgärderna för kontot med hjälp av Azure Portal-och Azure Resource Manager-API: er. 

1. Öppna ett Data Lake Storage Gen1-konto. Klicka på **alla resurser** i den vänstra rutan, och klicka sedan på det konto namn som du vill tilldela en användare eller säkerhets grupp från bladet alla resurser.

2. I bladet Data Lake Storage Gen1 konto klickar du på **Access Control (IAM)**. Bladet som standard visar prenumerations ägarna som ägare.
   
    ![Tilldela en säkerhets grupp till Azure Data Lake Storage Gen1 konto](./media/data-lake-store-secure-data/adl.select.user.icon1.png "Tilldela en säkerhets grupp till Azure Data Lake Storage Gen1 konto")

3. På bladet **Access Control (IAM)** klickar du på **Lägg till** för att öppna bladet **Lägg till behörigheter** . På bladet **Lägg till behörigheter** väljer du en **roll** för användaren/gruppen. Leta upp säkerhets gruppen som du skapade tidigare i Azure Active Directory och välj den. Om du har många användare och grupper att söka från använder du rutan **Välj** text för att filtrera efter grupp namnet. 
   
    ![Lägg till en roll för användaren](./media/data-lake-store-secure-data/adl.add.user.1.png "Lägg till en roll för användaren")
   
    Rollen **ägare** och **deltagare** ger åtkomst till en mängd olika administrations funktioner på data Lake-kontot. För användare som ska interagera med data i data Lake, men ändå behöver Visa konto hanterings information, kan du lägga till dem i rollen **läsare** . De här rollernas omfattning är begränsade till de hanterings åtgärder som är relaterade till Data Lake Storage Gen1 kontot.
   
    För data åtgärder definierar enskilda fil Systems behörigheter vad användarna kan göra. En användare som har en läsar roll kan därför bara se administrativa inställningar som är associerade med kontot, men kan läsa och skriva data baserat på de behörigheter som tilldelats av fil systemet. Data Lake Storage Gen1 behörigheter för fil systemet beskrivs i [tilldela säkerhets grupp som ACL: er till fil systemet Azure Data Lake Storage gen1](#filepermissions).

    > [!IMPORTANT]
    > Endast **ägar** rollen aktiverar fil system åtkomst automatiskt. **Deltagare**, **läsare** och alla andra roller kräver ACL: er för att ge alla åtkomst nivåer till mappar och filer.  **Ägar** rollen tillhandahåller fil-och mappbehörigheter för superanvändare som inte kan åsidosättas via ACL: er. Mer information om hur Azure RBAC-principer mappar till data åtkomst finns i [Azure RBAC för konto hantering](data-lake-store-security-overview.md#azure-rbac-for-account-management).

4. Om du vill lägga till en grupp/användare som inte visas på bladet **Lägg till behörigheter** , kan du bjuda in dem genom att skriva deras e-postadress i rutan **Markera** text och sedan välja dem i listan.
   
    ![Lägg till en säkerhets grupp](./media/data-lake-store-secure-data/adl.add.user.2.png "Lägg till en säkerhets grupp")
   
5. Klicka på **Spara**. Du bör se säkerhets gruppen som lagts till enligt nedan.
   
    ![Säkerhets gruppen har lagts till](./media/data-lake-store-secure-data/adl.add.user.3.png "Säkerhets gruppen har lagts till")

6. Din användare/säkerhets grupp har nu åtkomst till Data Lake Storage Gen1 kontot. Om du vill ge åtkomst till vissa användare kan du lägga till dem i säkerhets gruppen. På samma sätt kan du ta bort dem från säkerhets gruppen om du vill återkalla åtkomsten för en användare. Du kan också tilldela flera säkerhets grupper till ett konto. 

## <a name="assign-users-or-security-groups-as-acls-to-the-data-lake-storage-gen1-file-system"></a><a name="filepermissions"></a>Tilldela användare eller säkerhets grupper som ACL: er till Data Lake Storage Gen1 fil systemet
Genom att tilldela användar-/säkerhets grupper till Data Lake Storage Gen1 fil systemet, anger du åtkomst kontroll för de data som lagras i Data Lake Storage Gen1.

1. I bladet Data Lake Storage Gen1 konto klickar du på **datautforskaren**.
   
    ![Visa data via Datautforskaren](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Visa data via Datautforskaren")
2. Klicka på den mapp som du vill konfigurera ACL: en för i bladet **datautforskaren** och klicka sedan på **åtkomst**. Om du vill tilldela ACL: er till en fil, måste du först klicka på filen för att förhandsgranska den och sedan klicka på **åtkomst** från bladet för **fil granskning** .
   
    ![Ange ACL: er på Data Lake Storage Gen1 fil system](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er på Data Lake Storage Gen1 fil system")
3. **Access** -bladet listar de ägare och tilldelade behörigheter som redan har tilldelats roten. Klicka på ikonen **Lägg** till för att lägga till ytterligare åtkomst-ACL: er.
    > [!IMPORTANT]
    > Att ange åtkomst behörigheter för en enskild fil ger inte nödvändigt vis en användare/grupp åtkomst till filen. Sökvägen till filen måste vara tillgänglig för den tilldelade användaren/gruppen. Mer information och exempel finns i [vanliga scenarier som rör behörigheter](data-lake-store-access-control.md#common-scenarios-related-to-permissions).
   
    ![Lista standard och anpassad åtkomst](./media/data-lake-store-secure-data/adl.acl.2.png "Lista standard och anpassad åtkomst")
   
   * **Ägarna** och **alla andra** ger åtkomst till UNIX-typ, där du anger läsa, skriva, köra (RWX) till tre olika användar klasser: ägare, grupp och andra.
   * **Tilldelade behörigheter** motsvarar POSIX-ACL: er som gör att du kan ange behörigheter för vissa namngivna användare eller grupper utöver filens ägare eller grupp. 
     
     Mer information finns i [HDFS ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Mer information om hur ACL: er implementeras i Data Lake Storage Gen1 finns [Access Control i data Lake Storage gen1](data-lake-store-access-control.md).
4. Klicka på ikonen **Lägg till** för att öppna bladet **tilldela behörigheter** . I det här bladet klickar du på **Välj användare eller grupp** och letar sedan efter säkerhets gruppen som du skapade tidigare i Azure Active Directory i bladet **Välj användare eller grupp** . Om du har många grupper att söka från använder du text rutan överst för att filtrera efter grupp namnet. Klicka på den grupp som du vill lägga till och klicka sedan på **Välj**.
   
    ![Lägg till en grupp](./media/data-lake-store-secure-data/adl.acl.3.png "Lägg till en grupp")
5. Klicka på **Välj behörigheter**, Välj behörigheter, om behörigheterna ska tillämpas rekursivt, och om du vill tilldela behörigheterna som åtkomst-ACL, standard-ACL eller båda. Klicka på **OK**.
   
    ![Skärm bild av bladet tilldela behörigheter med alternativet Välj behörigheter som anropas och bladet Select Permissions med alternativet OK som kallas.](./media/data-lake-store-secure-data/adl.acl.4.png "Tilldela behörigheter till grupp")
   
    Mer information om behörigheter i Data Lake Storage Gen1 och standard-/åtkomst-ACL: er finns i [Access Control i data Lake Storage gen1](data-lake-store-access-control.md).
6. När du klickar på **OK** på bladet **Välj behörigheter** visas den nyligen tillagda gruppen och associerade behörigheter i **åtkomst** bladet.
   
    ![Skärm bild av Access-bladet med alternativet för data teknik som kallas för.](./media/data-lake-store-secure-data/adl.acl.5.png "Tilldela behörigheter till grupp")
   
   > [!IMPORTANT]
   > I den aktuella versionen kan du ha upp till 28 poster under **tilldelade behörigheter**. Om du vill lägga till fler än 28 användare bör du skapa säkerhets grupper, lägga till användare i säkerhets grupper, lägga till åtkomst till dessa säkerhets grupper för det Data Lake Storage Gen1 kontot.
   > 
   > 
7. Om det behövs kan du även ändra åtkomst behörigheterna när du har lagt till gruppen. Avmarkera eller markera kryss rutan för varje behörighets typ (läsa, skriva, köra) baserat på om du vill ta bort eller tilldela den behörigheten till säkerhets gruppen. Klicka på **Spara** för att spara ändringarna eller **Ignorera** för att ångra ändringarna.

## <a name="set-ip-address-range-for-data-access"></a>Ange IP-adressintervall för data åtkomst
Med Data Lake Storage Gen1 kan du ytterligare låsa åtkomsten till ditt data lager på nätverks nivå. Du kan aktivera brand väggen, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När den är aktive rad kan endast klienter som har IP-adresser inom det definierade intervallet ansluta till butiken.

![Brand Väggs inställningar och IP-åtkomst](./media/data-lake-store-secure-data/firewall-ip-access.png "Brand Väggs inställningar och IP-adress")

## <a name="remove-security-groups-for-a-data-lake-storage-gen1-account"></a>Ta bort säkerhets grupper för ett Data Lake Storage Gen1 konto
När du tar bort säkerhets grupper från Data Lake Storage Gen1-konton ändrar du bara åtkomsten till hanterings åtgärderna för kontot med hjälp av Azure Portal-och Azure Resource Manager-API: er.  

Åtkomst till data är oförändrad och hanteras fortfarande av åtkomst-ACL: er.  Undantaget till detta är användare/grupper i rollen ägare.  Användare/grupper som tas bort från rollen ägare är inte längre superanvändare och deras åtkomst går tillbaka till åtkomst till ACL-inställningar. 

1. I bladet Data Lake Storage Gen1 konto klickar du på **Access Control (IAM)**. 
   
    ![Tilldela en säkerhets grupp till Data Lake Storage Gen1 konto](./media/data-lake-store-secure-data/adl.select.user.icon.png "Tilldela en säkerhets grupp till Data Lake Storage Gen1 konto")
2. Klicka på den eller de säkerhets grupper som du vill ta bort på bladet **Access Control (IAM)** . Klicka på **Ta bort**.
   
    ![Säkerhets gruppen har tagits bort](./media/data-lake-store-secure-data/adl.remove.group.png "Säkerhets gruppen har tagits bort")

## <a name="remove-security-group-acls-from-a-data-lake-storage-gen1-file-system"></a>Ta bort ACL: er för säkerhets grupper från ett Data Lake Storage Gen1 fil system
När du tar bort ACL: er för säkerhets grupper från ett Data Lake Storage Gen1 fil system ändrar du åtkomsten till data i Data Lake Storage Gen1-kontot.

1. I bladet Data Lake Storage Gen1 konto klickar du på **datautforskaren**.
   
    ![Skapa kataloger i Data Lake Storage Gen1 konto](./media/data-lake-store-secure-data/adl.start.data.explorer.png "Skapa kataloger i Data Lake Storage Gen1 konto")
2. Klicka på den mapp som du vill ta bort ACL: en för i bladet **datautforskaren** och klicka sedan på **åtkomst**. Om du vill ta bort ACL: er för en fil måste du först klicka på filen för att förhandsgranska den och sedan klicka på **åtkomst** från bladet för **fil granskning** . 
   
    ![Ange ACL: er på Data Lake Storage Gen1 fil system](./media/data-lake-store-secure-data/adl.acl.1.png "Ange ACL: er på Data Lake Storage Gen1 fil system")
3. I **Access** -bladet klickar du på säkerhets gruppen som du vill ta bort. Klicka på **ta bort** på bladet **åtkomst information** .
   
    ![Skärm bild av Access-bladet med alternativet för data teknik som anropas och bladet åtkomst information med alternativet ta bort som kallas för.](./media/data-lake-store-secure-data/adl.remove.acl.png "Tilldela behörigheter till grupp")

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kopiera data från Azure Storage blobbar till Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Använda Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Komma igång med Data Lake Storage Gen1 med hjälp av PowerShell](data-lake-store-get-started-powershell.md)
* [Kom igång med Data Lake Storage Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Åtkomst till diagnostikloggar för Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md)
