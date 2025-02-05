# About The

ts-fsrs is a TypeScript package used to implement the [Free Spaced Repetition Scheduler (FSRS) algorithm](https://github.com/open-spaced-repetition/free-spaced-repetition-scheduler). It helps
developers apply FSRS to their flashcard applications, there by improving the user learning experience.

# Usage

```
npm install ts-fsrs
```

# Environment Variables
If you need to customize default parameters, you can modify the values using `.env`/`.env.local`/`.env.production`/`.env.development`.

Copy the [.env.local.example](./example/.env.local.example) file in this directory to .env.local (which will be ignored by Git):

```bash
cp .env.local.example .env.local
```

# Example

```typescript
import {createEmptyCard, formatDate, fsrs, generatorParameters, Rating} from 'ts-fsrs';

const params = generatorParameters({ enable_fuzz: true });
const f = fsrs(params);
const card = createEmptyCard(new Date('2022-2-1 10:00:00'));// createEmptyCard();
const now = new Date('2022-2-2 10:00:00');// new Date();
const scheduling_cards = f.repeat(card, now);

// console.log(scheduling_cards);
Object.keys(Rating).filter(key => typeof Rating[key as any] === 'number').forEach(key => {
  // @ts-ignore
  const { log, card } = scheduling_cards[Rating[key]];
  console.group(`${key}`);
  console.table({
    [`card_${key}`]: {
      ...card,
      due: formatDate(card.due),
      last_review: formatDate(card.last_review),
    },
  });
  console.table({
    [`log_${key}`]: {
      ...log,
      review: formatDate(log.review),
    },
  });
  console.groupEnd();
  console.log('----------------------------------------------------------------');
});
```

> More examples refer to the [Example](https://github.com/ishiko732/ts-fsrs/blob/master/example/index.ts)


# Basic Use 

## 1. **Initialization**:
To begin, create an empty card instance and set the current date:

```typescript
import { Card, createEmptyCard } from "ts-fsrs";
let card: Card = createEmptyCard();
```

## 2. **Parameter Configuration**:
The library allows for customization of SRS parameters. Use `generatorParameters` to produce the final set of parameters for the SRS algorithm. Here's an example setting a maximum interval:

```typescript
import { Card, createEmptyCard, generatorParameters, FSRSParameters } from "ts-fsrs";
let card: Card = createEmptyCard();
const params: FSRSParameters = generatorParameters({ maximum_interval: 1000 });
```

## 3. **Scheduling with FSRS**:
The core functionality lies in the `fsrs` function. When invoked, it returns a collection of cards scheduled based on different potential user ratings:

```typescript
import {
  Card,
  createEmptyCard,
  generatorParameters,
  FSRSParameters,
  FSRS,
  RecordLog,
} from "ts-fsrs";

let card: Card = createEmptyCard();
const f: FSRS = new FSRS();
let scheduling_cards: RecordLog = f.repeat(card, new Date());
```

## 4. **Retrieving Scheduled Cards**:
Once you have the `scheduling_cards` object, you can retrieve cards based on user ratings. For instance, to access the card scheduled for a 'Good' rating:

```typescript
const good: RecordLogItem = scheduling_cards[Rating.Good];
const newCard: Card = good.card;
```

## 5. **Understanding Card Attributes**:
Each `Card` object consists of various attributes that determine its status, scheduling, and other metrics:

```typescript
type Card = {
  due: Date;           // Date when the card is next due for review
  stability: number;   // A measure of how well the information is retained
  difficulty: number;  // Reflects the inherent difficulty of the card content
  elapsed_days: number; // Days since the card was last reviewed
  scheduled_days: number; // The interval at which the card is next scheduled
  reps: number;          // Total number of times the card has been reviewed
  lapses: number;        // Times the card was forgotten or remembered incorrectly
  state: State;          // The current state of the card (New, Learning, Review, Relearning)
  last_review?: Date;    // The most recent review date, if applicable
};
```
