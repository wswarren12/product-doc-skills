# Acceptance Criteria Reference

Detailed patterns and examples for writing testable acceptance criteria.

## Formats

### Given/When/Then (BDD - Recommended)

Best for complex features with multiple scenarios.

```
Given [precondition/context]
When [action taken]
Then [expected result]
And [additional result] (optional)
```

### Checklist Format

Best for simple features with clear pass/fail conditions.

```
- [ ] [Specific, testable condition]
- [ ] [Another condition]
```

### Rule-Based Format

Best for validation logic and business rules.

```
Rule: [Business rule description]
- Condition: [When this is true]
- Result: [This happens]
```

---

## Patterns by Feature Type

### Authentication Features

```gherkin
# Login
Given a registered user with email "user@example.com"
When they enter correct email and password
Then they are redirected to the dashboard
And a session token is stored

Given a registered user
When they enter incorrect password 3 times
Then the account is locked for 15 minutes
And an email notification is sent

# Registration
Given a visitor on the signup page
When they submit valid email, password (8+ chars, 1 number)
Then an account is created
And a verification email is sent

Given a visitor attempting signup
When they enter an email already in use
Then an error displays: "Email already registered"
And the password field is cleared
```

### CRUD Features

```gherkin
# Create
Given a logged-in user on the dashboard
When they click "Create New [Item]"
Then a creation form appears
And required fields are marked with asterisks

Given a user completing the create form
When they submit with all required fields valid
Then the [item] is saved to database
And they're redirected to the [item] detail page
And a success toast displays

# Read (List)
Given a user with 25 [items]
When they view the [items] list
Then 10 items display per page
And pagination controls appear
And items are sorted by created_at descending

# Update
Given a user viewing their [item]
When they click "Edit"
Then the form pre-populates with current values
And the "Save" button is disabled until changes are made

# Delete
Given a user on the [item] detail page
When they click "Delete"
Then a confirmation modal appears: "Are you sure?"
When they confirm deletion
Then the [item] is removed
And they're redirected to the list view
```

### Search & Filter Features

```gherkin
Given a user on the products page
When they type "blue shirt" in the search box
Then results update after 300ms debounce
And only products matching "blue" AND "shirt" display

Given a user searching with no results
When the search completes
Then a message displays: "No results for '[query]'"
And suggested actions display: "Try different keywords"

Given a user with filter: "Price: $10-$50"
When they also search "cotton"
Then results match BOTH the filter AND search term
```

### Notification Features

```gherkin
Given a user with notification preferences enabled
When an invoice becomes 7 days overdue
Then an email is sent to the client
And the invoice status updates to "Reminder Sent"
And a log entry is created with timestamp

Given an email fails to send (SMTP error)
When the retry limit (3 attempts) is reached
Then the invoice is flagged for manual review
And an alert is sent to the user
```

### Payment Features

```gherkin
Given a user on the checkout page
When they enter a valid credit card and click "Pay"
Then a loading indicator displays
And the card is charged via Stripe
And an order confirmation page displays
And an email receipt is sent

Given a payment failure (insufficient funds)
When the error is returned
Then a message displays: "Payment failed: [reason]"
And the card is NOT charged
And the user can retry with different payment
```

---

## Edge Cases to Always Consider

### Empty States
```gherkin
Given a new user with no [items]
When they view the [items] list
Then a helpful empty state displays
And a CTA button "Create your first [item]" is prominent
```

### Loading States
```gherkin
Given a user triggering a slow operation (>500ms)
When the operation is in progress
Then a loading indicator displays
And interactive elements are disabled
And the user cannot trigger duplicate requests
```

### Error States
```gherkin
Given a network failure during form submission
When the request times out after 10 seconds
Then an error message displays: "Connection failed. Please try again."
And form data is preserved
And a "Retry" button appears
```

### Permission/Access
```gherkin
Given a user without admin role
When they attempt to access /admin/settings
Then they are redirected to the dashboard
And a message displays: "You don't have permission to access this page"
```

### Input Validation
```gherkin
Given a user entering an email field
When they type "invalid-email"
Then real-time validation shows error: "Enter a valid email"
And the field border turns red
And form submission is blocked until fixed
```

### Concurrency
```gherkin
Given two users editing the same document
When User A saves after User B
Then User A sees a conflict notification
And both versions are preserved
And User A can choose which version to keep
```

---

## Anti-Patterns to Avoid

### ❌ Vague Criteria
```
- The system should be fast
- Users should find it intuitive
- Errors are handled gracefully
```

### ✅ Specific Criteria
```
- Page loads in under 2 seconds on 3G connection
- New users complete onboarding in under 3 minutes (based on analytics)
- All API errors return JSON with {error: string, code: number}
```

### ❌ Implementation Details in AC
```
Given the React component mounts
When the useEffect hook runs
Then the Redux store is updated
```

### ✅ Behavior Focus
```
Given a user opens the dashboard
When the page loads
Then their recent items display within 1 second
```

### ❌ Multiple Behaviors in One AC
```
Given a user signs up, logs in, creates a project, and invites a team member...
```

### ✅ Single Behavior per AC
```
Given a registered user on the login page
When they enter valid credentials
Then they are redirected to the dashboard
```

---

## Acceptance Criteria Checklist

For each feature, ensure criteria address:

- [ ] **Happy path** — Normal, successful flow
- [ ] **Validation errors** — Invalid input handling
- [ ] **Empty states** — No data scenarios
- [ ] **Loading states** — Async operation feedback
- [ ] **Error states** — System/network failures
- [ ] **Edge cases** — Boundary conditions
- [ ] **Permissions** — Access control
- [ ] **Concurrent access** — Multi-user scenarios (if applicable)
