# AI Shopping Agent

An AI-powered shopping assistant built with **Python, LangChain, Groq, and Streamlit**. It helps users discover products, compare customer ratings, search using product images, and place orders through a conversational interface.

## Features

- **Natural Language Product Search:** Find products using everyday queries.
- **Advanced Filtering:** Filter products by maximum price and organic status.
- **Product Ratings:** Retrieve average customer ratings and review counts.
- **Image-Based Product Search:** Upload a product image and use a vision model to identify similar products in the database.
- **Conversational Ordering:** Select a product and confirm an order through chat.
- **Database Integration:** Store products, customer reviews, and orders using SQLite.
- **Tool-Calling Agent:** Uses LangChain to decide which tools to invoke based on the user's request.

## Tech Stack

| Category | Technologies |
|---|---|
| Language | Python |
| Agent Framework | LangChain |
| LLM | Groq – Qwen |
| Vision Model | Llama 4 Scout via Groq |
| Frontend | Streamlit |
| Database | SQLite |
| Environment Management | uv |
| Image Processing | Base64 encoding |

## Architecture

```text
                 User
                  |
                  v
          Streamlit Interface
                  |
                  v
          LangChain AI Agent
                  |
             Groq LLM
                  |
          Tool Selection
                  |
     +------------+-------------+
     |            |             |
     v            v             v
 Product       Rating         Image
 Search        Lookup         Analysis
     |            |             |
     v            v             v
  SQLite     Reviews API     Vision LLM
     |            |             |
     +------------+-------------+
                  |
                  v
             Product Results
                  |
                  v
          User Confirms Order
                  |
                  v
             Checkout Tool
                  |
                  v
           SQLite Orders Table
```

## Agent Tools

The agent has four tools that it can invoke depending on the user's request.

### 1. `search_products`

Searches the SQLite product database using keywords.

**Parameters:**
- `query`: Product name, description, or category.
- `max_price`: Optional maximum price.
- `is_organic`: Optional organic-product filter.

**Example:**

```text
Find organic honey below $20.
```

The tool searches the database and returns matching products.

### 2. `get_rating`

Retrieves the average customer rating and total review count for a product.

**Parameter:**
- `product_id`: Unique product identifier.

The agent uses this tool to compare products and apply the user's minimum rating requirement.

### 3. `describe_product_image`

Analyzes an uploaded product image using the Llama 4 Scout vision model.

It extracts:
- Product type
- Search keywords
- Organic status
- Product description

The extracted information is passed to `search_products` to find similar products in the store.

### 4. `checkout`

Places an order for a selected product.

**Parameter:**
- `product_id`: ID of the selected product.

The tool inserts the order into the SQLite `orders` table and returns an order confirmation.

The agent must receive explicit user confirmation before calling this tool.

## Project Structure

```text
shopping-agent/
│
├── app.py                 # Streamlit user interface
├── shopping_agent.py      # LangChain agent and tools
├── reviews_api.py         # Product rating operations
├── setup_db.py            # Database and sample data setup
├── store.db               # SQLite database
├── pyproject.toml         # Project dependencies
├── uv.lock                # Locked dependency versions
├── .python-version        # Python version configuration
├── .gitignore
└── README.md
```

## Installation and Setup

### Prerequisites

- Python 3.14+
- [uv](https://docs.astral.sh/uv/)
- Groq API key

### 1. Clone the repository

```bash
git clone https://github.com/jeevanrushi07/shopping-agent.git

cd shopping-agent
```

### 2. Install dependencies

Install the project dependencies using uv:

```bash
uv sync
```

### 3. Configure environment variables

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key
```

Get your API key from [Groq Console](https://console.groq.com/).

Ensure the model IDs configured in `shopping_agent.py` are currently available to your Groq account.

### 4. Initialize the database

Run:

```bash
uv run python setup_db.py
```

This creates the SQLite database tables and inserts sample products and reviews.

**Note:** Running this script resets the sample reviews and repopulates the product records.

### 5. Run the application

Start the Streamlit application:

```bash
uv run streamlit run app.py
```

Open the local URL displayed in your terminal.

## Example Queries

**Product search:**

```text
Find organic honey below $20.
```

**Rating-based filtering:**

```text
Show me products with a rating above 4.5.
```

**Combined filtering:**

```text
Find organic almonds below $15 with a rating above 4.5.
```

**Image-based search:**

Upload a product image and select **Find similar products**.

**Order placement:**

```text
I want to order the first product.
```

The agent identifies the selected product and requests confirmation before placing the order.

## How the Agent Works

1. The user submits a natural-language query through Streamlit.
2. The LangChain agent sends the conversation to the Groq language model.
3. The model determines which tool is needed.
4. The selected tool retrieves product information, ratings, or image attributes.
5. The agent evaluates the returned results and presents matching products.
6. The user selects a product and explicitly confirms the order.
7. The checkout tool inserts the order into SQLite and returns a confirmation.

For image-based searches, the vision model first analyzes the uploaded image. The extracted product attributes are then used to search the product database.

## Database Design

The application uses SQLite with three tables:

| Table | Purpose |
|---|---|
| `products` | Stores product information, prices, categories, and organic status. |
| `reviews` | Stores customer ratings and review information. |
| `orders` | Stores order IDs, product details, prices, and timestamps. |

## Future Improvements

- Add user authentication and order history.
- Integrate a real payment gateway.
- Add persistent conversation memory.
- Improve product recommendations using semantic search.
- Add automated tests for agent tools and checkout workflows.
- Deploy the application to a cloud platform.

## Author

**Jeevan Rushi Sudula**

GitHub: [jeevanrushi07](https://github.com/jeevanrushi07)

Project: [AI Shopping Agent](https://github.com/jeevanrushi07/shopping-agent)
