# The Third Trade

## Final Project Proposal — OIM3690

**Find the exchange a direct swap would miss.**

The Third Trade is a campus item-swap planner that helps a student organizer find exchanges between people whose offers and requests do not line up as a simple direct swap.

The idea starts with a small problem: the person who has something I want might not want what I have. Someone else in the group may complete the exchange.

---

## The Three Ideas

1. **The Third Trade:** A tool for me, as a Babson student organizing a small group’s item swaps, that compares what each person has and wants and finds complete two-person or three-person exchanges.

2. **Venture Stress Test:** A tool for me when exploring a business idea that compares a specific assumption with real customer interview excerpts and identifies supporting evidence, conflicting evidence, and a useful next question.

3. **Five-Second Poster:** A tool for me when preparing a club event poster that collects what viewers remember after seeing the poster for five seconds and helps identify whether the main message came across.

## Why Not the Other Two

### Venture Stress Test

This idea could be useful, but its value depends on having a specific business assumption and relevant customer interviews to examine.

I would need to spend time gathering that evidence before I could meaningfully judge the recommendations. I would also need to check that the model was interpreting interview statements carefully rather than turning a few comments into confident business conclusions.

For this project, I prefer a problem where I can enter concrete information, work out an expected answer, and compare the application’s result with that answer.

The Third Trade gives me that opportunity: either a complete exchange exists among the accepted matches, or it does not.

### Five-Second Poster

I like the connection between design and what people actually remember. However, testing this idea well would require several viewers and a consistent way to show each poster and collect their responses.

A reply such as “Thursday” could mean that someone remembered an event date, a registration deadline, or simply a word on the poster. Interpreting that correctly would become a substantial part of the project.

I chose The Third Trade because its main result is easier to demonstrate and inspect. I can show every item moving through an exchange and explain exactly why the trade works or where it breaks.

## What I Am Building

I am building a web application that uses AI to suggest matches between students’ offered and wanted items, lets an organizer review those suggestions, and uses JavaScript to find complete two-person and three-person trades.

## Who It Is For

I am building this primarily for myself as a Babson student who wants a practical way to organize a small group’s swaps.

The situation I want to support is a group of students comparing things they have and things they would like: for example, a desk lamp, a mirror, a kettle, or a storage organizer.

With several offers and requests, I want to answer three questions quickly:

- Does anyone have something that fits another person’s request?
- Can those connections form a complete exchange?
- What would each person give and receive?

The first version will be used by one organizer entering the group’s information in one browser.

It will support more than three people in the participant list. The name refers to the third person who can make an exchange possible, rather than a limit of three people using the tool.

For the initial version, I will support up to ten participants, with one offered item and one wanted item per person. Individual trade results will contain two or three people.

I will ask another student to test the live application before final submission. I will record their actual name and observations in the README after that test happens.

## What the User Does

### 1. Add the people and their items

The organizer adds a participant using a form with:

- A participant name or label
- The item they are offering
- Relevant details about the offered item
- The item they want
- Any requirements that matter to them

For example, someone could offer:

“An adjustable LED desk lamp in working condition.”

Their request could be:

“A small mirror that can stand on a desk.”

Requirements should help the application distinguish between something that sounds similar and something the person would actually accept.

### 2. Ask for possible matches

The organizer clicks **Suggest Matches**.

The application sends the item descriptions, requirements, and internal participant IDs to a model through my own Vercel function.

The model suggests which offered items could fit other participants’ requests. Each suggestion includes a short explanation and identifies any missing information.

Participant names will stay in the browser; the model can compare the listings using IDs.

### 3. Review the suggestions

The organizer reviews each proposed connection.

They can accept it, reject it, or leave it waiting for more details.

A suggestion such as “This lamp may fit the request, but its power source is not stated” should remain unresolved until the organizer checks that detail.

Accepting a connection means the organizer considers the supplied descriptions compatible. It does not mean the application has obtained the participants’ agreement to exchange their belongings.

### 4. Find complete trades

The organizer clicks **Find Trades**.

JavaScript checks the accepted connections and displays complete two-person and three-person exchanges.

Each result clearly states:

- Who is involved
- What each person gives
- What each person receives
- Which reviewed connections make the exchange possible

### 5. Update and revisit the pool

The organizer can edit or remove a listing and return to the saved pool later in the same browser.

Changing a listing will invalidate the affected match decisions and trade results so that an old result cannot continue using outdated information.

### Example: why the third person matters

The following people are fictional demonstration participants.

| Participant | Offers | Wants |
| --- | --- | --- |
| Person A | Kettle | Mirror |
| Person B | Mirror | Desk lamp |
| Person C | Desk lamp | Kettle |

