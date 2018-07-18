---
title: "Lezione supplementare dell'esercitazione su Azure Analysis Services: righe di dettaglio | Microsoft Docs"
description: Descrive come creare un'espressione di righe di dettaglio nell'esercitazione su Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 626258488afec4b3c3f025ae85bd3b5866aa0cf3
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443978"
---
# <a name="supplemental-lesson---detail-rows"></a>Lezione supplementare: Righe di dettaglio

In questa lezione supplementare si userà l'Editor DAX per definire un'espressione di righe di dettaglio personalizzata. Un'espressione di righe di dettaglio è una proprietà su una misura, che offre agli utenti finali ulteriori informazioni sui risultati aggregati di una misura. 
  
Tempo previsto per il completamento della lezione: **10 minuti**  
  
## <a name="prerequisites"></a>prerequisiti  
Questa lezione supplementare fa parte di un'esercitazione sulla creazione di modelli tabulari. Prima di eseguire le attività di questa lezione supplementare, è necessario avere completato tutte le lezioni precedenti o avere a disposizione un progetto completo del modello di esempio Adventure Works Internet Sales.  
  
## <a name="whats-the-issue"></a>Qual è il problema?
Prima di aggiungere un'espressione di righe di dettaglio, verrà esaminata in dettaglio la misura InternetTotalSales.

1.  In SSDT fare clic sul menu **Modello** > **Analizza in Excel** per aprire Excel e creare una tabella pivot vuota.
  
2.  In **Campi tabella pivot** aggiungere la misura **InternetTotalSales** dalla tabella FactInternetSales in **Valori**, **CalendarYear** dalla tabella DimDate in **Colonne** e **EnglishCountryRegionName** in **Righe**. La tabella pivot offre ora i risultati aggregati dalla misura InternetTotalSales per area e anno. 

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-pivottable.png)

3. Nella tabella pivot fare doppio clic su un valore aggregato per un anno e un nome di area. Il valore per l'Australia e l'anno 2014. Viene aperto un nuovo foglio contenente alcuni dati, ma non molto utili.

    ![aas-lesson-detail-rows-pivottable](../tutorials/media/aas-lesson-detail-rows-sheet.png)
  
L'obiettivo è una tabella contenente colonne e righe dei dati che contribuiscono al risultato aggregato della misura InternetTotalSales. A tale scopo, aggiungere un'espressione di righe di dettaglio come proprietà della misura.

## <a name="add-a-detail-rows-expression"></a>Aggiungere un'espressione di righe di dettaglio

#### <a name="to-create-a-detail-rows-expression"></a>Per creare un'espressione di righe di dettaglio 
  
1. Nella griglia delle misure della tabella FactInternetSales fare clic sulla misura **InternetTotalSales**. 

2. In **Proprietà** > **Espressione righe di dettaglio** fare clic sul pulsante con i tre puntini per aprire l'Editor DAX.

    ![aas-lesson-detail-rows-ellipse](../tutorials/media/aas-lesson-detail-rows-ellipse.png)

3. Nell'Editor DAX immettere l'espressione seguente:

    ```
    SELECTCOLUMNS(
    FactInternetSales,
    "Sales Order Number", FactInternetSales[SalesOrderNumber],
    "Customer First Name", RELATED(DimCustomer[FirstName]),
    "Customer Last Name", RELATED(DimCustomer[LastName]),
    "City", RELATED(DimGeography[City]),
    "Order Date", FactInternetSales[OrderDate],
    "Internet Total Sales", [InternetTotalSales]
    )

    ```

    Questa espressione specifica che i nomi, le colonne e i risultati delle misure dalla tabella FactInternetSales e dalle tabelle correlate vengono restituiti quando un utente fa doppio clic su un risultato aggregato in una tabella pivot o un report.

4. Tornare a Excel ed eliminare il foglio creato nel passaggio 3, quindi fare doppio clic su un valore aggregato. Questa volta, dopo aver definito una proprietà di espressione di righe di dettaglio per la misura, viene aperto un nuovo foglio contenente dati molto più utili.

    ![aas-lesson-detail-rows-detailsheet](../tutorials/media/aas-lesson-detail-rows-detailsheet.png)

5. Ridistribuire il modello.

  
## <a name="see-also"></a>Vedere anche   

[SELECTCOLUMNS Function (DAX) (Funzione DAX SELECTCOLUMNS)](https://msdn.microsoft.com/library/mt761759.aspx)   
[Lezione supplementare: sicurezza dinamica](../tutorials/aas-supplemental-lesson-dynamic-security.md)   
[Lezione supplementare: Gerarchie incomplete](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)   
 