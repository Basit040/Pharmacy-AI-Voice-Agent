# Pharmacy AI Voice Agent 💊🤖

An intelligent voice-powered pharmacy assistant that handles drug information queries, prescription orders, and order tracking through phone calls. Built with Deepgram Agent API, OpenAI, and Twilio.

![Status](https://img.shields.io/badge/status-active-success.svg)
![Python](https://img.shields.io/badge/python-3.8+-blue.svg)

## Features ✨

- 🎙️ **Voice Interaction** - Natural phone conversations with customers
- 💊 **Drug Information** - Instant access to medication details, prices, and descriptions
- 📦 **Order Placement** - Place prescription orders with voice commands
- 🔍 **Order Tracking** - Look up existing orders by ID
- ⚡ **Real-time Processing** - Low-latency voice responses
- 🛡️ **Smart Validation** - Confirms customer details before order processing
- 📞 **Twilio Integration** - Works with standard phone lines

## Architecture 🏗️

```
Phone Call (Twilio) 
    ↓
WebSocket Connection
    ↓
Deepgram Agent API
    ├── Speech-to-Text (Nova-3)
    ├── AI Processing (GPT-4o-mini)
    └── Text-to-Speech (Aura-2)
    ↓
Function Calls
    ├── get_drug_info()
    ├── place_order()
    └── lookup_order()
    ↓
Response to Customer
```

## Technologies Used 🛠️

- **Deepgram Agent API** - Voice agent orchestration, STT & TTS
- **OpenAI GPT-4o-mini** - Natural language understanding and responses
- **Twilio** - Phone call handling and streaming
- **Python WebSockets** - Real-time bidirectional communication
- **asyncio** - Asynchronous task management

## Quick Start 🚀

### Prerequisites

- Python 3.8 or higher
- Deepgram API key ([Get it here](https://deepgram.com))
- OpenAI API key ([Get it here](https://openai.com))
- Twilio account with phone number ([Get it here](https://twilio.com))

### Installation

1. **Clone the repository**
```bash
git clone <your-repo-url>
cd pharmacy-ai-agent
```

2. **Install dependencies**
```bash
pip install websockets python-dotenv
```

3. **Set up environment variables**

Create a `.env` file in the root directory:
```env
DEEPGRAM_API_KEY=your_deepgram_api_key_here
OPENAI_API_KEY=your_openai_api_key_here
```

4. **Run the server**
```bash
python main.py
```

The server will start on `localhost:5000`

### Twilio Configuration

1. **Set up ngrok** (for local development)
```bash
ngrok http 5000
```

2. **Configure Twilio Webhook**
- Go to your Twilio phone number settings
- Set the "A Call Comes In" webhook to: `wss://your-ngrok-url/`
- Select "WebSocket" as the protocol

## Project Structure 📁

```
pharmacy-ai-agent/
├── main.py                    # Main WebSocket server and orchestration
├── pharmacy_functions.py      # Business logic for drug info and orders
├── config.json               # Agent configuration (STT, LLM, TTS settings)
├── .env                      # Environment variables (API keys)
├── README.md                 # This file
└── requirements.txt          # Python dependencies
```

## Configuration ⚙️

The `config.json` file controls the agent's behavior:

### Audio Settings
- **Input/Output**: μ-law encoding at 8kHz (phone quality)

### Agent Settings
- **STT**: Deepgram Nova-3 model
- **LLM**: OpenAI GPT-4o-mini (temperature: 0.7)
- **TTS**: Deepgram Aura-2 (Thalia voice)

### Available Functions

#### 1. `get_drug_info(drug_name)`
Get detailed information about medications
```python
# Example
get_drug_info("aspirin")
# Returns: name, description, price, quantity
```

#### 2. `place_order(customer_name, drug_name)`
Place a prescription order
```python
# Example
place_order("John Smith", "metformin")
# Returns: order_id, total, confirmation message
```

#### 3. `lookup_order(order_id)`
Check order status
```python
# Example
lookup_order(123)
# Returns: customer, drug, quantity, status, total
```

## Available Medications 💊

The system includes 10 common medications:

| Drug | Price | Quantity | Use Case |
|------|-------|----------|----------|
| Aspirin | $5.99 | 30 | Pain relief, fever reduction |
| Ibuprofen | $7.99 | 20 | Anti-inflammatory |
| Acetaminophen | $6.99 | 25 | Pain and fever control |
| Metformin | $12.50 | 60 | Type 2 diabetes |
| Lisinopril | $8.75 | 30 | Hypertension |
| Atorvastatin | $15.25 | 30 | Cholesterol management |
| Omeprazole | $11.99 | 28 | Acid reflux |
| Amlodipine | $9.50 | 30 | Hypertension, angina |
| Metoprolol | $7.25 | 30 | Heart rhythm disorders |
| Sertraline | $13.75 | 30 | Depression, anxiety |

## Example Conversations 💬

### Getting Drug Information
```
Customer: "What is metformin?"
Agent: "Metformin Hydrochloride is a biguanide antidiabetic medication for type 2 diabetes management. It costs $12.50 for a 60-day supply."
```

### Placing an Order
```
Customer: "I need to order lisinopril"
Agent: "I'd be happy to help you order lisinopril. Can you please spell out your full name for me?"
Customer: "John Smith, J-O-H-N S-M-I-T-H"
Agent: "Thank you, John Smith. I'll place an order for 30 tablets of Lisinopril at $8.75. Is this correct?"
Customer: "Yes"
Agent: "Order 1 placed: 30 Lisinopril for $8.75. Your order ID is 1."
```

### Checking Order Status
```
Customer: "Can you check order number 1?"
Agent: "Order 1 is for John Smith: 30 Lisinopril tablets totaling $8.75. The status is pending."
```

## How It Works 🔧

1. **Call Initiation**: Customer calls the Twilio number
2. **WebSocket Connection**: Twilio connects to your server via WebSocket
3. **Deepgram Agent Setup**: Server initializes Deepgram Agent with config
4. **Voice Streaming**: Audio streams between Twilio ↔ Server ↔ Deepgram
5. **Speech Processing**: 
   - Deepgram converts speech to text
   - OpenAI processes the intent
   - Functions execute as needed
   - Deepgram converts response to speech
6. **Response Delivery**: Audio streams back to customer's phone

## Key Features Explained 🎯

### Barge-In Handling
The agent detects when users start speaking and clears any ongoing speech, allowing for natural interruptions.

### Function Call Validation
Before executing orders, the system:
- Verifies drug availability
- Confirms customer details
- Validates order information

### In-Memory Storage
Orders and drug data are stored in memory (resets on restart). For production, integrate a database.

## Development Tips 💡

### Adding New Medications
Edit `pharmacy_functions.py`:
```python
DRUG_DB["new_drug"] = {
    "name": "Full Drug Name",
    "price": 10.99,
    "description": "What it treats",
    "quantity": 30
}
```

### Modifying Agent Personality
Edit the `prompt` field in `config.json` to change how the agent behaves.

### Adjusting Voice Settings
Change the `model` in the `speak` provider section:
- `aura-2-thalia-en` (default, friendly female)
- `aura-2-zeus-en` (authoritative male)
- `aura-2-athena-en` (professional female)

## Troubleshooting 🔧

### Connection Issues
- Verify ngrok is running and URL is correct in Twilio
- Check that server is running on port 5000
- Ensure firewall allows WebSocket connections

### API Key Errors
- Verify `.env` file exists and has correct keys
- Check Deepgram and OpenAI dashboards for API status
- Ensure keys have proper permissions

### Audio Quality Issues
- Confirm μ-law encoding settings match in config
- Check Twilio audio codec settings
- Test with different phone networks

## Production Considerations 🚀

For production deployment:

1. **Database Integration**: Replace in-memory storage with PostgreSQL/MongoDB
2. **Authentication**: Add user authentication for order management
3. **HIPAA Compliance**: Implement encryption and audit logging
4. **Load Balancing**: Use multiple server instances behind a load balancer
5. **Error Handling**: Add comprehensive error logging and monitoring
6. **Rate Limiting**: Implement rate limits for API calls
7. **Secure Storage**: Use environment variable managers (AWS Secrets Manager, etc.)



Built with ❤️ using Deepgram Agent API, OpenAI, and Twilio
