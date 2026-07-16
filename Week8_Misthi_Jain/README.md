# Week 8 - Single Agent Pipeline Project

## What This Was About
This week's task was to build a small single-agent assistant that can look at a query, figure out what kind of task it is, and send it to the right tool. Instead of just replying with plain text, it returns a structured response every time.

The agent needed to handle:
- Math queries -> Calculator tool
- Keyword extraction -> Keyword tool
- Anything else -> General response

## What I Did

### Agent Logic
I implemented the `agent(query)` function that was left as a TODO. It checks the lowercase version of the query and routes it:
- If it has "calculate" in it, I strip that word out and send the rest to the calculator
- If it has "keywords" in it, I send the full query to the keyword extractor
- Anything else just gets a general fallback response

### Output Format
Every response comes back like this, no matter which branch it hits:
```json
{
  "type": "calculation / keywords / general / error",
  "result": ...
}
```

### Error Handling
If the calculator fails (like dividing by zero or an incomplete expression), I made sure it shows up as `"type": "error"` instead of pretending it was a normal calculation. Also added a try/except around the whole thing just in case something unexpected breaks.

## Bonus Stuff I Added
- A third tool - word/character counter, triggers when the query has "count" in it
- Logging - every query and response gets saved with a timestamp in a `log` list so I can print the whole session later
- Routing now covers 4 types instead of 3 (calculation, keywords, count, general)

## How I Tested It
- Ran the given test cases first (calculate, keywords, general)
- Then went through interactive mode and tried a bunch more:
  - Division by zero
  - An incomplete expression like "calculate 5+"
  - A query with both "calculate" and "count" in it, just to see which one wins
  - Regular keyword and count queries
- Checked the log cell at the end to make sure everything got recorded properly

## Files Here
- `single_agent_pipeline.ipynb` - the notebook
- `README.md` - this file
- Week 8 diary entry

## Notes to Self
The keyword extractor picks up words like "extract" and "keywords" from the query itself since they're over 4 letters, so those show up in results sometimes. Not a bug, just how it's written. Also since "calculate" is checked before "count" in the if/elif chain, a query with both words in it goes to the calculator first.
