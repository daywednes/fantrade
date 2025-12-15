# MVP Product Requirements Document: BetFlow Exchange

## Document Information

| Field | Value |
|-------|-------|
| Product Name | BetFlow Exchange |
| Version | MVP 1.0 |
| Author | Product Team |
| Last Updated | December 2024 |
| Status | MVP Specification |
| Target Launch | 8 Weeks |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [MVP Scope Definition](#2-mvp-scope-definition)
3. [Personas](#3-personas)
4. [User Storylines](#4-user-storylines)
5. [MVP Architecture](#5-mvp-architecture)
6. [Core Components (MVP)](#6-core-components-mvp)
7. [Data Transfer Objects (DTOs)](#7-data-transfer-objects-dtos)
8. [Component Algorithms](#8-component-algorithms)
9. [Blockchain Payment Design](#9-blockchain-payment-design)
10. [MVP Launch Plan](#10-mvp-launch-plan)
11. [Success Criteria](#11-success-criteria)

---

## 1. Executive Summary

### 1.1 What We're Building

BetFlow Exchange is a peer-to-peer sports betting platform where users trade betting positions with each other through an order book model. Unlike traditional bookmakers, we never bet against users. We match buyers and sellers, taking a small commission on winnings.

### 1.2 MVP Hypothesis

We believe that active sports bettors will prefer an exchange model offering better odds and guaranteed order execution (via external hedging) over traditional bookmakers. We will validate this by launching with a single sport (Football/Soccer) and measuring user acquisition, retention, and trading volume.

### 1.3 MVP Core Features

The MVP includes only what's essential to test our hypothesis:

| Included | Excluded (Post-MVP) |
|----------|---------------------|
| Wallet-based authentication | Email/password auth |
| USDC deposits/withdrawals (Base chain) | Multi-chain support |
| Football/Soccer markets only | Other sports |
| Match winner markets only | Spreads, totals, props |
| Basic order book (back/lay) | Advanced order types |
| Single external hedge source (Betfair) | Multi-source aggregation |
| 2% flat commission | Tiered pricing |
| Web application only | Mobile apps |
| Manual KYC (if needed) | Automated KYC flow |

### 1.4 Timeline

| Phase | Duration | Milestone |
|-------|----------|-----------|
| Foundation | Weeks 1-2 | Auth, wallet, basic UI |
| Core Trading | Weeks 3-5 | Order book, matching, Betfair integration |
| Settlement | Weeks 6-7 | Results feed, settlement engine |
| Launch Prep | Week 8 | Testing, soft launch |

---

## 2. MVP Scope Definition

### 2.1 In Scope

**User Management**
- Wallet-based signup/login (MetaMask, Coinbase Wallet, WalletConnect)
- Basic profile (username, email for notifications)
- Session management with JWT tokens

**Wallet & Payments**
- USDC deposits on Base chain
- USDC withdrawals on Base chain
- Real-time balance display (available, reserved)
- Transaction history

**Markets**
- Football/Soccer only (top 5 European leagues + Champions League)
- Match winner (1X2) markets only
- Pre-match betting only (no in-play)
- Automated market creation from data feed

**Trading**
- Order book display with backs and lays
- Limit orders only (specify your price)
- Order placement, cancellation
- Basic order matching (price-time priority)
- External liquidity from Betfair when needed

**Settlement**
- Automated result ingestion
- Automated settlement and payout
- Basic settlement history

### 2.2 Out of Scope (Post-MVP)

- Multiple sports
- Spread and total markets
- In-play/live betting
- Market orders
- Advanced order types (IOC, FOK)
- Multi-chain deposits
- Mobile applications
- Social features
- Referral program
- Tiered commission
- Cash-out functionality
- API access for third parties

### 2.3 Technical Constraints

**Performance Targets (MVP)**
- Order placement latency: < 500ms (p95)
- Page load time: < 3 seconds
- Concurrent users supported: 500

**Infrastructure (MVP)**
- Single region deployment (US-East or EU-West)
- Basic monitoring and alerting
- Manual scaling as needed

---

## 3. Personas

### 3.1 Primary Persona: "Sharp Sam"

**Demographics**
- Age: 28-40
- Location: UK, Europe, or Latin America
- Occupation: Professional or semi-professional bettor
- Monthly betting volume: $5,000 - $50,000

**Background**
Sam has been betting on football for 8+ years. He understands odds deeply and shops across multiple bookmakers for the best prices. He's been limited or banned by several traditional bookmakers due to his consistent profitability. He currently uses Betfair Exchange but finds liquidity thin on smaller markets.

**Goals**
- Find the best possible odds for his selections
- Avoid being limited or banned for winning
- Execute larger stakes without moving the market
- Trade in and out of positions as odds shift

**Frustrations**
- Traditional bookmakers limit winning accounts
- Exchange liquidity is poor outside major markets
- High commissions eat into thin margins
- Slow withdrawals from traditional bookies

**Technology Comfort**
High. Uses multiple devices, comfortable with spreadsheets and betting models. Has used crypto but prefers simplicity.

**Quote**
"I don't need fancy features. I need liquidity, fair odds, and an operator that won't ban me for winning."

**MVP Value Proposition for Sam**
BetFlow offers better odds through peer-to-peer matching, guaranteed execution via external hedging, and a commitment to never limit winning players.

---

### 3.2 Secondary Persona: "Casual Carlos"

**Demographics**
- Age: 22-35
- Location: Global (crypto-friendly regions)
- Occupation: Tech worker, holds cryptocurrency
- Monthly betting volume: $200 - $2,000

**Background**
Carlos follows football casually, supporting his local club and watching major tournaments. He holds USDC and other crypto from his work in tech. He's bet occasionally with crypto bookmakers but finds the experience clunky. He's attracted to the transparency of an exchange model.

**Goals**
- Bet on matches he's watching for entertainment
- Use his existing crypto without converting to fiat
- Understand what odds he's getting
- Quick and easy deposits and withdrawals

**Frustrations**
- Traditional bookmakers require lengthy KYC and bank transfers
- Some crypto bookies feel scammy or have unclear odds
- Doesn't understand the juice/vig traditional bookies charge
- Withdrawal times can be days

**Technology Comfort**
High with crypto and web apps. Uses mobile primarily but comfortable on desktop.

**Quote**
"I have USDC sitting in my wallet. I just want to bet on the game tonight without jumping through hoops."

**MVP Value Proposition for Carlos**
BetFlow lets him deposit USDC instantly, bet on football with transparent peer-to-peer odds, and withdraw winnings in minutes—all with a wallet he already has.

---

### 3.3 Tertiary Persona: "Market Maker Mike"

**Demographics**
- Age: 30-45
- Location: Global
- Occupation: Quantitative trader or trading firm employee
- Monthly betting volume: $100,000+

**Background**
Mike works at a trading firm or runs his own quantitative betting operation. He provides liquidity on betting exchanges, profiting from the spread rather than directional bets. He has automated systems that place and adjust orders based on market conditions.

**Goals**
- Earn consistent returns from market making
- Access competitive commission rates
- Integrate via API for automated trading
- Understand platform reliability and uptime

**Frustrations**
- Limited API access on some platforms
- High commission rates reduce profitability
- Competition from the platform's own market making
- Unreliable uptime during high-volume periods

**Technology Comfort**
Expert. Builds custom trading systems, understands exchange mechanics deeply.

**Quote**
"I'll provide liquidity if the economics work. Show me your API docs and commission structure."

**MVP Consideration**
Mike is not the primary MVP target—we won't have API access at launch. However, designing our system with future API access in mind and avoiding practices that would deter market makers (like front-running) is important for post-MVP growth.

---

### 3.4 Anti-Persona: "Problem Gambler Pete"

**Demographics**
- Age: Any
- Pattern: Chasing losses, betting beyond means, signs of addiction

**Why Anti-Persona**
BetFlow is committed to responsible gambling. We explicitly do not want to attract or retain users showing signs of problem gambling. Our design should include friction points and self-exclusion options.

**Design Implications**
- Mandatory deposit limits during onboarding
- Prominent self-exclusion option
- No predatory re-engagement marketing
- Loss limit warnings
- Links to gambling support resources

---

## 4. User Storylines

### 4.1 Storyline: Sharp Sam's First Trade

**Context**
Sam has heard about BetFlow from a betting forum. He's skeptical but intrigued by the promise of no account limits.

**Scene 1: Discovery and Signup**

Sam visits BetFlow's landing page on his laptop. He reads the value proposition: peer-to-peer betting exchange, never get limited, USDC payments. He clicks "Start Trading."

The signup flow asks him to connect a wallet. Sam opens MetaMask, which he uses for occasional DeFi. He clicks "Connect MetaMask" and approves the connection. The site prompts him to sign a message to verify ownership—no transaction, no gas fee. He signs.

He's now logged in. The site asks for an optional email for notifications and a username for display. He enters both. Account created in under 60 seconds.

**Scene 2: First Deposit**

Sam sees his balance: $0.00 USDC. He clicks "Deposit." The site shows a deposit address on Base chain and explains he'll need USDC on Base.

Sam has USDC on Coinbase. He goes to Coinbase, finds his USDC, and initiates a withdrawal to the displayed address, selecting Base network. Coinbase charges a small fee. He sends $500.

Back on BetFlow, he sees "Pending deposit: $500" within seconds. After about 30 seconds, the deposit confirms and his available balance shows $500.00.

**Scene 3: Finding a Market**

Sam navigates to Markets. He sees a list of upcoming football matches organized by competition: Premier League, La Liga, Champions League. He's interested in tonight's Premier League match: Liverpool vs Arsenal.

He clicks on the match and sees the Match Winner market. The order book shows:

```
Liverpool to Win:
  Back: 2.35 ($320 available)
  Lay: 2.40 ($150 available)

Draw:
  Back: 3.40 ($85 available)
  Lay: 3.55 ($50 available)

Arsenal to Win:
  Back: 3.10 ($200 available)
  Lay: 3.20 ($180 available)
```

Sam's analysis suggests Arsenal is undervalued. The best back price of 3.10 is better than the 2.90 offered by most traditional bookmakers.

**Scene 4: Placing an Order**

Sam wants to back Arsenal at 3.10. He clicks on the 3.10 price. An order form appears pre-filled with:
- Selection: Arsenal to Win
- Side: Back
- Odds: 3.10
- Stake: (empty)

He enters $200 stake. The form shows:
- Potential profit if Arsenal wins: $420 (stake returned plus $420 profit)
- Loss if Arsenal doesn't win: $200

He clicks "Place Order." A confirmation modal shows the details. He confirms.

The order executes immediately—there was $200 available at 3.10, and his full stake matched. His balance updates:
- Available: $300
- Reserved: $200 (his stake on the matched bet)

He sees a notification: "Order matched at 3.10 odds."

**Scene 5: Viewing Positions**

Sam clicks "My Bets" and sees his active bet:
- Liverpool vs Arsenal
- Arsenal to Win
- Back @ 3.10
- Stake: $200
- Potential Payout: $620

The match is tomorrow. He can see the bet clearly listed. He's used to bookmakers hiding this information or making it hard to find.

**Scene 6: Settlement**

The next day, Sam watches the match. Arsenal wins 2-1. An hour after the match ends, he receives a notification: "Your bet has been settled."

He checks his balance:
- Available: $906.80
- Reserved: $0

The calculation: $620 payout minus 2% commission on $420 profit = $620 - $8.40 = $611.60 added to his previous $300 = $911.60. Wait, the numbers show $906.80. He checks—the commission is on net winnings only. Actually: $200 stake returned + $420 profit - $8.40 commission = $611.60 net addition. $300 + $611.60 = $911.60. Let me recalculate for display purposes.

Correction for display: Payout is $620 total. Commission is 2% of $420 profit = $8.40. Net received: $611.60. Previous available: $300. New available: $911.60.

Sam is satisfied. Clean process, good odds, fast settlement.

**Scene 7: Withdrawal**

Sam wants to withdraw his profits. He clicks "Withdraw," enters $400 and his external wallet address. He confirms.

Within 2 minutes, he receives confirmation that the withdrawal is complete. He checks his external wallet—$400 USDC arrived on Base. Total platform fees: a few cents in network fees.

Sam makes a note to bet more on BetFlow next weekend.

---

### 4.2 Storyline: Casual Carlos Bets on a Big Match

**Context**
Carlos sees a Champions League final advertised. He hasn't bet in months but wants some action on the game.

**Scene 1: Quick Signup**

Carlos finds BetFlow through a crypto Twitter thread. He's on his phone. He clicks the link and sees a clean landing page.

He taps "Start Trading" and chooses "WalletConnect." His Coinbase Wallet app opens, asking to connect to BetFlow. He approves. Back on the site, he signs a message to verify. Done.

Total signup time: 45 seconds. No email required (though prompted as optional).

**Scene 2: Small Deposit**

Carlos has $50 USDC in his Coinbase Wallet on Base chain already. He taps "Deposit" and sees instructions. Since he's already on Base, he simply sends $50 USDC to the displayed address directly from Coinbase Wallet.

The deposit confirms in under a minute. Balance: $50.

**Scene 3: Browsing and Betting**

He taps "Markets" and immediately sees the Champions League final prominently featured: Real Madrid vs Manchester City.

He taps the match and sees the Match Winner options. Real Madrid back odds: 2.85. He doesn't know what "lay" means and ignores that side.

He taps Real Madrid @ 2.85, enters $20 stake. The form shows potential payout: $57 if Real Madrid wins.

He taps "Place Order" and confirms. The order matches instantly.

**Scene 4: Match Night**

Carlos watches the match with friends. Real Madrid wins. He gets a push notification (he opted in during signup): "Your bet won! $55.86 credited."

He opens the app and sees his balance: $85.86. He withdrew nothing, so his $30 remaining plus $57 payout minus 2% commission on $37 profit ($0.74) = $30 + $56.26 = $86.26. 

Let me recalculate: Started with $50, bet $20 leaving $30 available. Bet wins: $20 stake + $37 profit = $57 gross. Commission: 2% × $37 = $0.74. Net addition: $56.26. Total: $30 + $56.26 = $86.26.

Carlos is happy. He might bet again next big match.

---

### 4.3 Storyline: Order Not Immediately Matched

**Context**
Sam wants to back Liverpool at better odds than currently available.

**Scene 1: Placing a Limit Order**

Sam looks at the Liverpool vs Chelsea market:

```
Liverpool to Win:
  Back: 1.90 ($500 available)
  Lay: 1.95 ($300 available)
```

The best back price is 1.90, but Sam thinks 1.95 is fair value. He wants to try to get matched at 1.95 if another user is willing to lay at that price.

He clicks to place an order:
- Selection: Liverpool to Win
- Side: Back
- Odds: 1.95 (he manually enters this, above current best back)
- Stake: $100

He places the order. Since no one is currently laying at 1.95 (the best lay is also 1.95 but only for backers—wait, I need to clarify the order book display).

Let me clarify: The lay side shows what layers are offering. A lay of 1.95 means someone will accept back bets at 1.95 odds. So if Sam backs at 1.95 and there's $300 available on the lay side at 1.95, his order would match.

Scene adjustment: Let's say the order book shows:
```
Liverpool to Win:
  Best Back: 1.85 ($500 available) ← backers offering at 1.85
  Best Lay: 1.95 ($300 available) ← layers offering at 1.95
```

Sam wants to back at 1.90, which is between the spread. No one is laying at 1.90 currently.

**Scene 2: Order Sits in Book**

Sam places his back order at 1.90 for $100. The system adds it to the order book. No immediate match.

His balance shows:
- Available: $400
- Reserved: $100

His open orders show:
- Liverpool to Win | Back | 1.90 | $100 | Status: Open

**Scene 3: External Liquidity Check**

Behind the scenes, the system checks Betfair. Betfair shows lay liquidity at 1.88 effective odds (after their commission). Since Sam wants 1.90 and external is only 1.88, the spread is negative—hedging would lose money. The system leaves Sam's order in the book, waiting for internal match.

**Scene 4: Another User Matches**

An hour later, another user (a layer) decides to lay Liverpool at 1.90. They see Sam's order in the book. They place a lay order at 1.90 for $100.

The matching engine pairs them. Both receive notifications:
- Sam: "Your order has been matched at 1.90"
- Layer: "Your order has been matched at 1.90"

Sam's position updates to show his active bet.

**Scene 5: Alternative - External Match**

Alternative scenario: What if Betfair odds moved? Suppose Betfair lay odds improved to 1.92 effective. Now:
- Sam wants to back at 1.90
- BetFlow can hedge by laying on Betfair at 1.92
- Spread: BetFlow collects from loser at 1.90 or pays winner at 1.90, while Betfair position is opposite at 1.92
- Net: slight loss per bet... 

Actually, let me reconsider the hedging economics:

If Sam backs Liverpool at 1.90 with $100:
- If Liverpool wins: Sam receives $190, BetFlow pays $90 profit
- If Liverpool loses: Sam loses $100, BetFlow receives $100

If BetFlow lays Liverpool on Betfair at 1.92 with $100:
- If Liverpool wins: BetFlow pays $92 to Betfair
- If Liverpool loses: BetFlow receives ~$100 from Betfair (minus commission)

Net exposure if Liverpool wins: BetFlow pays Sam $90, pays Betfair $92 = -$182... that's not right.

Let me reconsider the hedge structure:

When BetFlow takes Sam's back bet at 1.90, BetFlow is effectively laying Liverpool (betting against Liverpool). To hedge, BetFlow should BACK Liverpool on Betfair, not lay.

Corrected:
- Sam backs Liverpool at 1.90 ($100 stake) → If Liverpool wins, Sam profits $90
- BetFlow backs Liverpool on Betfair at 1.88 ($100 stake) → If Liverpool wins, BetFlow profits $88 on Betfair

If Liverpool wins:
- BetFlow pays Sam $90
- BetFlow receives $88 from Betfair
- Net: -$2 loss

If Liverpool loses:
- BetFlow receives $100 from Sam
- BetFlow loses $100 on Betfair
- Net: $0

This doesn't work. The hedge needs to be structured differently...

Actually, the correct approach for exchange hedging:

Sam backs Liverpool at 1.90 for $100. BetFlow needs to find a layer—either internal or external.

On Betfair, if someone will lay Liverpool at 1.88, that means BetFlow can back Liverpool at 1.88. But BetFlow needs to lay, not back.

So BetFlow needs to find back orders on Betfair to match against (i.e., people wanting to back Liverpool, whom BetFlow can lay to). Or find layers offering better than 1.90.

This is getting complex. For MVP simplicity, let me reframe:

**Simplified External Liquidity for MVP:**

BetFlow checks if Betfair has lay liquidity at odds better than or equal to Sam's requested back odds. If Betfair layers offer 1.90 or better, BetFlow can route Sam's order there.

- Sam wants to back Liverpool at 1.90
- Betfair has layers at 1.90 (effective after commission)
- BetFlow routes the order: Sam's bet is matched with Betfair liquidity
- BetFlow earns commission from Sam's winnings

If Betfair only offers 1.85 effective, Sam's 1.90 order cannot be filled externally (he'd be getting worse odds). Order stays in internal book.

---

### 4.4 Storyline: Withdrawal Flow

**Context**
Carlos wants to withdraw after his winning streak.

**Scene 1: Initiating Withdrawal**

Carlos has $150 balance. He taps "Withdraw" in the app.

The withdrawal screen shows:
- Available balance: $150.00
- Network: Base
- Estimated fee: $0.02

He enters $100 to withdraw and pastes his external wallet address (his main Coinbase Wallet address).

**Scene 2: Confirmation**

A confirmation screen shows:
- Amount: $100.00
- Network fee: $0.02
- You will receive: $99.98
- Destination: 0x7a3...8f2

He confirms. The system validates the address format and processes the request.

**Scene 3: Processing**

The withdrawal shows as "Processing" in his transaction history. Behind the scenes:
- Backend verifies available balance
- Backend checks withdrawal limits (MVP: $1,000/day without KYC)
- Backend signs and submits blockchain transaction
- Transaction broadcasts to Base network

**Scene 4: Completion**

Within 90 seconds, the transaction confirms. Carlos receives a notification: "Withdrawal complete."

His BetFlow balance: $50.00
His external wallet: +$99.98 USDC

---

### 4.5 Storyline: Bet Settlement

**Context**
System processes settlement after a match concludes.

**Scene 1: Match Ends**

Manchester United vs Newcastle ends 2-1 to Manchester United. The sports data provider sends result data to BetFlow.

**Scene 2: Result Verification**

The system receives the result:
- Match ID: 12345
- Home Score: 2
- Away Score: 1
- Status: Full Time
- Winner: Home (Manchester United)

MVP uses single-source verification with a 30-minute delay for major result corrections.

**Scene 3: Market Resolution**

After the delay, the system resolves the market:
- Market: Manchester United vs Newcastle - Match Winner
- Winning outcome: Manchester United
- Losing outcomes: Draw, Newcastle

**Scene 4: Bet Settlement**

The system queries all matched bets on this market:

Bet 1: User A backed Manchester United at 2.10 for $50
- Outcome: Won
- Gross payout: $105
- Profit: $55
- Commission (2%): $1.10
- Net payout: $103.90
- Action: Credit User A $103.90, release reserve

Bet 2: User B laid Manchester United at 2.10 for $50 (liability $55)
- Outcome: Lost (the team they bet against won)
- Payout: $0
- Action: Transfer $55 from User B's reserve to settlement pool

Bet 3: User C backed Newcastle at 3.50 for $30
- Outcome: Lost
- Payout: $0
- Action: Forfeit $30 from User C's reserve

Bet 4: User D laid Newcastle at 3.50 for $30 (liability $75)
- Outcome: Won (Newcastle didn't win)
- Gross payout: $30 (the stake they accepted)
- Commission (2%): $0.60
- Net payout: $29.40
- Action: Credit User D $29.40, release remaining reserve ($75 - $30 = $45 returned + $29.40 winnings)

**Scene 5: Notifications**

All users receive settlement notifications:
- User A: "Your bet won! $103.90 credited."
- User B: "Your bet lost. Better luck next time."
- User C: "Your bet lost. Better luck next time."
- User D: "Your bet won! $29.40 credited."

---

## 5. MVP Architecture

### 5.1 Architecture Overview

The MVP uses a simplified monolithic architecture with clear module boundaries, designed for fast development while allowing future decomposition into microservices.

**Architecture Diagram (Conceptual)**

```
┌─────────────────────────────────────────────────────────────┐
│                      CLIENT LAYER                           │
│                                                             │
│   ┌─────────────────────────────────────────────────────┐  │
│   │              Next.js Web Application                 │  │
│   │    • Wallet Connection (wagmi/viem)                 │  │
│   │    • Real-time updates (WebSocket)                  │  │
│   │    • Responsive design (mobile-friendly)            │  │
│   └─────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                     API LAYER (NestJS)                      │
│                                                             │
│   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐       │
│   │     Auth     │ │   Markets    │ │    Orders    │       │
│   │    Module    │ │    Module    │ │    Module    │       │
│   └──────────────┘ └──────────────┘ └──────────────┘       │
│   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐       │
│   │    Wallet    │ │  Settlement  │ │   Liquidity  │       │
│   │    Module    │ │    Module    │ │    Module    │       │
│   └──────────────┘ └──────────────┘ └──────────────┘       │
│                                                             │
│   ┌─────────────────────────────────────────────────────┐  │
│   │              WebSocket Gateway                       │  │
│   │         (Order book updates, notifications)         │  │
│   └─────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                             │
│                                                             │
│   ┌──────────────────────┐    ┌──────────────────────┐     │
│   │     PostgreSQL       │    │        Redis         │     │
│   │   • Users            │    │   • Order books      │     │
│   │   • Orders           │    │   • Sessions         │     │
│   │   • Bets             │    │   • Price cache      │     │
│   │   • Transactions     │    │   • Rate limiting    │     │
│   │   • Markets          │    │                      │     │
│   └──────────────────────┘    └──────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────┐
│                  EXTERNAL INTEGRATIONS                      │
│                                                             │
│   ┌──────────────┐ ┌──────────────┐ ┌──────────────┐       │
│   │    Betfair   │ │    Sports    │ │     Base     │       │
│   │   Exchange   │ │   Data API   │ │  Blockchain  │       │
│   │  (Liquidity) │ │  (Results)   │ │  (Payments)  │       │
│   └──────────────┘ └──────────────┘ └──────────────┘       │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 Technology Stack

| Layer | Technology | Rationale |
|-------|------------|-----------|
| Frontend | Next.js 14 (App Router) | Fast development, SSR for SEO, React ecosystem |
| Wallet Integration | wagmi + viem | Industry standard for Web3 React apps |
| Styling | Tailwind CSS | Rapid UI development, consistent design |
| Backend | NestJS (TypeScript) | Structured architecture, TypeScript end-to-end |
| Database | PostgreSQL 15 | Reliable, ACID compliant, JSON support |
| Cache/Queue | Redis | Order book storage, job queues, sessions |
| Blockchain | Base (USDC) | Low fees, Coinbase ecosystem |
| External Liquidity | Betfair Exchange API | Largest sports betting exchange |
| Sports Data | API-Football or Sportradar | Match schedules, live scores, results |
| Hosting | AWS (EC2/RDS/ElastiCache) | Reliable, scalable, familiar |

### 5.3 Module Responsibilities

**Auth Module**
- Wallet signature verification
- JWT token generation and validation
- Session management
- Nonce generation for replay protection

**Markets Module**
- Ingest sporting events from data provider
- Create and manage betting markets
- Provide market listings and details
- Handle market lifecycle (open, suspended, closed, settled)

**Orders Module**
- Order placement validation
- Order book management
- Matching engine
- Order cancellation
- Open orders queries

**Wallet Module**
- Deposit address generation
- Deposit monitoring and crediting
- Withdrawal processing
- Balance management
- Transaction history

**Settlement Module**
- Result ingestion
- Bet settlement calculation
- Payout processing
- Settlement history

**Liquidity Module**
- Betfair API integration
- External price monitoring
- Hedge execution when needed
- Market mapping (internal to Betfair)

---

## 6. Core Components (MVP)

### 6.1 Order Book Engine

**Purpose**
Stores and manages open orders for each market outcome, providing the foundation for the matching engine.

**MVP Scope**
- Single order book per outcome (backs and lays)
- Price levels aggregated for display (hiding individual order details)
- Top 5 price levels displayed per side
- Updates broadcast via WebSocket

**Data Model**

Order Book Structure (in Redis):
- Key pattern: `orderbook:{marketId}:{outcomeId}`
- Backs sorted set: score = negative odds (for descending sort), value = serialized orders
- Lays sorted set: score = odds (for ascending sort), value = serialized orders

### 6.2 Matching Engine

**Purpose**
Matches compatible orders when prices overlap, creating matched bets.

**MVP Scope**
- Price-time priority matching
- Limit orders only
- Full and partial matching
- Synchronous matching (within API request)

**Matching Rules**
- Back order at odds X matches with lay order at odds Y if X ≤ Y
- Matched price is the resting order's price (rewards liquidity providers)
- Earlier orders at same price have priority

### 6.3 External Liquidity (Betfair)

**Purpose**
Sources liquidity from Betfair when internal order book cannot satisfy orders.

**MVP Scope**
- Single source (Betfair Exchange)
- Football markets only
- Read prices and available amounts
- Route orders to Betfair when beneficial
- Basic market mapping

**MVP Limitation**
Full automated hedging is complex. For MVP, we implement "liquidity display"—showing Betfair liquidity in our order book (marked as external) and routing orders there if users accept external execution.

Simplified flow:
1. User places order
2. Check internal order book for match
3. If no internal match, check if Betfair has liquidity at acceptable price
4. If yes, execute on Betfair and reflect in our system
5. If no, order rests in internal book

### 6.4 Settlement Engine

**Purpose**
Processes bet outcomes after events conclude.

**MVP Scope**
- Automated result ingestion from sports data API
- 30-minute delay before settlement (for result corrections)
- Batch settlement processing
- Commission deduction
- Balance updates

---

## 7. Data Transfer Objects (DTOs)

### 7.1 Authentication DTOs

#### 7.1.1 Get Nonce Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| walletAddress | string | Yes | Valid Ethereum address (0x + 40 hex) | Identifies which wallet is requesting authentication; used to generate a unique challenge |

#### 7.1.2 Get Nonce Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| nonce | string | No | Random string user must sign; prevents replay attacks where old signatures could authenticate new sessions |
| message | string | No | Human-readable message containing nonce; displayed in wallet for user verification before signing |
| expiresAt | string | No | ISO timestamp when nonce expires (5 minutes); limits window for replay if nonce is leaked |

#### 7.1.3 Verify Signature Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| walletAddress | string | Yes | Valid Ethereum address | Identifies the wallet claiming to own this address |
| signature | string | Yes | Valid ECDSA signature | Cryptographic proof that private key owner signed the nonce message |
| message | string | Yes | Must match issued nonce message | The exact message that was signed; verified to contain valid unexpired nonce |

#### 7.1.4 Verify Signature Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| accessToken | string | No | JWT for API authentication; contains userId, expires in 24 hours |
| refreshToken | string | No | Long-lived token (30 days) for obtaining new access tokens without re-signing |
| expiresIn | integer | No | Seconds until accessToken expires; client schedules refresh before expiration |
| user | object | No | Basic user profile object; provides immediate access to user data without separate API call |

#### 7.1.5 Refresh Token Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| refreshToken | string | Yes | Valid unexpired refresh token | Presented to obtain new access token when current one expires or is near expiry |

#### 7.1.6 Refresh Token Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| accessToken | string | No | New JWT access token |
| expiresIn | integer | No | Validity period in seconds |

---

### 7.2 User DTOs

#### 7.2.1 User Profile Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| userId | string | No | Internal unique identifier; used in all user-related operations |
| walletAddress | string | No | User's connected wallet; displayed in UI, used for deposit instructions |
| username | string | Yes | Optional display name; null if not set; used in any social features |
| email | string | Yes | Optional notification email; null if not provided |
| createdAt | string | No | Account creation timestamp; used for account age displays |
| depositLimit | string | No | Daily deposit limit based on account status; responsible gambling feature |
| withdrawalLimit | string | No | Daily withdrawal limit based on verification status |

#### 7.2.2 Update Profile Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| username | string | No | 3-20 chars, alphanumeric and underscore | Display name for profile; validated for appropriateness |
| email | string | No | Valid email format | Contact email for notifications; requires verification if changed |

#### 7.2.3 Update Profile Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| user | object | No | Updated user profile object reflecting changes |
| emailVerificationSent | boolean | No | Whether verification email was sent to new address |

---

### 7.3 Market DTOs

#### 7.3.1 List Markets Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| competitionId | string | No | Valid competition ID | Filter by league (Premier League, La Liga, etc.); null returns all |
| status | string | No | One of: open, suspended, closed | Filter by market status; default returns open markets only |
| dateFrom | string | No | ISO date | Start date filter; enables browsing by match date |
| dateTo | string | No | ISO date | End date filter; combined with dateFrom for date range |
| page | integer | No | Positive integer, default 1 | Pagination for large result sets |
| pageSize | integer | No | 1-50, default 20 | Results per page; capped to prevent oversized responses |

#### 7.3.2 List Markets Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| markets | array | No | Array of market summary objects |
| totalCount | integer | No | Total markets matching filter; enables pagination UI |
| page | integer | No | Current page number |
| pageSize | integer | No | Results per page |
| hasMore | boolean | No | Quick check for "Load More" button visibility |

#### 7.3.3 Market Summary Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Unique market identifier for API calls |
| eventId | string | No | Parent event ID; groups related markets |
| competitionId | string | No | Competition/league ID |
| competitionName | string | No | Display name (e.g., "Premier League") |
| homeTeam | string | No | Home team name |
| awayTeam | string | No | Away team name |
| startTime | string | No | Event kickoff time ISO format; used for countdowns and sorting |
| status | string | No | Market status (open, suspended, closed, settled) |
| totalMatched | string | No | Total volume matched; indicates market activity/liquidity |
| outcomes | array | No | Array of outcomes with current best prices |

#### 7.3.4 Market Outcome Summary

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| outcomeId | string | No | Unique outcome identifier |
| name | string | No | Outcome name (e.g., "Liverpool", "Draw", "Arsenal") |
| bestBackOdds | number | Yes | Best available back price; null if no back orders |
| bestBackAvailable | string | Yes | Amount available at best back price |
| bestLayOdds | number | Yes | Best available lay price; null if no lay orders |
| bestLayAvailable | string | Yes | Amount available at best lay price |

#### 7.3.5 Get Market Detail Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | Yes | Valid market ID | Specifies which market to retrieve |

#### 7.3.6 Market Detail Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| marketId | string | No | Market identifier |
| eventId | string | No | Parent event ID |
| competitionId | string | No | Competition ID |
| competitionName | string | No | Competition display name |
| homeTeam | string | No | Home team |
| awayTeam | string | No | Away team |
| venue | string | Yes | Stadium/venue name; null if unknown |
| startTime | string | No | Kickoff time |
| status | string | No | Market status |
| totalMatched | string | No | Total matched volume |
| outcomes | array | No | Full outcome details with order book depth |
| rules | object | No | Settlement rules (how winner is determined, void conditions) |
| externalLiquidity | boolean | No | Whether external liquidity is available for this market |

#### 7.3.7 Outcome Detail Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| outcomeId | string | No | Outcome identifier |
| name | string | No | Outcome name |
| backs | array | No | Array of back price levels (best first) |
| lays | array | No | Array of lay price levels (best first) |
| lastMatchedOdds | number | Yes | Last trade price; null if never traded |
| lastMatchedAt | string | Yes | Last trade timestamp |

#### 7.3.8 Price Level Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| odds | number | No | Price at this level |
| available | string | No | Total stake available |
| isExternal | boolean | No | True if includes Betfair liquidity; affects execution certainty |

---

### 7.4 Order DTOs

#### 7.4.1 Place Order Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | Yes | Valid open market | Which market to place order on |
| outcomeId | string | Yes | Valid outcome in market | Which outcome within the market |
| side | string | Yes | One of: back, lay | Back = bet for outcome; Lay = bet against |
| odds | number | Yes | 1.01 to 1000, max 2 decimals | The price user wants; must be valid odds increment |
| stake | string | Yes | Positive, max 2 decimals, within balance | Amount to bet (for backs) or accept (for lays) |
| acceptBetterOdds | boolean | No | Default true | Whether to accept better price if available; improves fill rate |

#### 7.4.2 Place Order Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | Unique order identifier for tracking and cancellation |
| status | string | No | Order status: matched, partial, open, rejected |
| requestedOdds | number | No | Odds user requested |
| requestedStake | string | No | Stake user requested |
| matchedStake | string | No | Amount immediately matched (may be 0) |
| remainingStake | string | No | Amount remaining in order book (may be 0) |
| averageMatchedOdds | number | Yes | Weighted average fill price; null if nothing matched |
| reservedAmount | string | No | Amount locked from user's balance for this order |
| matches | array | No | Array of individual matches if any occurred |
| createdAt | string | No | Order creation timestamp |
| rejectionReason | string | Yes | Explanation if rejected; null if accepted |

#### 7.4.3 Match Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| matchId | string | No | Unique match/trade identifier |
| odds | number | No | Price at which this portion matched |
| stake | string | No | Stake amount for this match |
| matchedAt | string | No | Timestamp of match |
| matchType | string | No | "internal" or "external" (Betfair); transparency for user |

#### 7.4.4 Cancel Order Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| orderId | string | Yes | Valid open or partial order owned by user | Identifies which order to cancel |

#### 7.4.5 Cancel Order Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | Confirms which order was cancelled |
| status | string | No | New status: "cancelled" |
| cancelledStake | string | No | Amount that was unmatched and removed from book |
| releasedAmount | string | No | Funds returned to available balance |
| cancelledAt | string | No | Cancellation timestamp |
| note | string | Yes | Any notes (e.g., "Partially matched orders cannot be fully cancelled") |

#### 7.4.6 List Open Orders Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | No | Valid market ID | Filter to specific market; null returns all |

#### 7.4.7 List Open Orders Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orders | array | No | Array of open order objects |
| totalReserved | string | No | Total funds reserved across all open orders |

#### 7.4.8 Open Order Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| orderId | string | No | Order identifier |
| marketId | string | No | Market identifier |
| marketName | string | No | Human-readable market name (e.g., "Liverpool vs Arsenal") |
| outcomeId | string | No | Outcome identifier |
| outcomeName | string | No | Outcome name (e.g., "Liverpool") |
| side | string | No | Back or lay |
| odds | number | No | Order price |
| originalStake | string | No | Original order stake |
| remainingStake | string | No | Stake still in book |
| matchedStake | string | No | Stake already matched |
| reservedAmount | string | No | Funds locked for this order |
| createdAt | string | No | When order was placed |

---

### 7.5 Bet DTOs

#### 7.5.1 List Active Bets Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| marketId | string | No | Valid market ID | Filter to specific market |

#### 7.5.2 List Active Bets Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| bets | array | No | Array of active (unsettled) bet objects |
| totalAtRisk | string | No | Sum of all stakes at risk; key metric for user |
| totalPotentialProfit | string | No | Maximum possible winnings across all bets |

#### 7.5.3 Active Bet Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| betId | string | No | Unique bet identifier |
| marketId | string | No | Market identifier |
| marketName | string | No | Human-readable market name |
| outcomeId | string | No | Outcome identifier |
| outcomeName | string | No | Outcome name |
| side | string | No | Back or lay |
| odds | number | No | Matched odds |
| stake | string | No | Bet stake |
| potentialProfit | string | No | Profit if bet wins (before commission) |
| potentialLoss | string | No | Loss if bet loses |
| eventStartTime | string | No | When the sporting event starts |
| matchedAt | string | No | When bet was matched |

#### 7.5.4 List Settled Bets Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| fromDate | string | No | ISO date | Start of date range |
| toDate | string | No | ISO date | End of date range |
| outcome | string | No | One of: won, lost, void | Filter by result |
| page | integer | No | Positive, default 1 | Pagination |
| pageSize | integer | No | 1-50, default 20 | Results per page |

#### 7.5.5 List Settled Bets Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| bets | array | No | Array of settled bet objects |
| summary | object | No | Summary statistics for filtered bets |
| totalCount | integer | No | Total matching bets |
| page | integer | No | Current page |
| pageSize | integer | No | Page size |
| hasMore | boolean | No | More pages available |

#### 7.5.6 Settled Bet Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| betId | string | No | Bet identifier |
| marketId | string | No | Market identifier |
| marketName | string | No | Market name |
| outcomeId | string | No | Outcome identifier |
| outcomeName | string | No | Outcome name |
| side | string | No | Back or lay |
| odds | number | No | Matched odds |
| stake | string | No | Bet stake |
| result | string | No | "won", "lost", or "void" |
| grossPayout | string | No | Payout before commission (0 if lost) |
| commission | string | No | Commission deducted (0 if lost or void) |
| netPayout | string | No | Final payout received |
| profitLoss | string | No | Net profit/loss on this bet |
| settledAt | string | No | Settlement timestamp |

#### 7.5.7 Bet Summary Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| totalBets | integer | No | Count of bets in period |
| wonCount | integer | No | Winning bets |
| lostCount | integer | No | Losing bets |
| voidCount | integer | No | Voided bets |
| totalStaked | string | No | Sum of stakes |
| totalProfitLoss | string | No | Net result |
| totalCommissionPaid | string | No | Total commission (useful for tax purposes) |
| winRate | number | No | Win percentage (excluding voids) |

---

### 7.6 Wallet DTOs

#### 7.6.1 Get Balance Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| available | string | No | Funds available for orders or withdrawal |
| reserved | string | No | Funds locked in open orders and unsettled bets |
| total | string | No | Sum of available and reserved |
| pendingDeposits | string | No | Deposits awaiting confirmation |
| pendingWithdrawals | string | No | Withdrawals in process |
| currency | string | No | Always "USDC" for MVP |

#### 7.6.2 Get Deposit Info Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| address | string | No | User's deposit address on Base chain |
| chain | string | No | "base" for MVP |
| currency | string | No | "USDC" |
| minimumDeposit | string | No | Minimum amount to credit (e.g., "1.00") |
| confirmationsRequired | integer | No | Blocks to wait before credit (e.g., 12) |
| estimatedTime | string | No | Human-readable estimate (e.g., "~30 seconds") |
| qrCode | string | No | Base64 encoded QR code image for mobile scanning |

#### 7.6.3 Request Withdrawal Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| amount | string | Yes | Positive, max 2 decimals, within available | Amount to withdraw |
| address | string | Yes | Valid Ethereum address | Destination wallet address |

#### 7.6.4 Request Withdrawal Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| withdrawalId | string | No | Unique withdrawal identifier for tracking |
| status | string | No | "pending" initially |
| amount | string | No | Requested amount |
| fee | string | No | Network fee estimate |
| netAmount | string | No | Amount user will receive |
| address | string | No | Confirmed destination |
| estimatedCompletion | string | No | Expected completion time |
| createdAt | string | No | Request timestamp |

#### 7.6.5 Get Withdrawal Status Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| withdrawalId | string | Yes | Valid withdrawal ID | Which withdrawal to check |

#### 7.6.6 Get Withdrawal Status Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| withdrawalId | string | No | Withdrawal identifier |
| status | string | No | "pending", "processing", "completed", "failed" |
| amount | string | No | Withdrawal amount |
| fee | string | No | Actual fee charged |
| netAmount | string | No | Amount received |
| address | string | No | Destination address |
| transactionHash | string | Yes | Blockchain tx hash; null until broadcast |
| createdAt | string | No | Request time |
| completedAt | string | Yes | Completion time; null if not completed |
| failureReason | string | Yes | Explanation if failed |

#### 7.6.7 List Transactions Request

| Field | Type | Required | Constraints | Rationale |
|-------|------|----------|-------------|-----------|
| type | string | No | deposit, withdrawal, bet, settlement, commission | Filter by transaction type |
| fromDate | string | No | ISO date | Start of date range |
| toDate | string | No | ISO date | End of date range |
| page | integer | No | Positive, default 1 | Pagination |
| pageSize | integer | No | 1-100, default 50 | Results per page |

#### 7.6.8 List Transactions Response

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| transactions | array | No | Array of transaction objects |
| totalCount | integer | No | Total matching transactions |
| page | integer | No | Current page |
| pageSize | integer | No | Page size |
| hasMore | boolean | No | More pages exist |

#### 7.6.9 Transaction Object

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| transactionId | string | No | Unique transaction identifier |
| type | string | No | Transaction type |
| amount | string | No | Amount (positive = credit, negative = debit) |
| balanceAfter | string | No | Balance after this transaction; enables reconciliation |
| description | string | No | Human-readable description |
| referenceType | string | Yes | Type of related entity (order, bet, withdrawal) |
| referenceId | string | Yes | ID of related entity |
| timestamp | string | No | When transaction occurred |
| metadata | object | Yes | Additional context (tx hash for deposits, etc.) |

---

### 7.7 WebSocket DTOs

#### 7.7.1 Subscribe to Market Request

| Field | Type | Required | Rationale |
|-------|------|----------|-----------|
| action | string | Yes | "subscribe" |
| channel | string | Yes | "market" |
| marketId | string | Yes | Which market to receive updates for |

#### 7.7.2 Order Book Update Event

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| type | string | No | "orderbook_update" |
| marketId | string | No | Market identifier |
| outcomeId | string | No | Outcome identifier |
| timestamp | string | No | Update timestamp |
| backs | array | No | Updated back price levels |
| lays | array | No | Updated lay price levels |
| lastMatchedOdds | number | Yes | Last trade price if changed |

#### 7.7.3 User Notification Event

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| type | string | No | "notification" |
| category | string | No | "order", "bet", "wallet" |
| title | string | No | Notification title |
| message | string | No | Notification body |
| data | object | Yes | Related data (orderId, betId, etc.) |
| timestamp | string | No | When event occurred |

#### 7.7.4 Balance Update Event

| Field | Type | Nullable | Rationale |
|-------|------|----------|-----------|
| type | string | No | "balance_update" |
| available | string | No | New available balance |
| reserved | string | No | New reserved balance |
| total | string | No | New total balance |
| reason | string | No | What triggered update (order_placed, bet_settled, etc.) |
| timestamp | string | No | Update timestamp |

---

## 8. Component Algorithms

### 8.1 Order Matching Algorithm (MVP)

**Overview**

The MVP matching algorithm implements price-time priority for limit orders only. When a new order arrives, it attempts to match against resting orders on the opposite side of the book.

**Algorithm Steps**

Step 1: Validation

The system validates the incoming order. It checks that the market is open and not suspended. It verifies the user has sufficient available balance—for backs, this is the stake amount; for lays, this is the liability (stake × (odds - 1)). It confirms the odds are within valid bounds (1.01 to 1000) and at valid tick increments.

Step 2: Reserve Funds

Before attempting to match, the system reserves the required funds from the user's available balance. This prevents race conditions where concurrent orders might overdraw the balance.

Step 3: Fetch Opposite Side

The system retrieves orders from the opposite side of the book. For an incoming back order, it fetches lay orders sorted by odds ascending (lowest/best odds first). For an incoming lay order, it fetches back orders sorted by odds descending (highest/best odds first).

Step 4: Match Loop

For each resting order in price priority:

First, check price compatibility. A back at odds X can match with a lay at odds Y only if X ≤ Y. If prices are incompatible, stop matching (all subsequent orders will also be incompatible due to sorting).

Second, check for self-match. If the resting order belongs to the same user, skip it and continue to the next order.

Third, determine match quantity. The matched stake is the minimum of the incoming order's remaining stake and the resting order's remaining stake.

Fourth, calculate matched odds. The match occurs at the resting order's price. This rewards liquidity providers by guaranteeing their price.

Fifth, execute the match. Create a matched bet record linking both parties. Update both orders' filled quantities. Adjust reserved balances appropriately. Generate match event for notifications.

Sixth, check completion. If the incoming order is fully filled, exit the loop. Otherwise, continue to the next resting order.

Step 5: Handle Remaining Stake

If the incoming order has unfilled stake after matching all compatible internal orders:

Check external liquidity (Betfair). If Betfair offers compatible prices and the user accepted external execution, route the remaining stake to Betfair.

If no external match is possible or desired, add the remaining stake to the order book at the user's specified price.

Step 6: Return Result

Return the order result including order ID, status (matched/partial/open), matched amounts, and any remaining stake in the book.

**Price-Time Priority Enforcement**

Orders at the same price level are ordered by creation timestamp. Earlier orders are matched first. This is implemented by storing orders in a sorted data structure with composite keys: (price, timestamp).

**Partial Matching**

Both the incoming order and resting orders can be partially filled. Each partial fill creates a separate matched bet record. Users can cancel unfilled portions of their orders.

### 8.2 Balance Management Algorithm

**Overview**

The balance system maintains three values per user: available balance, reserved balance, and a derived total. All modifications are atomic database transactions.

**Operations**

Deposit Credit:
When a deposit confirms, increment available balance by deposit amount. Create transaction record with type "deposit."

Order Placement:
Calculate required reserve (stake for backs, liability for lays). Decrement available balance. Increment reserved balance. Create transaction record with type "order_reserve."

Order Match:
Reserved funds remain reserved but are now allocated to matched bet rather than open order. Update internal tracking but no balance change.

Order Cancellation:
Decrement reserved balance by unfilled order's reserve. Increment available balance by same amount. Create transaction record with type "order_cancel."

Bet Settlement - Win:
Calculate gross payout. Calculate commission (2% of profit). Calculate net payout. Decrement reserved balance by original reserve. Increment available balance by net payout. Create transaction records for settlement and commission.

Bet Settlement - Loss:
Decrement reserved balance by original reserve. Funds transfer to counterparty via settlement pool. Create transaction record with type "settlement."

Withdrawal Request:
Validate amount ≤ available balance. Decrement available balance. Create pending withdrawal record. After blockchain confirmation, create transaction record with type "withdrawal."

**Atomicity**

All balance modifications use database transactions with row-level locking on the user's balance record. This prevents race conditions from concurrent operations.

### 8.3 External Liquidity Algorithm (MVP Simplified)

**Overview**

The MVP implements a simplified external liquidity model: display Betfair prices in our order book and route orders there when internal liquidity is insufficient.

**Price Fetching**

A background job polls Betfair every 5 seconds for each active market. It retrieves the best 3 price levels for backs and lays. Prices are adjusted for Betfair commission (approximately 2%) to calculate effective odds. Adjusted prices are cached in Redis.

**Order Book Augmentation**

When displaying order book to users, internal orders are combined with cached Betfair prices. Betfair prices are marked with isExternal: true. Display logic: show internal liquidity first at each price level, then external.

**Order Routing**

When a new order cannot be matched internally:

Step 1: Check if user opted into external execution (acceptExternalMatch preference).

Step 2: Check if Betfair has liquidity at compatible prices.

Step 3: If yes, execute on Betfair via their API.

Step 4: Reflect the Betfair bet in our system as matched.

Step 5: If Betfair execution fails, either add to internal book or reject based on order type.

**Betfair Integration**

MVP uses Betfair's REST API with delayed (5-second) price data. Order execution uses their betting API. We maintain a funded Betfair account as treasury for external executions.

**Limitations**

This simplified model means:
- External prices may be slightly stale (5 seconds)
- External execution is not guaranteed (price may have moved)
- We take some price risk on external executions

Post-MVP improvements: real-time streaming prices, smarter execution algorithms, multiple external sources.

### 8.4 Settlement Algorithm

**Overview**

Settlement processes the outcomes of completed sporting events, determining winners and losers and distributing funds.

**Process**

Step 1: Result Ingestion

Sports data provider webhook delivers result. System validates the result corresponds to a known market. System stores result with status "pending_verification."

Step 2: Verification Delay

System waits 30 minutes before processing settlement. This allows for result corrections (e.g., own goals attributed to wrong player, final score corrections).

Step 3: Market Resolution

After delay, system resolves the market. For match winner markets: home win, away win, or draw is determined from the final score. Market status updates to "settled." Winning outcome is recorded.

Step 4: Bet Enumeration

System queries all matched bets for the market. Each bet is categorized: if bet's outcome matches winning outcome, bet is a winner; otherwise, bet is a loser.

Step 5: Payout Calculation

For each winning back bet:
- Gross payout = Stake × Odds
- Profit = Gross payout - Stake
- Commission = Profit × 0.02
- Net payout = Gross payout - Commission

For each winning lay bet (backed outcome lost):
- Gross payout = Stake (the stake they accepted from the backer)
- Commission = Stake × 0.02
- Net payout = Stake - Commission
- Liability is returned (was never at risk)

For each losing bet:
- Payout = 0
- Reserved funds are forfeit

Step 6: Balance Updates

For winners: release reserved amount, credit net payout to available balance.

For losers: release reserved amount to settlement pool, transfer to counterparty winners.

Step 7: Record Keeping

Create settlement transaction records for each bet. Update bet status to "settled" with outcome recorded. Generate notifications for all affected users.

**Error Handling**

If settlement calculations don't balance (winners' receipts ≠ losers' losses + commission), settlement is flagged for manual review.

If result is disputed or corrected within 24 hours, system can reverse and re-settle.

---

## 9. Blockchain Payment Design

### 9.1 MVP Blockchain Scope

**Chain: Base**

Base is selected for MVP because of low transaction fees (typically under $0.05), fast confirmations (2 seconds blocks), native USDC support, and easy fiat on-ramp via Coinbase.

**Token: USDC**

USDC is the sole currency for MVP. It provides stability (pegged to USD), wide availability, and regulatory acceptance.

### 9.2 Deposit Flow

**Architecture**

Each user receives a unique deposit address. This is derived deterministically from their user ID using a hierarchical deterministic (HD) wallet scheme. The platform's deposit watcher monitors these addresses for incoming transfers.

**User Experience**

User navigates to Deposit screen. System displays their unique deposit address and QR code. User sends USDC from their wallet (Coinbase, MetaMask, etc.) to this address. System detects the incoming transaction. After 12 block confirmations (approximately 30 seconds), system credits user's balance.

**Technical Flow**

Step 1: Address Generation
When user first requests deposit info, system generates address using HD derivation: master_key + user_id → deposit_address. Address is stored in database linked to user.

Step 2: Deposit Monitoring
Background service subscribes to USDC transfer events on Base. Filter: transfers to any known deposit address. When transfer detected, create pending deposit record.

Step 3: Confirmation Tracking
Service tracks block confirmations. At 12 confirmations, deposit is considered final. Credit user's available balance. Create transaction record.

Step 4: Notification
Send push/email notification to user: "Deposit of X USDC confirmed."

**Minimum Deposit**

Minimum deposit: 1 USDC. Deposits below this are not credited (gas costs would be disproportionate). This is clearly communicated on the deposit screen.

### 9.3 Withdrawal Flow

**User Experience**

User navigates to Withdraw screen. User enters amount and destination address. System shows estimated fee and net amount. User confirms withdrawal. System processes and sends funds. User receives notification when complete.

**Technical Flow**

Step 1: Request Validation
Validate amount ≤ available balance. Validate address is valid Ethereum format. Check daily withdrawal limit not exceeded.

Step 2: Balance Deduction
Deduct amount from available balance. Create pending withdrawal record.

Step 3: Queue Processing
Withdrawal processor picks up pending withdrawals. For MVP, processing is automatic for amounts under $1,000. Larger amounts queue for manual review.

Step 4: Transaction Execution
Processor signs transaction using platform hot wallet. Transaction sends USDC to user's address. Transaction broadcasts to Base network.

Step 5: Confirmation
Service monitors transaction confirmation. Upon confirmation, update withdrawal status to "completed." Store transaction hash. Notify user.

**Fee Handling**

Network fees are paid by the platform from operating funds. Withdrawal amount is what user receives (no deduction). This simplifies UX and the fees are minimal on Base.

**Security Controls**

Daily limit without additional verification: $1,000. New addresses have 1-hour cooling period before first withdrawal. Large withdrawals (over $5,000) require manual approval.

### 9.4 Treasury Management

**Hot Wallet**

Platform maintains a hot wallet for:
- Crediting deposits (receiving)
- Processing withdrawals (sending)
- External Betfair settlement

Hot wallet is limited to operational needs (e.g., $50,000 maximum).

**Cold Storage**

Excess funds beyond hot wallet limit are moved to cold storage. Cold storage uses multi-signature wallet requiring 2-of-3 signatures. Cold-to-hot transfers require manual process.

**Reconciliation**

Daily reconciliation verifies:
- Sum of user balances = hot wallet + cold storage - pending withdrawals
- All deposits have corresponding user credits
- All withdrawals have corresponding blockchain transactions

---

## 10. MVP Launch Plan

### 10.1 Development Timeline

**Weeks 1-2: Foundation**

- Project setup (NestJS, Next.js, PostgreSQL, Redis)
- Wallet authentication flow
- User registration and profiles
- Basic UI shell and navigation
- Database schema implementation

Deliverable: Users can sign up with wallet, see empty dashboard.

**Weeks 3-4: Trading Core**

- Market data ingestion from sports API
- Market listing and detail pages
- Order book data structure and display
- Order placement flow
- Basic matching engine

Deliverable: Users can view markets and place orders (no external liquidity yet).

**Weeks 5-6: Payments & Liquidity**

- Deposit address generation
- Deposit monitoring and crediting
- Withdrawal processing
- Betfair integration (price display)
- External order routing

Deliverable: Full trading loop with real funds.

**Weeks 7-8: Settlement & Launch**

- Result ingestion
- Settlement engine
- Comprehensive testing
- Security review
- Soft launch to waitlist

Deliverable: MVP live with limited users.

### 10.2 Launch Sequence

**Alpha (Week 8)**

- 50-100 users from waitlist
- Team members and advisors
- Focus on bug finding
- Direct feedback channel (Discord)

**Beta (Weeks 9-12)**

- Open to full waitlist (1,000+ users)
- Monitor system performance
- Iterate based on feedback
- Build liquidity

**Public Launch (Week 13+)**

- Remove waitlist
- Marketing push
- PR and content
- Ongoing iteration

### 10.3 Launch Markets

**Initial Competitions**

MVP launches with 6 football competitions:
- English Premier League
- Spanish La Liga
- German Bundesliga
- Italian Serie A
- French Ligue 1
- UEFA Champions League

**Market Types**

MVP: Match winner (1X2) only.

This provides approximately 50-100 active markets at any time.

### 10.4 Liquidity Strategy

**Internal Market Making**

Team operates market making bot providing initial liquidity. Bot maintains spreads on major matches. Bot uses platform funds with appropriate risk limits.

**External Display**

Betfair prices displayed in order book (marked as external). Users can see that liquidity exists even before organic growth.

**Incentives (Post-MVP)**

Consider maker fee rebates or liquidity mining rewards to attract organic market makers.

---

## 11. Success Criteria

### 11.1 MVP Success Metrics

| Metric | Target (Month 1) | Target (Month 3) |
|--------|------------------|------------------|
| Registered Users | 500 | 2,000 |
| Monthly Active Users | 100 | 500 |
| Total Deposits | $50,000 | $250,000 |
| Monthly Matched Volume | $100,000 | $500,000 |
| Orders Matched Internally | 20% | 40% |
| Settlement Accuracy | 100% | 100% |
| Critical Bugs | 0 | 0 |

### 11.2 User Feedback Targets

- Net Promoter Score (NPS): > 30
- Feature requests documented and prioritized
- Support response time: < 4 hours

### 11.3 Technical Performance

- API latency (p95): < 500ms
- Order matching latency (p95): < 200ms
- Uptime: > 99.5%
- Deposit credit time: < 2 minutes
- Withdrawal processing: < 5 minutes

### 11.4 Go/No-Go Criteria for Scale

After 3 months, evaluate whether to invest in scaling based on:

**Go Signals**
- User retention (30-day): > 30%
- Organic user growth without paid acquisition
- Internal match rate trending upward
- Positive unit economics (commission > costs)
- Strong user feedback

**No-Go Signals**
- User retention: < 15%
- Unable to attract liquidity providers
- Regulatory concerns in target markets
- Technical stability issues

---

## Appendix A: Competitive Analysis Summary

| Platform | Model | Strengths | Weaknesses | Our Differentiation |
|----------|-------|-----------|------------|---------------------|
| Betfair | P2P Exchange | Deep liquidity, established | High commission (5%), limited crypto | Lower fees, crypto-native |
| Smarkets | P2P Exchange | Low commission (2%) | Less liquidity than Betfair | Comparable fees + external liquidity |
| Polymarket | Prediction Market | Crypto-native, novel markets | Not sports-focused | Sports specialization |
| DraftKings | Bookmaker | Great UX, US legal | House edge, limits winners | No limits, better odds |
| Stake | Crypto Bookmaker | Crypto payments | Traditional bookmaker model | True P2P exchange |

---

## Appendix B: Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Betfair API changes/access revoked | Medium | High | Design for multiple sources, build internal liquidity |
| Low initial liquidity | High | Medium | Market making bot, external liquidity display |
| Regulatory action | Medium | High | Geo-blocking, legal consultation, jurisdiction selection |
| Smart contract vulnerability | Low | Critical | Audit before launch, limited treasury exposure |
| Result data errors | Low | High | Multi-source verification, manual review capability |
| Key person dependency | Medium | Medium | Documentation, cross-training |

---

## Appendix C: Glossary

**Back**: Betting that an outcome will happen. Traditional bet.

**Lay**: Betting that an outcome will NOT happen. Acting as bookmaker.

**Odds (Decimal)**: Multiplier for returns. 2.0 odds means $1 stake returns $2 (including stake).

**Stake**: Amount risked on a back bet.

**Liability**: Amount risked on a lay bet. Calculated as Stake × (Odds - 1).

**Matched Bet**: A bet where back and lay have been paired.

**Order Book**: Collection of unmatched back and lay orders.

**Maker**: User placing limit order that adds liquidity.

**Taker**: User whose order matches against existing orders.

---

*End of MVP Product Requirements Document*
