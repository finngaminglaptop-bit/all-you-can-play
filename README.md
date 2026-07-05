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
- Per-turn timers and "random first move" are ignored in online mode — turns
  just alternate starting with the host, since syncing a countdown fairly
  across two clocks needs a server-authoritative timer this project doesn't
  have.
- Hangman's secret word lives in the shared room document so every
  connected client can render/check guesses against it — a guesser who opens
  their browser's network tab could technically read it early. Same trust
  model as the rest of the site: whoever has the room code can see
  everything in that room.
- No presence/cleanup service: a "may have disconnected" banner appears if a
  player's heartbeat goes stale (~20s), but there's no auto-forfeit, and
  finished/abandoned rooms aren't automatically purged from Firestore.
- Reconnecting after a refresh works within the same browser tab (via
  `sessionStorage`) — closing the tab or switching devices mid-game loses
  your seat.

Published automatically via GitHub Pages on every push to `main`.
