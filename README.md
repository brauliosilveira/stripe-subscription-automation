# Stripe Subscription Automation

A production-grade revenue automation flow connecting a custom checkout experience, Stripe recurring billing, WooCommerce order orchestration, and a Nuxt/Supabase SaaS application for automatic user provisioning after payment approval.

This repository is presented as a public portfolio case study for recruiters, hiring managers, and founders evaluating senior full-stack product work. It highlights the product architecture, payment automation, webhook design, and provisioning strategy behind the integration while keeping the full source code private for commercial and security reasons.

## Executive Summary

This project was built to automate what is often one of the most fragile and revenue-critical parts of SaaS operations: turning a paid subscription into an active product account with as little manual intervention as possible.

The public website and billing layer are built with:

- WordPress
- WooCommerce
- Elementor
- a custom checkout experience
- Stripe for recurring monthly billing

Once the payment is approved, the system automatically bridges the commerce side and the SaaS application side:

- the purchase is confirmed on the website stack;
- the backend receives the paid order event;
- the SaaS tenant is provisioned automatically;
- the user account is created or updated;
- the customer receives access instructions without manual back-office work;
- transactional messaging keeps the customer informed across channels.

The result is a more reliable subscription lifecycle, faster customer activation, stronger post-purchase communication, and less operational overhead after checkout.

## The Problem

Subscription-based businesses often break down at the handoff between checkout and product access.

Common problems include:

- customers paying successfully but not getting access immediately;
- duplicate provisioning caused by repeated webhook delivery;
- failed or partial account creation;
- manual back-office work to create users after payment;
- inconsistent state between the billing system and the SaaS application;
- weak handling of renewals, upgrades, or returning subscribers;
- abandoned carts with no recovery automation;
- fragmented customer communication after payment.

This project was designed to make that handoff reliable, automated, and production-safe.

## What I Built

- A subscription flow powered by Stripe recurring billing through WooCommerce
- A custom checkout flow tailored for subscription conversion
- Automated user provisioning after approved payment
- Automatic tenant/company creation for new customers
- Automatic account update flow for renewals and upgrades
- Secure webhook processing for paid orders
- Idempotency checks to prevent duplicate provisioning
- Rollback strategy for partial failures
- Password setup flow for newly created accounts
- Transactional email delivery through Amazon SES
- WhatsApp messaging for payment confirmation, welcome flow, and access delivery
- Abandoned cart recovery through email and WhatsApp automation
- Integration into a broader commercial automation stack

## System Architecture

At a high level, the flow works across a connected revenue stack rather than a single isolated app:

### 1. Commerce layer

The public sales website runs on WordPress with WooCommerce and Elementor. Stripe is used as the recurring payment provider for monthly subscriptions, while the checkout experience is structured around conversion and subscription activation.

This layer is responsible for:

- product presentation and checkout;
- custom subscription checkout flow;
- collecting billing details;
- charging the subscription through Stripe;
- emitting the paid-order event after successful payment;
- supporting lifecycle automation such as recovery messaging and post-purchase communication.

### 2. SaaS application layer

The application side is built with:

- Nuxt 4
- Vue 3
- Nitro server routes
- Supabase Auth
- Supabase Postgres

This layer is responsible for:

- receiving the payment event;
- validating and processing the webhook;
- mapping the purchased product to the correct SaaS plan;
- creating or updating the customer account;
- activating access to the application.

## Technical Analysis of the App

Based on the application codebase, the SaaS side uses a Nuxt 4 server-driven architecture with Supabase as the auth and data layer.

Key implementation characteristics identified in the app:

- Nuxt 4 with Vue 3 for the application layer
- Nitro server routes handling backend logic
- `@nuxtjs/supabase` for auth and database integration
- service-role operations executed server-side only
- worker-based internal infrastructure used elsewhere in the platform
- environment-based integration secrets for webhook and service communication

An important architectural detail: on the app side, the payment automation is triggered by a WooCommerce webhook endpoint. In practice, Stripe powers the recurring subscription at checkout, while WooCommerce acts as the commerce source that notifies the SaaS backend after payment approval.

That means the integration chain is effectively:

`WordPress + WooCommerce + Stripe -> webhook -> Nuxt backend -> Supabase Auth + database -> account activation`

Beyond the provisioning path itself, this project sits inside a broader revenue automation layer that includes transactional email, WhatsApp messaging, and abandoned-cart recovery.

## Technical Flow

### 1. Subscription checkout

The customer subscribes through a custom checkout experience on the WordPress/WooCommerce site, using Stripe as the payment processor for recurring billing.

### 2. Paid-order event reaches the SaaS backend

After payment approval, the backend receives the order event and processes only paid statuses. In the analyzed application flow, paid digital orders are handled through statuses such as `processing` and `completed`.

### 3. Secure webhook verification

The webhook endpoint validates a shared secret using a timing-safe comparison before processing the request. This reduces the risk of unauthorized or spoofed requests.

### 4. Idempotent provisioning logic

Before creating anything, the system checks whether the order was already processed. This prevents duplicate tenants or duplicate user provisioning if the webhook is delivered more than once.

### 5. Product-to-plan mapping

The purchased WooCommerce product is mapped to the correct SaaS plan and billing cycle. This allows the commerce catalog to control which application plan should be activated.

