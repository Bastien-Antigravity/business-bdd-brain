---
type: spec
microservice: obsidian-brain
repo: tele-remote
feature_id: FEAT-001
status: draft
tags:
- \'#service/obsidian-brain\'
- '#state/draft'
- null
- '#type/spec'
---

# Feature: Dynamic Menu Building (Menu-on-the-fly)

## 🎯 Business Intent
- **User Story**: As a trader, I want my Telegram bot to show me buttons specifically for the bot I just started (e.g., "Start Scalping," "Stop Scalping"), so that I can control my infrastructure without using a CLI.
- **Problem Solved**: Eliminates the need to hard-code bot commands in the Tele-Remote server. It makes the UI "Self-Describing" based on connected components.

## 🎬 Scenarios (Gherkin)

### Scenario 1: Rendering a Simple Menu
- **Given** a component "MarketObserver" registers with a Menu JSON
- **And** the JSON contains a button "Start Stream" with command "cmd_start"
- **When** a user clicks on "MarketObserver" in the Telegram main menu
- **Then** Tele-Remote must render an Inline Keyboard with the "Start Stream" button
- **And** the button's callback data must be uniquely mapped to the "MarketObserver" instance

### Scenario 2: Recursive Sub-Menus
- **Given** a menu with a nested "Settings" folder
- **When** the user clicks "Settings"
- **Then** the current keyboard must be replaced by the sub-menu buttons
- **And** a "Back" button must be automatically added to return to the parent menu

### Scenario 3: Dynamic Data Replacement (Placeholders)
- **Given** a menu button with text "Status: {status}"
- **When** the component sends a status update "Running"
- **Then** the Telegram button text must be updated in real-time to "Status: Running"
- **And** this must not trigger a "New Message" alert (Edit Message instead)

## 🛠️ Technical Constraints
- **Library**: Use `go-telegram-bot-api` or equivalent.
- **Complexity**: Maximum menu depth of 3 levels to prevent Telegram UI clutter.
