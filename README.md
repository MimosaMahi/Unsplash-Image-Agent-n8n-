Unsplash Image Agent (n8n)
==========================

Project Overview

The Unsplash Image Agent is an automated backend workflow built using n8n that integrates with the Unsplash API to dynamically search and serve high-resolution stock images. The workflow functions as a lightweight serverless microservice: it exposes a RESTful GET endpoint, accepts search queries from incoming HTTP requests, fetches the top results from Unsplash, filters the API response, and outputs a clean JSON payload containing full-sized image URLs.

Workflow Architecture & Implementation Details
The pipeline consists of four sequential nodes configured as follows:

1. Webhook Trigger (Entry Point)
HTTP Method: GET

Response Mode: Configured to wait for the final node (Respond to Webhook) before delivering the HTTP response.

Input Handling: Captures query parameters passed via the request URL (e.g., ?q=coffee).

2. Unsplash API Integration
Node Type: HTTP Request

Endpoint: (https://api.unsplash.com/search/photos)

Request Method: GET

Query Parameters:

query: Dynamically populated using the incoming Webhook query parameter ({{ $json.query.q }}).

per_page: Set to 3 to limit the payload to the top three image results.

Authentication: Passes the Unsplash Access Key via the HTTP request header:

Authorization: Client-ID <YOUR_UNSPLASH_ACCESS_KEY>

3. Data Processing & Transformation
Node Type: Edit Fields / Set Node

Transformation Logic: Filters the detailed API response to extract only the full-resolution image URLs using JavaScript array mapping:

JavaScript
images = {{ $json.results.map(r => r.urls.full) }}
Output Structure: Isolates the array into a single structured field named images.

4. HTTP Response Delivery
Node Type: Respond to Webhook

Behavior: Returns the processed JSON payload directly to the client that initiated the Webhook request.

Expected API Output
Upon making a GET request to the workflow endpoint (e.g., GET /unsplash-agent?q=coffee), the workflow returns the following formatted JSON response:

JSON
{
  "images": [
    "https://images.unsplash.com/photo-1...",
    "https://images.unsplash.com/photo-2...",
    "https://images.unsplash.com/photo-3..."
  ]
}
