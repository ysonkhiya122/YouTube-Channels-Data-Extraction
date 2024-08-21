# YouTube Channel Data Analysis

## Project Overview
This project aims to gather and analyze data for the top 50 YouTube channels associated with each ABP network channel across different languages. The objective is to clean and preprocess the raw data, extract essential channel information using the YouTube Data API, and compile the final dataset into a structured format for further analysis.

## Problem Statement
The goal is to collect, clean, and process data for the top 50 YouTube channels in various languages related to ABP network channels. The project involves automating data extraction, cleaning, processing, and finally, compiling the data into a Google Sheet for further analysis.

## Prerequisites
To run this project, the following tools and platforms are required:

1. **Google Cloud Console**: Manage YouTube Data API credentials and access the API.
2. **Python**: For scripting and automating data extraction, cleaning, and processing tasks.
3. **Browser AI**: Used for extracting the list of top channels from YouTube search results.
   - [Browser AI](https://www.browse.ai/)
   - [Tutorial](https://youtu.be/13jp3m1JUAs)
4. **YouTube Data v3 API**: To fetch Channel IDs and additional insights like View Count and Subscribers Count.
5. **Google Colab Notebook**: For running Python code and performing data analysis collaboratively.
6. **Google Drive**: For storing raw data, cleaned data, and processed results.
7. **Google Sheets**: For compiling and presenting the final dataset in a structured format.

## Procedure

### 1. Data Extraction and Collection
**Objective**: Extract data for the top 50 YouTube channels for each of the ABP network's language-specific channels using Browser AI.

**Steps**:
1. Use Browser AI to train a bot to extract selected data such as channel name, handle, subscribers, description, URL, and image.
2. Use the bulk extract function to gather data for 8 language segments.
3. Store the extracted data in CSV format, with each file containing data for 50 channels per language segment.

---

### 2. Data Cleaning and Preprocessing
**Objective**: Clean and preprocess the raw data collected from YouTube to ensure accuracy and consistency.

**Code Snippet**:
```python
import pandas as pd

def clean_file(file_path):
    try:
        df = pd.read_csv(file_path, encoding='utf-8')
    except Exception as e:
        print(f"Error loading {file_path}: {e}")
        return None

    columns_to_remove = ['Task Link', 'Origin URL', 'YouTube Bengali News Channels Limit', 'Position']
    df = df.drop(columns=[col for col in columns_to_remove if col in df.columns], errors='ignore')

    if 'Channel Handle' in df.columns:
        df['Channel Handle'] = df['Channel Handle'].str.replace('@', '', regex=False)
    if 'Subscribers' in df.columns:
        df['Subscribers'] = df['Subscribers'].str.replace(' subscribers', '', regex=False)
        df['Subscribers'] = df['Subscribers'].apply(convert_subscribers_to_numeric)

    def fix_encoding(text):
        if isinstance(text, str):
            return text.encode('utf-8', errors='ignore').decode('utf-8')
        return text

    if 'Channel Description' in df.columns:
        df['Channel Description'] = df['Channel Description'].apply(fix_encoding)

    return df
```

**Steps**:
1. Remove unnecessary columns: Task Link, Origin URL, YouTube Bengali News Channels Limit, and Position.
2. Clean text fields:
   - Remove '@' from the Channel Handle column.
   - Remove 'subscribers' from the Subscribers column and convert it to an integer.
3. Fix encoding issues in the Channel Description column.
4. Process all CSV files in the input folder, clean them, and save the results to the output folder.

---

### 3. Channel ID Extraction
**Objective**: Use the YouTube Data API v3 to fetch the Channel ID for each channel.

**Code Snippet**:
```python
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError

def get_channel_id(youtube, channel_handle):
    try:
        request = youtube.search().list(
            part='snippet',
            q=channel_handle,
            type='channel',
            maxResults=1
        )
        response = request.execute()

        if 'items' in response and response['items']:
            return response['items'][0]['id']['channelId']
        return None
    except HttpError as e:
        print(f"HTTP Error occurred: {e}")
        return None
    except Exception as e:
        print(f"An error occurred: {e}")
        return None
```

**Steps**:
1. Fetch Channel ID for each channel using the YouTube Data API v3.
2. Create a new file storing the current date, channel name, handle, and extracted Channel ID.

---

### 4. Data Extraction from API
**Objective**: Extract additional insights for each channel using the Channel ID, including View Count, Total Uploads, and Subscribers Count.

**Code Snippet**:
```python
def get_channel_data(youtube, channel_id):
    try:
        request = youtube.channels().list(
            part='statistics',
            id=channel_id
        )
        response = request.execute()

        if 'items' in response and response['items']:
            stats = response['items'][0]['statistics']
            return {
                'View Count': stats.get('viewCount', 'N/A'),
                'Total Uploads': stats.get('videoCount', 'N/A'),
                'Subscribers Count': stats.get('subscriberCount', 'N/A')
            }
        return {'View Count': 'N/A', 'Total Uploads': 'N/A', 'Subscribers Count': 'N/A'}
    except HttpError as e:
        print(f"HTTP Error occurred: {e}")
        return {'View Count': 'N/A', 'Total Uploads': 'N/A', 'Subscribers Count': 'N/A'}
    except Exception as e:
        print(f"An error occurred: {e}")
        return {'View Count': 'N/A', 'Total Uploads': 'N/A', 'Subscribers Count': 'N/A'}
```

**Steps**:
1. Extract insights like View Count, Total Uploads, and Subscribers Count using the Channel ID.
2. Update the dataset with these insights, ensuring data integrity.

---

### 5. Data Compilation and Integration
**Objective**: Combine and append the processed data from all 8 language-specific files. Structure the final dataset in a Google Sheet with specific columns.

**Code Snippet**:
```python
import gspread
from oauth2client.service_account import ServiceAccountCredentials

def open_google_sheet(client, sheet_name):
    try:
        sheet = client.open(sheet_name).sheet1  # Select the first sheet
        return sheet
    except WorksheetNotFound:
        print(f"Google Sheet named '{sheet_name}' not found.")
        raise
    except APIError as e:
        print(f"API Error while accessing Google Sheet: {e}")
        raise
    except Exception as e:
        print(f"Failed to open Google Sheet: {e}")
        raise

def append_csv_to_sheet(csv_folder_path, sheet):
    def clean_large_floats(value):
        try:
            if isinstance(value, float) and not (-1e308 < value < 1e308):
                return str(value)
            return value
        except:
            return value

    for filename in os.listdir(csv_folder_path):
        if filename.endswith('.csv'):
            file_path = os.path.join(csv_folder_path, filename)
            try:
                df = pd.read_csv(file_path)
                
                for col in df.columns:
                    df[col] = df[col].map(clean_large_floats)

                data = df.values.tolist()

                sheet.append_rows(data)
                print(f"Successfully appended data from '{filename}' to the sheet.")
```

**Steps**:
1. Authenticate with Google Sheets API using service account credentials.
2. Open the target Google Sheet where the final data will be compiled.
3. Append processed data from all CSV files into the Google Sheet, ensuring that the data is structured with columns for Current Date, Channel Name, Channel Handle, Channel ID, View Count, Total Uploads, Subscribers Count, Channel Description, Channel URL, and Image URL.

---

## Conclusion
This document outlines the procedures and code snippets for each phase of the project, providing clear guidance on data extraction, cleaning, processing, and integration. The outcome is a comprehensive dataset that can be used for further analysis.


This `README.md` file provides a clear and structured overview of the project, detailing the objectives, procedures, and code snippets required for each step of the YouTube Channel Data Analysis.