No two people in this example can satisfy both sides of a direct swap.

However, the following exchange works:

- Person A gives the kettle to Person C.
- Person C gives the desk lamp to Person B.
- Person B gives the mirror to Person A.

Each person gives one item and receives an item they requested.

The application will show both a visual representation of this exchange and a plain-language “gives / receives” list.

## Data

### Information entered by the organizer

Each participant will have a record containing:

- A unique internal ID
- A display name or label
- An offered item
- Offered-item details
- A wanted item
- Requirements for an acceptable match

The application will also keep the organizer’s decisions about suggested connections.

### Where the data lives

During use, participant records and review decisions will be stored in JavaScript arrays and objects.

They will also be saved in `localStorage` so the organizer can refresh the page and continue in the same browser.

This is storage for that browser and website. Opening the live link on another device will create a separate pool. Clearing the website’s stored data will remove the saved pool.

The first version will not depend on shared accounts or a database. Shared access between several students can be considered later if user testing shows that it is necessary.

### What is sent to the model

When the organizer clicks **Suggest Matches**, the application will send:

- Internal participant IDs
- Offered-item descriptions
- Wanted-item descriptions
- Relevant requirements

The model will be asked to return structured suggestions containing:

- The ID of the person offering the item
- The ID of the person whose request it may satisfy
- Whether it appears compatible or needs clarification
- A short reason
- Any unresolved requirement

The application will validate the response before displaying usable suggestions.

It will reject malformed responses, unknown participant IDs, and suggestions that match a person with their own item.

### API and hosting

