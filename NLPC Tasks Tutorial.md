## Sample Python code for 5 common NLPC Task types

While the docs are being updated etc and don't include actual screenshots of what the tasks look like, here is sample code (along with screenshots) that you can use as a quick reference.

### 1. Categorizing Non-Text (Images/Audio File/Video File/Website)

API Endpoint: Categorization

###### What an Image Categorization task looks like:

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/walmart/imagecat.png "5e61534801f97b0011277e6e")

###### What an Video Categorization task looks like:
(Works for video FILES, not Youtube urls etc - use text categorization for that)
![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/videocat.png "5e67e303b6d22f00100cd578")

###### What an Audio Categorization task looks like:

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/audiocat.png "5e67e4c5b6d22f00100cd7e3")

###### What a Website Categorization task looks like:
(Better to use text categorization and embed the website using Markdown)
![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/webcat.png "5e67ebd8e8114c0017a35bcb")

#### Code
For image categorization, although others are similar.


```python
import requests
import json

payload = {}

## The attachment will be the file url
payload['attachment'] = "https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/walmart/toy.png")

## Stays the same, doesn't really matter
payload['callback_url'] = "http://www.example.com/callback"

## This can be a string, or an iFrame string (go to google doc->File->Publish->Embed)
paylodd['instruction'] = 'Identify tags'

### Change this to URL type: "image", "audio", "video" or "website"
payload["attachment_type"] = "image"

payload['metadata'] = {
    ## Usually you'll want to attach some metadata to be able to trace it back to original etc
}

## Fields can be multiple choice or single choice.
payload["taxonomies"] = {
    "Type": {
        "type": "category",
        "description": "What kind of toy is it?",
        "choices": [
            "Soft/Stuffed",
            "Vehicle",
            "Colorful",
            "Comics",
            "Movies/TV/Comics merchandise",
            "Single player",
            "Multi player",
            "Interactive",
            "Assembly",
        ],
        "allow_multiple": True ## Allows multiple choice or single choice
    },
    
    ## This is a bad example for how to use the "number" field but...
    "Ages": {
        "type": "number",
        "description": "Which ages is the toy for?",
        "min": 0,
        "max": 100,
        "step": 1,
    }
}

headers = {"Content-Type": "application/json"}

## Submit the tasks
task_request = requests.post(
    "https://api.scale.com/v1/task/categorization",
    json=payload,
    headers=headers,
    auth=("YOUR API KEY", ""),
)
print(task_request.json())
```


```python

```

### 2. Categorizing Text

API Endpoint: Categorization

Text categorization is extremely versatile and can include images, webpages and many other things, since it supports Markdown. Essentially, most categorization tasks can be converted into a text categorization task.

What the task looks like:

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/capgroup_cat.png "5e61534801f97b0011277e6e")

The code is almost exactly the same as above, and the only changes are the `attachment` field and the `attachment_type` field.


```python
import requests
import json

payload = {}

text = "The quick brown fox jumped over the lazy dog."

## The attachment will be the text string itself.
#### If it's a long passage, add Markdown. To add line breaks to text, insert "\n\n".
payload['attachment'] = (text)
payload["attachment_type"] = "text"

## Stays the same, doesn't really matter
payload['callback_url'] = "http://www.example.com/callback"

## This can be a string, or an iFrame string (go to google doc->File->Publish->Embed)
payload['instruction'] = 'Identify labels...'

payload['metadata'] = {
    ## Usually you'll want to attach some metadata to be able to trace it back to original etc
}

## Fields can be multiple choice or n
payload["taxonomies"] = {
    "Category 1": {
        "type": "category",
        ## The description is what will SHOW on the task (see above)
        "description": "Ask a question etc",
        "choices": [
            "Choice1",
            "Choice2",
            "Choice3",
            "None"
        ],
        "allow_multiple": True,
    },
    "Category 2": {
        "type": "category",
        "description": "Ask a question",
        "choices": [
            "Choice1",
            "Choice2",
            "Choice3",
            "None"
        ],
        "allow_multiple": False,
    }
}

headers = {"Content-Type": "application/json"}

## Submit the tasks
task_request = requests.post(
    "https://api.scale.com/v1/task/categorization",
    json=payload,
    headers=headers,
    auth=("YOUR API KEY", ""),
)
print(task_request.json())
```


