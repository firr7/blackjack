# Blackjack Royale

A fully-featured, zero-dependency blackjack game that runs entirely in the browser. All HTML, CSS, and JavaScript are contained in a single `index.html` file.

## How to run

Open `index.html` in any modern web browser — no build step or server required.

```bash
# Option A – open directly
open index.html

# Option B – serve locally (avoids any browser CORS restrictions)
python3 -m http.server 8000
# then navigate to http://localhost:8000
```

---

## Repository structure

```
blackjack/
└── index.html   # The entire application: HTML markup, CSS styles, and JavaScript logic
```

There are no external dependencies, build tools, package manifests, or test suites — everything ships in one self-contained file (~1,030 lines).

---

## Key technologies

| Technology | Usage |
|---|---|
| **HTML5** | Semantic page structure and DOM elements |
| **CSS3** | Flexbox layout, CSS Grid, custom properties (variables), keyframe animations, media queries for mobile |
| **Vanilla JavaScript (ES6+)** | All game logic — no libraries or frameworks |
| **Google Fonts CDN** | *Playfair Display* (headings) and *Cormorant Garamond* (body text) |

---

## Code organisation

The JavaScript block (lines 669–1027) is split into clearly labelled sections:

### `DECK & CARDS` (lines 670–719)
Manages the card deck and hand evaluation.

| Symbol | Description |
|---|---|
| `SUITS`, `RANKS` | Card suit and rank constants |
| `buildDeck()` | Creates a 6-deck shoe (312 cards) and shuffles it |
| `shuffle(arr)` | Fisher-Yates in-place shuffle |
| `cardValue(card)` | Returns the numeric value of a card (Ace = 11, face cards = 10) |
| `handTotal(hand)` | Sums a hand and reduces Ace values (11 → 1) to prevent busting |
| `isSoft(hand)` | Returns `true` when at least one Ace is still counted as 11 |
| `drawCard()` | Pops a card from the shoe; rebuilds the deck when fewer than 20 cards remain |

### `RENDER` (lines 721–779)
Handles all DOM updates for cards and score badges.

| Symbol | Description |
|---|---|
| `renderCard(card, hidden, delay)` | Builds and returns a `.card` `<div>` element |
| `renderHands(revealDealer)` | Clears and re-renders both the dealer and player hands |
| `updateScores(revealDealer)` | Updates score badges; adds `.bust` / `.blackjack` CSS classes |

### `BET` (lines 781–795)
Controls the betting phase.

| Symbol | Description |
|---|---|
| `placeBet(amount)` | Adds a chip value to `currentBet`; validates against `balance` |
| `clearBet()` | Resets `currentBet` to zero |

### `DEAL` (lines 797–827)
Starts a new hand.

| Symbol | Description |
|---|---|
| `deal()` | Deducts the bet from `balance`, deals two cards to each side, and checks for an immediate natural blackjack |

### `PLAY` (lines 829–878)
Implements player and dealer actions.

| Symbol | Description |
|---|---|
| `hit()` | Deals one card to the player; triggers bust or auto-stand on 21 |
| `stand()` | Disables player controls and hands off to `dealerPlay()` |
| `doubleDown()` | Doubles the bet, deals one final card, then stands |
| `dealerPlay()` | Reveals the dealer's hole card, then starts the recursive draw loop |
| `dealerDraw()` | Recursively draws for the dealer following Vegas rules (hit on soft 17, stand on hard 17+) with a 700 ms delay between cards |
| `resolveGame()` | Compares final totals and calls `endGame()` with the correct outcome |

### `RESULTS` (lines 889–961)
Determines outcomes and updates the balance.

| Symbol | Description |
|---|---|
| `endGame(outcome)` | Calculates payout (3:2 for blackjack, 1:1 for win, 0 for loss) and updates `balance`; resets to 1 000 credits if the player goes broke |
| `showResult(text, sub, cls)` | Fades in the result overlay |
| `hideResult()` | Hides the result overlay |
| `spawnCoins()` | Animates emoji coins on a win |

### `UI` (lines 963–1022)
Keeps button states in sync with `gameState`.

| Symbol | Description |
|---|---|
| `updateUI()` | Enables/disables chips and action buttons based on the current game state; swaps the Deal button label to *Nuova Mano* when the hand is over |
| `updateBetDisplay()` | Refreshes the displayed bet value |
| `disablePlayButtons()` | Disables Hit, Stand, and Double at the end of a hand |
| `setMessage(msg)` | Updates the status message below the action buttons |
| `newGame()` | Resets state for the next hand |

### `INIT` (lines 1024–1026)
Two calls that bootstrap the application: `buildDeck()` and `updateUI()`.

---

## Game state machine

```
betting  ──deal()──►  playing  ──stand()/bust──►  done
   ▲                                                 │
   └─────────────────newGame()──────────────────────┘
```

The `gameState` variable can be `'betting'`, `'playing'`, or `'done'`. Button availability and behaviour are derived entirely from this value inside `updateUI()`.

---

## Game rules

- **Deck:** 6 standard decks (312 cards), reshuffled automatically when fewer than 20 cards remain.
- **Blackjack pays 3:2.**
- **Dealer rules:** must draw to 16, **hit on soft 17**, stand on hard 17+.
- **Double Down:** available on the first two cards only, provided the player has sufficient credit.
- **Starting balance:** 1 000 credits; automatically topped back up to 1 000 if the player runs out.

---

## UI language

The interface is in Italian. Key terms:

| Italian | English |
|---|---|
| Banco | Dealer |
| Credito | Balance |
| Puntata | Bet |
| Azzera | Clear |
| Nuova Mano | New Hand |
| Sballato | Bust |
| Pareggio | Push (tie) |
