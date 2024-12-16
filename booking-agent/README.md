# Cyber App Workflow - README

## Overview
This repository provides a comprehensive guide to setting up and running the Cyber App workflow in your n8n environment. The Cyber App workflow is a robust chatbot system designed to:

- **Collect Leads**: Capture and store user details from your website.
- **Register User Information**: Seamlessly save user data to a connected database or platform. In this case, we are using Google Sheets, but you can use Airtable or any other platform.
- **Book Appointments and Calls**: Leverage calendar integration to automate scheduling tasks.

By utilizing the workflows in this repository, you can create a fully automated process to manage user interactions effectively. A chatbot that can respond to user inquiries from document information (RAG), collect leads, and book consultations.

---

## Contents
The project directory includes the following critical workflow files:

1. **cyber-app-workflow.json**: The main workflow containing the chatbot trigger and logic.
2. **calendar-and-booking.json**: A secondary workflow responsible for managing and scheduling appointments using calendar integration.
3. **ai-register.json**: A secondary workflow that handles user registration and updates the database with collected details.

These files are essential for setting up a functional automation pipeline.

---

## Prerequisites
Before proceeding, ensure you meet the following requirements:

1. **n8n Setup**: Install and configure n8n on your system. Refer to the [n8n installation guide](https://docs.n8n.io/getting-started/installation/).
2. **Google API Credentials**:
   - Set up a project in the Google Cloud Console.
   - Enable APIs for Google Calendar and Google Drive.
   - Download your credentials JSON file and upload it to your n8n instance.
3. **Superbase Account**:
   - Create an account and configure a database for user data storage.
   - Alternatively, use another database system like Pinecone if preferred.

---

## Setup Instructions
### Step 1: Import Workflows
1. Log in to your n8n instance.
2. Navigate to **Settings > Import Workflows**.
3. Import the following files one by one:
   - `cyber-app-workflow.json`
   - `calendar-and-booking.json`
   - `ai-register.json`

### Step 2: Configure Credentials
Ensure the following credentials are configured in your n8n environment:
- **Google API**:
  - Grant permissions for accessing Google Drive and Calendar.
  - Ensure your drive directory is set up to allow updates and triggers.
- **Database Credentials**:
  - Configure access credentials for Superbase or your chosen database platform.
  - Test connectivity to ensure proper setup.

### Step 3: Workflow Activation
- Once imported, activate the workflows.
- Check the connections between the workflows to ensure seamless data flow.

---

## How the Workflow Operates

### Cyber App Workflow
- **Trigger**: The workflow starts with a chatbot trigger embedded on your website.
- **Lead Collection**: Captures user input (e.g., name, email, and inquiry).
- **User Registration**: Sends the collected data to the `ai-register.json` workflow for processing.

### Calendar and Booking Workflow
- **Date Management**: Retrieves the current date and checks for available slots.
- **Appointment Booking**: Automatically schedules appointments or calls based on user preferences.

### AI Register Workflow
- **Database Updates**: Handles the registration of user information by saving data to a connected database.
- **Integration**: Ensures seamless updates between workflows.

---

## Additional Configuration

### Google Drive Setup
1. Set up a dedicated Google Drive directory for file storage and management.
2. Grant the necessary permissions for workflow triggers based on file updates.

### Manual and Automated Triggers
- The workflows are designed for full automation but can also be triggered manually as needed. Use the n8n interface to test and validate individual nodes.

### Customization Options
- If Superbase is unavailable, consider integrating other database platforms like MongoDB, Firebase, or PostgreSQL.
- Modify workflow nodes to align with your specific use case.

---

## Troubleshooting
- **Connection Issues**: Verify that your Google API credentials are valid and correctly configured.
- **Database Errors**: Check the database connection strings and ensure the target tables exist.
- **Workflow Errors**: Use the n8n execution log to debug issues and identify problematic nodes.

---

## Setting up Superbase DB
To set up your Superbase database, open your SQL editor in your Superbase account and copy and paste the following code:

```sql
-- Enable the pgvector extension to work with embedding vectors
CREATE EXTENSION IF NOT EXISTS pgvector;

-- Create a table to store your documents
CREATE TABLE documents_cyberapp (
  id BIGSERIAL PRIMARY KEY,
  content TEXT, -- corresponds to Document.pageContent
  metadata JSONB, -- corresponds to Document.metadata
  embedding VECTOR(1536) -- 1536 works for OpenAI embeddings, change if needed
);

-- Create a function to search for documents
CREATE FUNCTION match_documents_cyberapp (
  query_embedding VECTOR(1536),
  match_count INT DEFAULT NULL,
  filter JSONB DEFAULT '{}'
) RETURNS TABLE (
  id BIGINT,
  content TEXT,
  metadata JSONB,
  similarity FLOAT
)
LANGUAGE plpgsql
AS $$
BEGIN
  RETURN QUERY
  SELECT
    id,
    content,
    metadata,
    1 - (documents_cyberapp.embedding <=> query_embedding) AS similarity
  FROM documents_cyberapp
  WHERE metadata @> filter
  ORDER BY documents_cyberapp.embedding <=> query_embedding
  LIMIT match_count;
END;
$$;
```

Once this is set up, your database will be ready to store and retrieve document embeddings for the workflow.

---

## Support

For any issues or questions, you can:

- Open an issue on this repository.
- Consult the [n8n documentation](https://n8n.io/docs).
- Reach out to the n8n community forums for assistance.

---

## Future Enhancements

- Adding multi-language support for the chatbot.
- Integrating additional scheduling platforms.
- Enhanced reporting and analytics for collected leads.

---

Follow me on [LinkedIn](https://www.linkedin.com/in/stephen-chukwuma-575240222/) for updates on n8n workflows or to request assistance.