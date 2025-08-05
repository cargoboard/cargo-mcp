# Cargoboard MCP Server

A Model Context Protocol (MCP) server for the Cargoboard shipping API. This server provides essential functionality for creating quotations, orders, and generating shipment labels through a streamlined interface.

## Features

The MCP Server provides 5 essential tools:

- **create_quotation** - Create shipping quotation requests
- **create_order** - Create shipping orders  
- **get_quotation** - Retrieve quotation details
- **get_order** - Retrieve order details
- **print_shipment_labels** - Generate shipment labels for orders

## Setup

### Prerequisites

- Python 3.10 or higher
- `uv` package manager
- Cargoboard API key

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd cargo-mcp
   ```

2. **Install dependencies**
   ```bash
   uv sync
   ```

3. **Configure environment variables**
   
   Create a `.env` file in the project directory:
   ```bash
   # Your Cargoboard API key (required)
   CARGOBOARD_API_KEY=your_api_key_here
   
   # API endpoint (optional, defaults to production)
   CARGOBOARD_BASE_URL=https://api.cargoboard.com
   
   # Request timeout in seconds (optional, defaults to 30)
   CARGOBOARD_TIMEOUT=30
   ```

### MCP Client Setup

Add the server to your MCP client configuration:

```json
{
  "mcpServers": {
    "cargo-mcp": {
      "command": "uv",
      "args": [
        "run",
        "--with",
        "fastmcp",
        "fastmcp",
        "run",
        "/absolute/path/to/server.py"
      ]
    }
  }
}
```

**Important Notes:**
- Replace `/absolute/path/to/server.py` with the actual absolute path to your server.py file
- You can include environment variables directly in the config or use the `.env` file
- Ensure the path uses forward slashes even on Windows

### Alternative Configuration (with environment variables)

If you prefer to include environment variables directly in the configuration:

```json
{
  "mcpServers": {
    "cargo-mcp": {
      "command": "uv",
      "args": [
        "run",
        "--with",
        "fastmcp",
        "fastmcp",
        "run",
        "/absolute/path/to/server.py"
      ],
      "env": {
        "CARGOBOARD_API_KEY": "your_api_key_here",
        "CARGOBOARD_BASE_URL": "https://api.cargoboard.com"
      }
    }
  }
}
```

## Usage Guide

### Creating a Quotation

Use the `create_quotation` tool to get shipping price estimates:

```python
# Basic quotation request
quotation_data = {
    "product": "STANDARD",
    "shipper": {
        "reference": "SHIPPER-001",
        "name": "My Company",
        "address": {
            "postCode": "10115", 
            "countryCode": "DE"
        },
        "contactPerson": {
            "name": "John Doe",
            "phone": "+49123456789",
            "email": "john@company.com"
        }
    },
    "consignee": {
        "reference": "CUSTOMER-001",
        "name": "Customer Company", 
        "address": {
            "postCode": "20095",
            "countryCode": "DE"
        },
        "contactPerson": {
            "name": "Jane Smith",
            "phone": "+49987654321", 
            "email": "jane@customer.com"
        }
    },
    "lines": [{
        "content": "Electronics",
        "unitQuantity": 2,
        "unitPackageType": "PA",  # Package type
        "unitLength": 100,        # cm
        "unitWidth": 80,          # cm
        "unitHeight": 60,         # cm
        "unitWeight": 15.5        # kg
    }]
}
```

### Creating an Order

Use the `create_order` tool to place actual shipping orders:

```python
# Complete order request
order_data = {
    "product": "STANDARD",
    "shipper": {
        "reference": "SHIPPER-001",
        "name": "My Company",
        "address": {
            "postCode": "10115",
            "countryCode": "DE", 
            "street": "Main Street 123",        # Required for orders
            "city": "Berlin"                    # Required for orders
        },
        "contactPerson": {
            "name": "John Doe",
            "phone": "+49123456789",
            "email": "john@company.com"
        }
    },
    "consignee": {
        "reference": "CUSTOMER-001", 
        "name": "Customer Company",
        "address": {
            "postCode": "20095",
            "countryCode": "DE",
            "street": "Customer Alley 456",     # Required for orders
            "city": "Hamburg"                   # Required for orders
        },
        "contactPerson": {
            "name": "Jane Smith",
            "phone": "+49987654321",
            "email": "jane@customer.com"
        }
    },
    "lines": [{
        "content": "Electronics",
        "unitQuantity": 2,
        "unitPackageType": "PA",
        "unitLength": 100,
        "unitWidth": 80,
        "unitHeight": 60,
        "unitWeight": 15.5
    }],
    "wants_climate_neutral_shipment": True,    # Optional: eco-friendly shipping
    "wants_insurance": False,                  # Optional: shipment insurance
    "customer_order_code": "CUST-ORD-123"     # Optional: your internal reference
}
```

### Retrieving Information

**Get quotation details:**
```python
# Retrieve quotation by ID
quotation = get_quotation("quotation_id_here")
```

**Get order details:**
```python
# Retrieve order by ID  
order = get_order("order_id_here")
```

### Generating Shipment Labels

**Print shipping labels:**
```python
# Generate labels for confirmed order
labels = print_shipment_labels("order_id_here")
```

## Product Types

Available shipping products:

- **STANDARD** - Standard delivery service
- **EXPRESS** - Express delivery service  
- **EXPRESS_8** - Express delivery by 8 AM
- **EXPRESS_10** - Express delivery by 10 AM
- **EXPRESS_12** - Express delivery by 12 PM
- **EXPRESS_16** - Express delivery by 4 PM
- **FIX** - Fixed time delivery
- **FIX_8** - Fixed time delivery by 8 AM
- **FIX_10** - Fixed time delivery by 10 AM
- **FIX_12** - Fixed time delivery by 12 PM
- **FIX_16** - Fixed time delivery by 4 PM
- **DIRECT** - Direct delivery service

## Package Types

Common package type codes:

- **KT** - Package/Parcel
- **FP** - Euro Pallet
- **EP** - One Way Pallet

## Address Requirements

### For Quotations:
- `postCode` (required)
- `countryCode` (required, ISO 2-letter code)
- `name` (optional but recommended)

### For Orders:
- `postCode` (required)
- `countryCode` (required)
- `street` (required)
- `city` (required)
- `name` (required)

## Development

### Running the Server

```bash
# Run MCP server directly
uv run python server.py

# Run with specific environment
CARGOBOARD_API_KEY=your_key uv run python server.py
```

### Linting

```bash
# Run ruff linter
uv run ruff check .

# Auto-fix issues
uv run ruff check --fix .
```

### Testing

Test your setup:

```bash
# Run the server directly to test startup
timeout 5 uv run python server.py
```

## Error Handling

The server includes comprehensive error handling and logging. Common issues:

1. **Authentication Error**: Check your API key in the `.env` file
2. **Validation Error**: Ensure all required fields are provided with correct formats
3. **Network Error**: Check your internet connection and API endpoint URL
4. **Order Not Found**: Verify the order/quotation ID exists and is accessible

## Support

For API documentation and support:
- API Documentation: [Cargoboard API Docs](https://docs.cargoboard.com/)
- Contact support via Email: mcp@cargoboard.com