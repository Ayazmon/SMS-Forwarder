---
layout: page
title: Privacy Policy
include_in_header: true
---

**Last updated**  
May 28 2026

# Privacy Policy

SMS Forwarder is built with privacy as a first-class concern. This page explains, in plain English, what we do — and just as importantly, what we *don't* do — with the data that passes through the app.

<br>

## 1.0 What SMS Forwarder Does

SMS Forwarder is an iOS app that helps you set up an **Apple Shortcuts automation** on your iPhone. The automation triggers when an SMS arrives, applies the forwarding rules you've configured, and routes the message to a destination you choose — email, Telegram, WhatsApp, Slack, Discord, or a webhook.

All of this happens on your device. The app itself is the configurator; Apple Shortcuts is the delivery mechanism.

<br>

## 2.0 Information We Collect

### 2.1 Information stored locally on your device
The app stores the following on your iPhone only, using Apple's `SwiftData` framework:

- Your forwarding rules (sender filters, keyword filters, schedules)
- Your chosen destinations and their connection details (e.g. Telegram chat ID, Slack webhook URL)
- App preferences (theme, language)

This information **never leaves your device** unless required to deliver a forwarded message to the destination you picked.

### 2.2 Information we never collect
- We do not collect, store, or transmit the **content of your SMS messages**.
- We do not maintain a server-side log of messages you forward.
- We do not collect contacts, location, advertising identifiers, or analytics tied to a user identity.

<br>

## 3.0 How Forwarding Works

When the Apple Shortcuts automation fires, the SMS payload (sender, timestamp, body) is passed to SMS Forwarder's encrypted relay so it can reach the destination you chose. The payload is:

- Wrapped in **RSA-OAEP-SHA256** encryption before it leaves your device
- Tagged with a fresh **UUID nonce** and timestamp for replay protection
- Delivered straight to your chosen destination's API (Telegram bot, Slack webhook, email gateway, etc.)
- **Not stored** at rest on our infrastructure once delivered

The third-party destination you pick (e.g. Telegram, Slack, your email provider) has its own privacy practices that govern what they do with the forwarded message after delivery. We have no control over those services.

<br>

## 4.0 Your Rights

Because everything is stored on your device:

1. You can **inspect** every rule and destination from inside the app.
2. You can **delete** any rule, destination, or the entire data store at any time by removing them in-app or deleting the app from your device.
3. You can **stop** forwarding instantly by disabling the Apple Shortcuts automation.

There is no account to delete and no cloud profile to reset — everything lives with you.

<br>

## 5.0 Children

SMS Forwarder is a general-purpose utility and is not directed at children under 13. We do not knowingly collect information from children.

<br>

## 6.0 Changes to this Policy

If we change this policy, we will update the **Last updated** date at the top of the page and publish the new version at this URL. Material changes will be highlighted in the app's release notes.

<br>

## 7.0 Contact

Questions, concerns or feedback about this policy? Reach the developer at **[hoholapps@gmail.com](mailto:hoholapps@gmail.com)**.
