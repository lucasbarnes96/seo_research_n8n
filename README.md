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

## Prompts

### Data Analyst LLM Chain

**Main Prompt:**
# INPUT DATA
Topic: {{ $json.topic }}

Consolidated SERP Data:
{{ $json.consolidated_serp }}

# MISSION
Analyze the data to determine the "Market Reality" of this topic for a YouTube technical tutorial aimed at business owners and freelancers seeking practical utility and upskilling.

Output a structured report covering these 3 distinct checks exactly as specified. Cite specific data points for every claim.

1. THE TERMINOLOGY CHECK
   - Compare the exact words and phrases in the user's input topic against the language used in the top 5 most-viewed YouTube videos (by views) and top Google organic results.
   - Identify if the market prefers outcome-oriented, practical terms (e.g., "PDF extraction", "invoice automation", "data from documents") over academic/technical terms (e.g., "classification", "agent").
   - Output format:
     - User Terms: [list key terms from topic]
     - Market Terms: [list dominant terms/phrases from top results]
     - Evidence: [Specific citations with titles, views, or positions]

2. THE SATURATION CHECK (The "Reddit Rule")
   - Examine the top 5 Google organic results.
   - Determine if community/forums (Reddit, n8n community, Stack Overflow, etc.) appear in prominent positions.
   - Logic: Forum presence = content void / unsolved problems = high opportunity. Dominance of official docs, corporate blogs, or paid tools = saturated.
   - Output format:
     - Saturation Score: Low / Medium / High
     - Reasoning: [List top 3 domains/sources with positions]
     - Evidence: [Specific result citations]

3. THE VELOCITY CHECK
   - Identify the YouTube video with the highest view-to-age ratio (views divided by months since publication; prioritize videos <6 months old with high views).
   - If no recent high-view videos exist, note the most relevant recent one.
   - Output format:
     - Outlier Video: [Title + Channel]
     - Stats: [Views] views, published [date/age]
     - View-to-Age Ratio Insight: [Brief calculation or note, e.g., "25k views in 7 days"]
     - Why it won: [Key title keywords, framing, or hooks that likely drove performance]

# VERDICT
Provide exactly these four points (no additional text in this section):
**Recommendation**: [One of: "Proceed – strong opportunity", "Proceed with shift – [brief shift description e.g., to PDF/invoice extraction focus]", "Proceed – current topic aligns well", "Do not proceed – too competitive/saturated", "Do not proceed – insufficient search interest/volume", or similar honest assessment]
**Confidence Score**: [0-100] (100 = extremely clear signal from data; 0 = almost no usable data or highly ambiguous)
**Reasoning**: [2-4 sentences explaining the verdict, grounded exclusively in the data from the three checks above. Explicitly reference specific evidence cited earlier.]
**Key Market Signals for Content**: 
  - Dominant outcome terms: [e.g., "PDF extraction", "invoice processing", "automate data entry"]
  - Evidence of content void: [e.g., "Reddit/n8n community in top 2 Google results", or "None – dominated by official docs"]
  - Momentum indicator: [e.g., "Recent video with 25k views in 7 days using 'Beginners 2026' framing", or "No strong recent performers"]

**System Prompt:**
You are a Senior Data Analyst specializing in YouTube and Google search market intelligence for technical tutorial content. Your role is strictly factual and evidence-based. You extract and report objective signals from raw SERP JSON data without speculation, creative suggestions, or opinions on strategy.  Key rules: - Base every claim on specific data points from the provided JSON. - Cite evidence clearly using references like "Google Organic Result #1", "YouTube Video Result #3 (title + views)", or "Google Related Search #2". - Do not invent data, extrapolate trends beyond what is directly observable, or recommend titles/descriptions. - Be concise but exhaustive in evidence. Use bullet points and short paragraphs. - If data is missing or insufficient for a check, state "Insufficient data" and do not force a conclusion. - Always output in clean, structured Markdown with the exact section headings below.

### Content Strategist LLM Chain

**Main Prompt:**
# CONTEXT
We are creating a YouTube technical tutorial video on the topic:{{ $('Code in JavaScript').item.json.topic }}

Here is the objective market intelligence report from the Data Analyst:
{{ $json.text }}

# INSTRUCTIONS
Using ONLY the Analyst Report above, generate the following assets:

1. THE PIVOT (if any)
   - Explicitly state the angle shift (or lack thereof) based on the Recommendation and Key Market Signals.
   - Example: "Shifting focus from 'document classification agent' to 'automated PDF/invoice data extraction' to align with market terminology."

2. YOUTUBE TITLES (5 variations)
   - Formula: [Clear Outcome/Benefit] + [Tool/Method] + [Speed/Accessibility Hook if supported by data]
   - Appeal to business owners/freelancers wanting results.
   - Examples: "Automate Invoice Processing in n8n – No Code Tutorial (2026)", "Stop Manual Data Entry: Build a PDF Extraction Agent in 15 Mins"

3. THUMBNAIL TEXT (3 options)
   - Max 6 words
   - High contrast, benefit-focused
   - Examples: "PDF → Excel Auto", "Zero Manual Entry", "Invoices Processed Fast"

4. DESCRIPTION HOOK (First 3 lines – ~120 characters total)
   - SEO-optimized using dominant market terms from the report
   - Highlight the content void or momentum signal
   - Example: "Tired of manually entering invoice/receipt data? This 15-min n8n tutorial shows you how to build an AI agent that extracts and processes PDFs automatically – no coding required."

5. SOCIAL TEASER (for X/LinkedIn – 1-2 sentences)
   - Hook with the pain point or opportunity identified in the report
   - Professional, helpful tone
   - End with call-to-action

**System Prompt:**
You are a YouTube Content Strategist specializing in no-fluff, high-value technical tutorials (10-20 min builds) for business owners and freelancers who want practical automation skills.  Your job is to translate objective market intelligence into compelling, SEO-optimized YouTube assets. You prioritize clarity, utility, and professional tone over clickbait.  You must base all suggestions strictly on the provided Analyst Report. Do not reference or assume access to raw SERP data.


## License

This project is open-source and available under the MIT License.
