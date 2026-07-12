# 🗄️ Pixie Database Schema Documentation

## Overview

The Pixie database is designed to support user authentication, cat reporting, AI-powered image matching, image management, and user notifications.

To avoid redundancy, both **Lost Cats** and **Stray Cats** are stored in a single **Reports** table using the **report_type** attribute. This design simplifies data management, improves scalability, and allows the AI system to search across all reports efficiently.

---

# 1. Users Table

The **Users** table stores information about all registered users who use the Pixie platform. Every user has a unique account that allows them to create reports, receive notifications, and manage their personal information.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **id** | A unique identifier for each user. It serves as the primary key and is automatically generated. |
| **full_name** | Stores the user's full name. |
| **email** | Stores the user's email address. This field must be unique since it is used during authentication. |
| **password_hash** | Stores the encrypted password instead of the plain text password to improve security. |
| **phone** | Stores the user's phone number for communication purposes. |
| **city** | Stores the city where the user lives. |
| **created_at** | Stores the date and time when the user account was created. |
| **updated_at** | Stores the last date and time the user's information was updated. |

---

# 2. Reports Table

The **Reports** table is the core table of the Pixie system. It stores every report submitted by users, whether the report is about a **lost cat** or a **stray cat**.

Instead of creating two separate tables, a single table is used with the **report_type** attribute to distinguish between report types.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **id** | A unique identifier for each report. |
| **user_id** | A foreign key that references the user who created the report. |
| **report_type** | Determines whether the report is **LOST** or **STRAY**. |
| **title** | Stores a short title describing the report. |
| **description** | Stores additional details about the cat. |
| **breed** | Stores the breed entered manually by the user. |
| **ai_predicted_breed** | Stores the breed predicted by the AI model after image analysis. |
| **ai_confidence** | Stores the confidence score of the AI prediction as a decimal value between **0** and **1**. |
| **gender** | Stores the cat's gender (**MALE**, **FEMALE**, or **UNKNOWN**). |
| **age** | Stores the estimated age of the cat. |
| **color** | Stores the primary color or coat pattern of the cat. |
| **last_seen_location** | Stores a textual description of where the cat was last seen. |
| **location_name** | Stores a human-readable location name displayed in the application. |
| **latitude** | Stores the latitude coordinate of the report location. |
| **longitude** | Stores the longitude coordinate of the report location. |
| **status** | Represents the report status (**OPEN**, **FOUND**, or **CLOSED**). |
| **is_ai_verified** | Indicates whether the AI has successfully verified or matched this report. |
| **is_active** | Determines whether the report is currently visible in the application. |
| **created_at** | Stores the report creation date. |
| **updated_at** | Stores the last modification date. |

---

# 3. Images Table

The **Images** table stores all images uploaded for each report.

A single report may contain multiple images, allowing users to upload different views of the same cat.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **id** | A unique identifier for each image. |
| **report_id** | A foreign key referencing the report to which the image belongs. |
| **image_url** | Stores the image URL or storage path. |
| **is_primary** | Indicates whether this image is the primary image displayed for the report. |
| **created_at** | Stores the upload date of the image. |
| **updated_at** | Stores the last modification date. |

---

# 4. Matches Table

The **Matches** table stores AI-generated similarity matching results.

Whenever a user scans a cat using the **Find a Cat** feature, the AI compares the uploaded image against existing reports. If similar cats are found, the matching information is stored in this table.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **id** | A unique identifier for each AI match. |
| **query_report_id** | References the report that initiated the AI search. |
| **matched_report_id** | References the report determined by the AI to be similar. |
| **similarity_score** | Stores the similarity score between two reports as a value between **0** and **1**. |
| **created_at** | Stores the date when the match was generated. |
| **updated_at** | Stores the last modification date. |

---

# 5. Notifications Table

The **Notifications** table stores notifications sent to users regarding important events within the application.

Examples include:

- A similar cat has been found.
- An AI match has been generated.
- A report has been updated.

## Attributes

| Attribute | Description |
|-----------|-------------|
| **id** | A unique identifier for each notification. |
| **user_id** | A foreign key referencing the notification recipient. |
| **title** | Stores the notification title. |
| **message** | Stores the notification content. |
| **is_read** | Indicates whether the notification has been read by the user. |
| **created_at** | Stores the notification creation date. |
| **updated_at** | Stores the last modification date. |

---

# 🔗 Table Relationships

## Users → Reports (One-to-Many)

One user can create multiple reports, while each report belongs to exactly one user. This relationship is established through the **user_id** foreign key in the **Reports** table.

---

## Reports → Images (One-to-Many)

Each report can contain multiple images, while every image belongs to only one report.

---

## Reports → Matches (One-to-Many)

A report may participate in multiple AI similarity matches. The **Matches** table connects two reports using **query_report_id** and **matched_report_id**, allowing the AI system to store potential matches.

---

## Users → Notifications (One-to-Many)

Each user can receive multiple notifications, while each notification belongs to only one user.

---

# 💡 Database Design Rationale

The database follows normalization principles to reduce redundancy while maintaining simplicity and scalability.

Instead of separating lost cat reports and stray cat reports into different tables, both are stored in a unified **Reports** table and distinguished using the **report_type** attribute. This design simplifies queries, minimizes duplicate structures, and enables the AI-powered matching system to search across all reports efficiently.

The **Images** table allows multiple images to be associated with a single report, improving both the user experience and AI recognition accuracy. The **Matches** table stores AI-generated similarity results, supporting the **Find a Cat** feature by linking newly scanned cats with existing reports. Finally, the **Notifications** table keeps users informed about important events, such as AI matches and report updates.