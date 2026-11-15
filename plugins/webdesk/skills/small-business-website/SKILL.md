---
name: small-business-website
description: Build a one-page website for a small business with WebDesk. Use when the user wants a website, landing page or online presence for a business (a cafe, tradie, clinic, salon, studio and so on) and is happy to receive a finished site as a ZIP they own and can host anywhere.
---

# Small business website with WebDesk

WebDesk builds a complete one-page website from a short brief and returns a ZIP of plain HTML, CSS and JavaScript that the business owns. Each website includes one free redo. Payment is by prepaid credits, one per website: $29 USD for one, or $23 each in a pack of 25.

## Setup

The `webdesk` MCP server needs an API key in the `WEBDESK_API_KEY` environment variable. Keys are free to create at https://redcenterstudio.com/webdesk/agents/. Never ask the user to paste a key into the chat; ask them to set the variable and restart Claude Code.

## Tools

`create_website`, `get_website_status`, `get_download_link`, `revise_website`, `get_credit_balance`, `buy_credits`. Full inputs and outputs: https://redcenterstudio.com/webdesk/agents/docs/

## How to work

1. Collect the brief: business name, type of business, main services, location, tone and colour palette. Ask only for what is missing.
2. Check `get_credit_balance` before building. If there are no credits, explain the price and use `buy_credits` only after the user agrees; it returns a Stripe Checkout link for the user to pay. Never spend or buy credits without the user's clear yes.
3. Call `create_website`, then poll `get_website_status` until it is ready (usually a few minutes).
4. Give the user the link from `get_download_link` and a short summary of the site.
5. If they want changes, use `revise_website`. The first redo is free; say so before using it.

Self-serve websites are delivered in minutes. A done-for-you launch by the studio is a separate paid add-on, done within 72 hours of receiving the buyer's details.
