---
title: Aktivera Azure Machine Learning Studio i ett virtuellt nätverk
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar Azure Machine Learning Studio för att komma åt data som lagras i ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/21/2020
ms.custom: contperf-fy20q4, tracking-python
ms.openlocfilehash: 8dc8446ecbc203622ce7c2163136c1c26aac1cc7
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032736"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>Använda Azure Machine Learning Studio i ett virtuellt Azure-nätverk

I den här artikeln får du lära dig hur du använder Azure Machine Learning Studio i ett virtuellt nätverk. Studio innehåller funktioner som AutoML, designer och data etiketter. Du måste följa stegen i den här artikeln för att kunna använda dessa funktioner i ett virtuellt nätverk.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> - Ge Studio åtkomst till data som lagras i ett virtuellt nätverk.
> - Få åtkomst till Studio från en resurs i ett virtuellt nätverk.
> - Förstå hur Studio påverkar lagrings säkerheten.

Den här artikeln är del fem i en serie med fem delar som vägleder dig genom att skydda ett Azure Machine Learning-arbetsflöde. Vi rekommenderar starkt att du läser igenom de föregående delarna för att skapa en virtuell nätverks miljö.

Se de andra artiklarna i den här serien:

[1. VNet-översikt](how-to-network-security-overview.md)  >  [2. Skydda arbets ytan](how-to-secure-workspace-vnet.md)  >  [3. Skydda inlärnings miljö](how-to-secure-training-vnet.md)  >  [4. Skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)  >  **5. Aktivera Studio-funktioner**


