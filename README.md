# DAX Companies Data Collection and Analysis Project

This project collects, processes, and analyzes data about DAX (Deutscher Aktienindex) companies through web scraping, NLP, and data analysis.

## Table of Contents
- [Prerequisites](#prerequisites)
- [Setup Instructions](#setup-instructions)
- [Project Structure](#project-structure)
- [Running the Tasks](#running-the-tasks)
- [DuckDB Data Dictionary](#duckdb-data-dictionary)

---

## Prerequisites

- Python 3.8 or higher
- pip (Python package installer)
- Internet connection (for web scraping tasks)

---

## Setup Instructions

### 1. Create a Virtual Environment

#### On Windows:
```bash
# Create virtual environment
python -m venv venv

# Activate the virtual environment
.\venv\Scripts\activate
```

#### On macOS/Linux:
```bash
# Navigate to the project directory
cd "/path/to/project"

# Create virtual environment
python3 -m venv venv

# Activate the virtual environment
source venv/bin/activate
```

### 2. Install Dependencies

After activating the virtual environment, install all required packages:

```bash
pip install -r requirements.txt
```

### 3. Download spaCy Language Model

For the NLP task (Task 1.3), you need to download the spaCy language model:

```bash
python -m spacy download en_core_web_sm
```

---

## Project Structure

```
.
├── task_1_1_wikipedia_scrapping.ipynb      # Phase 1, Task 1
├── task_1_2_google_finance_scraping.ipynb  # Phase 1, Task 2
├── task_1_3_named_entities_nlp.ipynb       # Phase 1, Task 3
├── task_1_4_get_company_logos.ipynb        # Phase 1, Task 4
├── task_2_1_data_analysis.ipynb            # Phase 2, Task 1
├── task_2_2_EDA.ipynb                      # Phase 2, Task 2
├── requirements.txt                        # Python dependencies
├── dax_data.db                            # DuckDB database (created after Phase 1)
├── logos/                                 # Company logos folder
└── ss/                                    # Screenshots folder
```

---

## Running the Tasks

### Phase 1: Data Collection and Processing

Phase 1 tasks must be executed **sequentially** as each task depends on the data from the previous task.

#### **Task 1.1: Wikipedia Scraping**
**First task of Phase 1** - Scrapes DAX company information from Wikipedia.

1. Open `task_1_1_wikipedia_scrapping.ipynb` in VS Code or Jupyter
2. Run all cells sequentially
3. This creates the `dax_data.db` database with the `dax` table

**What it does:**
- Scrapes the Wikipedia DAX page
- Extracts ticker symbols, company names, and sectors
- Stores data in DuckDB table `dax`

#### **Task 1.2: Google Finance Scraping**
**Second task of Phase 1** - Fetches financial data from Google Finance.

1. Open `task_1_2_google_finance_scraping.ipynb`
2. Run all cells sequentially
3. This adds the `finance` table to the database

**What it does:**
- Retrieves tickers from the `dax` table
- Scrapes current price, market cap, and company descriptions from Google Finance
- Stores data in DuckDB table `finance`

#### **Task 1.3: Named Entity Recognition (NLP)**
**Third task of Phase 1** - Extracts named entities from company descriptions.

1. Open `task_1_3_named_entities_nlp.ipynb`
2. Run all cells sequentially
3. This adds the `entities` table to the database

**What it does:**
- Reads company descriptions from the `finance` table
- Uses spaCy NLP to extract named entities (ORG, PERSON, GPE, PRODUCT, LOC, NORP)
- Stores entity types in DuckDB table `entities`

#### **Task 1.4: Get Company Logos**
**Fourth task of Phase 1** - Downloads company logos.

1. Open `task_1_4_get_company_logos.ipynb`
2. Run all cells sequentially
3. Company logos are saved to the `logos/` folder

**What it does:**
- Downloads or generates company logos
- Saves logos to the `logos/` directory

---

### Phase 2: Data Analysis and Visualization

Phase 2 tasks analyze the collected data.

#### **Task 2.1: Data Analysis**
1. Open `task_2_1_data_analysis.ipynb`
2. Run all cells sequentially

**What it does:**
- Connects to the DuckDB database
- Performs data analysis on the collected data
- Joins tables and generates insights

#### **Task 2.2: Exploratory Data Analysis (EDA)**
1. Open `task_2_2_EDA.ipynb`
2. Run all cells sequentially

**What it does:**
- Performs comprehensive exploratory data analysis
- Creates visualizations and statistical summaries

---

## DuckDB Data Dictionary

The project uses a DuckDB database (`dax_data.db`) with the following tables:

### Table: `dax`
Created by: **Task 1.1**

| Column Name   | Data Type | Description                                      |
|---------------|-----------|--------------------------------------------------|
| ticker        | TEXT      | Stock ticker symbol (e.g., "ADS", "AIR", "BAS")  |
| company_name  | TEXT      | Full company name (e.g., "Adidas AG")            |
| sector        | TEXT      | Industry sector (e.g., "Consumer Discretionary") |

**Purpose:** Stores basic information about DAX constituent companies scraped from Wikipedia.

**Sample Data:**
```
ticker | company_name        | sector
-------|---------------------|-------------------------
ADS    | Adidas AG           | Consumer Discretionary
AIR    | Airbus SE           | Industrials
BAS    | BASF SE             | Materials
```

---

### Table: `finance`
Created by: **Task 1.2**

| Column Name    | Data Type | Description                                           |
|----------------|-----------|-------------------------------------------------------|
| ticker         | TEXT      | Stock ticker symbol (foreign key to `dax.ticker`)     |
| current_price  | REAL      | Current stock price in EUR                            |
| market_cap     | TEXT      | Market capitalization (e.g., "50.5B", "2.3T")         |
| about          | TEXT      | Company description from Google Finance               |

**Purpose:** Stores financial data and company descriptions scraped from Google Finance.

**Sample Data:**
```
ticker | current_price | market_cap | about
-------|---------------|------------|------------------------------------------
ADS    | 215.60        | 41.2B      | Adidas AG designs and manufactures...
AIR    | 142.34        | 112.8B     | Airbus SE manufactures commercial...
```

**Notes:**
- `current_price` may be NULL if price data is unavailable
- `market_cap` is stored as TEXT to preserve formatting (B for billions, T for trillions)
- `about` may be NULL or 'N/A' if description is unavailable

---

### Table: `entities`
Created by: **Task 1.3**

| Column Name  | Data Type | Description                                                    |
|--------------|-----------|----------------------------------------------------------------|
| ticker       | TEXT      | Stock ticker symbol (foreign key to `dax.ticker`)              |
| entity_list  | TEXT      | Comma-separated list of entity types found in company description |

**Purpose:** Stores named entity types extracted from company descriptions using spaCy NLP.

**Sample Data:**
```
ticker | entity_list
-------|----------------------------------
ADS    | ORG, GPE, PRODUCT, PERSON
AIR    | ORG, GPE, LOC
BAS    | ORG, GPE, NORP, PRODUCT
```

**Entity Types:**
- **ORG**: Organizations (companies, institutions)
- **PERSON**: People's names
- **GPE**: Geopolitical entities (countries, cities, states)
- **PRODUCT**: Products and services
- **LOC**: Locations (non-GPE)
- **NORP**: Nationalities or religious/political groups

**Notes:**
- Only unique entity types are stored (duplicates removed)
- Empty string if no entities found or description unavailable

---

### Database Relationships

```
dax (ticker) ──┬── finance (ticker)
               └── entities (ticker)
```

All tables can be joined on the `ticker` column to create a comprehensive view of each DAX company.

**Example Join Query:**
```sql
SELECT 
    d.ticker,
    d.company_name,
    d.sector,
    f.current_price,
    f.market_cap,
    e.entity_list
FROM dax d
LEFT JOIN finance f ON d.ticker = f.ticker
LEFT JOIN entities e ON d.ticker = e.ticker;
```

---

## Troubleshooting

### Common Issues

1. **403 Forbidden errors when scraping:**
   - The code includes proper User-Agent headers to avoid this
   - If issues persist, check your internet connection

2. **spaCy model not found:**
   - Run: `python -m spacy download en_core_web_sm`

3. **Database locked errors:**
   - Close all connections to `dax_data.db` before running tasks
   - Ensure only one notebook is accessing the database at a time

4. **Module import errors:**
   - Ensure virtual environment is activated
   - Reinstall requirements: `pip install -r requirements.txt`

---

## Notes

- Always activate the virtual environment before running any tasks
- Tasks in Phase 1 must be run in order (1.1 → 1.2 → 1.3 → 1.4)
- Phase 2 tasks require Phase 1 to be completed first
- The DuckDB database file (`dax_data.db`) will be created automatically
- Web scraping tasks may take several minutes to complete

---

## Dependencies

Key packages used in this project:
- **beautifulsoup4**: Web scraping
- **requests**: HTTP requests
- **duckdb**: Embedded database
- **pandas**: Data manipulation
- **spacy**: Natural Language Processing
- **matplotlib**: Data visualization
- **numpy**: Numerical computing
- **jupyter**: Interactive notebooks

For complete list, see [requirements.txt](requirements.txt)