### 6. New account creation or existing account update

If the customer is new, the system creates:

- the company/tenant;
- the auth user;
- the user profile linked to the tenant.

If the customer already exists, the system updates the existing company account for renewal or upgrade scenarios.

### 7. Activation and customer messaging

For new customers, the system generates a password-creation link and sends access instructions through email. It also sends WhatsApp confirmation messages, giving the user a second channel for onboarding and access recovery.

The broader customer communication layer also supports:

- transactional email through Amazon SES;
- WhatsApp messages for approved payment and welcome flow;
- abandoned-cart recovery across email and WhatsApp touchpoints.

## Key Engineering Decisions

### Use webhook-driven automation instead of manual provisioning

The integration removes the need for back-office account creation after payment and shortens the time between purchase and product access.

### Treat payment processing as a revenue-critical workflow

This is not just a checkout integration. It is a revenue system, because every failure between payment approval and account activation directly impacts customer experience, activation speed, and conversion into actual product usage.

### Verify secrets with timing-safe comparison

The backend uses a timing-safe approach to validate the webhook token, which is a stronger choice than a naive string comparison for sensitive webhook entry points.

### Design for duplicate deliveries

Webhook systems can retry events. Idempotency is essential here, so the backend checks whether the order has already been processed before provisioning.

### Support both new customers and lifecycle updates

The automation handles not only first-time buyers, but also renewal and upgrade paths, which makes the integration more realistic for subscription operations.

### Use rollback on partial failure

If user creation fails after tenant creation, the flow performs cleanup rather than leaving behind inconsistent records.

### Use multi-channel communication after checkout

Relying on a single channel after payment is fragile. By combining email and WhatsApp messaging, the system improves onboarding reliability and gives customers multiple ways to recover access or continue setup.

## Reliability and Security Considerations

The analyzed implementation includes several production-minded safeguards:

- timing-safe secret validation;
- idempotency checks by order identifier;
- selective processing of valid paid statuses only;
- sanitization of customer input fields;
- rollback behavior for partial provisioning failures;
- server-side use of privileged credentials only;
- fallback customer messaging through more than one channel.

## Why This Project Matters

This project demonstrates:

- full-stack thinking across marketing site, commerce, payments, and SaaS application layers;
- checkout and post-checkout conversion thinking, not just backend implementation;
- webhook architecture for revenue-critical workflows;
- practical integration between WordPress/WooCommerce and a modern app backend;
- automated user provisioning after successful payment;
- subscription-focused thinking through Stripe recurring billing;
- multi-channel messaging tied to payment and activation events;
- production-oriented handling of idempotency, security, and operational reliability;
- the ability to connect no-code/low-code website tooling with custom application infrastructure.

It also shows a very practical business skill: building the bridge between "customer paid" and "customer can actually use the product."

## Stack

### Commerce and website layer

- WordPress
- WooCommerce
- Elementor
- Custom checkout flow
- Stripe recurring subscriptions
- Abandoned cart recovery flows

### Application layer

- Nuxt 4
- Vue 3
- Nitro server routes
- Supabase Auth
- Supabase Postgres
- Node.js server-side integrations
- AWS SES for transactional email
- WhatsApp API integration for customer messaging

## High-Level Architecture

```text
WordPress + WooCommerce + Elementor
                |
                v
       Custom subscription checkout
                |
                v
      Stripe recurring billing
                |
                v
      Paid order / webhook event
                |
                v
       Nuxt backend (Nitro route)
                |
                +--> secret validation
                +--> idempotency check
                +--> product-to-plan mapping
                +--> tenant creation or update
                +--> auth user provisioning
                +--> email + WhatsApp onboarding
                +--> lifecycle messaging
                |
                v
      Supabase Auth + Database
                |
                v
       Activated SaaS account
```

## Demo and Media

This case study is supported by screenshots showing the path from subscription checkout to automated SaaS account activation.

- `Plan Selection - Choosing the Subscription Tier`
- `Custom Checkout - Subscription Purchase Flow`
- `Payment Approved - Successful Billing Confirmation`
- `Automatic Account Provisioning - User Created After Payment`
- `Activated Account - Immediate Access to the SaaS App`
- `Post-Purchase Messaging - Access Delivery via Email and WhatsApp`

## About This Repository

This is a public portfolio case study for full-stack, backend, integrations, automation, and SaaS-focused roles.

The complete source code, internal integrations, secrets, and infrastructure details remain private for commercial and security reasons.

## My Role

I designed and implemented the automation logic connecting payment confirmation to product access, including:

- integration design between website and application layers;
- custom checkout and subscription activation flow design;
- webhook processing strategy;
- provisioning logic for new and existing customers;
- secure server-side handling of privileged operations;
- account activation flow;
- customer communication through Amazon SES and WhatsApp;
- abandoned-cart recovery automation strategy;
- failure handling and operational safeguards.

## Contact

If you would like a live walkthrough or want to discuss SaaS billing flows, webhook architecture, product automation, or full-stack roles, feel free to reach out.

- Website: https://www.brauliosilveira.com
- LinkedIn: https://linkedin.com/in/brauliosilveira
- Email: hi@brauliosilveira.com
- YouTube: https://youtube.com/@venderpelowhats
