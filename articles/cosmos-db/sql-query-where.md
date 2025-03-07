---
title: Clausola WHERE in Azure Cosmos DB
description: Informazioni sulla clausola WHERE SQL per Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 362024868de269ed64a440a25e8c19c5b68bef80
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003467"
---
# <a name="where-clause"></a>Clausola WHERE

La clausola WHERE facoltativa`WHERE <filter_condition>`() specifica le condizioni che devono essere soddisfatte dagli elementi JSON di origine affinché la query li includa nei risultati. Un elemento JSON deve valutare le condizioni `true` specificate per essere considerato per il risultato. Il livello di indice utilizza la clausola WHERE per determinare il subset più piccolo di elementi di origine che può far parte del risultato.
  
## <a name="syntax"></a>Sintassi
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argomenti

- `<filter_condition>`  
  
   Specifica la condizione che deve essere soddisfatta per i documenti da restituire.  
  
- `<scalar_expression>`  
  
   Espressione che rappresenta il valore da calcolare. Per informazioni dettagliate, vedere [espressioni scalari](sql-query-scalar-expressions.md) .  
  

## <a name="remarks"></a>Note
  
  Affinché il documento venga restituito, un'espressione specificata come condizione di filtro deve restituire true. Solo un valore booleano true soddisferà la condizione, i valori non definiti, null, false, numero, matrice o oggetto non soddisfano la condizione. 

## <a name="examples"></a>Esempi

La query seguente richiede elementi che contengono una `id` proprietà il cui valore `AndersenFamily`è. Esclude tutti gli elementi che non dispongono di una `id` proprietà o il cui valore non corrisponde. `AndersenFamily`

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

I risultati sono:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Espressioni scalari nella clausola WHERE

Nell'esempio precedente è stata illustrata una semplice query di uguaglianza. L'API SQL supporta inoltre varie [espressioni scalari](sql-query-scalar-expressions.md). Quelle di uso più comune sono le espressioni binarie e unarie. Anche i riferimenti di proprietà dell'oggetto JSON sono espressioni valide.

È possibile utilizzare gli operatori binari supportati seguenti:  

|**Tipo di operatore**  | **Valori** |
|---------|---------|
|Aritmetico | +,-,*,/,% |
|Bit per bit    | \|, &, ^, <<, >>, >>> (spostamento a destra riempimento zero) |
|Logico    | AND, OR, NOT      |
|Confronto | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|String     |  \|\| (concatenazione) |

Nelle query seguenti vengono utilizzati operatori binari:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

È anche possibile usare gli operatori unari +,-, ~ e non nelle query, come illustrato negli esempi seguenti:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

È anche possibile usare i riferimenti alle proprietà nelle query. Ad esempio, `SELECT * FROM Families f WHERE f.isRegistered` restituisce l'elemento JSON che contiene la `isRegistered` proprietà con valore uguale `true`a. Qualsiasi altro valore, ad esempio `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`o `<array>`, esclude l'elemento dal risultato. 

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione](sql-query-getting-started.md)
- [Esempi relativi a Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Clausola FROM](sql-query-from.md)