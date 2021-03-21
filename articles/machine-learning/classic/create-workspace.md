---
title: 'ML Studio (klassisk): skapa en arbets yta – Azure'
description: Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio (klassisk)-arbets yta. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520552"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Skapa och dela en Machine Learning Studio (klassisk)-arbets yta

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Om du vill använda Azure Machine Learning Studio (klassisk) måste du ha en Machine Learning Studio (klassisk)-arbets yta. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.

## <a name="create-a-studio-classic-workspace"></a>Skapa en Studio-arbetsyta (klassisk)

Om du vill öppna en arbets yta i Machine Learning Studio (klassisk) måste du vara inloggad på det Microsoft-konto som du använde för att skapa arbets ytan, eller så måste du ta emot en inbjudan från ägaren för att ansluta till arbets ytan. Från Azure Portal kan du hantera arbets ytan, vilket inkluderar möjligheten att konfigurera åtkomst.

1. Logga in på [Azure-portalen](https://portal.azure.com/)

    > [!NOTE]
    > Du måste vara administratör för Azure-prenumeration för att kunna logga in och skapa en Studio-arbetsyta (klassisk). 
    >
    > 

2. Klicka på **+ ny**

3. I rutan Sök skriver du **Machine Learning Studio (klassisk) arbets yta** och väljer det matchande objektet. Välj sedan klicka på **skapa** längst ned på sidan.

4. Ange arbets ytans information:

   - *Arbets ytans namn* får innehålla högst 260 tecken, och får inte sluta med blank steg. Namnet får inte innehålla följande tecken: `< > * % & : \ ? + /`
   - Den *webb tjänst plan* du väljer (eller skapar), tillsammans med den associerade *pris nivån* som du väljer, används om du distribuerar webb tjänster från den här arbets ytan.

     ![Skapa en ny Studio-arbetsyta (klassisk)](./media/create-workspace/create-new-workspace.png)

5. Klicka på **Skapa**.

   Machine Learning är för närvarande tillgängligt i ett begränsat antal regioner. Om din prenumeration inte innehåller någon av dessa regioner kan du se fel meddelandet "du har inga prenumerationer i de tillåtna regionerna".  Om du vill begära att en region läggs till i din prenumeration skapar du en ny Microsoft-supportbegäran från Azure Portal, väljer **fakturering** som problem typ och följer anvisningarna för att skicka in din begäran.


> [!NOTE]
> Machine Learning Studio (klassisk) förlitar sig på ett Azure Storage-konto som du anger för att spara mellanliggande data när arbets flödet körs. Om lagrings kontot tas bort när arbets ytan har skapats, eller om åtkomst nycklarna ändras, slutar arbets ytan att fungera och alla experiment på arbets ytan kommer att Miss förväntas.
Om du råkar ta bort lagrings kontot, återskapar du lagrings kontot med samma namn i samma region som det borttagna lagrings kontot och synkroniserar om åtkomst nyckeln. Om du har ändrat åtkomstnycklarna för lagringskontot synkroniserar du om åtkomstnycklarna på arbetsytan med hjälp av Azure Portal.

När arbets ytan har distribuerats kan du öppna den i Machine Learning Studio (klassisk).

1. Bläddra till Machine Learning Studio (klassisk) på [https://studio.azureml.net/](https://studio.azureml.net/) .

2. Välj din arbetsyta i det övre högra hörnet.

    ![Välj arbetsyta](./media/create-workspace/open-workspace.png)

3. Klicka på **mina experiment**.

    ![Öppna experiment](./media/create-workspace/my-experiments.png)

Information om hur du hanterar din Studio (klassisk) arbets yta finns i [hantera en Azure Machine Learning Studio (klassisk)-arbets yta](manage-workspace.md).
Om du stöter på problem med att skapa din arbets yta, se [fel söknings guide: skapa och ansluta till en Machine Learning Studio (klassisk)-arbets yta](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Dela en Azure Machine Learning Studio (klassisk)-arbets yta
När en Machine Learning Studio (klassisk)-arbets yta har skapats kan du bjuda in användare till din arbets yta att dela åtkomst till din arbets yta och alla experiment, data uppsättningar osv. Du kan lägga till användare i en av två roller:

* **Användare** – en arbets yta som användare kan skapa, öppna, ändra och ta bort experiment, data uppsättningar osv. i arbets ytan.
* **Ägare** – en ägare kan bjuda in och ta bort användare på arbets ytan, förutom vad en användare kan göra.

> [!NOTE]
> Det administratörs konto som skapar arbets ytan läggs automatiskt till i arbets ytan som ägare av arbets ytan. Andra administratörer eller användare i den prenumerationen beviljas dock inte automatiskt åtkomst till arbets ytan – du måste bjuda in dem uttryckligen.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Dela en Studio-arbetsyta (klassisk)

1. Logga in på Machine Learning Studio (klassisk) på [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Klicka på **Inställningar** i den vänstra panelen

3. Klicka på fliken **användare**

4. Klicka på **Bjud in fler användare** längst ned på sidan

    ![Inställningar för Studio](./media/create-workspace/settings.png)

5. Ange en eller flera e-postadresser. Användarna måste ha ett giltigt Microsoft-konto eller ett organisations konto (från Azure Active Directory).

6. Välj om du vill lägga till användarna som ägare eller användare.

7. Klicka på knappen **OK** .

Varje användare som du lägger till får ett e-postmeddelande med anvisningar om hur du loggar in på den delade arbets ytan.

> [!NOTE]
> För att användare ska kunna distribuera eller hantera webb tjänster på den här arbets ytan måste de vara deltagare eller administratör i Azure-prenumerationen. 

## <a name="troubleshoot-storage-accounts"></a>Felsöka lagrings konton


Den Machine Learning tjänsten behöver ett lagrings konto för att lagra data. Du kan använda ett befintligt lagrings konto, eller så kan du skapa ett nytt lagrings konto när du skapar den nya Machine Learning Studio-arbetsytan (om du har en kvot för att skapa ett nytt lagrings konto).

När den nya Machine Learning Studio-arbetsytan (klassisk) har skapats kan du logga in på Machine Learning Studio (klassisk) med hjälp av Microsoft-konto som du använde för att skapa arbets ytan. Om du stöter på fel meddelandet "arbets ytan hittades inte" (liknar följande skärm bild) kan du använda följande steg för att ta bort dina cookies från webbläsaren.

![Arbets ytan hittades inte](media/troubleshooting-creating-ml-workspace/screen3.png)

**Ta bort cookies från webbläsare**

1. Om du använder Internet Explorer klickar du på knappen **verktyg** i det övre högra hörnet och väljer **Internet alternativ**.  

   ![Internetalternativ](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Klicka på ta bort under fliken **Allmänt** **...**

   ![fliken Allmänt](media/troubleshooting-creating-ml-workspace/screen5.png)

3. I dialog rutan **ta bort webb historik** kontrollerar du att **cookies och webbplats data** är markerade och klickar på **ta bort**.

   ![Ta bort cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

När cookies har tagits bort startar du om webbläsaren och går sedan till sidan [Microsoft Azure Machine Learning Studio (klassisk)](https://studio.azureml.net) . När du uppmanas att ange ett användar namn och lösen ord anger du samma Microsoft-konto som du använde för att skapa arbets ytan.


## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar en arbets yta finns i [hantera en Azure Machine Learning Studio (klassisk)-arbets yta](manage-workspace.md).