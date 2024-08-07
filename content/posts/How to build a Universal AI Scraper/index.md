---
title: "How to build a Universal AI Scraper"
date: 2024-06-18
description: "Lately, I've dived into the world of web scrapers, and with the rapid developments in AI, I thought it would be fascinating to attempt creating a 'universal' scraper."
tags: [Artificial Intelligence], [Deep-dive Analysis]
---
Lately, I've dived into the world of web scrapers, and with the rapid developments in AI, I thought it would be fascinating to attempt creating a 'universal' scraper. This would be one that can navigate the web step by step until it finds exactly what it's looking for. It's a work in progress, but I wanted to share what I've achieved so far.

### **Specifications**

Starting with a URL and a high-level objective, the web scraper should:

1. Analyse the given webpage
2. Extract text information from relevant sections
3. Perform any necessary interactions
4. Continue until the goal is achieved

### **Tools Used**

Though this is mainly a backend project, I decided to use **NextJs** in case I want to add a frontend later. For the web crawling, I chose [**Crawlee**](https://crawlee.dev/), which is built on top of [**Playwright**](https://playwright.dev/), a browser automation library.

Crawlee adds some nice features for disguising the scraper as a regular user and offers a request queue to manage the order of requests, which is super useful if I ever want to make this available for others.

For the AI components, I'm using [**OpenAI**](https://platform.openai.com/docs/api-reference/introduction)'s API along with Microsoft Azure's [**OpenAI Service**](https://azure.microsoft.com/en-us/products/ai-services/openai-service). Across these, I'm leveraging three different models:

- **GPT-4-32k** ('gpt-4-32k')
- **GPT-4-Turbo** ('gpt-4-1106-preview')
- **GPT-4-Turbo-Vision** ('gpt-4-vision-preview')

The GPT-4-Turbo models are similar to the original GPT-4 but boast a larger context window (128k tokens) and are much faster (up to 10x). However, these improvements come at a price: the GPT-4-Turbo models are a bit less intelligent than the original GPT-4, which posed challenges in the more complex stages of my scraper. So, I switched to GPT-4-32K when I needed more processing power.

GPT-4-32K is a variant of the original GPT-4 but offers a 32k context window instead of 4k. I ended up using Azure's OpenAI service to access GPT-4-32K because OpenAI currently limits access to that model on their platform.

### **Getting Started**

I began by working backwards from my constraints. Since I was using a Playwright crawler, I knew I would eventually need an element selector from the page to interact with it.

For those unfamiliar, an element selector is a string that identifies a specific part of a page. For example, to select the fourth paragraph on a page, you could use the selector `p:nth-of-type(4)`. If you wanted to select a button with the text 'Click Me,' you could use `button:has-text('Click Me')`. Playwright identifies the element you want using a selector and then performs an action on it, like 'click()' or 'fill()'.

Given this requirement, my first task was to identify the 'element of interest' on a given webpage. I'll refer to this as the 'GET_ELEMENT' function from here on out.

## **Getting the Element of Interest**

### **Approach 1: Screenshot + Vision Model:**

HTML data can be highly detailed and lengthy. Most of it focuses on styling, layout, and interactive logic rather than the text content. I was concerned that text models would struggle with this complexity, so I thought of bypassing it using the GPT-4-Turbo-Vision model. This model would 'look' at the rendered page and extract the most relevant text, which I could then use to search through the raw HTML for the element containing that text.

![Alt text](static/support/images/Untitled (2).png)
