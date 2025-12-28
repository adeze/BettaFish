# MindSpider - AI Crawler Designed for Public Opinion Analysis

> Disclaimer:
> All content in this repository is for educational and reference purposes only and is prohibited for commercial use. No individual or organization may use the content of this repository for illegal purposes or to infringe upon the legal rights of others. The crawler technology involved in this repository is for educational and research purposes only and shall not be used for large-scale crawling or other illegal activities on other platforms. This repository assumes no legal responsibility for any legal liability arising from the use of its content. Using the content of this repository indicates your agreement to all terms and conditions of this disclaimer.

## Project Overview

MindSpider is an intelligent public opinion crawler system based on Agent technology. It automatically identifies trending topics through AI and performs precise content crawling on multiple social media platforms. The system adopts a modular design and can realize a fully automated process from topic discovery to content collection.

This part is learned from the well-known GitHub crawler project [MediaCrawler](https://github.com/NanmiCoder/MediaCrawler)

Two-step crawling approach:

- Module 1: Search Agent identifies trending news from **13** social media platforms and technical forums including Weibo, Zhihu, GitHub, Coolapk, etc., and maintains a daily topic analysis table.
- Module 2: Full-platform crawler deeply crawls fine-grained public opinion feedback for each topic.

<div align="center">
<img src="img\example.png" alt="banner" width="700">

MindSpider Running Example
</div>

### Technical Architecture

- **Programming Language**: Python 3.9+
- **AI Framework**: Deepseek by default, can integrate multiple APIs (topic extraction and analysis)
- **Crawler Framework**: Playwright (browser automation)
- **Database**: MySQL (data persistent storage)
- **Concurrency Processing**: AsyncIO (asynchronous concurrent crawling)

## Project Structure

```
MindSpider/
├── BroadTopicExtraction/           # Topic extraction module
│   ├── database_manager.py         # Database manager
│   ├── get_today_news.py          # News collector
│   ├── main.py                    # Module main entry
│   └── topic_extractor.py         # AI topic extractor
│
├── DeepSentimentCrawling/         # Deep crawling module
│   ├── keyword_manager.py         # Keyword manager
│   ├── main.py                   # Module main entry
│   ├── platform_crawler.py       # Platform crawler manager
│   └── MediaCrawler/             # Multi-platform crawler core
│       ├── base/                 # Base classes
│       ├── cache/                # Cache system
│       ├── config/               # Configuration files
│       ├── media_platform/       # Platform implementations
│       │   ├── bilibili/        # Bilibili crawler
│       │   ├── douyin/          # Douyin crawler
│       │   ├── kuaishou/        # Kuaishou crawler
│       │   ├── tieba/           # Tieba crawler
│       │   ├── weibo/           # Weibo crawler
│       │   ├── xhs/             # Xiaohongshu crawler
│       │   └── zhihu/           # Zhihu crawler
│       ├── model/               # Data models
│       ├── proxy/               # Proxy management
│       ├── store/               # Storage layer
│       └── tools/               # Toolset
│
├── schema/                       # Database schema
│   ├── db_manager.py            # Database manager
│   ├── init_database.py         # Initialization script
│   └── mindspider_tables.sql    # Table structure definition
│
├── config.py                    # Global configuration file
├── main.py                      # System main entry
├── requirements.txt             # Dependency list
└── README.md                    # Project documentation
```

## System Workflow

### Overall Architecture Flow Chart

```mermaid
flowchart TB
    Start[Start] --> CheckConfig{Check Configuration}
    CheckConfig -->|Invalid| ConfigError[Configuration Error<br/>Please check environment variables in env]
    CheckConfig -->|Valid| InitDB[Initialize Database]
    
    InitDB --> BroadTopic[BroadTopicExtraction<br/>Topic Extraction Module]
    
    BroadTopic --> CollectNews[Collect Trending News]
    CollectNews --> |Multi-platform Collection| NewsSource{News Sources}
    NewsSource --> Weibo[Weibo Trending]
    NewsSource --> Zhihu[Zhihu Hot List]
    NewsSource --> Bilibili[Bilibili Trending]
    NewsSource --> Toutiao[Toutiao News]
    NewsSource --> Other[Other Platforms...]
    
    Weibo --> SaveNews[Save News to Database]
    Zhihu --> SaveNews
    Bilibili --> SaveNews
    Toutiao --> SaveNews
    Other --> SaveNews
    
    SaveNews --> ExtractTopic[AI Topic Extraction]
    ExtractTopic --> |DeepSeek API| GenerateKeywords[Generate Keyword List]
    GenerateKeywords --> GenerateSummary[Generate News Summary]
    GenerateSummary --> SaveTopics[Save Topic Data]
    
    SaveTopics --> DeepCrawl[DeepSentimentCrawling<br/>Deep Crawling Module]
    
    DeepCrawl --> LoadKeywords[Load Keywords]
    LoadKeywords --> PlatformSelect{Select Crawling Platform}
    
    PlatformSelect --> XHS[Xiaohongshu Crawler]
    PlatformSelect --> DY[Douyin Crawler]
    PlatformSelect --> KS[Kuaishou Crawler]
    PlatformSelect --> BILI[Bilibili Crawler]
    PlatformSelect --> WB[Weibo Crawler]
    PlatformSelect --> TB[Tieba Crawler]
    PlatformSelect --> ZH[Zhihu Crawler]
    
    XHS --> Login{Login Required?}
    DY --> Login
    KS --> Login
    BILI --> Login
    WB --> Login
    TB --> Login
    ZH --> Login
    
    Login -->|Yes| QRCode[QR Code Login]
    Login -->|No| Search[Keyword Search]
    QRCode --> Search
    
    Search --> CrawlContent[Crawl Content]
    CrawlContent --> ParseData[Parse Data]
    ParseData --> SaveContent[Save to Database]
    
    SaveContent --> MoreKeywords{More Keywords?}
    MoreKeywords -->|Yes| LoadKeywords
    MoreKeywords -->|No| GenerateReport[Generate Crawling Report]
    
    GenerateReport --> End[End]
    
    style Start fill:#90EE90
    style End fill:#FFB6C1
    style BroadTopic fill:#87CEEB,stroke:#000,stroke-width:3px
    style DeepCrawl fill:#DDA0DD,stroke:#000,stroke-width:3px
    style ExtractTopic fill:#FFD700
    style ConfigError fill:#FF6347
```

### Workflow Description

#### 1. BroadTopicExtraction (Topic Extraction Module)

This module is responsible for automatic discovery and extraction of daily trending topics:

1. **News Collection**: Automatically collect trending news from multiple mainstream platforms (Weibo, Zhihu, Bilibili, etc.)
2. **AI Analysis**: Use DeepSeek API for intelligent news analysis
3. **Topic Extraction**: Automatically identify trending topics and generate related keywords
4. **Data Storage**: Save topics and keywords to MySQL database

#### 2. DeepSentimentCrawling (Deep Crawling Module)

Based on extracted topic keywords, perform deep content crawling on major social platforms:

1. **Keyword Loading**: Read keywords extracted for the day from database
2. **Platform Crawling**: Use Playwright for automated crawling on 7 major platforms
3. **Content Parsing**: Extract posts, comments, interaction data, etc.
4. **Sentiment Analysis**: Conduct sentiment tendency analysis on crawled content
5. **Data Persistence**: Store all data in structured format in database

## Database Architecture

### Core Data Tables

1. **daily_news** - Daily News Table
   - Stores trending news collected from various platforms
   - Includes title, link, description, ranking, and other information

2. **daily_topics** - Daily Topics Table
   - Stores AI-extracted topics and keywords
   - Includes topic name, description, keyword list, etc.

3. **topic_news_relation** - Topic-News Relationship Table
   - Records associations between topics and news
   - Includes association score

4. **crawling_tasks** - Crawling Tasks Table
   - Manages crawling tasks for each platform
   - Records task status, progress, results, etc.

5. **Platform Content Tables** (inherited from MediaCrawler)
   - xhs_note - Xiaohongshu notes (temporarily deprecated, see details: https://github.com/NanmiCoder/MediaCrawler/issues/754)
   - douyin_aweme - Douyin videos
   - kuaishou_video - Kuaishou videos
   - bilibili_video - Bilibili videos
   - weibo_note - Weibo posts
   - tieba_note - Tieba posts
   - zhihu_content - Zhihu content

## Installation and Deployment

### System Requirements

- Python 3.9 or higher
- MySQL 5.7 or higher, or PostgreSQL
- Conda environment: pytorch_python11 (recommended)
- Operating System: Windows/Linux/macOS


### 1. Clone Project

```bash
git clone https://github.com/yourusername/MindSpider.git
cd MindSpider
```

### 2. Create and Activate Environment

#### Conda Configuration Method

```bash
# Create conda environment named pytorch_python11 with specified Python version
conda create -n pytorch_python11 python=3.11
# Activate the environment
conda activate pytorch_python11
```

#### UV Configuration Method

> [UV is a fast, lightweight Python package and environment management tool suitable for low dependency and convenient management needs. Refer to: https://github.com/astral-sh/uv]

- Install uv (if not installed)
```bash
pip install uv
```
- Create virtual environment and activate
```bash
uv venv --python 3.11 # Create Python 3.11 environment
source .venv/bin/activate   # Linux/macOS
# or
.venv\Scripts\activate      # Windows
```


### 3. Install Dependencies

```bash
# Install Python dependencies
pip install -r requirements.txt

or
# uv version for faster installation
uv pip install -r requirements.txt


# Install Playwright browser drivers
playwright install
```

### 4. Configure System

Copy .env.example file to .env file and place in project root directory. Edit the `.env` file and set database and API configuration:

```python
# MySQL database configuration
DB_HOST = "your_database_host"
DB_PORT = 3306
DB_USER = "your_username"
DB_PASSWORD = "your_password"
DB_NAME = "mindspider"
DB_CHARSET = "utf8mb4"

# MINDSPIDER API key
MINDSPIDER_BASE_URL=your_api_base_url
MINDSPIDER_API_KEY=sk-your-key
MINDSPIDER_MODEL_NAME=deepseek-chat
```

### 5. Initialize System

```bash
# Check system status
python main.py --status
```

## Usage Guide

### Complete Workflow

```bash
# 1. Run topic extraction (get trending news and keywords)
python main.py --broad-topic

# 2. Run crawler (crawl content from platforms based on keywords)
python main.py --deep-sentiment --test

# Or run complete workflow at once
python main.py --complete --test
```

### Using Modules Separately

```bash
# Only get today's trending topics and keywords
python main.py --broad-topic

# Only crawl specific platforms
python main.py --deep-sentiment --platforms xhs dy --test

# Specify date
python main.py --broad-topic --date 2024-01-15
```

## Crawler Configuration (Important)

### Platform Login Configuration

**First-time use requires login to each platform, this is the most critical step:**

1. **Xiaohongshu Login** (temporarily deprecated, see details: https://github.com/NanmiCoder/MediaCrawler/issues/754)
```bash
# Test Xiaohongshu crawling (QR code will pop up)
python main.py --deep-sentiment --platforms xhs --test
# Scan with Xiaohongshu APP to login, login status will be saved automatically
```

2. **Douyin Login**
```bash
# Test Douyin crawling
python main.py --deep-sentiment --platforms dy --test
# Scan with Douyin APP to login
```

3. **Other Platforms Similarly**
```bash
# Kuaishou
python main.py --deep-sentiment --platforms ks --test

# Bilibili
python main.py --deep-sentiment --platforms bili --test

# Weibo
python main.py --deep-sentiment --platforms wb --test

# Tieba
python main.py --deep-sentiment --platforms tieba --test

# Zhihu
python main.py --deep-sentiment --platforms zhihu --test
```

### Login Issue Troubleshooting

**If login fails or gets stuck:**

1. **Check Network**: Ensure normal access to the corresponding platform
2. **Disable Headless Mode**: Edit `DeepSentimentCrawling/MediaCrawler/config/base_config.py`
   ```python
   HEADLESS = False  # Change to False to see browser interface
   ```
3. **Manual Verification Handling**: Some platforms may require manual slider verification
4. **Re-login**: Delete `DeepSentimentCrawling/MediaCrawler/browser_data/` directory and login again

### Other Issues

https://github.com/666ghj/BettaFish/issues/185

### Crawling Parameter Adjustment

It's recommended to adjust crawling parameters before actual use:

```bash
# Small-scale testing (recommended to test this way first)
python main.py --complete --test

# Adjust crawling quantity
python main.py --complete --max-keywords 20 --max-notes 30
```

### Advanced Features

#### 1. Specify Date Operations
```bash
# Extract topics for specified date
python main.py --broad-topic --date 2024-01-15

# Crawl content for specified date
python main.py --deep-sentiment --date 2024-01-15
```

#### 2. Specify Platform Crawling
```bash
# Only crawl Bilibili and Douyin
python main.py --deep-sentiment --platforms bili dy --test

# Crawl specific quantity of content from all platforms
python main.py --deep-sentiment --max-keywords 30 --max-notes 20
```

## Common Parameters

```bash
--status              # Check project status
--setup               # Initialize project (deprecated, now auto-initialized)
--broad-topic         # Topic extraction
--deep-sentiment      # Crawler module
--complete            # Complete workflow
--test                # Test mode (limited data)
--platforms xhs dy    # Specify platforms
--date 2024-01-15     # Specify date
```

## Supported Platforms

| Code | Platform | Code | Platform |
|-----|-----|-----|-----|
| xhs | Xiaohongshu | wb | Weibo |
| dy | Douyin | tieba | Tieba |
| ks | Kuaishou | zhihu | Zhihu |
| bili | Bilibili | | |

## FAQ

### 1. Crawler Login Failure
```bash
# Issue: QR code doesn't display or login fails
# Solution: Disable headless mode and login manually
# Edit: DeepSentimentCrawling/MediaCrawler/config/base_config.py
HEADLESS = False

# Re-run login
python main.py --deep-sentiment --platforms xhs --test
```

### 2. Database Connection Failure
```bash
# Check configuration
python main.py --status

# Check if database configuration in config.py is correct
```

### 3. Playwright Installation Failure
```bash
# Reinstall
pip install playwright

or

uv pip install playwright

playwright install
```

### 4. Crawled Data is Empty
- Ensure platform login was successful
- Check if keywords exist (run topic extraction first)
- Use test mode to verify: `--test`

### 5. API Call Failure
- Check if DeepSeek API key is correct
- Confirm API quota is sufficient

## Notes

1. **First-time use must login to each platform**
2. **Recommended to use test mode first for verification**
3. **Comply with platform usage rules**
4. **For educational and research use only**

## Project Development Guide

### Extend New News Sources

Add new news sources in `BroadTopicExtraction/get_today_news.py`:

```python
async def get_new_platform_news(self) -> List[Dict]:
    """Get trending news from new platform"""
    # Implement news collection logic
    pass
```

### Extend New Crawler Platforms

1. Create new platform directory under `DeepSentimentCrawling/MediaCrawler/media_platform/`
2. Implement platform core function modules:
   - `client.py`: API client
   - `core.py`: Crawler core logic
   - `login.py`: Login logic
   - `field.py`: Data field definitions

### Database Extension

If you need to add new data tables or fields, please update `schema/mindspider_tables.sql` and run:

```bash
python schema/init_database.py
```

## Performance Optimization Recommendations

1. **Database Optimization**
   - Regularly clean historical data
   - Create indexes for high-frequency query fields
   - Consider using partitioned tables to manage large amounts of data

2. **Crawling Optimization**
   - Set appropriate crawling intervals to avoid being restricted
   - Use proxy pools to improve stability
   - Control concurrency to avoid resource exhaustion

3. **System Optimization**
   - Use Redis to cache hot data
   - Asynchronous task queues to handle time-consuming operations
   - Regularly monitor system resource usage

## API Interface Description

The system provides Python API for secondary development:

```python
from BroadTopicExtraction import BroadTopicExtraction
from DeepSentimentCrawling import DeepSentimentCrawling

# Topic extraction
async def extract_topics():
    extractor = BroadTopicExtraction()
    result = await extractor.run_daily_extraction()
    return result

# Content crawling
def crawl_content():
    crawler = DeepSentimentCrawling()
    result = crawler.run_daily_crawling(
        platforms=['xhs', 'dy'],
        max_keywords=50,
        max_notes=30
    )
    return result
```

## License

This project is for educational and research use only and should not be used for commercial purposes. When using this project, please comply with relevant laws and regulations and platform terms of service.

---

**MindSpider** - Empowering public opinion insights with AI, your reliable assistant for intelligent content analysis
