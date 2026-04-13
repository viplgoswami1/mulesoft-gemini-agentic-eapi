# MuleSoft Gemini Agentic EAPI

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![MuleSoft](https://img.shields.io/badge/MuleSoft-Mule%204.4%2B-00A0DF)](https://www.mulesoft.com)
[![Java](https://img.shields.io/badge/Java-17-orange)](https://adoptium.net)
[![Gemini](https://img.shields.io/badge/Google-Gemini%20AI-4285F4)](https://ai.google.dev)

![](https://static.scarf.sh/a.png?x-pxid=97fb6777-7e13-4701-9321-8a66692c0002)

A **MuleSoft Experience API** that integrates Google Gemini AI into enterprise workflows,
enabling agentic AI capabilities within the Anypoint Platform ecosystem.

---

## Overview

This API acts as an intelligent orchestration layer between enterprise systems and
Google's Gemini AI, enabling:

- **Agentic workflows** — AI-driven decision making within Mule flows
- **Natural language processing** — Process unstructured data with Gemini
- **Enterprise integration** — Connect Gemini AI to any backend system via MuleSoft

---

## Architecture

```
Client Request
      ↓
MuleSoft Gemini Agentic EAPI
      ↓
Google Gemini AI API
      ↓
Response with AI-generated insights
```

---

## Getting Started

### Prerequisites

- Anypoint Studio 7.15+
- Mule Runtime 4.4.0+
- Java 17
- Google Cloud account with Gemini API access
- Google Gemini API Key

### Installation

1. Clone the repository:
```bash
git clone https://github.com/viplgoswami1/mulesoft-gemini-agentic-eapi.git
cd mulesoft-gemini-agentic-eapi
```

2. Import into Anypoint Studio:
   - **File** → **Import** → **Anypoint Studio Project from File System**
   - Select the cloned folder
   - Click **Finish**

3. Configure your credentials in `src/main/resources/config.yaml`:
```yaml
gemini:
  api:
    key: "YOUR_GEMINI_API_KEY"
    url: "https://generativelanguage.googleapis.com/v1beta"
    model: "gemini-pro"
```

---

## Configuration

### Get a Gemini API Key

1. Go to **https://ai.google.dev**
2. Click **Get API key in Google AI Studio**
3. Create a new API key
4. Copy the key into your config

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `GEMINI_API_KEY` | Google Gemini API key | ✅ |
| `GEMINI_MODEL` | Model name (default: `gemini-pro`) | ❌ |
| `GEMINI_MAX_TOKENS` | Max response tokens (default: 1024) | ❌ |

---

## API Endpoints

### POST /gemini/generate

Generate AI content using Gemini.

**Request:**
```json
{
  "prompt": "Summarize the following product description for a Google Shopping listing",
  "context": "Your product description here...",
  "maxTokens": 500
}
```

**Response:**
```json
{
  "generatedText": "AI-generated content here...",
  "model": "gemini-pro",
  "tokenCount": 245,
  "status": "SUCCESS"
}
```

### POST /gemini/agent/execute

Execute an agentic workflow with multi-step reasoning.

**Request:**
```json
{
  "task": "Analyze product catalog and suggest optimizations",
  "data": { ... },
  "steps": ["analyze", "recommend", "format"]
}
```

---

## Example Mule Flow

```xml
<flow name="geminiAgenticFlow">
    <http:listener path="/gemini/generate" method="POST"/>
    
    <transform:message>
        <transform:set-payload><![CDATA[%dw 2.0
output application/json
---
{
    contents: [{
        parts: [{
            text: payload.prompt ++ "\n\n" ++ payload.context
        }]
    }]
}]]></transform:set-payload>
    </transform:message>
    
    <http:request 
        url="https://generativelanguage.googleapis.com/v1beta/models/gemini-pro:generateContent"
        method="POST">
        <http:headers>
            <http:header key="x-goog-api-key" value="${gemini.api.key}"/>
        </http:headers>
    </http:request>
    
    <logger message="#[payload]"/>
</flow>
```

---

## Also See

- 🔌 [Google Merchant Center MuleSoft Connector](https://github.com/viplgoswami1/google-merchant-center-connector) — Custom connector for Google Shopping API
- 📦 [Maven Central](https://central.sonatype.com/artifact/io.github.viplgoswami1/google-merchant-center-mule-connector) — Published connector

---

## Contributing

1. Fork the repository
2. Create your feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m 'feat: add my feature'`
4. Push to the branch: `git push origin feature/my-feature`
5. Open a Pull Request

---

## License

Apache License 2.0 — see [LICENSE](LICENSE) for details.

---

## Author

**Viplove Goswami**
- GitHub: [@viplgoswami1](https://github.com/viplgoswami1)
- LinkedIn: [Viplove Goswami](https://linkedin.com/in/viplovegoswami)
- Maven Central: [io.github.viplgoswami1](https://central.sonatype.com/namespace/io.github.viplgoswami1)

---

*Built with ❤️ for the MuleSoft and AI community*
