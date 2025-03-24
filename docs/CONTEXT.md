# Complete Step-by-Step Guide to Building a Crypto AI Agent with n8n

This comprehensive guide walks you through building a sophisticated cryptocurrency AI agent system using n8n. The system automatically collects market data, performs technical analysis, generates AI-powered insights, and alerts you to trading opportunities.

## Prerequisites

- Computer with internet connection
- Basic computer skills

## Part 1: Setting Up Your Environment

### Step 1: Install n8n

1. Visit [n8n.io](https://n8n.io/)
2. Click "Download" or "Get Started"
3. Follow the installation instructions:
   - **Windows**: Download and run the installer
   - **Mac**: `brew install n8n`
   - **Linux**: `npm install n8n -g`

### Step 2: Start n8n

1. Open Command Prompt/Terminal
2. Run `n8n start`
3. Wait for n8n to start
4. Open [http://localhost:5678](http://localhost:5678)
5. Create a new account if prompted

### Step 3: Install Python Dependencies

1. Download Python from [python.org/downloads](https://python.org/downloads/)
2. Install Python (check "Add to PATH" during installation)
3. Open Command Prompt/Terminal
4. Run: `pip install pydantic==2.5.2 pandas==2.1.3`

### Step 4: Set Up Supabase Account

1. Visit [supabase.com](https://supabase.com/)
2. Click "Start for free" or "Sign Up"
3. Create an account
4. Click "New Project"
5. Name it "CryptoAIAgent"
6. Create a strong password
7. Select a region closest to you
8. Click "Create new project"

### Step 5: Create Supabase Database Tables

1. In your Supabase dashboard, open "SQL Editor"
2. Click "New query"
3. Paste this SQL code:

```sql
-- Create crypto_prices table
CREATE TABLE crypto_prices (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  symbol TEXT NOT NULL,
  timestamp TIMESTAMPTZ NOT NULL,
  open NUMERIC NOT NULL,
  high NUMERIC NOT NULL,
  low NUMERIC NOT NULL,
  close NUMERIC NOT NULL,
  volume NUMERIC NOT NULL
);

-- Create market_signals table
CREATE TABLE market_signals (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  symbol TEXT NOT NULL,
  timestamp TIMESTAMPTZ NOT NULL,
  signal_type TEXT NOT NULL,
  confidence NUMERIC NOT NULL,
  details JSONB NOT NULL,
  is_notified BOOLEAN DEFAULT FALSE
);

-- Create market_analysis table
CREATE TABLE market_analysis (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  symbol TEXT NOT NULL,
  timestamp TIMESTAMPTZ NOT NULL,
  analysis TEXT NOT NULL,
  data JSONB NOT NULL
);

-- Create indexes for performance
CREATE INDEX idx_crypto_prices_symbol_timestamp ON crypto_prices(symbol, timestamp DESC);
CREATE INDEX idx_market_signals_timestamp ON market_signals(timestamp DESC);
CREATE INDEX idx_market_signals_symbol ON market_signals(symbol);
CREATE INDEX idx_market_analysis_symbol_timestamp ON market_analysis(symbol, timestamp DESC);
```

4. Click "Run" to create the tables

### Step 6: Get Your Supabase API Keys

1. In Supabase, go to Settings → API
2. Find "Project API keys"
3. Copy and save:
   - Project URL
   - "anon public" key

### Step 7: Get External API Keys

#### CryptoCompare API:
1. Go to [cryptocompare.com](https://www.cryptocompare.com/)
2. Sign up for an account
3. Navigate to Account → API Keys
4. Click "Get API Key"
5. Copy and save your API key

#### Telegram Bot:
1. Open Telegram app
2. Search for "@BotFather" and start a chat
3. Type "/newbot" and follow the instructions
4. Copy the API token provided
5. Create a group and add your bot
6. Send a message in the group
7. Visit `https://api.telegram.org/bot[YOUR_BOT_TOKEN]/getUpdates`
8. Find and copy the "chat" section "id" number

#### Claude API (Optional):
1. Go to [anthropic.com](https://www.anthropic.com/)
2. Apply for API access
3. Once approved, create an API key
4. Copy and save this key

## Part 2: Building the Data Collection Workflow

### Step 1: Create a New Workflow
1. In n8n, click "Workflows" in the sidebar
2. Click "Create new workflow"
3. Name it "Crypto AI Agent - Data Collection"

### Step 2: Import the Workflow JSON
1. Click the three dots menu (⋮)
2. Select "Import from JSON"
3. Paste the content from "crypto-workflow-part1" JSON file
4. Click "Import"

### Step 3: Configure the Workflow
1. Update the "Global Variables" node with your API keys:
   - CryptoCompare API key
   - Telegram bot token
   - Telegram chat ID
   - Claude API key (if available)

2. Configure Supabase connection:
   - Click any Supabase node
   - Click "Create new" under Credentials
   - Name: "Supabase Account"
   - API Key: Your Supabase anon public key
   - Supabase URL: Your Supabase URL
   - Click "Create"

### Step 4: Test the Data Collection Workflow
1. Click "Execute Workflow" 
2. Verify each node completes successfully
3. Check your Supabase tables for new data

### Step 5: Activate the Workflow
1. Toggle "Active" at the bottom of the page
2. Workflow will now run automatically every 10 minutes

## Part 3: Building the Market Analysis Workflow

### Step 1: Create a New Workflow
1. Click "Workflows" in the sidebar
2. Click "Create new workflow"
3. Name it "Crypto AI Agent - Market Analysis"

### Step 2: Import the Workflow JSON
1. Click the three dots menu (⋮)
2. Select "Import from JSON"
3. Paste the content from "crypto-workflow-part2-fixed" JSON file
4. Click "Import"

### Step 3: Configure the Workflow
1. Update the "Global Variables" node:
   - Telegram bot token
   - Telegram chat ID

2. Configure Supabase connection:
   - Use the same Supabase credentials created earlier

### Step 4: Test the Market Analysis Workflow
1. Click "Execute Workflow"
2. Verify each node completes successfully
3. Check your Supabase market_signals table for new entries

### Step 5: Activate the Workflow
1. Toggle "Active" at the bottom of the page
2. Workflow will now run automatically every 15 minutes

## Part 4: Building the AI Analysis Workflow

### Step 1: Create a New Workflow
1. Click "Workflows" in the sidebar
2. Click "Create new workflow"
3. Name it "Crypto AI Agent - AI Analysis"

### Step 2: Import the Workflow JSON
1. Click the three dots menu (⋮)
2. Select "Import from JSON"
3. Paste the content from "crypto-workflow-part3-fixed" JSON file
4. Click "Import"

### Step 3: Configure the Workflow
1. Update the "Global Variables" node:
   - Claude API key
2. Configure Supabase connection:
   - Use the same Supabase credentials created earlier

### Step 4: Test the AI Analysis Workflow
1. Click "Execute Workflow"
2. Verify each node completes successfully
3. Check your Supabase market_analysis table for new entries

### Step 5: Activate the Workflow
1. Toggle "Active" at the bottom of the page
2. Workflow will now run automatically every hour

## Part 5: Building the Dashboard

### Step 1: Create a Dashboard Workflow
1. Click "Workflows" in the sidebar
2. Click "Create new workflow"
3. Name it "Crypto Dashboard Data Provider"

### Step 2: Import the Dashboard Workflow JSON
1. Click the three dots menu (⋮)
2. Select "Import from JSON"
3. Paste the content from "crypto-dashboard-data-provider" JSON file
4. Click "Import"

### Step 3: Configure the Dashboard Workflow
1. Configure Supabase connection:
   - Use the same Supabase credentials created earlier

### Step 4: Activate the Dashboard Workflow
1. Toggle "Active" at the bottom of the page
2. Note the webhook URL from the "Webhook" node

### Step 5: Create the Dashboard HTML File
1. Open a text editor
2. Copy the content from "crypto-dashboard-html" file
3. Replace `YOUR_WEBHOOK_URL_HERE` with your actual webhook URL
4. Save as "crypto-dashboard.html"

### Step 6: Access the Dashboard
1. Double-click the HTML file
2. Your web browser will open showing your dashboard

## Part 6: Testing the Complete System

### Step 1: Verify Data Collection
1. Run your Data Collection workflow manually
2. Check the Supabase crypto_prices table
3. Confirm that price data is being collected

### Step 2: Verify Market Analysis
1. Run your Market Analysis workflow manually
2. Check the Supabase market_signals table
3. Confirm signal detection and Telegram notifications

### Step 3: Verify AI Analysis
1. Run your AI Analysis workflow manually
2. Check the Supabase market_analysis table
3. Confirm AI-generated analyses are being created

### Step 4: Verify Dashboard Functionality
1. Refresh your dashboard webpage
2. Confirm all data displays correctly
3. Verify charts and tables update with new data

## Troubleshooting

### Workflow Failures
- Check red nodes for specific error messages
- Common issues:
  - Incorrect API keys
  - Database connection problems
  - Python installation errors

### No Data Appearing
- Ensure workflows are active
- Verify API key permissions
- Check database table structure

### Dashboard Issues
- Verify webhook URL in HTML file
- Ensure Dashboard workflow is active
- Check browser console for errors (F12)

## Conclusion

Congratulations! You've built a sophisticated crypto AI agent system that:

- Collects market data every 10 minutes
- Analyzes patterns every 15 minutes  
- Generates AI insights every hour
- Alerts you to trading opportunities
- Provides a real-time dashboard

This automated system gives you powerful tools for cryptocurrency market monitoring and analysis!