> [!IMPORTANT]
> Om din arbets yta är i ett __suveränt moln__, till exempel Azure Government eller Azure Kina 21Vianet, stöder integrerade notebook-datorer _inte_ användningen av lagring i ett virtuellt nätverk. Då kan du i stället använda Jupyter-notebook-filer från en beräkningsinstans. Mer information finns i avsnittet [åtkomst data i en Compute instance-anteckningsbok](how-to-secure-training-vnet.md#access-data-in-a-compute-instance-notebook) .


## <a name="prerequisites"></a>Förutsättningar

+ Läs [översikten över nätverks säkerhet](how-to-network-security-overview.md) för att förstå vanliga scenarier och arkitektur för virtuella nätverk.

+ Ett befintligt virtuellt nätverk och undernät som ska användas.

+ En befintlig [Azure Machine Learning arbets yta med privat länk aktive rad](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint).

+ Ett befintligt [Azure Storage-konto lade till det virtuella nätverket](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints).

## <a name="configure-data-access-in-the-studio"></a>Konfigurera data åtkomst i Studio

Några av Studio funktionerna är inaktiverade som standard i ett virtuellt nätverk. Om du vill aktivera dessa funktioner igen måste du aktivera hanterad identitet för lagrings konton som du tänker använda i Studio. 

Följande åtgärder är inaktiverade som standard i ett virtuellt nätverk:

* Förhandsgranska data i Studio.
* Visualisera data i designern.
* Distribuera en modell i designern ([standard lagrings konto](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Skicka ett AutoML experiment ([standard lagrings konto](#enable-managed-identity-authentication-for-default-storage-accounts)).
* Starta ett etikettande projekt.

Studio har stöd för läsning av data från följande data lager typer i ett virtuellt nätverk:

* Azure-blobb
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL Database

### <a name="configure-datastores-to-use-workspace-managed-identity"></a>Konfigurera data lager för att använda arbets ytans hanterade identiteter

När du har lagt till ett Azure Storage-konto till ditt virtuella nätverk med en [tjänst slut punkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints) eller [privat slut punkt](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-private-endpoints)måste du konfigurera ditt data lager för att använda [hanterad identitets](../active-directory/managed-identities-azure-resources/overview.md) autentisering. På så sätt kan Studio åtkomst till data i ditt lagrings konto.

Azure Machine Learning använder [data lager](concept-data.md#datastores) för att ansluta till lagrings konton. Använd följande steg för att konfigurera ett data lager för att använda hanterad identitet:

1. I Studio väljer du __data lager__.

1. Om du vill uppdatera ett befintligt data lager väljer du data lagret och väljer __uppdatera autentiseringsuppgifter__.

    Om du vill skapa ett nytt data lager väljer du __+ nytt data lager__.

1. I data lager inställningarna väljer du __Ja__ för  __Använd arbets ytans hanterade identitet för förhands granskning och profilering i Azure Machine Learning Studio__.

    ![Skärm bild som visar hur du aktiverar hanterad arbets ytans identitet](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

De här stegen lägger till den arbetsytans hanterade identiteten som en __läsare__ till lagrings tjänsten med hjälp av Azure RBAC. Med __läsar__ åtkomst kan arbets ytan Hämta brand Väggs inställningar för att säkerställa att data inte lämnar det virtuella nätverket. Det kan ta upp till 10 minuter innan ändringarna börjar gälla.

### <a name="enable-managed-identity-authentication-for-default-storage-accounts"></a>Aktivera hanterad identitets autentisering för standard lagrings konton

Varje Azure Machine Learning arbets yta levereras med två standard lagrings konton som definieras när du skapar din arbets yta. Studio använder standard lagrings kontona för att lagra experiment-och modell artefakter, som är viktiga för vissa funktioner i Studio.

I följande tabell beskrivs varför du måste aktivera hanterad identitetsautentisering för arbets ytans standard lagrings konton.

|Lagringskonto  | Kommentarer  |
|---------|---------|
|Standard-Blob Storage för arbets yta| Lagrar modell till gångar från designern. Du måste aktivera hanterad identitets autentisering på det här lagrings kontot för att distribuera modeller i designern. <br> <br> Du kan visualisera och köra en pipeline för designer om den använder ett data lager som inte är standard som har kon figurer ATS för att använda hanterad identitet. Men om du försöker distribuera en tränad modell utan hanterad identitet som är aktive rad i standard data lagret, kommer distributionen att Miss Miss läge, oavsett andra data lager som används.|
|Standard fil lager för arbets yta| Lagrar AutoML-experiment till gångar. Du måste aktivera hanterad identitets autentisering på det här lagrings kontot för att skicka AutoML experiment. |


![Skärm bild som visar var standard data lager kan hittas](./media/how-to-enable-studio-virtual-network/default-datastores.png)


### <a name="grant-workspace-managed-identity-__reader__-access-to-storage-private-link"></a>Bevilja hantering av hanterad identitets __läsare__ åtkomst till lagrings privat länk

Om ditt Azure Storage-konto använder en privat slut punkt måste du bevilja åtkomst till den privata länken till den arbets yta som hanteras av identitets **läsaren** . Mer information finns i den inbyggda rollen [läsare](../role-based-access-control/built-in-roles.md#reader) . 

Om ditt lagrings konto använder en tjänst slut punkt kan du hoppa över det här steget.

## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>Få åtkomst till Studio från en resurs i VNet

Om du ansluter till Studio från en resurs i ett virtuellt nätverk (till exempel en beräknings instans eller virtuell dator) måste du tillåta utgående trafik från det virtuella nätverket till Studio. 

Om du till exempel använder nätverks säkerhets grupper (NSG) för att begränsa utgående trafik, lägger du till en regel till ett __service tag-__ mål för __AzureFrontDoor. frontend__.

## <a name="technical-notes-for-managed-identity"></a>Tekniska kommentarer för hanterad identitet

Att använda hanterad identitet för att komma åt lagrings tjänster påverkar säkerhets överväganden. I det här avsnittet beskrivs ändringarna för varje typ av lagrings konto. 

Dessa överväganden är unika för den __typ av lagrings konto__ som du ansluter till.

### <a name="azure-blob-storage"></a>Azure Blob Storage

För __Azure Blob Storage__ läggs den arbets ytans hanterade identiteten också till som en [BLOB-datakälla](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) så att den kan läsa data från Blob Storage.

### <a name="azure-data-lake-storage-gen2-access-control"></a>Azure Data Lake Storage Gen2 åtkomst kontroll

Du kan använda både Azure RBAC-och POSIX-typ åtkomst kontrol listor (ACL: er) för att styra data åtkomsten i ett virtuellt nätverk.

Om du vill använda Azure RBAC lägger du till arbets ytans hanterade identitet i rollen [BLOB data Reader](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) . Mer information finns i [rollbaserad åtkomst kontroll i Azure](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control).

För att kunna använda ACL: er kan den arbets ytans hanterade identitet tilldelas åtkomst precis som andra säkerhets principer. Mer information finns i [åtkomst kontrol listor på filer och kataloger](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="azure-data-lake-storage-gen1-access-control"></a>Azure Data Lake Storage Gen1 åtkomst kontroll

Azure Data Lake Storage Gen1 stöder endast åtkomst kontrol listor med POSIX-typ. Du kan tilldela den arbets ytans hanterade identitets åtkomst till resurser precis som vilken annan säkerhets princip som helst. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

### <a name="azure-sql-database-contained-user"></a>Azure SQL Database innesluten användare

Om du vill komma åt data som lagras i en Azure SQL Database med hjälp av hanterad identitet måste du skapa en SQL-innesluten användare som mappar till den hanterade identiteten Mer information om hur du skapar en användare från en extern provider finns i [skapa inneslutna användare som är mappade till Azure AD-identiteter](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).

När du har skapat en SQL-innesluten användare beviljar du behörigheter till den med hjälp av [kommandot bevilja T-SQL](/sql/t-sql/statements/grant-object-permissions-transact-sql).

### <a name="azure-machine-learning-designer-intermediate-module-output"></a>Utdata från mellanliggande modul i Azure Machine Learning designer

Du kan ange platsen för utdata för alla moduler i designern. Använd det här för att lagra mellanliggande data uppsättningar på en annan plats för säkerhets-, loggnings-eller gransknings syfte. Ange utdata:

1. Välj den modul vars utdata du vill ange.
1. I fönstret Modulnamn som visas till höger väljer du **Inställningar för utdata**.
1. Ange det data lager du vill använda för varje utdata i modulen.
 
Se till att du har åtkomst till de mellanliggande lagrings kontona i ditt virtuella nätverk. Annars fungerar inte pipelinen.

Du bör också [Aktivera hanterad identitets autentisering](#configure-datastores-to-use-workspace-managed-identity) för mellanliggande lagrings konton för att visualisera utdata.

## <a name="next-steps"></a>Nästa steg

Den här artikeln är en valfri del av en serie virtuella nätverks serier i fyra delar. Se resten av artiklarna för att lära dig hur du skyddar ett virtuellt nätverk:

* [Del 1: översikt över virtuella nätverk](how-to-network-security-overview.md)
* [Del 2: skydda arbets ytans resurser](how-to-secure-workspace-vnet.md)
* [Del 3: skydda tränings miljön](how-to-secure-training-vnet.md)
* [Del 4: skydda inferencing-miljön](how-to-secure-inferencing-vnet.md)