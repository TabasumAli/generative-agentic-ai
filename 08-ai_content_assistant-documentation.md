# AI Content Assistant - Documentation

## 1. The Problem

Creating quality content across different platforms (blogs, social media, email newsletters) is time-consuming. Writers and marketers struggle to consistently adapt tone, generate catchy headlines, format posts correctly, and extract metadata like word counts or relevant hashtags. The goal is to streamline multi-platform content generation with AI.

## 2. The Project — AI Content Assistant

A Streamlit web application that lets users input topics, select platforms, choose tone, and instantly generate structured content. It connects to the Groq API (using the `openai/gpt-oss-120b` model) to create ready-to-publish material complete with titles, main copy, captions, and hashtags.

## 3. How the App Works

Configure Inputs → Validate API Key → Construct Prompt → Send to Groq → Parse JSON → Render Interactive UI

The app accepts user criteria, builds a tailored system prompt enforcing JSON output, parses the response with fallback handling, and renders formatted preview cards with statistical analysis.

## 4. Input & Configuration Validation

Before triggering AI generation, the app validates inputs:

- **API Key Check:** Performs a low-token test call to Groq to verify credentials.
- **Required Parameters:** Ensures topic, content type, platform, audience, and tone are defined.
- **Advanced Controls:** Accepts optional instructions, key points to emphasize, and feedback for iterative rewrites.
- **Status:** Valid (Green) or Invalid (Red) indicator.

## 5. Structured AI Generation

The app sends user preferences to Groq with strict JSON output formatting.

The AI returns:

- **Title:** Attention-grabbing headline or title.
- **Content:** Main body text with markdown/HTML formatting.
- **Caption:** Concise summary or social media description.
- **Hashtags:** Array of 5 relevant and trending tags.

## 6. App Feature Breakdown

| Feature | Description |
|---|---|
| Multi-Platform Rules | Tailors formatting for LinkedIn, Twitter/X, Instagram, Blogs, and Newsletters |
| Tone Adjustment | Supports Professional, Casual, Inspirational, Humorous, Persuasive, and more |
| Interactive Clipboard | One-click copy buttons for titles, main content, captions, and hashtags |
| Statistical Engine | Calculates total word count, character count, and estimated reading time |
| Feedback Loop | Allows up to 3 iterative regenerations per topic using user feedback |

## 7. Interactive Results Dashboard

Organizes generated output into clean section panels:

Headline Box → Formatted Body Display → Social Caption Expander → Hashtags Expander → Stats Metrics → Regeneration Controls

Includes single-click copy buttons, text download export options (.txt), and session history tracking.

## 8. From Raw JSON to Clean Presentation

Converts unstructured raw API outputs into formatted UI elements.

- **Formatting Pipeline:** Automatically converts Markdown bolding/italics and list markers into clean HTML line breaks and markup.
- **Fallback Handler:** If JSON parsing fails due to malformed LLM responses, regex extracts the title, content body, and hashtags to prevent application crashes.

## 9. What This Project Demonstrates

Combines: Python, Streamlit, Groq API / LLMs, Regex text parsing, Custom CSS styling, Iterative feedback handling, Session state management, and Defensive exception handling.

User Criteria → Prompt Construction → AI Generation → JSON Parsing → Interactive UI → Exportable Asset
Github link: https://github.com/TabasumAli/Content-Writing-Assistant
The goal isn't just to call an LLM API. The goal is to turn raw model outputs into a practical, multi-platform publishing workspace.
