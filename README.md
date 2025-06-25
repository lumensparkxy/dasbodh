# Dasbodh API

A comprehensive Azure Functions-based API for accessing shlokas (verses) from Dasbodh, a renowned spiritual text. This project provides multiple endpoints to retrieve verses by various criteria including random selection, chapter-based filtering, and specific verse lookup.

## 📖 About Dasbodh

Dasbodh is a 17th-century spiritual text written by Saint Ramdas in Marathi. It contains philosophical and spiritual teachings presented in the form of a dialogue between a guru and disciple. This API provides programmatic access to over 7,000 verses organized across 20 chapters.

## 🚀 Features

- **Random Verse Access**: Get a random shloka for daily inspiration
- **Chapter-based Filtering**: Retrieve verses from specific chapters
- **Paragraph-specific Lookup**: Access verses by chapter and paragraph
- **Structured Data**: Well-organized JSON responses with metadata
- **Azure Functions**: Serverless architecture for high availability
- **RESTful API**: Simple HTTP-based access

## 🏗️ Architecture

This project is built using:
- **Azure Functions** - Serverless compute platform
- **Node.js** - Runtime environment
- **JSON** - Data storage format
- **Mocha & Chai** - Testing framework

## 📊 Data Structure

Each verse contains:
- `chapter`: Chapter number (1-20)
- `paragraph`: Paragraph number within chapter
- `dashak`: Section name in Marathi
- `samas`: Subsection name in Marathi
- `shlok`: The actual verse text in Marathi

## 🔗 API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `/api/getshlok` | GET | Get a random shloka |
| `/api/getshlokbydashak` | GET | Get shloka by chapter and paragraph |
| `/api/getshlockbydashak` | GET | Get all shlokas from a specific chapter |
| `/api/getFactorial` | GET | Calculate factorial (demo endpoint) |
| `/api/binding` | GET/POST | Hello world endpoint |

## 📚 Documentation

For detailed documentation, please refer to the [docs](./docs/) folder:

- [API Documentation](./docs/api-endpoints.md) - Complete API reference
- [Workflow Guide](./docs/workflow.md) - Application architecture and workflow
- [Usage Examples](./docs/usage-examples.md) - Simple use cases and examples

## 🚦 Quick Start

### Prerequisites
- Node.js (v14 or higher)
- Azure Functions Core Tools (for local development)

### Installation
```bash
git clone https://github.com/lumensparkxy/dasbodh.git
cd dasbodh
npm install
```

### Running Locally
```bash
npm start
```

### Running Tests
```bash
npm test
```

## 🌐 Live API

The API is deployed at: `https://dasbodh.azurewebsites.net`

Example: Get a random shloka
```
GET https://dasbodh.azurewebsites.net/api/getshlok?code=YOUR_FUNCTION_KEY
```

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## 📄 License

This project is licensed under the ISC License.

## 🙏 Acknowledgments

- Original text: Dasbodh by Saint Ramdas
- Spiritual guidance and cultural preservation
- Open source community

