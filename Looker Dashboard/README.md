# YouTube Channel Data Analysis - Calculated Fields Documentation

This document outlines the calculated fields necessary for analyzing YouTube channel data in Looker Studio. These fields will help you track growth rates, engagement metrics, and channel performance.

---

## 1. Growth Rate Calculations

These fields calculate the percentage growth in views, subscribers, and uploads between the current and new data.

- **View Growth (%)**  
   **Formula:**  
   ```sql
   ((New View Count - View Count) / View Count) * 100
   ```
   **Description:** Measures the percentage increase in views compared to the previous period.

- **Subscriber Growth (%)**  
   **Formula:**  
   ```sql
   ((New Subscribers Count - Subscribers Count) / Subscribers Count) * 100
   ```
   **Description:** Measures the percentage growth in subscribers compared to the previous period.

- **Upload Growth (%)**  
   **Formula:**  
   ```sql
   ((New Total Uploads - Total Uploads) / Total Uploads) * 100
   ```
   **Description:** Measures the percentage growth in the total number of uploads compared to the previous period.

---

## 2. Engagement Ratios

These fields help you evaluate the engagement levels of channels by calculating different ratios between views, subscribers, and uploads.

- **Subscriber-to-View Ratio**  
   **Formula:**  
   ```sql
   View Count / Subscribers Count
   ```
   **Description:** Shows how many views the channel gets for each subscriber, indicating the engagement level.

- **Views per Upload**  
   **Formula:**  
   ```sql
   View Count / Total Uploads
   ```
   **Description:** Measures the average number of views each upload gets.

- **Subscribers per Upload**  
   **Formula:**  
   ```sql
   Subscribers Count / Total Uploads
   ```
   **Description:** Measures how many subscribers a channel gains per upload on average.

---

## 3. Channel Size Segmentation

This field categorizes channels based on their subscriber count, allowing for segmentation by audience size.

- **Subscriber Size Segment**  
   **Formula:**  
   ```sql
   CASE 
     WHEN Subscribers Count < 500000 THEN "Small"
     WHEN Subscribers Count BETWEEN 500000 AND 1000000 THEN "Medium"
     WHEN Subscribers Count BETWEEN 1000000 AND 5000000 THEN "Large"
     ELSE "Very Large"
   END
   ```
   **Description:** Groups channels into categories based on their subscriber count (Small, Medium, Large, Very Large).

---

## 4. Channel Region/Language Categorization

This field classifies channels by region or language based on keywords found in the channel description.

- **Region**  
   **Formula:**  
   ```sql
   CASE 
     WHEN Channel Description LIKE "%Telugu%" THEN "Telugu"
     WHEN Channel Description LIKE "%Tamil%" THEN "Tamil"
     WHEN Channel Description LIKE "%Marathi%" THEN "Marathi"
     WHEN Channel Description LIKE "%Gujarati%" THEN "Gujarati"
     ELSE "Other"
   END
   ```
   **Description:** Categorizes channels based on the language or region they focus on, derived from their descriptions.

---

## 5. New vs. Existing Metrics Comparison

These fields calculate the absolute changes in views, subscribers, and uploads between the current and new data.

- **View Count Change**  
   **Formula:**  
   ```sql
   New View Count - View Count
   ```
   **Description:** Calculates the increase in total views.

- **Subscriber Change**  
   **Formula:**  
   ```sql
   New Subscribers Count - Subscribers Count
   ```
   **Description:** Calculates the increase in subscribers.

- **Uploads Change**  
   **Formula:**  
   ```sql
   New Total Uploads - Total Uploads
   ```
   **Description:** Calculates the increase in the number of uploads.

---

## 6. Performance Score

This field provides a composite metric to rank channels based on a combination of views, subscribers, and uploads.

- **Performance Score**  
   **Formula:**  
   ```sql
   (View Count * 0.5) + (Subscribers Count * 0.4) + (Total Uploads * 0.1)
   ```
   **Description:** A weighted score that gives 50% weight to views, 40% to subscribers, and 10% to uploads. Useful for ranking channels by overall performance.

---

## 7. Engagement per Subscriber

This field measures the number of views per subscriber, helping to identify highly engaged channels.

- **Views per Subscriber**  
   **Formula:**  
   ```sql
   View Count / Subscribers Count
   ```
   **Description:** Shows the average number of views each subscriber contributes, giving an indication of engagement.

---

## 8. Optional Calculations

Additional fields that may be useful for further analysis.

- **Average Views per Subscriber Growth**  
   **Formula:**  
   ```sql
   (New View Count / New Subscribers Count)
   ```
   **Description:** Measures the average number of views gained for each new subscriber.

---

## Summary

By creating these calculated fields in Looker Studio, you can unlock deeper insights into the performance of YouTube channels, track growth trends, and compare channels on metrics like engagement, performance, and region. These fields will enable you to generate detailed and meaningful visualizations to inform decision-making.

This documentation can be referenced when setting up Looker Studio reports and serves as a guide for calculated field creation and data transformation.
