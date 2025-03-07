---
title: API REST (V4) - Python - QnA Maker
titleSuffix: Azure Cognitive Services
description: Informazioni ed esempi di codice basati su Python per iniziare rapidamente a usare l'API Traduzione testuale Microsoft in Servizi cognitivi Microsoft in Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/05/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: db04dc6369fa6f66467efa64e5787d3f5ddb0b3c
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390884"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>Come usare l'API REST QnA Maker con Python
<a name="HOLTop"></a>

Questo articolo illustra come usare l'[API QnA Maker Microsoft](../Overview/overview.md)  con Python per eseguire le operazioni seguenti.

- [Creare una nuova knowledge base.](#Create)
- [Aggiornare una knowledge base esistente.](#Update)
- [Ottenere lo stato di una richiesta per creare o aggiornare una knowledge base.](#Status)
- [Pubblicare una knowledge base esistente.](#Publish)
- [Sostituire i contenuti di una knowledge base esistente.](#Replace)
- [Scaricare il contenuto di una knowledge base.](#GetQnA)
- [Ottenere risposte a una domanda usando una knowledge base.](#GetAnswers)
- [Ottenere informazioni su una knowledge base.](#GetKB)
- [Ottenere informazioni su tutte le knowledge base appartenenti all'utente specificato.](#GetKBsByUser)
- [Eliminare una knowledge base.](#Delete)
- [Ottenere le chiavi dell'endpoint correnti.](#GetKeys)
- [Rigenerare le chiavi dell'endpoint correnti.](#PutKeys)
- [Ottenere il set corrente di variazioni delle parole senza distinzione maiuscole/minuscole.](#GetAlterations)
- [Sostituire il set corrente di variazioni delle parole senza distinzione maiuscole/minuscole.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questo codice è necessario [Python 3.x](https://www.python.org/downloads/).

È necessario avere un [account delle API Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) con l'**API QnA Maker Microsoft**. È infine necessaria una chiave di sottoscrizione a pagamento configurata nel [dashboard di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Creare la knowledge base

Il codice seguente crea una nuova knowledge base usando il metodo [Create](https://go.microsoft.com/fwlink/?linkid=2092179).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def create_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
      {
          "id": 0,
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "source": "Custom Editorial",
          "questions": [
              "How do I programmatically update my Knowledge Base?"
          ],
          "metadata": [
              {
                  "name": "category",
                  "value": "api"
              }
          ]
      }
    ],
    "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Risposta alla richiesta di creazione della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Torna all'inizio](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Aggiornare la knowledge base

Il codice seguente aggiorna una knowledge base esistente usando il metodo [Update](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    'add': {
        'qnaList': [
            {
                'id': 1,
                'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/azure-bot-service/templates/qnamaker/#navtitle',
                'source': 'Custom Editorial',
                'questions': [
                    'How can I change the default message from QnA Maker?'
                ],
                'metadata': []
            }
        ],
        'urls': [
            'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
        ]
    },
    'update': {
        'name': 'New KB Name'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Risposta alla richiesta di aggiornamento della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Torna all'inizio](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Ottenere lo stato della richiesta

È possibile chiamare il metodo [Operation](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) per controllare lo stato di una richiesta di creazione o aggiornamento di una knowledge base. Per informazioni sull'uso di questo metodo, vedere il codice di esempio per il metodo [Create](#Create) o [Update](#Update).

[Torna all'inizio](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Pubblicare una knowledge base

Il codice seguente pubblica una knowledge base esistente usando il metodo [Publish](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def publish_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = publish_kb(path, '')
print(pretty_print(result))
```

**Risposta alla richiesta di pubblicazione della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "result": "Success."
}
```

[Torna all'inizio](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Sostituire la knowledge base

Il codice seguente sostituisce il contenuto della knowledge base specificata usando il metodo [Replace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def replace_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'qnaList': [
      {
          'id': 0,
          'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update',
          'source': 'Custom Editorial',
          'questions': [
              'How do I programmatically update my Knowledge Base?'
          ],
          'metadata': [
              {
                  'name': 'category',
                  'value': 'api'
              }
          ]
      }
    ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb(path, content)
print(pretty_print(result))
```

**Risposta alla richiesta di sostituzione della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
    "result": "Success."
}
```

[Torna all'inizio](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Scaricare il contenuto di una knowledge base

Il codice seguente scarica il contenuto della knowledge base specificata usando il metodo [Download knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env)


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_qna(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_qna(path)
print(pretty_print(result))
```

**Risposta alla richiesta di download della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Torna all'inizio](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Ottenere risposte a una domanda usando una knowledge base

Il codice seguente ottiene le risposte a una domanda usando la knowledge base specificata mediante il metodo **Generate answers**.

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
1. Aggiungere il codice riportato di seguito.
1. Sostituire il valore di `host` con il nome del sito Web per la sottoscrizione di QnA Maker. Per altre informazioni, vedere [Creare un servizio QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
1. Sostituire il valore di `endpoint_key` con una chiave endpoint valida per la sottoscrizione. Si noti che è diversa dalla chiave di sottoscrizione. È possibile ottenere le chiavi dell'endpoint usando il metodo [Get endpoint keys](#GetKeys).
1. Sostituire il valore di `kb` con l'ID della knowledge base su cui si vogliono eseguire query per ottenere risposte. Tenere presente che questa knowledge base deve essere già stata pubblicata tramite il metodo [Publish](#Publish).
1. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_answers(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
    return response.read()


# Convert the request to a string.
content = json.dumps(question)
result = get_answers(method, content)
print(pretty_print(result))
```

**Risposta alla richiesta di ottenere risposte**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Torna all'inizio](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Ottenere informazioni su una knowledge base

Il codice seguente ottiene informazioni sulla knowledge base specificata usando il metodo [Get knowledge base details](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kb(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method + kb
result = get_kb(path)
print(pretty_print(result))
```

**Risposta alla richiesta di dettagli sulla knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Torna all'inizio](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Ottenere tutte le knowledge base per un utente

Il codice seguente ottiene informazioni su tutte le knowledge base di un utente specificato usando il metodo [Get knowledge bases for user](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kbs(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_kbs(path)
print(pretty_print(result))
```

**Risposta alla richiesta di ottenere le knowledge base per l'utente**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Torna all'inizio](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Eliminare una knowledge base

Il codice seguente elimina la knowledge base specificata usando il metodo [Delete knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def delete_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("DELETE", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = delete_kb(path, '')
print(pretty_print(result))
```

**Risposta alla richiesta di eliminazione della knowledge base**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "result": "Success."
}
```

[Torna all'inizio](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Ottenere le chiavi dell'endpoint

Il codice seguente ottiene le chiavi dell'endpoint correnti usando il metodo [Get endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_keys(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_keys(path)
print(pretty_print(result))
```

**Risposta alla richiesta di ottenere le chiavi dell'endpoint**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Torna all'inizio](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Aggiornare le chiavi dell'endpoint

Il codice seguente rigenera le chiavi dell'endpoint correnti usando il metodo [Refresh endpoint keys](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey"

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def refresh_keys(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + key_type
result = refresh_keys(path, '')
print(pretty_print(result))
```

**Risposta alla richiesta di aggiornamento delle chiavi dell'endpoint**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Torna all'inizio](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Ottenere le variazioni delle parole

Il codice seguente ottiene le variazioni delle parole correnti usando il metodo [Download alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_alterations(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_alterations(path)
print(pretty_print(result))
```

**Risposta alla richiesta di ottenere le variazioni delle parole**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Torna all'inizio](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Sostituire le variazioni delle parole

Il codice seguente sostituisce le variazioni delle parole correnti usando il metodo [Replace alterations](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace).

1. Creare un nuovo progetto Python nell'ambiente di sviluppo integrato preferito.
2. Aggiungere il codice riportato di seguito.
3. Sostituire il valore di `key` con una chiave di accesso valida per la sottoscrizione.
4. Eseguire il programma.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def put_alterations(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'wordAlterations': [
      {
          'alterations': [
              'botframework',
              'bot frame work'
          ]
      }
    ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations(path, content)
print(pretty_print(result))
```

**Risposta alla richiesta di sostituire le variazioni delle parole**

Viene restituita una risposta con esito positivo in formato JSON, come illustrato nell'esempio seguente: 

```json
{
  "result": "Success."
}
```

[Torna all'inizio](#HOLTop)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sull'API REST QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>Vedere anche 

[Panoramica di QnA Maker](../Overview/overview.md)
