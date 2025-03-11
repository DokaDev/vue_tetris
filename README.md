# Vue Tetris

A web-based Tetris game built with Vue.js.

## Features

- Responsive Tetris game based on Vue.js
- Standard Tetris rules applied (bag system)
- Hold function support (C key)
- Keyboard control support (arrow keys and space bar)
- Next block preview
- Level system and scoring system

## Installation and Running

1. Clone the repository

```bash
git clone https://github.com/yourusername/tetris.git
cd tetris
```

2. Install dependencies

```bash
npm install
```

3. Run development server

```bash
npm run dev
```

4. Build

```bash
npm run build
```

## Game Controls

- ← → : Move left/right
- ↑ : Rotate clockwise
- Z : Rotate counter-clockwise
- X : Rotate clockwise
- ↓ : Soft drop (move down one space)
- Space : Hard drop (drop to the bottom)
- C : Hold (store/swap current block)

## Game System

### Bag System
- Seven different Tetris blocks (I, J, L, O, S, T, Z) are in one bag.
- The same block will not appear again until all blocks from one bag have appeared once.
- A new bag is created when all 7 blocks have been used.

### Hold System
- Press the C key to store the current block in the hold slot.
- The held block is exchanged with the current block when the C key is pressed again.
- Each block can only be held once until it is placed on the field.

## Scoring System

- 1 line cleared: 100 × current level
- 2 lines cleared: 300 × current level
- 3 lines cleared: 500 × current level
- 4 lines cleared: 800 × current level

## Technology Stack

- Vue.js
- HTML/CSS
- JavaScript
