# User Flow Examples

Well-structured user flows for common product patterns.

## Flow Format

Every flow should be:
1. **Numbered sequentially** — Each step is one action
2. **Actor-explicit** — Clear who does what (User vs System)
3. **Outcome-focused** — What the user sees/experiences
4. **Error-aware** — Include decision points and failures

---

## Authentication Flows

### User Registration

```
1. User clicks "Sign Up" button
2. System displays registration form
3. User enters email address
4. User enters password (min 8 chars, 1 number required)
5. User clicks "Create Account"
6. System validates inputs
   - If invalid: Display inline errors, return to step 3
7. System creates account
8. System sends verification email
9. System displays: "Check your email to verify your account"
10. User clicks link in email
11. System marks account as verified
12. System redirects to dashboard with success message
```

### User Login

```
1. User navigates to /login
2. System displays login form
3. User enters email
4. User enters password
5. User clicks "Log In"
6. System validates credentials
   - If invalid: Display "Invalid email or password"
   - If account locked: Display "Account locked. Try again in X minutes"
7. System creates session
8. System redirects to dashboard (or intended destination)
```

### Password Reset

```
1. User clicks "Forgot Password" on login page
2. System displays email input form
3. User enters registered email
4. User clicks "Send Reset Link"
5. System sends reset email (silent success even if email not found - security)
6. System displays: "If an account exists, you'll receive reset instructions"
7. User clicks link in email (valid for 24 hours)
8. System displays new password form
9. User enters new password twice
10. User clicks "Reset Password"
11. System validates password match and strength
    - If invalid: Display errors, return to step 9
12. System updates password
13. System invalidates all existing sessions
14. System redirects to login with: "Password updated. Please log in."
```

---

## CRUD Flows

### Create Item

```
1. User clicks "+ New [Item]" from list view
2. System displays creation form
3. User fills required fields (marked with *)
4. User optionally fills optional fields
5. User clicks "Save" (or "Create")
   - If validation errors: Highlight invalid fields, show errors
6. System saves to database
7. System redirects to item detail view
8. System displays success toast: "[Item] created"
```

### Edit Item

```
1. User views [Item] detail page
2. User clicks "Edit" button
3. System displays form pre-filled with current values
4. User modifies desired fields
5. "Save" button enables when changes detected
6. User clicks "Save"
   - If validation errors: Highlight invalid fields, show errors
7. System saves changes with updated_at timestamp
8. System displays success toast: "Changes saved"
9. System shows updated detail view
```

### Delete Item (with confirmation)

```
1. User clicks "Delete" on item detail or list row
2. System displays confirmation modal:
   - Title: "Delete [Item Name]?"
   - Body: "This action cannot be undone."
   - Buttons: "Cancel" | "Delete"
3. User clicks "Delete" to confirm
   - If "Cancel": Close modal, no action
4. System soft-deletes item (sets deleted_at)
5. System closes modal
6. System redirects to list view (if from detail)
7. System displays success toast: "[Item] deleted"
```

---

## Search & Filter Flows

### Search with Filters

```
1. User views list of [Items]
2. User types in search box
3. System waits 300ms debounce
4. System filters results matching search term
5. System displays result count: "X results for '[query]'"
6. User clicks "Filter" button
7. System displays filter panel/dropdown
8. User selects filter options (date range, category, status)
9. User clicks "Apply"
10. System combines search + filters
11. System updates URL params for shareability
12. System displays filtered results with active filter badges
13. User clicks "X" on filter badge to remove individual filter
14. User clicks "Clear all" to reset to default view
```

---

## E-commerce Flows

### Add to Cart

```
1. User views product detail page
2. User selects options (size, color, quantity)
3. User clicks "Add to Cart"
4. System validates stock availability
   - If out of stock: Display "Sorry, this item is out of stock"
5. System adds item to cart (stored in session/DB)
6. System updates cart icon badge with new count
7. System displays slide-out cart preview OR success toast
8. User can click "View Cart" or continue shopping
```

### Checkout

```
1. User clicks "Checkout" from cart
2. System displays checkout page
3. System checks user authentication
   - If not logged in: Prompt login or guest checkout
4. User enters/confirms shipping address
5. User selects shipping method
6. System calculates totals (subtotal, shipping, tax)
7. User enters payment information
8. User reviews order summary
9. User clicks "Place Order"
10. System displays loading indicator
11. System processes payment via payment provider
    - If payment fails: Display error, return to step 7
12. System creates order record with status "Confirmed"
13. System sends order confirmation email
14. System displays order confirmation page with order number
15. System clears cart
```

---

## Onboarding Flows

### First-Time User Onboarding

```
1. User completes registration
2. System redirects to onboarding flow
3. System displays welcome screen with progress indicator (1/4)
4. User clicks "Get Started"
5. System displays Step 1: Profile Setup
6. User enters name, uploads avatar (optional)
7. User clicks "Continue"
8. System displays Step 2: Preferences
9. User selects relevant options
10. User clicks "Continue"
11. System displays Step 3: First [Item] Creation (guided)
12. System pre-fills example data with tooltips
13. User completes or skips
14. System displays Step 4: Success/Summary
15. User clicks "Go to Dashboard"
16. System marks user as onboarded
17. System displays dashboard with contextual tips
```

---

## Notification Flows

### Email Notification Trigger

```
1. System scheduled job runs (daily at 9am UTC)
2. System queries for conditions (e.g., overdue invoices)
3. For each matching item:
   a. System checks notification preferences
   b. System generates email content from template
   c. System sends via email provider (SendGrid/SES)
   d. System logs send attempt with timestamp
   e. If failure: Queue for retry (max 3 attempts)
4. System updates item status (e.g., "reminder_sent_at")
5. On successful send: System logs delivery confirmation
```

---

## Multi-User Flows

### Invite Team Member

```
1. Admin clicks "Invite Team Member"
2. System displays invite form
3. Admin enters email address
4. Admin selects role (Admin, Member, Viewer)
5. Admin clicks "Send Invite"
6. System creates pending invite record
7. System sends invite email with unique link (expires in 7 days)
8. System displays: "Invite sent to [email]"
9. [Recipient flow]:
   a. Recipient clicks link in email
   b. System validates link (not expired, not used)
   c. System displays account creation form
   d. Recipient creates password
   e. Recipient clicks "Join Team"
   f. System creates user with assigned role
   g. System marks invite as accepted
   h. System redirects to team dashboard
```

---

## Error Recovery Flows

### Network Failure During Save

```
1. User edits form and clicks "Save"
2. System attempts API call
3. Network request fails/times out (10s)
4. System displays error: "Could not save. Please try again."
5. System preserves form data (no data loss)
6. System displays "Retry" button
7. User clicks "Retry"
8. System reattempts save
   - If success: Normal success flow
   - If failure again: "Still having trouble. Check your connection."
9. User can also dismiss and continue editing
```

---

## Flow Documentation Tips

### Do's
- Start each step with the actor (User/System)
- One action per step
- Include decision points with outcomes
- Note timing requirements (debounce, timeouts)
- Include validation and error paths

### Don'ts
- Combine multiple actions in one step
- Skip error handling
- Use vague language ("the thing updates")
- Forget loading/processing states
- Ignore edge cases (empty, many items)
