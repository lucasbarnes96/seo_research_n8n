# SEO Research Content Workflow for n8n

This repository features an n8n workflow designed to automate SEO research and content strategy generation. It leverages SERP data, YouTube search results, and Google Gemini's AI capabilities to provide a comprehensive market analysis and actionable content recommendations.

## Overview

The workflow automates the process of:
1.  **Data Collection**: 
    -   Fetches a search query from a Google Sheet.
    -   Performs a Google Search using SerpApi.
    -   Performs a YouTube Search using SerpApi.
2.  **Analysis**:
    -   Uses Google Gemini to analyze the consolidated SERP and YouTube data.
    -   Generates a "Market Reality" report covering Terminology, Saturation, and Velocity checks.
3.  **Strategy Generation**:
    -   Passes the analysis to a "Content Strategist" AI agent (Gemini).
    -   Produces specific YouTube titles, thumbnail text, hooks, and social teasers.
4.  **Reporting**:
    -   Updates the original Google Sheet with the analysis, recommendations, and content assets.

## Prerequisites

To use this workflow, you will need:

*   **n8n**: A self-hosted instance or n8n Cloud account.
*   **SerpApi Account**: For Google and YouTube search results.
*   **Google Cloud Platform Project**: Enabled with the Generative Language API (for Gemini) and Google Sheets API.
*   **Google Sheet**: A structured sheet to input queries and receive results.

## Setup

1.  **Import the Workflow**:
    -   Download `seo-research-content.json` from this repository.
    -   In your n8n dashboard, click "Add workflow" -> "Import from..." -> "Local File".
    -   Select the JSON file.

2.  **Configure Credentials**:
    You will need to set up the following credentials in n8n and connect them to the respective nodes:
    -   **SerpApi**: Enter your API key.
    -   **Google Gemini (PaLM/Gemini API)**: Enter your API key.
    -   **Google Sheets (OAuth2)**: Authenticate with your Google account.

3.  **Configure Nodes**:
    -   **Google Sheets Nodes (Get & Update)**:
        -   Select your specific Spreadsheet and Sheet ID.
        -   Ensure your sheet columns match the data mapping in the "Update row in sheet" node (e.g., Status, Recommendation, YouTube Titles, etc.).

## License

This project is open-source and available under the MIT License.
