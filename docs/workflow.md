# Workflow Documentation

This document explains the application architecture, data flow, and development workflow for the Dasbodh API project.

## 🏗️ Application Architecture

### Overview
The Dasbodh API is built using a serverless architecture with Azure Functions, providing scalable and cost-effective access to spiritual text data.

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   HTTP Client   │───▶│  Azure Functions │───▶│   JSON Data     │
│                 │    │                  │    │                 │
│ • Web Browser   │    │ • getshlok       │    │ • all.json      │
│ • Mobile App    │    │ • getshlokby...  │    │ • chapter*.json │
│ • API Client    │    │ • getFactorial   │    │ • dasbodh.json  │
│ • cURL          │    │ • binding        │    │                 │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                │
                                ▼
                       ┌──────────────────┐
                       │  Timer Trigger   │
                       │                  │
                       │ fetchMandiData   │
                       │ (Daily at 7PM)   │
                       └──────────────────┘
```

### Components

#### 1. Azure Functions (HTTP Triggers)
- **getshlok**: Returns random shloka from the complete dataset
- **getshlokbydashak**: Filters by chapter and paragraph
- **getshlockbydashak**: Filters by chapter only  
- **getFactorial**: Demo mathematical function
- **binding**: Hello world demonstration

#### 2. Azure Functions (Timer Trigger)
- **fetchMandiData**: Scheduled function that runs daily at 7 PM to fetch market data from government API

#### 3. Data Layer
- **JSON Files**: Static data storage containing verses and metadata
- **File Structure**: Organized by chapters with aggregated collections

## 📊 Data Flow

### 1. Request Processing Flow

```
HTTP Request
    │
    ▼
┌─────────────────┐
│ Azure Functions │
│ Runtime         │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Function        │
│ Handler         │
│ (index.js)      │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Read JSON       │
│ Data Files      │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Filter/Process  │
│ Data            │
└─────────────────┘
    │
    ▼
┌─────────────────┐
│ Return JSON     │
│ Response        │
└─────────────────┘
```

### 2. Data Processing Logic

#### Random Shloka Selection
```javascript
// 1. Load complete dataset
data = JSON.parse(fs.readFileSync('data/all.json', 'utf8'))

// 2. Extract relevant fields  
limited_data = data.map(({ dashak, samas, shlok }) => ({ dashak, samas, shlok }))

// 3. Generate random index
rand_shlok = Math.floor(Math.random() * data.length)

// 4. Return selected shloka
return limited_data[rand_shlok]
```

#### Chapter-based Filtering
```javascript
// 1. Load dataset
data = JSON.parse(fs.readFileSync('data/all.json', 'utf8'))

// 2. Create filter function
function contains(value) {
    return (value.chapter == req.query.chapter) && (value.paragraph == req.query.paragraph);
}

// 3. Apply filter and map results
bychapter = data.filter(contains).map(({ dashak, samas, shlok }) => ({ dashak, samas, shlok }))
```

## 🔄 Development Workflow

### 1. Local Development Setup

```bash
# Clone repository
git clone https://github.com/lumensparkxy/dasbodh.git
cd dasbodh

# Install dependencies
npm install

# Install Azure Functions Core Tools
npm install -g azure-functions-core-tools@4

# Start local development server
npm start
# or
func start
```

### 2. Project Structure

```
dasbodh/
├── .github/                 # GitHub Actions workflows
├── .vscode/                # VS Code configuration
├── binding/                # Hello world function
│   ├── function.json      # Function configuration
│   └── index.js          # Function implementation
├── data/                   # JSON data files
│   ├── all.json          # Aggregated data
│   ├── chapter1.json     # Chapter-specific data
│   └── ...               # Additional chapters
├── docs/                   # Documentation
├── fetchMandiData/        # Timer trigger function
├── getFactorial/          # Demo function
├── getshlockbydashak/     # Chapter filter function
├── getshlok/              # Random shloka function
├── getshlokbydashak/      # Chapter+paragraph filter
├── scripts/               # Utility scripts
├── test/                  # Test files
├── host.json             # Azure Functions host config
├── package.json          # Node.js dependencies
└── README.md            # Project documentation
```

### 3. Function Configuration

Each function has a `function.json` configuration file:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger", 
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out", 
      "name": "res"
    }
  ]
}
```

### 4. Data Management Workflow

#### Adding New Data
1. **Source**: Obtain chapter data in JSON format
2. **Structure**: Ensure data follows the schema:
   ```json
   {
     "chapter": 1,
     "paragraph": 1, 
     "dashak": "Section name",
     "samas": "Subsection name",
     "shlok": "Verse text"
   }
   ```
3. **Integration**: Add to appropriate chapter file
4. **Aggregation**: Run merge script to update `all.json`

#### Data Merge Process
```bash
cd scripts
python merge_jsons.py
```

### 5. Testing Workflow

#### Unit Testing
```bash
npm test
```

#### Manual Testing
```bash
# Start local server
func start

# Test endpoints
curl http://localhost:7071/api/getshlok
curl "http://localhost:7071/api/getshlockbydashak?chapter=1"
```

### 6. Deployment Workflow

#### Azure Deployment
1. **Build**: Ensure all dependencies are installed
2. **Package**: Create deployment package
3. **Deploy**: Use Azure CLI or portal
4. **Configure**: Set function keys and app settings
5. **Test**: Verify all endpoints work in production

#### CI/CD Pipeline
The project can be integrated with GitHub Actions for automated deployment:

```yaml
# .github/workflows/deploy.yml
name: Deploy to Azure Functions
on:
  push:
    branches: [main]
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: npm install
      - run: npm test
      - name: Deploy to Azure Functions
        uses: Azure/functions-action@v1
```

## 🔧 Configuration Management

### Environment Variables
- `AzureWebJobsStorage`: Storage account connection string
- `FUNCTIONS_WORKER_RUNTIME`: Set to "node" 
- `WEBSITE_NODE_DEFAULT_VERSION`: Node.js version

### Function App Settings
- Authentication keys
- CORS settings
- Application Insights configuration

## 📈 Monitoring and Observability

### Application Insights
- Function execution metrics
- Performance monitoring
- Error tracking
- Custom telemetry

### Logging
```javascript
context.log('Function executed successfully');
context.log('Chapter requested:', req.query.chapter);
```

### Health Monitoring
- Azure portal monitoring
- Function execution status
- Performance metrics
- Error rates

## 🔒 Security Considerations

### Authentication
- Function keys for API access
- Host keys for administrative operations
- Azure Active Directory integration (optional)

### Data Security
- Static JSON files (no sensitive data)
- HTTPS enforcement
- CORS configuration

### Access Control
- Function-level authorization
- IP restrictions (if needed)
- Rate limiting through Azure API Management

## 🚀 Scaling and Performance

### Auto-scaling
- Azure Functions automatically scales based on load
- Consumption plan provides cost-effective scaling
- No manual intervention required

### Performance Optimization
- Minimize JSON file reads
- Use appropriate data structures
- Implement caching if needed

### Cost Optimization
- Pay-per-execution model
- Automatic scaling down during low usage
- No infrastructure management overhead

## 🛠️ Troubleshooting

### Common Issues
1. **Function key missing**: Ensure `?code=` parameter is included
2. **Data not found**: Verify JSON files are properly deployed
3. **CORS errors**: Configure CORS in Azure portal
4. **Timeout errors**: Check function execution time limits

### Debug Mode
```bash
# Enable debug logging
func start --verbose

# Check function logs
func logs
```

### Performance Issues
- Monitor Application Insights for slow requests
- Check JSON file sizes and loading times
- Review function memory usage