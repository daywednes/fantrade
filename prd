# Product Requirements Document: P2P Sports Betting Exchange

## Document Information

| Field | Value |
|-------|-------|
| Product Name | BetFlow Exchange (Working Title) |
| Version | 1.0 |
| Author | Product Team |
| Last Updated | December 2024 |
| Status | Draft |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Product Vision & Objectives](#3-product-vision--objectives)
4. [System Architecture Overview](#4-system-architecture-overview)
5. [Core Components](#5-core-components)
6. [Data Transfer Objects (DTOs)](#6-data-transfer-objects-dtos)
7. [Component Algorithms](#7-component-algorithms)
8. [Blockchain Payment Design (Base Chain)](#8-blockchain-payment-design-base-chain)
9. [Commission & Fee Structure](#9-commission--fee-structure)
10. [Launch Strategy](#10-launch-strategy)
11. [Risk Management](#11-risk-management)
12. [Success Metrics](#12-success-metrics)
13. [Appendices](#13-appendices)

---

## 1. Executive Summary

BetFlow Exchange is a peer-to-peer sports betting platform utilizing an order book model similar to Polymarket's prediction market structure. Unlike traditional bookmakers who take positions against bettors, our platform matches buyers and sellers of betting contracts, ensuring the house never holds directional risk.

The key innovation is our liquidity aggregation system that sources liquidity from external exchanges (Betfair, Smarkets, Polymarket, Pinnacle) when internal order books lack depth. This guarantees users can always place orders while the platform hedges externally, creating a sustainable zero-risk revenue model through commission fees.

Payment infrastructure utilizes USDC stablecoin on Base blockchain, providing sub-$0.05 transaction fees and seamless fiat on/off ramps through Coinbase integration.

---

## 2. Problem Statement

### 2.1 Traditional Bookmaker Limitations

Traditional sports betting platforms operate as counterparties to bettors, creating inherent conflicts of interest. They manage risk through unfavorable odds, betting limits on successful players, and account restrictions. This model leads to poor user experience for sophisticated bettors and unsustainable risk exposure for operators.

### 2.2 Existing Exchange Limitations

Current betting exchanges like Betfair and Smarkets solve the counterparty problem but suffer from liquidity fragmentation. Users frequently encounter unmatched orders, particularly in less popular markets. New exchanges face the classic chicken-and-egg problem: users won't join without liquidity, and liquidity won't exist without users.

### 2.3 Crypto Betting Platform Issues

Existing crypto betting platforms typically operate as traditional bookmakers accepting cryptocurrency, not true exchanges. They inherit the same conflict-of-interest problems while adding complexity. Those that are true exchanges often use high-fee blockchains or obscure tokens, creating friction for mainstream adoption.

### 2.4 Our Solution

BetFlow addresses these problems through three innovations: an order book model eliminating house risk, cross-exchange liquidity aggregation solving the cold-start problem, and Base chain USDC payments providing low-cost mainstream-accessible transactions.

---

## 3. Product Vision & Objectives

### 3.1 Vision Statement

Create the most liquid, fair, and accessible sports betting exchange where users trade betting positions with each other, the platform earns sustainable commission revenue, and external liquidity bridges ensure orders are always serviceable.

### 3.2 Primary Objectives

**Objective 1: Zero House Risk**
The platform never holds directional betting exposure. All user positions are either matched internally between users or hedged externally on partner exchanges. Revenue derives exclusively from commission fees.

**Objective 2: Guaranteed Liquidity**
Users can always place orders at competitive odds. When internal liquidity is insufficient, the platform automatically sources liquidity from external exchanges, accepting orders only when profitable hedging is available.

**Objective 3: Low-Cost Transactions**
Utilize Base blockchain for deposits, withdrawals, and settlement. Target average transaction cost under $0.05 to enable micro-betting and frequent trading without fee erosion.

**Objective 4: Regulatory Positioning**
Structure as a technology platform matching willing counterparties rather than a gambling operator. This peer-to-peer exchange model may provide favorable regulatory treatment in certain jurisdictions.

### 3.3 Target Users

**Primary: Active Sports Bettors**
Users placing 10+ bets per month who understand odds comparison and seek best prices. They currently use multiple bookmakers and understand the value proposition of an exchange model.

**Secondary: Arbitrage Traders**
Sophisticated users who exploit price discrepancies across platforms. They provide valuable liquidity and tight spreads in exchange for low fees.

**Tertiary: Casual Crypto Users**
Cryptocurrency holders seeking entertainment and familiar with DeFi concepts. They value non-custodial options and on-chain transparency.

---

## 4. System Architecture Overview

### 4.1 High-Level Architecture

The system consists of six primary layers: Client Applications, API Gateway, Core Services, Data Infrastructure, External Integrations, and Blockchain Layer.

**Client Applications Layer**
Web application (Next.js), iOS application, Android application, and public API for third-party integrations. All clients communicate through the API Gateway.

**API Gateway Layer**
Single entry point handling authentication, rate limiting, request routing, and response caching. Implements JWT-based authentication for web2 sessions and wallet signature verification for web3 interactions.

**Core Services Layer**
Five microservices handle distinct domains: Order Service, Market Service, Wallet Service, Liquidity Service, and Settlement Service. Services communicate through message queues for asynchronous operations and direct gRPC calls for synchronous requirements.

**Data Infrastructure Layer**
PostgreSQL serves as the primary database for user accounts, orders, and transactions. Redis provides in-memory order book storage and caching. TimescaleDB handles time-series data for price history and analytics. Apache Kafka manages event streaming between services.

**External Integrations Layer**
Connections to external betting exchanges (Betfair, Smarkets), prediction markets (Polymarket), odds providers (Pinnacle), and sports data providers for event information and results.

**Blockchain Layer**
Base chain smart contracts handle deposits, withdrawals, and settlement. Integration with Circle for USDC operations and Coinbase for fiat on/off ramps.

### 4.2 Service Communication Patterns

**Synchronous Operations**
Order placement, balance queries, and market data retrieval use REST APIs with sub-100ms latency requirements. The API Gateway routes these to appropriate services via internal load balancers.

**Asynchronous Operations**
Order matching, external hedging, settlement processing, and notification delivery use message queues. This ensures system resilience and allows independent scaling of components.

**Real-Time Updates**
WebSocket connections deliver order book updates, position changes, and market movements. A dedicated WebSocket service maintains client connections and subscribes to relevant Kafka topics.

---

## 5. Core Components

### 5.1 Order Book Engine

**Purpose**
Maintains the central limit order book for each market, storing all open orders sorted by price-time priority. Provides real-time order book snapshots and updates to connected clients.

**Responsibilities**
- Store and index open orders by market, price level, and timestamp
- Provide order book depth snapshots at configurable granularity
- Publish order book change events for real-time client updates
- Calculate implied odds and market statistics
- Manage order lifecycle states (open, partial, filled, cancelled)

**Data Structures**
The order book maintains two sorted collections per market: backs (bets for an outcome) sorted by odds descending, and lays (bets against an outcome) sorted by odds ascending. This ensures best prices appear at the top of each side.

### 5.2 Matching Engine

**Purpose**
Executes the order matching algorithm, pairing compatible back and lay orders to create matched bets. Operates as a deterministic state machine ensuring consistent results across system replays.

**Responsibilities**
- Match incoming orders against resting orders using price-time priority
- Handle partial fills and order splitting
- Generate match events for downstream processing
- Maintain matching statistics and audit logs
- Process bulk order operations for market makers

**Matching Priority**
Orders match first by price (best price wins), then by time (earlier orders have priority at the same price). This standard exchange model incentivizes competitive pricing and rewards early liquidity provision.

### 5.3 Liquidity Aggregation Service

**Purpose**
Sources external liquidity when internal order books cannot satisfy user orders. Monitors external exchange prices, calculates hedging profitability, and executes external trades to back internal commitments.

**Responsibilities**
- Maintain real-time connections to external exchanges
- Map internal markets to external market identifiers
- Calculate hedging costs including external commissions
- Execute hedge trades on external platforms
- Monitor hedge position status and handle failures
- Provide synthetic liquidity quotes to the order book

**External Sources**
Primary sources include Betfair Exchange (largest liquidity pool for sports), Smarkets (competitive commissions), and Pinnacle (sharp lines for price discovery). Secondary sources include Polymarket for events with prediction market overlap.

### 5.4 Market Service

**Purpose**
Manages sporting event data, market creation, odds feeds, and result settlement. Serves as the source of truth for what markets exist and their current status.

**Responsibilities**
- Ingest sporting event data from providers
- Create and configure betting markets
- Manage market lifecycle (pre-event, in-play, closed, settled)
- Process event results and trigger settlement
- Handle voided events and special circumstances

**Market Types**
Initial launch supports match winner (moneyline), point spread (handicap), and total points (over/under) markets. Future phases add player props, parlays, and live betting.

### 5.5 Wallet Service

**Purpose**
Manages user fund balances, processes deposits and withdrawals, and maintains transaction history. Operates both on-chain (for crypto operations) and off-chain (for internal transfers and betting).

**Responsibilities**
- Track user USDC balances (available, reserved, total)
- Process blockchain deposits with confirmation requirements
- Execute withdrawal requests after security checks
- Reserve funds for open orders and matched bets
- Release funds on bet settlement or order cancellation
- Generate transaction receipts and history

**Balance Types**
Each user maintains three balance figures: available balance (can be withdrawn or used for new orders), reserved balance (locked for open orders and unsettled bets), and total balance (sum of available and reserved).

### 5.6 Settlement Service

**Purpose**
Processes bet outcomes after sporting events conclude. Calculates payouts, updates balances, and generates settlement records.

**Responsibilities**
- Receive verified event results from Market Service
- Identify all bets requiring settlement for each event
- Calculate winnings based on matched odds and stakes
- Credit winner accounts and release loser reserves
- Handle edge cases (dead heats, postponements, walkovers)
- Generate settlement reports for audit and user notification

### 5.7 Risk Management Service

**Purpose**
Monitors platform exposure, enforces betting limits, and detects suspicious activity. Ensures the platform maintains its zero-risk position and complies with responsible gambling requirements.

**Responsibilities**
- Calculate real-time platform exposure by market
- Enforce user betting limits and loss limits
- Detect and flag unusual betting patterns
- Monitor external hedge positions for discrepancies
- Generate risk reports for operations team
- Implement circuit breakers for extreme scenarios

---

## 6. Data Transfer Objects (DTOs)

This section defines the request and response structures for all API endpoints. Each field includes its data type, constraints, and rationale explaining why the field is necessary.

### 6.1 Authentication DTOs

#### 6.1.1 Wallet Authentication Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| walletAddress | string | Yes | Valid Ethereum address format (0x + 40 hex chars) | Identifies the user's blockchain wallet, serves as their unique account identifier, and enables verification against on-chain data |
| message | string | Yes | Must contain server-generated nonce | Contains the challenge message the user signed, including a nonce to prevent replay attacks where old signatures could be reused |
| signature | string | Yes | Valid ECDSA signature | Cryptographic proof that the owner of the private key corresponding to walletAddress signed the message, proving ownership without exposing the key |
| timestamp | integer | Yes | Within 5 minutes of server time | Prevents replay attacks by ensuring the authentication attempt is recent; signatures older than 5 minutes are rejected |

#### 6.1.2 Wallet Authentication Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| accessToken | string | No | JWT token for authenticating subsequent API requests; contains encoded user ID and permissions with expiration |
| refreshToken | string | No | Long-lived token for obtaining new access tokens without requiring wallet signature; improves UX for extended sessions |
| expiresIn | integer | No | Seconds until accessToken expires; allows client to proactively refresh before expiration |
| userId | string | No | Internal user identifier; may differ from wallet address to support multiple wallets per account in future |
| isNewUser | boolean | No | Indicates first-time authentication; triggers onboarding flows and welcome bonuses in client application |

#### 6.1.3 Token Refresh Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| refreshToken | string | Yes | Valid unexpired refresh token | The long-lived token issued during authentication; validated against stored token to issue new access token |

#### 6.1.4 Token Refresh Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| accessToken | string | No | New JWT token replacing the expired one |
| expiresIn | integer | No | Validity period of new token in seconds |

---

### 6.2 User DTOs

#### 6.2.1 User Profile Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| userId | string | No | Unique internal identifier for the user account |
| walletAddress | string | No | Primary blockchain wallet linked to account; displayed in UI and used for deposits/withdrawals |
| username | string | Yes | Optional display name for leaderboards and social features; null if not set |
| email | string | Yes | Optional email for notifications and account recovery; null if not provided |
| emailVerified | boolean | No | Whether email ownership is confirmed; affects notification delivery and security features |
| kycStatus | string | No | Know Your Customer verification status (none, pending, verified, rejected); determines withdrawal limits and feature access |
| kycTier | integer | No | Verification level (0-3) determining transaction limits; higher tiers require more documentation but allow larger transactions |
| createdAt | string | No | Account creation timestamp in ISO 8601 format; used for tenure-based benefits and analytics |
| lastActiveAt | string | No | Most recent activity timestamp; used for session management and re-engagement campaigns |
| preferences | object | No | User configuration including timezone, odds format, default stake; personalizes the experience |

#### 6.2.2 User Preferences Update Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| timezone | string | No | Valid IANA timezone identifier | Displays event times in user's local timezone; affects notification scheduling |
| oddsFormat | string | No | One of: decimal, american, fractional | Odds display preference; decimal (2.0), american (+100), or fractional (1/1) based on user's familiarity |
| defaultStake | number | No | Positive number, max 6 decimal places | Pre-fills stake input for faster order placement; reduces friction for regular bettors |
| notificationSettings | object | No | Valid notification preferences object | Controls which events trigger push/email notifications (bet matched, bet settled, price alerts) |
| responsibleGambling | object | No | Valid RG settings object | Self-imposed limits for deposit amounts, loss limits, and session duration; supports responsible gambling compliance |

#### 6.2.3 User Statistics Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| userId | string | No | User identifier for the statistics |
| totalBetsPlaced | integer | No | Lifetime count of bets; used for loyalty tiers and user segmentation |
| totalBetsWon | integer | No | Lifetime winning bets; calculates win rate for user profile |
| totalBetsLost | integer | No | Lifetime losing bets; calculates loss patterns for responsible gambling monitoring |
| totalAmountWagered | string | No | Lifetime stake volume as decimal string; determines fee tier and VIP status |
| totalProfitLoss | string | No | Net profit/loss as decimal string; displayed on profile, used for leaderboards |
| winRate | number | No | Percentage of winning bets; displayed on profile for social proof |
| averageOdds | number | No | Mean odds of placed bets; indicates user's risk preference |
| favoriteMarkets | array | No | Most frequently bet market types; used for personalized recommendations |
| currentStreak | object | No | Current winning/losing streak details; gamification element for engagement |

---

### 6.3 Market DTOs

#### 6.3.1 Markets List Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| sportId | string | No | Valid sport identifier | Filters markets by sport (football, basketball, etc.); null returns all sports |
| competitionId | string | No | Valid competition identifier | Filters by league/tournament; enables browsing by competition |
| status | string | No | One of: scheduled, live, closed, settled | Filters by market lifecycle stage; users typically want scheduled or live markets |
| startTimeFrom | string | No | ISO 8601 datetime | Lower bound for event start time; enables date-based filtering |
| startTimeTo | string | No | ISO 8601 datetime | Upper bound for event start time; combined with startTimeFrom for date range queries |
| page | integer | No | Positive integer, default 1 | Pagination offset; avoids loading all markets at once |
| pageSize | integer | No | 1-100, default 20 | Results per page; balances payload size with API call frequency |
| sortBy | string | No | One of: startTime, volume, liquidity | Sort order for results; startTime most common, volume/liquidity for active traders |
| sortDirection | string | No | One of: asc, desc | Ascending or descending sort; typically ascending for startTime, descending for volume |

#### 6.3.2 Markets List Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| markets | array | No | Array of market summary objects matching query criteria |
| totalCount | integer | No | Total markets matching filters; enables pagination UI (showing "page X of Y") |
| page | integer | No | Current page number; confirms pagination state |
| pageSize | integer | No | Results per page; confirms applied page size |
| hasMore | boolean | No | Whether additional pages exist; simplifies "load more" logic |

#### 6.3.3 Market Summary Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Unique market identifier for subsequent API calls |
| eventId | string | No | Parent event identifier; groups related markets (same match, different bet types) |
| sportId | string | No | Sport category; enables sport-specific UI treatments |
| sportName | string | No | Human-readable sport name for display |
| competitionId | string | No | League/tournament identifier |
| competitionName | string | No | Human-readable competition name |
| eventName | string | No | Event description (e.g., "Team A vs Team B") |
| marketType | string | No | Market category (moneyline, spread, total); determines UI layout |
| marketName | string | No | Specific market description (e.g., "Match Winner", "Total Points Over/Under 45.5") |
| outcomes | array | No | Possible outcomes with current best odds |
| status | string | No | Current lifecycle status (scheduled, live, suspended, closed, settled) |
| startTime | string | No | Event start time in ISO 8601; used for countdown displays and sorting |
| inPlayAvailable | boolean | No | Whether live betting is offered; affects UI and user expectations |
| totalVolume | string | No | Total amount matched on this market; indicator of market activity and reliability |
| liquidityDepth | object | No | Summary of available liquidity at various price levels; helps users gauge fillability |

#### 6.3.4 Market Detail Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | Yes | Valid market identifier | Specifies which market to retrieve full details for |
| includeOrderBook | boolean | No | Default true | Whether to include full order book depth; can be false for lightweight queries |
| orderBookDepth | integer | No | 1-20, default 5 | Number of price levels to include per side; balances detail with payload size |

#### 6.3.5 Market Detail Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Market identifier |
| eventId | string | No | Parent event identifier |
| eventDetails | object | No | Full event information including teams, venue, broadcast info |
| marketType | string | No | Market category |
| marketName | string | No | Market description |
| outcomes | array | No | Full outcome definitions with IDs and names |
| orderBook | object | Yes | Order book data if requested; structure defined in Order Book DTO |
| status | string | No | Current market status |
| startTime | string | No | Event start time |
| suspendedReason | string | Yes | Explanation if market is suspended (injury, weather, etc.); null if not suspended |
| rules | object | No | Market-specific rules (settlement rules, dead heat rules, void conditions) |
| statistics | object | No | Market statistics (volume, trade count, price history) |
| externalIds | object | No | Mapping to external exchange market IDs; enables cross-platform price comparison |

---

### 6.4 Order Book DTOs

#### 6.4.1 Order Book Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Market this order book represents |
| outcomeId | string | No | Specific outcome within market (e.g., "Team A to win") |
| lastUpdated | string | No | Timestamp of last order book change; enables client-side cache invalidation |
| backs | array | No | Array of price levels for backing (betting for) this outcome, sorted by odds descending |
| lays | array | No | Array of price levels for laying (betting against) this outcome, sorted by odds ascending |
| lastMatchedPrice | number | Yes | Most recent trade price; null if no trades yet |
| lastMatchedTime | string | Yes | Timestamp of most recent trade; null if no trades |
| totalBackLiquidity | string | No | Sum of all back order stakes; quick liquidity indicator |
| totalLayLiquidity | string | No | Sum of all lay order stakes |
| spreadPercentage | number | Yes | Gap between best back and lay as percentage; null if one side empty |

#### 6.4.2 Price Level Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| odds | number | No | Price for this level in decimal format |
| availableStake | string | No | Total stake available at this price level; aggregates multiple orders |
| orderCount | integer | No | Number of orders at this price level; indicates if liquidity is from one large order or many small ones |
| isAggregated | boolean | No | Whether this includes external liquidity; true if partially sourced from external exchanges |

---

### 6.5 Order DTOs

#### 6.5.1 Place Order Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | Yes | Valid active market | Identifies which market the order is for |
| outcomeId | string | Yes | Valid outcome in market | Specifies which outcome within the market |
| side | string | Yes | One of: back, lay | Back means betting for the outcome; lay means betting against |
| orderType | string | Yes | One of: limit, market | Limit orders specify exact odds; market orders accept best available price |
| odds | number | Conditional | Greater than 1.01, max 1000 | Required for limit orders; the price at which user will trade |
| stake | string | Yes | Positive decimal, max 2 decimal places | Amount to wager in USDC; string to preserve precision |
| liability | string | Conditional | Positive decimal | Required for lay orders; maximum amount user could lose if outcome occurs |
| persistenceType | string | No | One of: lapse, persist, market_on_close | Behavior when market goes in-play: lapse (cancel), persist (keep), or market_on_close (match at start) |
| minFillStake | string | No | Positive decimal, less than stake | Minimum acceptable partial fill; order rejected if this minimum cannot be immediately matched |
| timeInForce | string | No | One of: GTC, IOC, FOK | Good Till Cancel (default), Immediate or Cancel, Fill or Kill; controls order lifetime |
| referenceId | string | No | Max 64 characters | Client-generated reference for idempotency and reconciliation |

#### 6.5.2 Place Order Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | System-generated unique order identifier |
| referenceId | string | Yes | Client reference echoed back if provided |
| status | string | No | Order status: open, partial, filled, cancelled, rejected |
| side | string | No | Confirms order side |
| odds | number | No | Confirms order odds (may differ from request for market orders) |
| requestedStake | string | No | Original stake requested |
| filledStake | string | No | Amount already matched; 0 for fully unmatched |
| remainingStake | string | No | Amount still in order book |
| averageFilledOdds | number | Yes | Weighted average odds of fills; null if no fills yet |
| reservedAmount | string | No | Funds locked for this order |
| createdAt | string | No | Order creation timestamp |
| fills | array | No | Array of individual match events if partially/fully filled |
| rejectionReason | string | Yes | Explanation if order rejected; null otherwise |

#### 6.5.3 Cancel Order Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| orderId | string | Yes | Valid open or partial order | Identifies which order to cancel |
| reason | string | No | Max 256 characters | Optional user-provided reason; logged for analytics |

#### 6.5.4 Cancel Order Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | Confirms which order was cancelled |
| status | string | No | New status (cancelled) |
| cancelledStake | string | No | Amount that was unmatched and returned to available balance |
| filledStake | string | No | Amount that was already matched before cancellation; remains as open bet |
| releasedAmount | string | No | Funds returned to available balance |
| cancelledAt | string | No | Timestamp of cancellation |

#### 6.5.5 Order History Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| status | string | No | Comma-separated list of statuses | Filter by order status; null returns all |
| marketId | string | No | Valid market identifier | Filter by specific market |
| side | string | No | One of: back, lay | Filter by order side |
| fromDate | string | No | ISO 8601 datetime | Start of date range |
| toDate | string | No | ISO 8601 datetime | End of date range |
| page | integer | No | Positive integer, default 1 | Pagination offset |
| pageSize | integer | No | 1-100, default 20 | Results per page |

#### 6.5.6 Order History Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orders | array | No | Array of order detail objects |
| totalCount | integer | No | Total orders matching filters |
| page | integer | No | Current page |
| pageSize | integer | No | Results per page |
| hasMore | boolean | No | Whether more pages exist |

#### 6.5.7 Order Detail Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | Unique order identifier |
| referenceId | string | Yes | Client reference if provided |
| marketId | string | No | Market identifier |
| marketName | string | No | Human-readable market name |
| outcomeId | string | No | Outcome identifier |
| outcomeName | string | No | Human-readable outcome name |
| side | string | No | Back or lay |
| orderType | string | No | Limit or market |
| status | string | No | Current order status |
| requestedOdds | number | Yes | Original requested odds; null for market orders |
| requestedStake | string | No | Original requested stake |
| filledStake | string | No | Amount matched |
| remainingStake | string | No | Amount in order book |
| averageFilledOdds | number | Yes | Weighted average fill price |
| createdAt | string | No | Order creation timestamp |
| updatedAt | string | No | Last update timestamp |
| fills | array | No | Array of match details |

---

### 6.6 Bet DTOs

#### 6.6.1 Active Bets Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | No | Valid market identifier | Filter bets by specific market |
| status | string | No | One of: matched, partially_settled | Filter by bet status |
| page | integer | No | Positive integer, default 1 | Pagination offset |
| pageSize | integer | No | 1-100, default 20 | Results per page |

#### 6.6.2 Active Bets Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| bets | array | No | Array of active bet objects |
| totalExposure | string | No | Sum of potential losses across all active bets; key risk metric |
| totalPotentialProfit | string | No | Sum of potential winnings; motivational display |
| totalCount | integer | No | Total active bets |
| page | integer | No | Current page |
| pageSize | integer | No | Results per page |

#### 6.6.3 Bet Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| betId | string | No | Unique bet identifier |
| orderId | string | No | Originating order identifier; links bet to order |
| marketId | string | No | Market identifier |
| marketName | string | No | Human-readable market name |
| eventStartTime | string | No | When the sporting event begins |
| outcomeId | string | No | Outcome identifier |
| outcomeName | string | No | Human-readable outcome name |
| side | string | No | Back or lay |
| odds | number | No | Matched odds for this bet |
| stake | string | No | Amount wagered |
| liability | string | No | Maximum loss (stake for backs, calculated for lays) |
| potentialProfit | string | No | Maximum win if bet succeeds |
| status | string | No | Bet status (matched, won, lost, void, partially_settled) |
| matchedAt | string | No | When the bet was matched |
| settledAt | string | Yes | When bet was settled; null if unsettled |
| settlementAmount | string | Yes | Amount won or lost; null if unsettled |
| counterpartyType | string | No | Whether matched with user or external exchange; transparency for users |

#### 6.6.4 Bet History Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| status | string | No | Comma-separated list | Filter by settlement status |
| sportId | string | No | Valid sport identifier | Filter by sport |
| fromDate | string | No | ISO 8601 datetime | Start of date range |
| toDate | string | No | ISO 8601 datetime | End of date range |
| page | integer | No | Positive integer, default 1 | Pagination offset |
| pageSize | integer | No | 1-100, default 20 | Results per page |

#### 6.6.5 Bet History Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| bets | array | No | Array of settled bet objects |
| summary | object | No | Aggregate statistics for the filtered period |
| totalCount | integer | No | Total bets matching filters |
| page | integer | No | Current page |
| pageSize | integer | No | Results per page |

#### 6.6.6 Bet History Summary Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| totalBets | integer | No | Count of bets in period |
| totalStaked | string | No | Sum of all stakes |
| totalReturns | string | No | Sum of all settlement returns |
| netProfitLoss | string | No | Net result for period |
| winCount | integer | No | Number of winning bets |
| lossCount | integer | No | Number of losing bets |
| voidCount | integer | No | Number of voided bets |
| winRate | number | No | Percentage of non-void bets won |

---

### 6.7 Wallet DTOs

#### 6.7.1 Wallet Balance Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| userId | string | No | User identifier |
| currency | string | No | Always "USDC" for this platform |
| availableBalance | string | No | Funds available for new orders or withdrawal; decimal string for precision |
| reservedBalance | string | No | Funds locked in open orders and unsettled bets |
| totalBalance | string | No | Sum of available and reserved |
| pendingDeposits | string | No | Deposits awaiting blockchain confirmation |
| pendingWithdrawals | string | No | Withdrawals being processed |
| lastUpdated | string | No | Timestamp of last balance change |

#### 6.7.2 Deposit Address Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| chain | string | Yes | One of: base, polygon, ethereum | Blockchain to generate deposit address for; affects displayed address and confirmation requirements |

#### 6.7.3 Deposit Address Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| address | string | No | Blockchain address to send USDC to |
| chain | string | No | Confirms which chain this address is for |
| currency | string | No | Confirms expected currency (USDC) |
| minimumDeposit | string | No | Minimum deposit amount to be credited; prevents dust deposits |
| confirmationsRequired | integer | No | Block confirmations needed before credit; varies by chain |
| expiresAt | string | Yes | Address expiration if applicable; null if permanent |
| memo | string | Yes | Additional identifier if required by address type; null if not needed |
| qrCodeUrl | string | No | URL to QR code image for mobile wallet scanning |

#### 6.7.4 Withdrawal Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| amount | string | Yes | Positive decimal, within available balance | Amount of USDC to withdraw |
| destinationAddress | string | Yes | Valid blockchain address | Where to send the funds |
| chain | string | Yes | One of: base, polygon, ethereum | Which blockchain to use for withdrawal |
| twoFactorCode | string | Conditional | 6-digit code | Required if 2FA enabled; security verification |

#### 6.7.5 Withdrawal Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| withdrawalId | string | No | Unique withdrawal identifier for tracking |
| status | string | No | Withdrawal status (pending, processing, completed, failed) |
| amount | string | No | Amount being withdrawn |
| fee | string | No | Network fee deducted from amount |
| netAmount | string | No | Amount recipient will receive |
| destinationAddress | string | No | Confirmed destination |
| chain | string | No | Confirmed blockchain |
| estimatedCompletionTime | string | No | Expected completion timestamp |
| transactionHash | string | Yes | Blockchain transaction hash; null until broadcast |
| createdAt | string | No | Request timestamp |

#### 6.7.6 Transaction History Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| type | string | No | One of: deposit, withdrawal, bet_stake, bet_settlement, fee, bonus | Filter by transaction type |
| fromDate | string | No | ISO 8601 datetime | Start of date range |
| toDate | string | No | ISO 8601 datetime | End of date range |
| page | integer | No | Positive integer, default 1 | Pagination offset |
| pageSize | integer | No | 1-100, default 50 | Results per page |

#### 6.7.7 Transaction History Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| transactions | array | No | Array of transaction objects |
| totalCount | integer | No | Total transactions matching filters |
| page | integer | No | Current page |
| pageSize | integer | No | Results per page |

#### 6.7.8 Transaction Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| transactionId | string | No | Unique transaction identifier |
| type | string | No | Transaction type |
| amount | string | No | Transaction amount (positive for credits, negative for debits) |
| balanceAfter | string | No | Balance immediately after transaction; enables reconciliation |
| description | string | No | Human-readable description |
| referenceId | string | Yes | Related entity ID (orderId, betId, withdrawalId); null for standalone |
| referenceType | string | Yes | Type of reference entity; null for standalone |
| timestamp | string | No | When transaction occurred |
| metadata | object | Yes | Additional context (blockchain hash for deposits, market name for bets) |

---

### 6.8 Liquidity Aggregation DTOs (Internal)

These DTOs are used internally between services and are not exposed via public API.

#### 6.8.1 External Odds Query

| Field | Type | Required | Rationale |
|-------|------|----------|-----------|
| internalMarketId | string | Yes | Our market identifier to look up |
| outcomeId | string | Yes | Specific outcome within market |
| side | string | Yes | Back or lay position needed |
| minimumStake | string | Yes | Minimum liquidity required |

#### 6.8.2 External Odds Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| available | boolean | No | Whether external liquidity exists |
| sources | array | No | Array of available external sources with prices |
| bestOdds | number | Yes | Best available price across sources; null if unavailable |
| bestSource | string | Yes | Which exchange offers best price; null if unavailable |
| availableStake | string | Yes | Maximum stake at best price; null if unavailable |
| commission | number | Yes | External exchange commission rate; null if unavailable |
| effectiveOdds | number | Yes | Odds after commission adjustment; null if unavailable |
| latencyMs | integer | No | Time to fetch external odds; monitors integration health |
| timestamp | string | No | When prices were fetched; staleness indicator |

#### 6.8.3 Hedge Execution Request

| Field | Type | Required | Rationale |
|-------|------|----------|-----------|
| internalOrderId | string | Yes | Links hedge to internal order for reconciliation |
| source | string | Yes | Which external exchange to use |
| externalMarketId | string | Yes | Market identifier on external exchange |
| side | string | Yes | Back or lay |
| odds | number | Yes | Price to execute at |
| stake | string | Yes | Amount to hedge |
| urgency | string | Yes | Execution priority (immediate, best_effort) |

#### 6.8.4 Hedge Execution Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| hedgeId | string | No | Unique hedge identifier |
| internalOrderId | string | No | Linked internal order |
| source | string | No | External exchange used |
| status | string | No | Execution status (filled, partial, failed) |
| requestedOdds | number | No | Requested price |
| executedOdds | number | Yes | Actual fill price; null if failed |
| requestedStake | string | No | Requested amount |
| executedStake | string | No | Actually filled amount |
| commission | string | No | Commission paid to external exchange |
| netCost | string | No | Total cost including commission |
| externalBetId | string | Yes | Bet ID on external exchange; null if failed |
| executedAt | string | Yes | Execution timestamp; null if failed |
| failureReason | string | Yes | Explanation if failed; null if successful |

---

### 6.9 Settlement DTOs (Internal)

#### 6.9.1 Settlement Trigger

| Field | Type | Required | Rationale |
|-------|------|----------|-----------|
| marketId | string | Yes | Market to settle |
| winningOutcomeId | string | Yes | Outcome that won |
| resultSource | string | Yes | Where result came from (official_feed, manual_verification) |
| resultDetails | object | Yes | Detailed result (scores, times, official confirmation) |
| settlementTime | string | Yes | When settlement is being processed |
| operator | string | Yes | User ID of operator if manual; system if automatic |

#### 6.9.2 Settlement Result

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Settled market |
| settlementId | string | No | Unique settlement batch identifier |
| winningOutcomeId | string | No | Confirmed winning outcome |
| totalBetsSettled | integer | No | Number of bets processed |
| totalStakeSettled | string | No | Sum of all settled stakes |
| totalPayouts | string | No | Sum of all winner payouts |
| platformCommission | string | No | Total commission collected |
| settledAt | string | No | Settlement completion timestamp |
| settlementDetails | array | No | Per-bet settlement records |
| hedgeSettlements | array | No | External hedge position settlements |

---

## 7. Component Algorithms

This section describes the algorithmic logic for each major system component. Descriptions focus on the conceptual approach rather than implementation details.

### 7.1 Order Matching Algorithm

**Overview**
The matching engine implements a price-time priority algorithm, the standard for financial exchanges. Orders are matched based on best price first, with earlier orders receiving priority at identical prices.

**Matching Process**

Step 1: Order Validation
When a new order arrives, the system validates that the user has sufficient balance (for backs) or sufficient balance to cover liability (for lays). It confirms the market is active and accepting orders. For limit orders, it verifies the odds are within acceptable bounds.

Step 2: Order Book Query
The engine queries the opposite side of the order book. For a new back order, it examines existing lay orders. For a new lay order, it examines existing backs. Orders are retrieved in price-time priority: best price first, then earliest timestamp.

Step 3: Match Evaluation
For each potential counterparty order, the engine evaluates whether the prices are compatible. A back order at odds X can match with a lay order at odds Y if X is less than or equal to Y. The matched odds are the price of the resting (earlier) order, rewarding liquidity providers.

Step 4: Quantity Determination
The matched quantity is the minimum of the incoming order's remaining stake and the resting order's available stake. This may result in partial fills for either or both orders.

Step 5: Match Execution
For each match, the engine creates a matched bet record linking both parties. It updates both orders' filled and remaining quantities. It adjusts both users' reserved balances accordingly. It generates match events for downstream systems.

Step 6: Residual Handling
If the incoming order has remaining stake after matching all compatible resting orders, the behavior depends on order type. Limit orders join the order book at their specified price. Market orders are cancelled for the unfilled portion. IOC (Immediate or Cancel) orders are cancelled entirely.

Step 7: External Liquidity Check
If internal matching did not fully satisfy the order and the order type permits, the system queries the Liquidity Aggregation Service. If external liquidity exists at profitable spreads, the system may accept additional fill against external hedging.

**Edge Cases**

Self-matching prevention: Orders from the same user on opposite sides of the same market are not matched against each other. The incoming order skips past same-user resting orders.

Minimum fill enforcement: If an order specifies a minimum fill amount and this cannot be immediately satisfied, the entire order is rejected rather than partially filled below the minimum.

Suspended markets: Orders submitted to suspended markets are rejected. Resting orders remain in the book but are not matchable until the market resumes.

### 7.2 Liquidity Aggregation Algorithm

**Overview**
The aggregator maintains real-time connections to external exchanges and provides synthetic liquidity when internal order books are insufficient.

**Price Monitoring Process**

Step 1: Market Mapping
Each internal market maintains mappings to equivalent markets on external exchanges. These mappings account for differences in market structure (e.g., our "Match Winner" maps to Betfair's "Match Odds" market with specific runner IDs).

Step 2: Continuous Price Fetching
Background workers poll external exchanges at configurable intervals (typically 1-5 seconds depending on the source). Each poll retrieves current best prices, available liquidity, and market status.

Step 3: Price Normalization
External prices are converted to our decimal odds format. Commission rates are applied to calculate effective prices. For example, Betfair odds of 2.0 with 5% commission yields effective odds of approximately 1.95 for backers.

Step 4: Spread Calculation
The system continuously calculates the spread between our users' potential orders and external effective prices. A positive spread indicates profitable hedging opportunity.

**Hedging Decision Process**

Step 1: Order Assessment
When an order cannot be internally matched, the system evaluates external hedging viability. It queries current external prices for the required side, outcome, and stake.

Step 2: Profitability Calculation
The system calculates whether accepting the user's order and hedging externally yields profit. Variables include: user's order odds, external effective odds, our commission rate, and external commission rate.

Formula concept: Spread = (User odds - External effective odds) × Stake - Our commission - Transaction costs

Step 3: Execution Decision
If spread is positive and exceeds minimum profit threshold, the system accepts the user's order at their requested price and simultaneously places the hedge on the external exchange.

Step 4: Hedge Execution
The external order is placed via the exchange's API. Execution may be immediate (for market orders) or uncertain (for limit orders). The system monitors hedge status.

Step 5: Reconciliation
If the external hedge fully executes, the user's order is confirmed matched. If the external hedge partially fills, the user's order is partially matched for that amount. If the external hedge fails entirely, the user's order is cancelled and funds released.

**Risk Controls**

Maximum exposure per market: The system limits total unhedged exposure across any single market. Once reached, additional orders are only accepted if internally matchable.

Latency monitoring: External API latency is tracked. If latency exceeds thresholds, the system reduces reliance on that source to avoid stale prices.

Source diversification: When multiple external sources offer similar prices, the system distributes hedges to reduce counterparty concentration risk.

### 7.3 Settlement Algorithm

**Overview**
Settlement processes the outcome of sporting events, determining bet winners and losers, and distributing funds accordingly.

**Settlement Process**

Step 1: Result Ingestion
Sporting event results arrive from data providers via webhook or polling. Results include the final score, outcome determination, and any special circumstances (postponements, walkovers, dead heats).

Step 2: Result Verification
Critical markets undergo multi-source verification. The system compares results from multiple providers. Discrepancies trigger manual review queue. Minor markets may settle on single-source confirmation after delay.

Step 3: Market Resolution
Once verified, the market is marked with its winning outcome. All outcomes are classified as won, lost, or void based on the result and market rules.

Step 4: Bet Identification
The system queries all matched bets on the market. Each bet is categorized by its outcome: bets on the winning outcome are winners, bets on losing outcomes are losers.

Step 5: Payout Calculation
For each winning bet:
- Back bet payout: Stake × Odds (includes original stake)
- Lay bet payout: Original stake returned (the backer lost)

For each losing bet:
- Back bet: Stake forfeited (already in reserve)
- Lay bet: Liability paid to counterparty (from reserve)

Step 6: Commission Deduction
Platform commission is calculated on net winnings. For a back bet that won: Commission = (Payout - Stake) × Commission Rate. Lay bet winners (where the backed outcome lost) pay commission on the collected stake.

Step 7: Balance Updates
Winner accounts are credited with their net winnings (after commission). Loser reserves are released and transferred to winners. All transactions are logged with settlement reference.

Step 8: Hedge Settlement
For bets matched against external hedges, the system reconciles external settlement. External winnings are collected via exchange APIs. External losses are paid from platform treasury. Net position should be neutral plus commission earned.

**Special Circumstances**

Dead heats: When multiple outcomes tie, stakes are divided proportionally. A two-way dead heat results in half-stake settlement.

Void bets: Certain circumstances (event not completed, betting rule violations) result in void bets. All void bet stakes are returned to both parties without commission.

Partial settlement: Some markets support partial early settlement (e.g., first goal scorer when player scores but match continues). The system handles partial resolution while keeping other outcomes open.

### 7.4 Risk Management Algorithm

**Overview**
Risk management ensures the platform maintains its zero-house-risk position by monitoring exposures and enforcing limits.

**Exposure Monitoring**

Step 1: Position Aggregation
The system continuously calculates net position across all outcomes for each market. Internal matches are net neutral. External hedges create potential timing risk until settled.

Step 2: Unhedged Exposure Detection
Any bet matched internally where the counterparty subsequently defaults (insufficient settlement funds) creates unhedged exposure. The system flags accounts with settlement risk for enhanced monitoring.

Step 3: External Hedge Position Tracking
All external hedge positions are tracked against their internal counterparts. Position mismatches (external partial fill, external void while internal stands) are flagged for manual resolution.

**Limit Enforcement**

User betting limits: Each user has maximum stake limits per bet and per market, determined by their KYC tier and account history. Limit orders exceeding maximums are rejected.

Market exposure limits: Total matched volume per market is capped based on available external hedging depth. Markets approaching limits show reduced available liquidity.

Concentration limits: Single user cannot exceed percentage threshold of total market volume. Prevents market manipulation and settlement risk concentration.

Loss limits: Users can set personal loss limits (responsible gambling feature). System blocks new bets once daily/weekly/monthly loss limit reached.

**Anomaly Detection**

Pattern recognition: System monitors for suspicious patterns including rapid-fire opposing bets (potential wash trading), consistent last-second bets before results (potential information asymmetry), and unusual stake sizes relative to account history.

Response actions: Detected anomalies trigger alerts to operations team. Severe patterns trigger automatic account suspension pending review.

### 7.5 Order Book Synchronization Algorithm

**Overview**
Maintains consistency between the authoritative order book state (in memory and database) and client views across WebSocket connections.

**State Management**

Master state: The matching engine maintains the authoritative order book state in memory (Redis) with persistent backup (PostgreSQL). All modifications flow through the matching engine.

Sequence numbering: Every order book modification generates a sequence number. This enables clients to detect missed updates and request resync.

**Update Distribution**

Step 1: Modification Event
When the order book changes (new order, match, cancellation), the matching engine generates an update event containing: market ID, sequence number, change type, affected price levels, and new quantities.

Step 2: Event Publishing
Updates publish to a message queue (Kafka topic partitioned by market). This ensures ordered delivery per market.

Step 3: WebSocket Distribution
WebSocket servers subscribe to relevant topics based on connected client subscriptions. Each receives updates and forwards to subscribed clients.

Step 4: Client Application
Clients receive incremental updates and apply them to local order book state. If sequence numbers indicate missed updates, client requests full snapshot.

**Snapshot Handling**

Periodic snapshots: Full order book snapshots are generated periodically (every N updates or time interval). These are cached for fast delivery to new subscribers.

On-demand snapshots: New WebSocket connections receive latest snapshot plus any subsequent updates. This minimizes snapshot generation load while ensuring consistency.

---

## 8. Blockchain Payment Design (Base Chain)

### 8.1 Chain Selection Rationale

**Why Base Chain**

Base is selected as the primary blockchain for several strategic reasons.

Transaction costs: Base consistently delivers transaction fees under $0.05 USD, often as low as $0.01. This enables micro-betting and frequent trading without fee erosion eating into user stakes.

Settlement speed: Base achieves block times of approximately 2 seconds with typical finality within 10-20 seconds. This allows near-instant deposit crediting and responsive withdrawal processing.

USDC support: Circle provides native USDC issuance on Base, meaning Base USDC is first-class stablecoin rather than a bridged token. This reduces smart contract risk and ensures full Circle backing.

Coinbase integration: As a Coinbase-incubated chain, Base has seamless Coinbase on/off ramp integration. Users can fund accounts directly from Coinbase with zero bridging friction.

Ecosystem growth: Base has rapidly growing developer ecosystem and user adoption. Building on Base positions the platform within a thriving ecosystem rather than an isolated chain.

**Multi-Chain Support (Future)**

While Base is the primary chain at launch, the architecture supports adding additional chains. Polygon (low fees, established ecosystem) and Arbitrum (DeFi liquidity) are candidates for future integration based on user demand.

### 8.2 Smart Contract Architecture

The platform utilizes four primary smart contracts deployed on Base.

**Treasury Contract**
Holds the platform's operational USDC reserves. Functions include: receiving user deposits, disbursing user withdrawals, paying external hedge settlements, and collecting commission revenues. The contract implements multi-signature controls requiring multiple authorized signers for large withdrawals or parameter changes.

**Deposit Router Contract**
Manages user deposit addresses and routing. Each user receives a unique deposit address (deterministically derived from their user ID). Deposits to these addresses are automatically routed to the Treasury. The contract emits deposit events consumed by the backend deposit monitoring service.

**Withdrawal Processor Contract**
Handles withdrawal queue and execution. Withdrawal requests are submitted by the backend after off-chain verification. The contract verifies request signatures, checks daily limits, and executes transfers. Emergency pause functionality allows halting withdrawals if security concerns arise.

**Escrow Contract (Future Phase)**
For users preferring non-custodial betting, an escrow contract will hold bet stakes until settlement. Both parties deposit their stakes/liability. Settlement outcome is submitted by oracle, triggering automatic payout. This provides trustless betting for users willing to pay higher gas costs.

### 8.3 Deposit Flow

**User Journey**

Step 1: Address Generation
User navigates to deposit screen and selects Base chain. System generates their unique deposit address (or retrieves existing). Address is displayed with QR code for mobile wallet scanning.

Step 2: User Transfer
User sends USDC from their wallet (Coinbase, MetaMask, Rainbow, etc.) to the displayed address. They see estimated confirmation time and minimum deposit amount.

Step 3: Transaction Detection
The deposit monitoring service detects the incoming transaction via Base node subscription. Transaction details are logged immediately.

Step 4: Confirmation Waiting
The system waits for sufficient block confirmations. Base requires 12-20 confirmations for security (approximately 30-60 seconds). User sees pending deposit in their balance display.

Step 5: Credit
Once confirmed, the backend credits the user's available balance. A transaction record is created. User receives notification (in-app, push, and/or email based on preferences).

**Technical Flow**

Backend runs blockchain indexer service subscribing to Transfer events on USDC contract filtered to known deposit addresses. Each detected transfer triggers confirmation countdown. Upon sufficient confirmations, idempotent credit operation updates user balance in database within transaction ensuring atomicity.

### 8.4 Withdrawal Flow

**User Journey**

Step 1: Withdrawal Request
User enters amount and destination address. System displays estimated network fee and net amount to be received.

Step 2: Security Verification
If two-factor authentication is enabled, user provides 2FA code. System may impose cooling periods for new addresses or large amounts.

Step 3: Request Processing
Backend validates sufficient available balance, applies withdrawal limits, and queues the request.

Step 4: Execution
Automated processor (or manual approval for large amounts) signs and submits blockchain transaction via Withdrawal Processor Contract.

Step 5: Completion
Transaction is broadcast to network. User sees transaction hash immediately. Upon confirmation, withdrawal status updates to completed.

**Risk Controls**

Address whitelist: Users can optionally enable withdrawal address whitelisting. New addresses require 24-48 hour cooling period before withdrawals allowed.

Velocity limits: Daily and monthly withdrawal limits based on KYC tier. Tier 0 (no KYC): $1,000 daily. Tier 1 (basic KYC): $10,000 daily. Tier 2 (enhanced KYC): $100,000 daily.

Manual review queue: Withdrawals exceeding thresholds or exhibiting risk signals require operations team approval.

Hot/cold wallet split: Treasury maintains limited hot wallet balance for immediate withdrawals. Larger withdrawals draw from cold storage with additional approval requirements and processing time.

### 8.5 Balance Management

**Balance Types**

Available Balance: Funds immediately usable for placing orders or withdrawal. Increases with deposits, bet winnings, and order cancellations. Decreases with order placement and withdrawal initiation.

Reserved Balance: Funds locked for open orders (potential stake/liability) and unsettled bets. Cannot be withdrawn. Automatically adjusted as orders match, cancel, or bets settle.

Pending Balance: Deposits awaiting confirmation and withdrawals in processing. Displayed separately to set user expectations.

**Reserve Calculation**

Back Order Reserve: For a back order, the reserve equals the stake amount. If the user places a $100 back order at 2.0 odds, $100 is moved from available to reserved.

Lay Order Reserve: For a lay order, the reserve equals the potential liability. If the user places a lay order accepting $100 stake at 3.0 odds, their liability is $200 (they pay $300 if they lose, receiving the $100 stake). $200 is reserved.

Matched Bet Reserve: Once matched, reserves remain held until settlement. Winner's reserve plus loser's reserve combine to pay winner.

### 8.6 Settlement on Chain (Future Phase)

Initial launch performs settlement off-chain with on-chain deposits and withdrawals only. Future phases introduce optional on-chain settlement for users desiring full transparency and trustlessness.

**On-Chain Settlement Flow**

Step 1: Bet Creation
When two users match, both submit transactions locking their stakes into Escrow Contract. Contract holds funds until settlement.

Step 2: Event Resolution
Authorized oracle (initially centralized, later Chainlink or UMA) submits event result to Settlement Oracle Contract.

Step 3: Automatic Payout
Escrow Contract reads result from oracle, determines winner, and automatically transfers combined stakes minus commission to winner.

**Trade-offs**

Advantages: Full transparency, no counterparty risk, censorship resistance.

Disadvantages: Higher costs (two transactions per bet), slower UX (transaction confirmation delays), complexity for users unfamiliar with blockchain transactions.

The off-chain-default with on-chain-optional approach serves both mainstream users (who want simplicity) and crypto-native users (who want trustlessness).

### 8.7 Gas Fee Management

**Platform-Sponsored Transactions**

For critical operations (settlement, commission collection), the platform sponsors gas fees rather than deducting from user balances. This simplifies UX and ensures settlement always executes regardless of user balance.

**User-Paid Transactions**

Withdrawals deduct network fee from withdrawal amount. The displayed net amount accurately reflects what users receive. Fee estimation uses recent Base gas prices with safety margin.

**Batching Optimization**

Multiple small settlements to the same user are batched into single transactions where possible. This reduces overall gas costs for high-frequency bettors.

---

## 9. Commission & Fee Structure

### 9.1 Core Commission Model

The platform uses a maker-taker fee model with commission on net winnings.

**Maker Fee (Liquidity Providers)**
Users who place limit orders adding liquidity to the order book pay 0.5% commission on net winnings from matched bets. The low fee incentivizes limit order placement, building order book depth.

**Taker Fee (Liquidity Takers)**
Users who place orders matching against existing orders pay 1.5% commission on net winnings. Higher fee reflects the value of immediate execution.

**Net Winnings Calculation**
Commission applies only to profit, not total returns. A winning back bet at $100 stake with $200 return has $100 net winnings, so 1.5% taker fee equals $1.50.

### 9.2 Volume-Based Discounts

High-volume users receive fee discounts based on rolling 30-day matched volume.

**Tier Structure**

Tier 1 (Standard): Below $10,000 monthly volume. Maker 0.5%, Taker 1.5%.

Tier 2 (Active): $10,000 to $50,000 monthly volume. Maker 0.3%, Taker 1.2%.

Tier 3 (VIP): $50,000 to $250,000 monthly volume. Maker 0.1%, Taker 0.8%.

Tier 4 (Elite): Above $250,000 monthly volume. Maker 0%, Taker 0.5%.

**Tier Calculation**
Volume is recalculated daily based on trailing 30-day matched stakes. Users automatically move between tiers. Tier status is displayed in account settings.

### 9.3 Special Fee Programs

**Market Maker Program**
Professional market makers who provide consistent liquidity across multiple markets may qualify for zero maker fees and reduced taker fees via application-based program.

**Referral Revenue Share**
Users referring new bettors receive 20% of the platform's commission revenue from referred users for 12 months. This creates alignment without requiring complex token mechanics.

**Launch Promotion**
First three months post-launch: all users receive Tier 3 pricing regardless of volume. Builds initial liquidity and user acquisition momentum.

### 9.4 Blockchain Fees

**Deposits**
Free from platform perspective. User pays only the sending transaction fee from their originating wallet.

**Withdrawals**
Network fee is deducted from withdrawal amount. User sees net amount clearly before confirming. Current Base fees typically $0.01-0.05 per withdrawal.

---

## 10. Launch Strategy

### 10.1 Phase 1: Private Alpha (Weeks 1-4)

**Objectives**
Validate core matching and settlement functionality. Identify critical bugs before public exposure. Gather initial user feedback on UX.

**Scope**
Approximately 100 invited users (internal team, advisors, select community members). Limited to top 3 sports (Football, Basketball, Tennis). Basic markets only (match winner). Platform provides majority of liquidity via internal market making bot.

**Success Criteria**
Zero critical bugs in matching or settlement. Order placement to match latency under 200ms. Settlement accuracy 100%.

### 10.2 Phase 2: Public Beta (Weeks 5-12)

**Objectives**
Scale to thousands of users. Validate liquidity aggregation under real conditions. Refine commission structure based on behavior.

**Scope**
Open registration with waitlist priority. Expanded to 10 sports and additional market types (spreads, totals). External liquidity aggregation fully operational. Mobile web optimization.

**Success Criteria**
5,000 registered users. $500,000 cumulative matched volume. Internal liquidity providing 30%+ of matches (reducing external hedging reliance).

### 10.3 Phase 3: Public Launch (Month 4+)

**Objectives**
Full market launch with comprehensive feature set. Native mobile applications. Marketing push for user acquisition.

**Scope**
All planned sports and market types. iOS and Android native apps. Advanced features (in-play betting, cash-out, social features). API access for third-party integration.

**Success Criteria**
50,000 registered users. $5,000,000 monthly matched volume. Internal liquidity providing 50%+ of matches. Positive unit economics (commission revenue exceeds hedging costs).

### 10.4 Geographic Rollout

**Initial Markets**
Launch in jurisdictions with favorable regulatory environment for peer-to-peer exchanges: select Latin American markets, certain Asian markets, and European markets where exchange betting is permitted.

**Expansion Approach**
Market-by-market legal analysis before entry. Geo-blocking for prohibited jurisdictions. Partnership with local payment providers where needed.

---

## 11. Risk Management

### 11.1 Operational Risks

**External Exchange Dependency**
Risk: External exchanges (Betfair, Smarkets) change APIs, increase fees, or restrict access.
Mitigation: Maintain integrations with multiple exchanges. Build internal liquidity to reduce external reliance over time. Contractual agreements where possible.

**Smart Contract Vulnerabilities**
Risk: Bugs in smart contracts enable fund theft or lock.
Mitigation: Professional security audits before launch. Formal verification for critical functions. Gradual treasury growth with limits. Bug bounty program.

**Settlement Disputes**
Risk: Users dispute event results or settlement calculations.
Mitigation: Multi-source result verification. Clear settlement rules published per market. Manual review process for disputes. Reserve fund for error remediation.

### 11.2 Financial Risks

**Hedging Execution Failure**
Risk: External hedge orders fail to execute after internal order is matched.
Mitigation: Pre-flight checks verify external liquidity before accepting orders. Automatic position unwinding if hedge fails. Exposure limits cap maximum unhedged risk.

**Stablecoin Depeg**
Risk: USDC loses parity with USD.
Mitigation: Primarily affects user perception as users both deposit and withdraw in USDC. Monitor depeg events and communicate transparently. Evaluate multi-stablecoin support if risks increase.

**Commission Erosion**
Risk: Competitive pressure forces commission reductions below sustainable levels.
Mitigation: Focus on liquidity depth as competitive advantage rather than pure price competition. Volume-based economics improve with scale.

### 11.3 Regulatory Risks

**Licensing Requirements**
Risk: Jurisdictions require gambling licenses for peer-to-peer exchange operation.
Mitigation: Legal analysis per market. Obtain licenses where feasible and beneficial. Exit markets where requirements are prohibitive.

**AML/KYC Obligations**
Risk: Regulatory requirements for user identification increase.
Mitigation: Tiered KYC already designed into platform. Partner with identity verification providers. Implement transaction monitoring.

---

## 12. Success Metrics

### 12.1 Primary KPIs

**Monthly Matched Volume**
Total USDC value of matched bets per month. Target: $1M (Month 6), $10M (Month 12).

**Internal Match Rate**
Percentage of matched volume occurring between platform users versus external hedging. Target: 30% (Month 6), 50% (Month 12).

**Net Revenue**
Commission revenue minus hedging costs and operational expenses. Target: Positive by Month 9.

### 12.2 User Metrics

**Registered Users**
Total accounts created. Target: 10,000 (Month 6), 50,000 (Month 12).

**Monthly Active Users**
Users placing at least one bet per month. Target: 20% of registered base.

**User Retention (30-Day)**
Percentage of new users returning within 30 days. Target: 40%.

### 12.3 Platform Health Metrics

**Order Book Depth**
Average USDC available within 3% of best price across active markets. Target: $10,000 average.

**Matching Latency**
Time from order submission to match or order book addition. Target: p99 under 100ms.

**Settlement Accuracy**
Percentage of bets settled correctly without dispute. Target: 99.99%.

---

## 13. Appendices

### Appendix A: Glossary

**Back**: A bet that an outcome will occur. Equivalent to a traditional bet.

**Lay**: A bet that an outcome will not occur. Taking the role of bookmaker for that outcome.

**Maker**: User whose limit order rests in the order book providing liquidity.

**Taker**: User whose order matches against resting orders, consuming liquidity.

**Liability**: Maximum potential loss on a lay bet. Calculated as (Odds - 1) × Stake.

**Matched Bet**: A bet where back and lay orders have been paired, creating binding obligations.

**Order Book**: Collection of all open orders for a market, organized by price and time.

**Settlement**: Process of determining bet outcomes after event conclusion and distributing funds.

### Appendix B: Odds Format Conversion

**Decimal to American**
If decimal odds ≥ 2.0: American = (Decimal - 1) × 100
If decimal odds < 2.0: American = -100 ÷ (Decimal - 1)

**Decimal to Fractional**
Fractional = (Decimal - 1) expressed as fraction
Example: 2.5 decimal = 3/2 fractional

**Implied Probability**
Probability = 1 ÷ Decimal Odds
Example: 2.0 odds = 50% implied probability

### Appendix C: Market Types Reference

**Match Winner (Moneyline)**: Bet on which team/player wins the match.

**Spread (Handicap)**: Bet adjusted by point spread. Team must win by more than spread or lose by less than spread.

**Total (Over/Under)**: Bet on whether combined score exceeds or falls below a specified number.

**Outright (Futures)**: Bet on tournament/season winner before competition concludes.

**Props (Specials)**: Bets on specific occurrences within an event (first scorer, total corners, etc.).

---

*End of Product Requirements Document*
