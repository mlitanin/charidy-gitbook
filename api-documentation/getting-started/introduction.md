# Introduction

Welcome to the documentation for the **Charidy Organization Dashboard API**. This API allows organization administrators and developers to programmatically manage their fundraising activities, including campaigns, donations, teams, and settings.

## Documentation Structure

This documentation is divided into the following sections:

* [**Authentication**](authentication.md) Learn how to authenticate using email/password or SMS to obtain a JWT token required for all API requests.
* [**Organizations**](organizations.md) Manage organization profiles, settings, and select the active organization context for multi-org users.
* [**Campaigns**](../campaigns/campaigns.md) The core of the platform. Create, update, and retrieve campaigns, including goals, dates, and matchers.
  * [Donation Levels](../campaigns/donation-levels.md)
  * [Teams & Ambassadors](../campaigns/teams.md)
  * [Media & Story](../campaigns/story-content.md)
* [**Donations**](../campaigns/donations.md) Retrieve and manage donation transactions and donor data.
* [**Media Storage**](../media-storage/media-storage.md) Upload and manage images and videos used across the platform.
* [**Users & Legal**](../users-and-legal/users.md) Manage organization team members (users) and legal entities for tax receipts.

***

> **Note: Coming Soon** Documentation for the **Donor & Ambassador Dashboard API** is currently in development and will be available soon.
