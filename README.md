## Customer Experience Analytics – Task 1: Data Collection & Preprocessing
📌 Project Overview
This project analyzes customer satisfaction with mobile banking apps in Ethiopia by scraping and processing Google Play Store reviews for:

## Commercial Bank of Ethiopia (CBE)

## Bank of Abyssinia (BOA)

## Dashen Bank

The goal is to collect, clean, and store structured user feedback for further sentiment and thematic analysis.

## ⚡ Objective
Omega Consultancy is assisting Ethiopian banks in improving customer experience and retention. As a Data Analyst, you will:

Scrape user reviews from Google Play.

Preprocess & clean the raw data.

Store in structured format (CSV).

Prepare datasets for NLP analysis.

## 🛠 Technologies & Tools
Python (Data processing)

Google Play Scraper (Review extraction)

Pandas (Data cleaning)

Oracle Database (Optional) (Storage)

📂 Directory Structure
task-1/
│── README.md       # Documentation
│── requirements.txt # Required packages
│── scraper.py      # Web scraping script
│── preprocess.py   # Data cleaning script
│── CBE_bank_reviews_clean.csv # Final cleaned dataset (CBE)
│── BOA_bank_reviews_clean.csv # Final cleaned dataset (BOA)
│── Dashen_bank_reviews_clean.csv # Final cleaned dataset (Dashen)
## 🔹 Installation
## Before running the scripts, install dependencies:

bash
pip install -r requirements.txt
Required Libraries
google-play-scraper
pandas
## 📌 Web Scraping Approach
The scraper.py script collects reviews from Google Play Store for each bank:

python
from google_play_scraper import reviews
import pandas as pd
from google_play_scraper import Sort

app_id = "com.combanketh.mobilebanking"  # CBE's App ID
result, _ = reviews(app_id, lang="en", country="ET", count=400, sort=Sort.NEWEST)

df = pd.DataFrame(result)
df["bank"] = "CBE"
df["source"] = "Google Play"

df.to_csv("CBE_bank_reviews.csv", index=False)
✅ This process is repeated for BOA & Dashen.

## 🧹 Data Cleaning & Preprocessing
The preprocess.py script ensures clean and structured data:

python
import pandas as pd

df = pd.read_csv("CBE_bank_reviews.csv")

# Remove duplicates & missing values
df.drop_duplicates(subset=["review"], inplace=True)
df.dropna(subset=["review"], inplace=True)

# Normalize date format
df["date"] = pd.to_datetime(df["date"]).dt.strftime("%Y-%m-%d")

df.to_csv("CBE_bank_reviews_clean.csv", index=False)
print("✅ Data preprocessing completed.")
📊 Expected Output
## Final dataset structure:

Review	Rating	Date	Bank	Source
"Great app, but slow transfers!"	3	2025-06-07	CBE	Google Play
"Login issues persist!"	1	2025-06-06	CBE	Google Play
📝 Next Steps

🔹 Proceed to Task 2: Sentiment & Thematic Analysis (NLP). 🔹 Store data in Oracle DB (Task 3) for structured querying. 🔹 Visualize insights (Task 4) for reporting.

### Customer Experience Analytics – Task 2: Sentiment & Thematic Analysis 📊
🔹 Project Overview
Task 2 focuses on analyzing customer feedback from mobile banking apps in Ethiopia, specifically for:

Commercial Bank of Ethiopia (CBE)

Bank of Abyssinia (BOA)

Dashen Bank

Using Natural Language Processing (NLP), we extract sentiment trends and categorize reviews into meaningful themes.

## 🔹 Objective
Omega Consultancy aims to help Ethiopian banks improve customer experience by: 1️⃣ Analyzing sentiment trends (positive, neutral, negative). 2️⃣ Grouping complaints & praises into key themes (e.g., UI, reliability). 3️⃣ Deriving actionable insights for product enhancement.

## 🛠 Technologies & Tools
Python → Data processing

NLTK / VADER → Sentiment analysis

Hugging Face (DistilBERT) → Deep NLP model

spaCy / TF-IDF → Keyword extraction

Seaborn / Matplotlib → Data visualization

📂 Directory Structure
task-2/
│── README.md               # Documentation  
│── requirements.txt        # Required packages  
│── sentiment_analysis.py   # NLP-based sentiment detection  
│── thematic_analysis.py    # Keyword extraction & clustering  
│── aggregated_sentiment.csv  # Sentiment summary per bank & rating  
│── CBE_thematic_analysis.csv  # Final clustered review themes  

## 🔹 1️⃣ Sentiment Analysis Approach
The sentiment_analysis.py script labels reviews as positive, neutral, or negative using VADER & DistilBERT:

python
from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
import pandas as pd
from transformers import pipeline

# Load dataset
df = pd.read_csv("CBE_bank_reviews_clean.csv")

