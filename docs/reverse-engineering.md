# Product Research and Reverse-Engineering Method

The goal is to understand publicly observable scheduling behavior and independently implement equivalent general-purpose functionality. This is product analysis, not extraction of proprietary code.

## 1. Research boundaries

Use only legitimate, public sources:

- Public product pages and help articles.
- Public developer documentation and API references.
- Your own account and test events, where permitted by the service terms.
- Browser-visible workflows that do not bypass authentication or access controls.
- Open-source projects whose licenses permit study and reuse.

Do not:
- Access private endpoints or another user's data.
- Bypass authentication, rate limits, or technical restrictions.
- Copy proprietary source code, private API schemas, branding, illustrations, or text.
- Misrepresent this project as an official Calendly product.

## 2. Create a feature inventory

For each workflow, record:

| Area | Questions to investigate |
|---|---|
| Event types | What can a host configure? |
| Availability | How are weekly hours and date exceptions represented? |
| Booking page | What information does an invitee need? |
| Timezones | How is the viewer's timezone displayed and changed? |
| Slot generation | How do duration, buffers, and notice affect slots? |
| Booking | What happens after a slot is selected? |
| Changes | How are cancellation and rescheduling handled? |
| Integrations | What calendar and notification connections exist? |
| Teams | What additional rules are needed for shared scheduling? |

Record observations as behavior statements, not copied implementation assumptions.

## 3. Use a repeatable observation template

For every feature, document:

- **Scenario:** the user goal.
- **Initial state:** account, event, and schedule configuration.
- **Action:** steps performed through the public UI.
- **Observed result:** visible behavior and messages.
- **Edge cases:** unavailable dates, timezone changes, conflicts, and invalid input.
- **Our design:** an independent implementation decision.
- **Tests:** acceptance tests that verify our behavior.

## 4. Build a behavior matrix

Example cases for availability:

| Condition | Expected behavior |
|---|---|
| Host has no schedule | No slots returned |
| Date is blocked by an override | No slots returned |
| Existing booking occupies a slot | Conflicting slot excluded |
| Slot is shorter than event duration | Slot excluded |
| Slot violates minimum notice | Slot excluded |
| Invitee changes display timezone | Same instant, different local display |
| Daylight-saving transition occurs | Resolve local times using explicit timezone rules |

## 5. Validate with tests

Translate each observation into automated tests. Avoid depending on undocumented behavior that may change. Prefer documented public APIs for integrations.

## 6. Research log

Keep a dated log in this repository when adding new findings. Cite the public source, summarize the relevant behavior in your own words, and record what design decision it informed.
