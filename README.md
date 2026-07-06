# All-you-can-Play

A free game room for playing with friends — on one shared screen, or online
from separate devices with a room code. No sign-up. Everything runs
client-side in `index.html`; online mode syncs game state through a small
Firestore database (see "Online multiplayer" below).

## Features

- **Room setup flow** — pick a game, name the room, generate a shareable
  room code, and configure per-game rules before starting.
- **Games**
  - **4 in a Row** — drop discs and connect four (or five) in a row.
    Configurable board size, win length, and disc colours.
  - **Tic Tac Toe** — classic grid play with selectable grid size (3×3 to
    5×5), best-of-1/3/5 series, and a minimax AI opponent.
  - **Hangman** — guess a word letter by letter, with category word lists
    or a custom word set by another player. Supports 2–8 players.
  - **Guess the Code** — both players secretly set a numeric code (3–6
    digits, room owner picks the length); a random player goes first, then
    turns alternate guessing each other's code. Correctly-placed digits
    light up green and stay revealed. Optional "present" hint shows how
    many guessed digits exist elsewhere in the code, classic-Mastermind
    style. "Notes" toggle controls whether a running list of past guesses
    and their exact-match counts stays visible. Optional per-turn timer
    (host-configurable) skips a turn if it runs out — this one works in
    online mode too, synced off a shared turn-start timestamp rather than
    each client's own clock (each side counts down from when *it* saw the
    turn begin, so the two players' clocks disagreeing doesn't throw the
    countdown off). Your own code stays visible the whole game; guesses can
    be typed in ahead of your turn, though submitting still waits for it.
    Once the match ends, nothing is forced — the result and "Play
    again"/"Main menu" options appear inline at the bottom of the same
    screen, so the board (and a "Reveal my code" button) stay up for as
    long as you want before moving on.
  - Each game supports **vs AI**, local **Pass & Play**, or **Online**.
- **Tools hub**
  - **Spin the Wheel** — add custom items/names and spin for a random pick,
    with spin history.
  - **Dice Roller** — roll any number of dice with custom side counts.
  - Countdown Timer and Card Draw are planned ("Soon").

## Online multiplayer

Pick **Online** as the opponent/mode when creating a room. The host gets a
6-letter code and a waiting screen; anyone who enters that code (or picks
the room from the "Open rooms" lobby on the Join screen) joins live and
play starts automatically once enough players are in. Moves sync through a
`rooms` collection in the same free Firebase project used by this account's
other tools (see `firestore.rules` in the `flashcard-maker` repo for the
security rules governing it).

**Known limitations, by design (no accounts, no server logic):**
- 4 in a Row and Tic Tac Toe still ignore per-turn timers and "random first
  move" in online mode — turns just alternate starting with the host.
  Guess the Code's timer is the exception: it's synced off a shared
  `turnStartedAt` timestamp written to the room doc, so both clients count
  down from the same point rather than trusting their own clock.
- Hangman's secret word, and both players' secret codes in Guess the Code,
  live in the shared room document so the other client can check guesses
  against them — a player who opens their browser's network tab could
  technically read the opponent's secret early. Same trust model as the
  rest of the site: whoever has the room code can see everything in that
  room.
- A player explicitly leaving (Quit/Leave room) ends the game immediately
  for whoever's left, across all online games. A merely-stale connection
  (closed laptop, dead wifi) is softer: only a "may have disconnected"
  banner appears (~20s of no heartbeat) — there's no forced forfeit for
  that case, since a stale heartbeat can't be told apart from someone just
  thinking. Finished/abandoned rooms aren't automatically purged from
  Firestore either way.
- Reconnecting after a refresh works within the same browser tab (via
  `sessionStorage`) — closing the tab or switching devices mid-game loses
  your seat.

Published automatically via GitHub Pages on every push to `main`.
