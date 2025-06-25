# API Endpoints Documentation

This document provides comprehensive documentation for all available API endpoints in the Dasbodh API.

## Base URL

**Production**: `https://dasbodh.azurewebsites.net`  
**Local Development**: `http://localhost:7071`

## Authentication

Most endpoints require function-level authentication. Include the function key as a query parameter:
```
?code=YOUR_FUNCTION_KEY
```

## Endpoints Overview

### 1. Get Random Shloka

**Endpoint**: `/api/getshlok`  
**Method**: `GET`  
**Authentication**: Function key required  
**Description**: Returns a random shloka from the entire collection.

#### Request
```http
GET /api/getshlok?code=YOUR_FUNCTION_KEY
```

#### Response
```json
{
  "dashak": "दशक पहिला - स्तवनांचा",
  "samas": "समास पहिला - ग्रंथारंभलक्षण", 
  "shlok": "श्रोते पुसती कोण ग्रंथ । काय बोलिलें जी येथ । श्रवण केलियानें प्राप्त । काय आहे ॥ १॥"
}
```

#### Response Fields
- `dashak`: Section name in Marathi
- `samas`: Subsection name in Marathi  
- `shlok`: The actual verse text in Marathi

---

### 2. Get Shloka by Chapter and Paragraph

**Endpoint**: `/api/getshlokbydashak`  
**Method**: `GET`  
**Authentication**: Function key required  
**Description**: Returns shlokas from a specific chapter and paragraph.

#### Request Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `chapter` | integer | Yes | Chapter number (1-20) |
| `paragraph` | integer | Yes | Paragraph number within chapter |

#### Request
```http
GET /api/getshlokbydashak?chapter=1&paragraph=1&code=YOUR_FUNCTION_KEY
```

#### Response
```json
[
  {
    "dashak": "दशक पहिला - स्तवनांचा",
    "samas": "समास पहिला - ग्रंथारंभलक्षण",
    "shlok": "श्रोते पुसती कोण ग्रंथ । काय बोलिलें जी येथ । श्रवण केलियानें प्राप्त । काय आहे ॥ १॥"
  },
  {
    "dashak": "दशक पहिला - स्तवनांचा", 
    "samas": "समास पहिला - ग्रंथारंभलक्षण",
    "shlok": "ग्रंथा नाम दासबोध । गुरुशिष्यांचा संवाद । येथ बोलिला विशद । भक्तिमार्ग ॥ २॥"
  }
]
```

#### Error Response
```json
"Please provide chapter Number AND paragraph number"
```

---

### 3. Get Shlokas by Chapter

**Endpoint**: `/api/getshlockbydashak`  
**Method**: `GET`  
**Authentication**: Function key required  
**Description**: Returns all shlokas from a specific chapter.

#### Request Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `chapter` | integer | Yes | Chapter number (1-20) |

#### Request
```http
GET /api/getshlockbydashak?chapter=1&code=YOUR_FUNCTION_KEY
```

#### Response
```json
[
  {
    "chapter": 1,
    "paragraph": 1,
    "dashak": "दशक पहिला - स्तवनांचा",
    "samas": "समास पहिला - ग्रंथारंभलक्षण",
    "shlok": "श्रोते पुसती कोण ग्रंथ । काय बोलिलें जी येथ । श्रवण केलियानें प्राप्त । काय आहे ॥ १॥"
  }
]
```

#### Error Response
```json
"Please provide Chapter Number"
```

---

### 4. Calculate Factorial (Demo)

**Endpoint**: `/api/getFactorial`  
**Method**: `GET`  
**Authentication**: Anonymous access  
**Description**: Calculates the factorial of a given number. This is a demonstration endpoint.

#### Request Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `number` | integer | Yes | Number to calculate factorial for |

#### Request
```http
GET /api/getFactorial?number=5
```

#### Response
```json
120
```

---

### 5. Hello World (Demo)

**Endpoint**: `/api/binding`  
**Method**: `GET`, `POST`  
**Authentication**: Anonymous access  
**Description**: A simple hello world endpoint for testing.

#### Request Parameters
| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `name` | string | No | Name for personalized greeting |

#### Request
```http
GET /api/binding?name=John
```

#### Response
```json
"Hello, John. This HTTP triggered function executed successfully."
```

## Error Handling

### HTTP Status Codes
- `200`: Success
- `400`: Bad Request (missing required parameters)
- `401`: Unauthorized (invalid or missing function key)
- `500`: Internal Server Error

### Common Error Responses

#### Missing Function Key
```json
{
  "error": "Unauthorized",
  "message": "Function key is required"
}
```

#### Invalid Parameters
```json
"Please provide chapter Number AND paragraph number"
```

## Rate Limiting

Azure Functions automatically handles scaling and rate limiting. For production use, consider implementing additional rate limiting based on your usage requirements.

## Data Source

The API serves data from JSON files containing:
- 20 chapters of Dasbodh
- Over 7,000 individual shlokas
- Structured metadata including dashak and samas information

## SDK and Libraries

Currently, there are no official SDKs available. The API uses standard HTTP requests and can be integrated with any programming language or HTTP client.

## Examples in Different Languages

### JavaScript (Node.js)
```javascript
const fetch = require('node-fetch');

async function getRandomShloka() {
  const response = await fetch('https://dasbodh.azurewebsites.net/api/getshlok?code=YOUR_KEY');
  const shloka = await response.json();
  console.log(shloka);
}
```

### Python
```python
import requests

def get_random_shloka():
    response = requests.get('https://dasbodh.azurewebsites.net/api/getshlok?code=YOUR_KEY')
    return response.json()
```

### cURL
```bash
curl "https://dasbodh.azurewebsites.net/api/getshlok?code=YOUR_KEY"
```