```python

```

### 3. Named Entity Recognition (NER)

API Endpoint: namedentityrecognition

What the task looks like:

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/NER_capgroup.png "NER")

In NER, you can't use Markdown, so all content will be shown as a large block of text with no formatting whatsoever.


```python
import requests
import json

ner_task = {}

payload['callback_url'] = "http://www.example.com/callback"

ner_task['attachment_type'] = "text"

## This is where you add the content to be labeled. Do NOT use Markdown.
ner_task['attachment'] = "String of text"

ner_task['metadata'] = {
    ## Usually you'll want to attach some metadata to be able to trace it back to original etc
}

ner_task['labels'] = [
    # For individual labels
    {'name': 'ACTUAL_NAME1', 'display_name': 'WHAT_IS_DISPLAYED1', 'description': 'DESCRIPTION'},
    
    # For categories that expand into a list of labels
    {'name': 'ACTUAL_NAME2', 'children': [
        {'name': 'CHILD_NAME1', 'display_name': 'WHAT_IS_DISPLAYED1', 'description': 'DESCRIPTION'}
    ]},
    
    # ...and so on
]

headers = {"Content-Type": "application/json"}

## Submit the tasks
task_request = requests.post(
    "https://api.scale.com/v1/task/namedentityrecognition",
    json=payload,
    headers=headers,
    auth=("YOUR API KEY", ""),
)

print(task_request.json())

```



### 4. Comparison

API Endpoint: comparison

What the task looks like (for IMAGES)

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/comparedemo.png "Comparison")


```python
payload = {}
    ## Adding task info
payload['attachments'] = ["https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/room3.png",
                          "https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/room2.png"]

payload['callback_url'] = "http://www.example.com/callback"

payload['instruction'] = 'Are these photos of the same room?'

payload["attachment_type"] = "image"

## For multiple choice questions, ask for CHOICES
payload["choices"] = ["They are the same room", 
                       "Different rooms", 
                       "Can't tell"]

## For free form text collection, create a dictionary. 
## Each key is a FIELD, and its value is the DESCRIPTION
payload["fields"] = {
    'difference': 'Difference between rooms',
    'like': 'Which room do you like more and why?'
}

headers = {"Content-Type": "application/json"}

## Submit the tasks
task_request = requests.post(
    "https://api.scale.com/v1/task/comparison",
    json=payload,
    headers=headers,
    auth=("API KEY", ""),
)
print(task_request.json())

print ("\n")
```


```python

```

### 5. OCR Transcription

API endpoint: transcription

What the task can look like (for IMAGES)

![](https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/ocrdemo.png "5e6800ac341b5b00173aa7d9")

OCR allows for single field questions and also repeatable fields. The code below includes both.


```python
payload = {}
    ## Adding task info
payload['attachment'] = "https://public-demo-data.s3.us-east-2.amazonaws.com/demos/rooms/walmart/toy.png"
payload['callback_url'] = "http://www.example.com/callback"
payload['instruction'] = 'Transcribe product name, price and available delivery options.'
payload["attachment_type"] = "image"
## For traceability

payload["fields"] = {
    'name': 'What is the full name of the product?',
    'price': 'What is the price of the product? Include "$" sign etc.'
}

payload['repeatable_fields'] = {
    'delivery': 'Delivery option'
}

headers = {"Content-Type": "application/json"}

## Submit the tasks
task_request = requests.post(
    "https://api.scale.com/v1/task/transcription",
    json=payload,
    headers=headers,
    auth=("YOUR API KEY", ""),
)
print(task_request.json())

print ("\n")
```
