# How to Solve CAPTCHA Using selenium Python

CAPTCHAs are essential tools for securing websites against bots, but developers often need to solve them programmatically for testing or research. In this article, we'll guide you through a Python-based approach to solving CAPTCHAs using the CaptchaSonic Chrome Extension and Selenium.
#
Overview
The solution involves:

Downloading and configuring the CaptchaSonic Chrome extension.
Automating browser interactions using Selenium WebDriver.
Using an API key to communicate with the CaptchaSonic service.
#
Step-by-Step Guide
1. Prerequisites
Before diving into the code, ensure you have the following:

- Python installed on your system.
- Google Chrome and the appropriate ChromeDriver version.
- An API key from CaptchaSonic.
- The dotenv, selenium, and other required libraries installed:

```
pip install python-dotenv selenium

```
2. Code for Solving CAPTCHA
Here’s a Python script to automate CAPTCHA solving:
```python
import os
import zipfile
import urllib.request
import json
from dotenv import load_dotenv
from selenium import webdriver
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC

# Load environment variables
load_dotenv()

def download_and_extract_extension(url, extract_to):
    """Downloads and extracts a ZIP file."""
    zip_path = os.path.join(extract_to, 'extension.zip')
    print(f"Downloading extension from {url} to {zip_path}")
    urllib.request.urlretrieve(url, zip_path)
    with zipfile.ZipFile(zip_path, 'r') as zip_ref:
        zip_ref.extractall(extract_to)
    os.remove(zip_path)
    print(f"Extracted extension to {extract_to}")
    return extract_to

def update_config_with_api_key(config_path, api_key):
    """Updates the API key in the extension's configuration file."""
    print(f"Updating config at {config_path} with API key")
    with open(config_path, 'r') as file:
        config = json.load(file)
    config['APIKEY'] = api_key
    with open(config_path, 'w') as file:
        json.dump(config, file, indent=2)

def main():
    # Define directories and URLs
    current_dir = os.getcwd()
    extension_url = 'https://github.com/Captcha-Sonic/CaptchaSonic-Extension/releases/download/v0.1.3/CaptchaSonic_Chrome_v0.1.3.zip'
    extension_dir = download_and_extract_extension(extension_url, current_dir)
    config_path = os.path.join(extension_dir, 'assets', 'defaultConfig.json')
    api_key = os.getenv('APIKEY')  # Load API key from environment variable
    
    # Update configuration
    update_config_with_api_key(config_path, api_key)

    # Check for manifest.json file
    manifest_path = os.path.join(extension_dir, 'manifest.json')
    if not os.path.exists(manifest_path):
        print(f"Manifest file not found at {manifest_path}")
    else:
        print(f"Manifest file found at {manifest_path}")

    # Set up Selenium with the CaptchaSonic extension
    chrome_options = Options()
    chrome_options.add_argument(f'--disable-extensions-except={extension_dir}')
    chrome_options.add_argument(f'--load-extension={extension_dir}')
    service = Service()  # Specify the chromedriver path if needed

    with webdriver.Chrome(service=service, options=chrome_options) as driver:
        driver.get('https://accounts.hcaptcha.com/demo')
        # Wait for the page to load
        WebDriverWait(driver, 20).until(
            EC.presence_of_element_located((By.TAG_NAME, "body"))
        )
        # Save a screenshot for verification
        driver.save_screenshot('example.png')

if __name__ == "__main__":
    main()
```
##
Explanation of the Code
Downloading the CaptchaSonic Extension:
The script downloads the extension as a ZIP file, extracts it, and prepares it for use.

Configuring the Extension:
The API key is added to the extension's configuration file, enabling communication with CaptchaSonic.

Selenium Setup:

The Chrome browser is launched with the CaptchaSonic extension.
Selenium automates browser actions, like visiting a test CAPTCHA page.
Interacting with CAPTCHA:
The CaptchaSonic extension handles CAPTCHA solving.
##

How to Use the Script
* Set the API Key:
* Create a .env file in the same directory as the script and add:

```
APIKEY=your_api_key

```

* Run the Script:
* Execute the script with:

```
python script_name.py
```