# Initialize sentiment models
vader_analyzer = SentimentIntensityAnalyzer()
bert_analyzer = pipeline("sentiment-analysis")

# Apply sentiment analysis
df["vader_sentiment"] = df["review"].apply(lambda x: "positive" if vader_analyzer.polarity_scores(x)["compound"] > 0.05 else "negative")
df["bert_sentiment"] = df["review"].apply(lambda x: bert_analyzer(x)[0]["label"])

# Save results
df.to_csv("CBE_sentiment_analysis.csv", index=False)
print("✅ Sentiment analysis completed! Saved as CBE_sentiment_analysis.csv.")

## 🔹 2️⃣ Thematic Analysis Approach
We extract keywords and cluster reviews into 3–5 themes per bank:

python
import pandas as pd
from sklearn.feature_extraction.text import TfidfVectorizer

# Load processed review dataset
df = pd.read_csv("CBE_sentiment_analysis.csv")

# Define themes
themes = {
    "Account Access Issues": ["login", "password", "authentication", "error"],
    "Transaction Performance": ["transfer", "delay", "speed"],
    "User Interface & Experience": ["UI", "design", "navigation"],
    "Customer Support": ["support", "response", "service"],
    "Feature Requests": ["fingerprint", "notification", "budgeting"]
}

# Assign themes based on keyword matches
df["identified_theme"] = df["review"].apply(lambda x: [theme for theme, keywords in themes.items() if any(word in x for word in keywords)])

# Save thematic clustering results
df.to_csv("CBE_thematic_analysis.csv", index=False)
print("✅ Thematic clustering completed! Results saved as CBE_thematic_analysis.csv.")
This helps banks identify key areas for improvement.


## 🚀 sentiment accuracy
1️⃣ Refine sentiment analysis model accuracy. 2️⃣ Improve clustering logic using LDA topic modeling. 3️⃣ Integrate insights into business strategy reporting.

💾 Customer Experience Analytics – Task 3: SQL Storage & Automation
📌 Objective
To enable structured querying and downstream analytics, Task 3 involved exporting sentiment- and theme-tagged reviews into an Oracle-compatible relational schema with reproducibility and automation in mind.

### 🏛 Schema Design
A normalized database schema was created with the following tables:

### Table	Description
CUSTOMER_REVIEW	Review text, rating, sentiment label, sentiment score
THEME_TAG	Extracted theme (e.g. UI, Access, Support)
BANK	Bank metadata (bank_id, bank_name, source info)
Exported with:

### sql
SELECT DBMS_METADATA.GET_DDL('TABLE', 'CUSTOMER_REVIEW') FROM DUAL;
⚙️ Automation Workflow
Scripts were built for auto-export of query results to CSV using:

SQL*Plus (Oracle CLI)

PowerShell scripting with robust path handling

SPOOL directives to ensure clean output

### powershell
$sqlQuery = "SELECT * FROM CUSTOMER_REVIEW ORDER BY sentiment_score DESC;"
$sqlplusArgs = @("-s", "$user/$password@$tns", "@query.sql")
Start-Process sqlplus.exe -ArgumentList $sqlplusArgs -Wait
🧱 Reproducibility
Version-controlled .sql schema exports

Export logs under /task-3/logs/

PowerShell error handling (e.g. SP2-0556) resolved via dynamic paths

✅ Outcome: All reviews inserted with complete metadata and theme classifications, reproducible across environments.

📊 Customer Experience Analytics – Task 4: Insights & Visualization
📌 Objective
Translate stored data into strategic business insights via sentiment trends, theme mapping, and data-driven visualizations.

🔍 Analytical Pipeline
python
# Parse and explode theme tags
df["parsed_theme"] = df["identified_theme"].apply(ast.literal_eval)
df_exploded = df.explode("parsed_theme")
df_exploded = df_exploded[df_exploded["parsed_theme"] != "Other"]

# Sentiment & Theme grouping
insights = df_exploded.groupby(["bank", "parsed_theme", "sentiment_label"]).size().reset_index(name="count")
📈 Visualizations
1️⃣ Sentiment Distribution per Bank

Dashen: high positive skew

BOA: major dip into negative territory

2️⃣ Rating Histogram

BOA's reviews heavily clustered at 1–2 stars

Dashen displays healthy rightward bias (4–5 stars)

3️⃣ Top Pain Points by Bank

BOA: Login failures, crashes

CBE: Authentication errors

Dashen: Fewer severe complaints, mostly feature requests


✅ Recommendations
BOA:

Prioritize crash/debug fixes and authentication reliability

Add basic in-app support and improved error guidance

CBE:

Implement fallback for biometric login failures

Refine account recovery paths

Dashen:

Expand feature set (budgeting, alerts)

Preserve current performance consistency

