# MuleSoft Gemini Agentic EAPI

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![MuleSoft](https://img.shields.io/badge/MuleSoft-Mule%204.4%2B-00A0DF)](https://www.mulesoft.com)
[![Java](https://img.shields.io/badge/Java-17-orange)](https://adoptium.net)
[![Gemini](https://img.shields.io/badge/Google-Gemini%20AI-4285F4)](https://ai.google.dev)

![](https://static.scarf.sh/a.png?x-pxid=97fb6777-7e13-4701-9321-8a66692c0002)

A **MuleSoft Experience API** that integrates with Google Gemini AI for Native Checkout
Functionality. This EAPI is a direct plug-and-play app to enable UCP for any eCommerce
application, enabling agentic AI capabilities within the Anypoint Platform ecosystem.

---

## Architecture

This project follows MuleSoft's **API-led connectivity** pattern across three layers:

```
Client Request
      ↓
mulesoft-gemini-agentic-eapi          ← Experience API (this repo)
      ↓
mulesoft-gemini-agentic-papi          ← Process API (orchestration layer)
      ↓               ↓
sap-gemini-agentic-cart-sapi    sap-gemini-agentic-checkout-sapi
(Cart System API)               (Checkout System API)
      ↓               ↓
         SAP CCv2 (Merchant Commerce Engine)
```

| Layer | Repository | Role |
|-------|------------|------|
| **EAPI** | [mulesoft-gemini-agentic-eapi](https://github.com/viplgoswami1/mulesoft-gemini-agentic-eapi) | Client-facing experience, Gemini AI integration |
| **PAPI** | [mulesoft-gemini-agentic-papi](https://github.com/viplgoswami1/mulesoft-gemini-agentic-papi) | Business logic orchestration, routing |
| **Cart SAPI** | `sap-gemini-agentic-cart-sapi` *(coming soon)* | SAP cart operations |
| **Checkout SAPI** | `sap-gemini-agentic-checkout-sapi` *(coming soon)* | SAP checkout operations |
| **SAP CCv2** | SAP Commerce Cloud v2 (Merchant Commerce Engine) | Backend commerce engine |

---

## Overview

This API acts as an intelligent orchestration layer between enterprise systems and
Google's Gemini AI, enabling:

- **Agentic workflows** — AI-driven decision making within Mule flows
- **Natural language processing** — Process unstructured data with Gemini
- **UCP Native Checkout** — Enable Universal Cart Protocol for any eCommerce app
- **Enterprise integration** — Connect Gemini AI to SAP and any backend via MuleSoft

---

## Getting Started

### Prerequisites

- Anypoint Studio 7.15+
- Mule Runtime 4.4.0+
- Java 17
- Google Cloud account with Gemini API access
- Google Gemini API Key
- [mulesoft-gemini-agentic-papi](https://github.com/viplgoswami1/mulesoft-gemini-agentic-papi) running locally or deployed

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

3. Configure credentials in `src/main/resources/config.yaml`:
```yaml
gemini:
  api:
    key: "YOUR_GEMINI_API_KEY"
    url: "https://generativelanguage.googleapis.com/v1beta"
    model: "gemini-pro"
papi:
  url: "http://localhost:8082"
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
| `PAPI_URL` | URL of mulesoft-gemini-agentic-papi | ✅ |

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

### POST /cart

Create a new cart session (proxied to PAPI → Cart SAPI).

**Request:**
```json
{
  "line_items": [{ "item": { "id": "WH1000XM5-BLK" }, "quantity": 1 }],
  "context": { "address_country": "US", "address_region": "CA", "postal_code": "94509", "currency": "USD" },
  "signals": { "dev.ucp.buyer_ip": "203.0.113.42" }
}
```

### PUT /cart/{cartId}

Update an existing cart (proxied to PAPI → Cart SAPI).

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

## Related Repositories

| Repo | Description |
|------|-------------|
| [mulesoft-gemini-agentic-papi](https://github.com/viplgoswami1/mulesoft-gemini-agentic-papi) | Process API — orchestrates Cart and Checkout SAPIs |
| `sap-gemini-agentic-cart-sapi` *(coming soon)* | System API — SAP cart operations |
| `sap-gemini-agentic-checkout-sapi` *(coming soon)* | System API — SAP checkout operations |
| [google-merchant-center-connector](https://github.com/viplgoswami1/google-merchant-center-connector) | Custom MuleSoft connector for Google Shopping API |
| [Maven Central](https://central.sonatype.com/artifact/io.github.viplgoswami1/google-merchant-center-mule-connector) | Published connector |

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
