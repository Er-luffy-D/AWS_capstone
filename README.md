# 🚀 Crypto Price Tracker - AWS Capstone Project

A real-time cryptocurrency price tracking application built with Flask, featuring price alerts and watchlist functionality. This application can run both locally and on AWS infrastructure using DynamoDB, SNS, and Elastic Beanstalk.

## 📋 Table of Contents

- [About the Project](#about-the-project)
- [Features](#features)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [AWS Deployment](#aws-deployment)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Contributing](#contributing)

## 🎯 About the Project

The Crypto Price Tracker is a full-stack web application designed to help users monitor cryptocurrency prices in real-time. Users can create watchlists, set price alerts, and receive notifications when their target prices are reached. The application demonstrates cloud-native development using AWS services.

### How It Works

1. **User Authentication**: Secure signup and login system with password hashing
2. **Real-time Price Fetching**: Integrates with CoinGecko API to fetch current Bitcoin and Ethereum prices
3. **Watchlist Management**: Users can add cryptocurrencies to their personal watchlist
4. **Price Alerts**: Set target prices and receive notifications via AWS SNS when prices drop below thresholds
5. **Price History**: Stores historical price data in DynamoDB for trend analysis
6. **Dual Mode**: Supports both local development (in-memory storage) and production AWS deployment (DynamoDB + SNS)

## ✨ Features

- **User Registration & Authentication**
  - Secure signup with email and password
  - Password hashing using Werkzeug security
  - Session-based authentication

- **Real-time Cryptocurrency Tracking**
  - Live prices for Bitcoin and Ethereum
  - Automatic price updates via CoinGecko API
  - Price history tracking

- **Watchlist Functionality**
  - Add cryptocurrencies to personal watchlist
  - Quick access to favorite coins
  - Persistent storage (AWS DynamoDB)

- **Price Alerts**
  - Set custom price thresholds for any tracked cryptocurrency
  - Automatic alert triggering when prices drop
  - SNS notifications for triggered alerts
  - Alert status tracking to prevent duplicate notifications

- **Responsive Dashboard**
  - Clean, user-friendly interface
  - Real-time price display
  - Triggered alerts overview
  - Watchlist management

## 🛠️ Technology Stack

### Backend
- **Python 3.x** - Core programming language
- **Flask** - Web framework
- **Gunicorn** - WSGI HTTP Server for production

### Frontend
- **HTML5** - Structure
- **CSS3** - Styling
- **Jinja2** - Template engine

### APIs & Services
- **CoinGecko API** - Cryptocurrency price data
- **AWS DynamoDB** - NoSQL database for users, watchlists, alerts, and price history
- **AWS SNS** - Push notifications for price alerts
- **AWS Elastic Beanstalk** - Application deployment and hosting

### Development Tools
- **Boto3** - AWS SDK for Python
- **Requests** - HTTP library for API calls
- **Werkzeug** - Security utilities for password hashing

## 📁 Project Structure

```
AWS_capstone/
│
├── .ebextensions/              # AWS Elastic Beanstalk configuration
│   └── python.config           # Python environment settings
│
├── static/                     # Static files (CSS, JS, images)
│   └── style.css              # Application styles
│
├── templates/                  # HTML templates
│   ├── index.html             # Landing page
│   ├── login.html             # Login page
│   ├── signup.html            # Registration page
│   └── dashboard.html         # User dashboard
│
├── app.py                      # Local development version (in-memory storage)
├── aws_app.py                  # Production version (AWS services)
├── config.py                   # Configuration management
├── requirements.txt            # Python dependencies
├── .gitignore                 # Git ignore rules
└── README.md                  # Project documentation
```

### Key Files

- **`app.py`**: Standalone version for local development using in-memory storage
- **`aws_app.py`**: Production-ready version integrated with AWS services (DynamoDB, SNS)
- **`config.py`**: Centralized configuration with environment variables
- **`.ebextensions/python.config`**: AWS Elastic Beanstalk deployment configuration

## 📋 Prerequisites

### For Local Development
- Python 3.7 or higher
- pip (Python package installer)
- Virtual environment (recommended)

### For AWS Deployment
- AWS Account with appropriate permissions
- AWS CLI configured
- Elastic Beanstalk CLI (EB CLI)
- DynamoDB tables created:
  - `Users` (Partition Key: email)
  - `Watchlist` (Partition Key: email, Sort Key: coin_id)
  - `Alerts` (Partition Key: email, Sort Key: coin_id)
  - `MarketPrices` (Partition Key: coin, Sort Key: timestamp)
- SNS Topic created (optional, for email notifications)

## 🚀 Installation

### 1. Clone the Repository

```bash
git clone https://github.com/Er-luffy-D/AWS_capstone.git
cd AWS_capstone
```

### 2. Create Virtual Environment

```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in the root directory (for local testing with AWS services):

```bash
# Flask Configuration
SECRET_KEY=your-secret-key-here

# AWS Configuration
AWS_REGION=us-east-1

# DynamoDB Tables
DYNAMODB_TABLE_USERS=Users
DYNAMODB_TABLE_WATCHLIST=Watchlist
DYNAMODB_TABLE_ALERTS=Alerts
DYNAMODB_TABLE_PRICES=MarketPrices

# SNS Configuration (optional)
SNS_TOPIC_ARN=arn:aws:sns:us-east-1:YOUR_ACCOUNT_ID:crypto-alerts
```

### AWS Setup

#### 1. Create DynamoDB Tables

**Users Table:**
```bash
aws dynamodb create-table \
    --table-name Users \
    --attribute-definitions AttributeName=email,AttributeType=S \
    --key-schema AttributeName=email,KeyType=HASH \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

**Watchlist Table:**
```bash
aws dynamodb create-table \
    --table-name Watchlist \
    --attribute-definitions \
        AttributeName=email,AttributeType=S \
        AttributeName=coin_id,AttributeType=S \
    --key-schema \
        AttributeName=email,KeyType=HASH \
        AttributeName=coin_id,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

**Alerts Table:**
```bash
aws dynamodb create-table \
    --table-name Alerts \
    --attribute-definitions \
        AttributeName=email,AttributeType=S \
        AttributeName=coin_id,AttributeType=S \
    --key-schema \
        AttributeName=email,KeyType=HASH \
        AttributeName=coin_id,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

**MarketPrices Table:**
```bash
aws dynamodb create-table \
    --table-name MarketPrices \
    --attribute-definitions \
        AttributeName=coin,AttributeType=S \
        AttributeName=timestamp,AttributeType=S \
    --key-schema \
        AttributeName=coin,KeyType=HASH \
        AttributeName=timestamp,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

#### 2. Create SNS Topic (Optional)

```bash
aws sns create-topic --name crypto-alerts --region us-east-1
```

Then subscribe your email:
```bash
aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:YOUR_ACCOUNT_ID:crypto-alerts \
    --protocol email \
    --notification-endpoint your-email@example.com
```

## 🏃 Running the Application

### Local Development (Without AWS)

Use `app.py` for quick local testing with in-memory storage:

```bash
python app.py
```

The application will start on `http://127.0.0.1:5000/`

### Local Development (With AWS Services)

Use `aws_app.py` to test with actual AWS services locally:

```bash
# Ensure AWS credentials are configured
aws configure

# Set environment variables (or use .env file)
export SECRET_KEY=your-secret-key
export AWS_REGION=us-east-1
# ... (set other variables)

# Run the application
python aws_app.py
```

The application will start on `http://0.0.0.0:5000/`

## ☁️ AWS Deployment

### Deploy to Elastic Beanstalk

#### 1. Initialize Elastic Beanstalk

```bash
eb init -p python-3.9 crypto-tracker --region us-east-1
```

#### 2. Create Environment

```bash
eb create crypto-tracker-env
```

#### 3. Set Environment Variables

```bash
eb setenv SECRET_KEY=your-production-secret-key \
    AWS_REGION=us-east-1 \
    DYNAMODB_TABLE_USERS=Users \
    DYNAMODB_TABLE_WATCHLIST=Watchlist \
    DYNAMODB_TABLE_ALERTS=Alerts \
    DYNAMODB_TABLE_PRICES=MarketPrices \
    SNS_TOPIC_ARN=arn:aws:sns:us-east-1:YOUR_ACCOUNT_ID:crypto-alerts
```

#### 4. Deploy Application

```bash
eb deploy
```

#### 5. Open Application

```bash
eb open
```

### Important AWS Permissions

Ensure your Elastic Beanstalk instance role has the following permissions:
- DynamoDB: `PutItem`, `GetItem`, `Query`, `UpdateItem`
- SNS: `Publish`

## 📖 Usage

### 1. Create an Account
- Navigate to the home page
- Click "Sign Up"
- Enter your email and password
- Submit the form

### 2. Login
- Click "Login" from the home page
- Enter your credentials
- You'll be redirected to the dashboard

### 3. View Cryptocurrency Prices
- The dashboard displays current prices for Bitcoin and Ethereum
- Prices are fetched in real-time from CoinGecko API

### 4. Add to Watchlist
- On the dashboard, use the "Add to Watchlist" form
- Enter the cryptocurrency name (bitcoin or ethereum)
- Click "Add"

### 5. Set Price Alerts
- Use the "Set Alert" form on the dashboard
- Enter the cryptocurrency name
- Enter your target price
- When the price drops below your target, you'll receive an alert
- If SNS is configured, you'll also receive an email notification

### 6. View Triggered Alerts
- Triggered alerts appear at the top of the dashboard
- Shows which cryptocurrencies hit your target prices

### 7. Logout
- Click the "Logout" button to end your session

## 🔌 API Endpoints

| Method | Endpoint | Description | Authentication |
|--------|----------|-------------|----------------|
| GET | `/` | Landing page | No |
| GET/POST | `/signup` | User registration | No |
| GET/POST | `/login` | User authentication | No |
| GET | `/dashboard` | User dashboard with prices and alerts | Yes |
| POST | `/add_to_watchlist` | Add cryptocurrency to watchlist | Yes |
| POST | `/set_alert` | Create price alert | Yes |
| GET | `/logout` | End user session | Yes |

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the project
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is part of an AWS Capstone project for educational purposes.

## 👨‍💻 Author

**Er-luffy-D**

## 🙏 Acknowledgments

- CoinGecko API for cryptocurrency price data
- AWS for cloud infrastructure services
- Flask framework and community

---

**Note**: Remember to never commit sensitive information like AWS credentials, secret keys, or API tokens to version control. Always use environment variables or AWS Secrets Manager for sensitive data.