I plan to use the [OpenRouter API](https://openrouter.ai/docs/quickstart), accessed through a function in this repository’s `api/` folder.

The application and its function will be deployed on Vercel.

The API key will be stored only in Vercel’s environment variables as `OPENROUTER_API_KEY`. It will never be included in browser JavaScript or committed to the repository.

During development, I will use a model permitted by the current course key and verify its response format. I will arrange a key with an appropriate credit limit if needed to keep the project working through the demo and final submission.

Every `fetch` will use `try/catch` and check `response.ok`.

### What the AI is responsible for

The AI feature will compare descriptions that may use different words for a similar item.

For example, “table light” and “desk lamp” may describe a compatible offer and request. However, an item that is wired should not satisfy a requirement for wireless operation.

The instructions will be specific to this application:

- Help a student organizer compare the supplied offers and requests.
- Use only the items and participant IDs provided.
- Treat explicit requirements as constraints.
- Explain a possible match using details from the descriptions.
- Identify missing information instead of assuming it.
- Do not invent item condition, availability, price, or participant agreement.
- Return suggestions for the organizer to review.

JavaScript will calculate the complete exchanges after review. A model’s statement that a trade exists will not be enough to display it as a valid result.

## Features

### 1. Manage a small swap pool

The organizer can add, edit, and remove up to ten participant listings.

Each participant has one offered item and one wanted item, with space for useful details and requirements.

Required fields will have clear validation messages, and unsuccessful submissions will preserve what the organizer has typed.

### 2. Get AI-assisted compatibility suggestions

The organizer can request suggestions about which offers may satisfy other participants’ requests.

Each suggestion will include a short explanation grounded in the entered descriptions.

The model request will happen only when the organizer asks for it. Editing a field will not automatically send another request.

### 3. Review matches before using them

The organizer can accept or reject suggestions and identify connections that need clarification.

Only accepted connections will be used to calculate trades.

This review step matters because similar wording does not guarantee that an item meets someone’s requirements.

### 4. Find and explain complete exchanges

JavaScript will search the accepted connections for two-person and three-person trade cycles.

For a result to be valid:

- Every person in it must be different.
- Each person gives their offered item once.
- Each person receives one item that matches their request through an accepted connection.
- The final connection returns to the starting person.
- The same cycle must not appear repeatedly just because the starting person changes.

If two possible trades use the same person or item, the application will present them as alternatives. It will not imply that both can happen at the same time.

The result will say what each participant gives and receives, without requiring the organizer to understand the underlying code.

### 5. Save progress and keep results current

The application will save participant listings and review decisions in `localStorage`.

After a refresh, it will restore that information and recalculate the results.

Editing or deleting a listing will remove affected approvals and outdated results. A model response based on older listings will not overwrite newer work.

A clearly labeled **Clear Pool** action will allow the organizer to start again after confirming the action.

### Intended visual direction

I want The Third Trade to feel like a playful campus swap board.

The main visual elements will be item tickets, clear “I have” and “I want” labels, and a simple diagram showing an exchange closing into a loop.

The result should feel satisfying because the connection becomes understandable.

My main design rule will be:

**A visitor should be able to tell what each person gives and receives without tracing confusing arrows or reading a paragraph of explanation.**

On a phone, the same information will stack into a readable sequence.

I will settle the exact colors, typography, spacing, and component states in `DESIGN.md` after the proposal is approved.

## States and Their Tests

These are planned tests. I will record actual outcomes only after running them.

### Required application states

| State | What I will do | What I should see |
| --- | --- | --- |
| Empty | Open the live link in a fresh private window. | An empty participant list, an available form, and a clear instruction to add at least two people before requesting matches. No old trade results appear. |
| Invalid input | Submit a listing with a blank or whitespace-only participant label, offer, or request. | An inline message identifies the missing field. Other entered information remains, and no incomplete listing is saved. |
| Invalid action | Try requesting matches with fewer than two participants, or try adding an eleventh participant. | A message explains the relevant limit. The existing pool remains intact. |
| Loading | Slow the connection in DevTools and click Suggest Matches. | A visible “Comparing offers and requests…” message appears. The button prevents duplicate requests, and the existing listings remain visible. |
| In use | Enter the three-person kettle, mirror, and lamp example, review its three compatible connections, and find trades. | One complete three-person exchange appears, with the correct item and recipient on every step. |
| Error | With listings already saved, switch the browser to Offline and request matches. | A message explains that the request could not finish. The listings remain available, and a Retry action is offered. After restoring the connection, retrying works. |
| Long answer | Send a controlled long explanation through the same response-handling path used by the live page. | The explanation wraps or expands without covering the review controls or creating horizontal scrolling on a phone. |
| Incomplete or invalid AI answer | Test a response cut off midway, malformed structured data, or a suggestion containing an unknown ID. | The application does not turn the response into accepted matches. It explains that the suggestions could not be read reliably and offers a retry. |

The long-answer and invalid-response cases will use clearly labeled test fixtures, with the method documented in the README. These simulated responses will be separate from tests of the real model connection.

Visitors will not supply an API key or sign in, so the first version does not have BYOK or signed-out states.

If the server key is missing, rejected, or out of credit, the page will show a service-unavailable error and preserve the organizer’s work.

### Trade logic and saved-state tests

| Test | What I will do | What I should see |
| --- | --- | --- |
| Direct swap | Enter two people who want each other’s offered item and accept both connections. | One two-person trade, with no unnecessary third participant. |
| Open chain | Accept A giving to B and B giving to C, without a connection that completes an exchange. | “No complete trade found among your accepted matches.” The application does not describe the chain as a finished trade. |
| Duplicate prevention | Use a three-person cycle that could be displayed starting from A, B, or C. | The same cycle appears once. |
| More than three participants | Add a fourth person whose request cannot be fulfilled while the original three-person cycle remains valid. | The original cycle still appears. The fourth person is not forced into it. |
| Overlapping alternatives | Create two possible trades that share one participant. | Both appear as alternatives, with a clear explanation that the shared listing cannot be used in both exchanges. |
| Changed listing | Edit an offer or requirement used in a displayed trade. | Its affected connections require review again, and the old trade disappears. |
| Deleted listing | Remove someone who is part of a displayed trade. | That person and all affected connections and results disappear. Unrelated listings remain. |
| Older AI response | Change a listing while a model request is still running. | The reply for the older version cannot restore outdated suggestions or results. The organizer is told to compare the updated pool. |
| Refresh | Save a pool and review decisions, then refresh the same browser tab. | The listings and decisions return, and the trade results are recalculated. |
| Clear pool | Click Clear Pool and confirm. | The app’s saved pool is removed, results disappear, and the empty state returns. Refreshing does not restore the cleared pool. |
| Phone and keyboard | Complete the main workflow on a narrow screen and using the keyboard. | Labels remain readable, controls remain reachable, focus is visible, and the page does not scroll horizontally. |

### AI evaluation examples

I will use a small set of repeatable examples and rerun them whenever I change the model instructions.

| Input | What a good reply must contain |
| --- | --- |
| Offer: “Adjustable LED desk lamp.” Request: “A table light for studying.” | A possible match with an explanation connecting the descriptions, while identifying any unstated requirement that matters. |
| Offer: “Wired headphones only.” Request: “Bluetooth headphones; wireless is required.” | No compatible-match claim, because the explicit requirement conflicts with the offer. |
| Offer: “Storage box.” Request: “A box that fits under a bed with 15 cm clearance.” | A request for the box’s height or other clarification. The model must not assume it fits. |
| Offer: “Desk mirror.” Request: “Electric kettle.” | No compatible-match claim. |
| Several listings with assigned IDs | Suggestions refer only to existing participants, never match a participant to themselves, and do not invent an additional item to complete a trade. |

These checks will assess the model’s suggestions separately from the JavaScript that finds complete cycles.

## What I Do Not Know Yet

### Whether the trade logic can find a complete exchange correctly

Before building the application, my agent ran an isolated JavaScript check of the trade-matching logic.

Eight small tests passed. They covered:

- A direct two-person exchange
- A three-person cycle with no direct two-person swap
- An open chain that should produce no complete trade
- Ignoring self-matches
- Removing repeated rotations while keeping genuinely different cycles
- Handling repeated connections and unknown participant IDs
- Removing a trade when a required connection is removed
- Keeping overlapping trades as separate alternatives

This supports using a small, understandable cycle search for the first version.

It does not establish that the finished interface, saved state, or live model connection works. Those still need to be implemented and tested.

### Whether the model produces useful, readable match suggestions

The proposed live OpenRouter integration has not yet been tested for this project.

Before submitting the proposal, I still need to make a small real model request with:

1. The kettle, mirror, and desk-lamp example.
2. A similar-wording example such as “table light” and “desk lamp.”
3. A conflicting requirement such as wired versus Bluetooth headphones.

I will record the actual request, relevant output, and what I learned here after running it.

The key questions are whether the permitted model follows the application’s instructions, produces a response the code can validate, and identifies missing information without guessing.

### How to keep results trustworthy when listings change

I need to work out a simple way to associate accepted connections and AI requests with the listings they were based on.

If someone changes an item or requirement, the application must not continue using a previous approval as though the description were unchanged.

I will test this explicitly, including the case where an older model response arrives after an edit.

### How to make the result understandable to another person

A cycle can be correct in code and still be confusing on the screen.

I need to find out whether another student can understand who gives and receives each item without help.

That observation will guide the layout and wording of the result.

### Keeping the first version manageable

The initial version will focus on one organizer, up to ten listings, and cycles of two or three people.

Shared accounts, a live campus marketplace, item photographs, and longer trade cycles will go on the Later list if they become relevant. They are outside the first version’s planned features.

## How This Uses the Coursework

| Course concept | How I will use it |
| --- | --- |
| HTML forms and validation | Collect participant listings and explain missing information. |
| Arrays and objects | Store participants, suggestions, and review decisions. |
| Functions and conditionals | Separate responsibilities and check whether a connection or trade is valid. |
| Loops | Examine accepted connections and find two-person and three-person cycles. |
| DOM manipulation and events | Update listings, suggestions, and results when the organizer takes an action. |
| `localStorage` and JSON | Save and restore the organizer’s pool. |
| `async`, `await`, and `fetch` | Request AI suggestions through my server function. |
| `try/catch` and `response.ok` | Handle failed requests without losing entered information. |
| API-key safety and Vercel | Keep the model key on the server and deploy a public working link. |
| Responsive CSS | Make the form, reviews, and trade results usable on a phone. |
| Browser DevTools | Inspect requests, reproduce errors, and test loading and narrow-screen behavior. |

I plan to use plain HTML, CSS, and JavaScript so I can explain the functions that do the main work.

The new part I expect to learn is how to represent accepted connections and check whether they form a closed exchange.

## User Testing and Verification Plan

Before the deployed URL is due, I will run every proposal test on the live link in a private window and record the actual results under `## Tests` in the README.

Because I am building the tool for myself, I will also have another student use it while I watch without helping.

I will ask that person to:

1. Enter the demonstration listings.
2. Request and review suggestions.
3. Find a complete trade.
4. Explain what each person gives and receives.
5. Change one listing and find out what happens to the result.

I will record what they tried and where they became confused.

I will then make at least one improvement based on that observation, commit the change, and link the commit under `## User test` in the README.

After my final code change, I will rerun all proposal tests and date the results.

For the required AI-output question, I will describe one specific piece of output I reviewed, what I changed or how I verified it, and the actual commit that shows that work.

## Build and Delivery Plan

I will keep this project in its own public repository.

The proposal will be committed before application implementation. After the instructor approves it, I will write and commit `PRD.md`, `DESIGN.md`, and `AGENTS.md` before the code each file governs.

I will build in small stages:

1. Participant entry and validation.
2. Saved state and editing.
3. A working model request with loading and error handling.
4. Reviewable compatibility suggestions.
5. Two-person and three-person trade results.
6. Complete state testing, phone checks, and the observed user test.

