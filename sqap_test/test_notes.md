# General
1. Maintenance manual and requirements.txt are missing python version number. 
2. create a conda enviornment rather than python enviornment as maineteneace manual suggests. 
3. **QA** for all test cases is Tony L.

## Test execution notes

Source: [Project test cases](./test_cases.md)  
Transfer results to: [Excel testing documentation](./Spence%20Space%20QA%20Testing%20Documentation.xlsx)

This document combines tester-recorded observations with source-based predictions for all 38 cases.
Predictions are educated estimates, not executed test results. Existing tester results and observations are preserved.
Blank PASS/FAIL fields remain unexecuted or unrecorded; see each case's comments for its separate prediction.
Use disposable test accounts and synthetic data; do not record passwords, invitation codes, or personal receipt information.

### Source-review scope

- Reviewed on 09Nov2026 against local source at revision `489e000`, including the working-tree settings changes.
- Scope is the Django application served by `runserver`, not standalone HTML files opened in a browser. Root-level HTML mockups are not automatically Django routes.
- Predictions assume database migrations have been applied and valid test data is available. Source review cannot verify the running server, database contents, camera behavior, or browser storage.
- Predicted outcomes: **4 PASS, 32 FAIL, 2 BLOCKED**. These are not measured execution totals.
- BLOCKED means prerequisites are unavailable; it does not establish that household data is exposed or correctly isolated.

### Recording and Excel transfer

- Match each entry to Excel using its **Test ID**.
- Copy **QA Name**, **Date of Test**, **PASS/FAIL**, and **Resolved?** into the corresponding Excel fields.
- Copy actual results, comments, evidence links, and defect links into **Additional Comments**.
- Keep source-based predictions labeled as such when transferring comments to Excel; do not copy predictions into actual PASS/FAIL or execution-date fields.
- Leave PASS/FAIL blank if a case is not run or is blocked; explain the blocker in comments.
- Leave Resolved? blank unless a defect exists; record resolution only after retesting.
- Record environment and test data for each run. For retests, duplicate the case entry rather than overwriting the original observations.
- If no expected implementation or supported scan sample is available, document that limitation rather than assuming an outcome.

## Prototype 1

### P1-AC-01 - Create an account

**Test ID:** P1-AC-01  
**Date of Test:**  09Nov2026
**PASS/FAIL:**  PASS
**Resolved?:** 

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An unused test email and a password meeting the displayed requirements.

**Steps:**
1. Open account registration.
2. Enter the test email, password, and any required confirmation fields.
3. Submit the form.

**Expected result:** The account is created successfully.

**Actual result:**
- after creating account with a more complex password, account was created on second try.

**Additional Comments / annotations:**
- first submission with password == 'password'. recieved error message "the password is too common"
- must try this account creation with a fake email; check for character sanitization for fields like name, household name; why to test for sql injects or character overflows to access website with out account creation. 
- **Source-based prediction (not executed): PASS (high confidence), with required setup.** [SignUpForm](../accounts/forms.py) extends Django's UserCreationForm and requires a username, email, password confirmation, and either household creation details or a valid invitation code. [signup_view](../accounts/views.py) saves the user and household profile, logs the user in, and redirects to the dashboard. This supports your recorded successful registration, but email and password alone are not all the required inputs. Email ownership verification is not implemented in this signup flow.

**Evidence (screenshots/logs):**
<p align="center">
    <img src="/scrnshts/p1-ac-01-a.png"
            alt="Spence Space account registration screen captured during the account-creation test; the screenshot documents the registration flow within the website."
            width="600">
    <br>
    <em>Figure 1: Account Creation test P1-AUTH-01.</em> 
    <img src="/scrnshts/p1-ac-01-b_fail.png"
        alt="fail on insufficent password."
        width="600">
    <br>
    <em>Figure 2: Account fail - not complex password</em> 
    <img src="/scrnshts/p1-ac-01-c.png"
        width="600">
    <br>
    <em>Figure 3: Account Creation 2 - complex password</em> 
    <img src="/scrnshts/p1-ac-01-b_success.png"
        width="600">
    <br>
    <em>Figure 4: Account creation success </em> 
</p>

**Defect issue link:**

**Retest date / observations:**

--- 

### P1-AUTH-01 - Log in to an existing account

**Test ID:** P1-AUTH-01  
**Date of Test:**  09Nov2026
**PASS/FAIL:**  PASS
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A registered test account; user is signed out.

**Steps:**
1. Open the login page.
2. Enter the existing account's email and password.
3. Submit the form.

**Expected result:** Login succeeds and takes the user to the Spence Space homepage.

**Actual result:**

**Additional Comments / annotations:**
- Login succesful, however there is no links of menu to navigate away from dashboard.
- **Source-based prediction (not executed): FAIL for the full stated email-login/homepage requirement (high confidence).** [FridgeLoginView](../accounts/views.py) uses Django's LoginView without a custom email authentication form, and [settings](../spence_space/settings.py) has no custom authentication backend. The [login label](../accounts/templates/accounts/login.html) says Email / Username / Phone, but authentication is by username; an email works as a username only if that exact value was chosen as the username. LOGIN_REDIRECT_URL points to the household dashboard, and [home_view](../accounts/views.py) also redirects there; the [dashboard](../accounts/templates/accounts/dashboard.html) lacks the main application navigation. Your recorded PASS is preserved: confirm which identifier was used and whether the dashboard satisfies the intended homepage requirement before finalizing the full case.

**Evidence (screenshots/logs):**
<p align="center">
    <img src="/scrnshts/homepage_404.png"
            width="600">
    <br>
    <em>Figure 1: Failed Homepage from Acct Dashboard Navigation</em> 
</p>

**Defect issue link:**
- there is no link or dropdown on account dashboard to navigate to main page where I can select my fridges, discover, grocery etc... 
- in order for me to get access to spencespace main page, I had to append /discover at the end of url http://127.0.0.1:8000 

**Retest date / observations:**

--- 

### P1-NAV-01 - Navigate to My Fridges and back

**Test ID:** P1-NAV-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  FAIL
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in and on the homepage.

**Steps:**
1. Select the My Fridges navigation link.
2. Check the destination.
3. Use the application's navigation to return to the homepage.

**Expected result:** My Fridges opens, and the user can return to the homepage.

**Actual result:**
- Page Not Found (404)

**Additional Comments / annotations:**
- From previous P1-AUTH-01 comment I was unable to navigate to homepage via account dashboard. I have to type in url. see previouis test case comment. 
- **Source-based prediction (not executed): FAIL (high confidence).** The [Discover navigation](../recipes/templates/recipes/discover.html) links My Fridges to `fridge.html`, while [root routes](../spence_space/urls.py) and [account routes](../accounts/urls.py) define no fridge page. From `/discover/`, this relative link targets `/discover/fridge.html`, which has no route. The logo's `index.html` return link is also unrouted. This agrees with your recorded 404.

**Evidence (screenshots/logs):**
<p align="center">
    <img src="/scrnshts/myfrdige_404.png"
            width="600">
    <br>
    <em>Figure 1: Failed MyFridge Navigation</em> 
</p>

**Defect issue link:**

**Retest date / observations:**

--- 

### P1-NAV-02 - Navigate to Discover and back

**Test ID:** P1-NAV-02  
**Date of Test:**  
**PASS/FAIL:**  FAILED
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in and on the homepage.

**Steps:**
1. Select the Discover navigation link.
2. Check the destination.
3. Use the application's navigation to return to the homepage.

**Expected result:** Discover opens, and the user can return to the homepage.

**Actual result:**
- Page Not Found (404)

**Additional Comments / annotations:**
- record the clicked link, resulting URL, and observed 404 under P1-NAV-01 through P1-NAV-04, as applicable. A working direct URL does not make the broken navigation test pass.
- prototype is in debug mode as well; ensure debug is set to false prior to production. 
- Discover exists at /discover/, but the navigation uses href="discover.html". Django has no matching .html route. 
- My Fridges, Grocery List, and My Recipes have no registered Django routes in the current URL configuration. Changing their links alone won’t implement those pages.
- **Source-based prediction (not executed): FAIL (high confidence).** [Root routes](../spence_space/urls.py) expose `/discover/`, but the [Discover template](../recipes/templates/recipes/discover.html) links to relative `discover.html` and `index.html`, neither of which has a matching route. Opening `/discover/` directly bypasses the broken link and does not satisfy navigation and return-home steps. This agrees with your recorded failure.

**Evidence (screenshots/logs):**
<p align="center">
    <img src="/scrnshts/discover_404.png"
            width="600">
    <br>
    <em>Figure 1: Failed Discover Navigation</em> 
</p>

**Defect issue link:**

**Retest date / observations:**

### P1-NAV-03 - Navigate to Grocery List and back

**Test ID:** P1-NAV-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in and on the homepage.

**Steps:**
1. Select the Grocery List navigation link.
2. Check the destination.
3. Use the application's navigation to return to the homepage.

**Expected result:** Grocery List opens, and the user can return to the homepage.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [Discover navigation](../recipes/templates/recipes/discover.html) targets `grocery.html`, but [root routes](../spence_space/urls.py) and [account routes](../accounts/urls.py) contain no grocery-list endpoint. The household dashboard also has no grocery-list link. Expect an unrouted URL rather than a working list and return-home workflow.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-NAV-04 - Navigate to My Recipes and back

**Test ID:** P1-NAV-04  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in and on the homepage.

**Steps:**
1. Select the My Recipes navigation link.
2. Check the destination.
3. Use the application's navigation to return to the homepage.

**Expected result:** My Recipes opens, and the user can return to the homepage.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [Discover navigation](../recipes/templates/recipes/discover.html) targets `recipes.html`, but [recipe routes](../recipes/urls.py) expose only Discover and no My Recipes page. The return-home `index.html` link is also unrouted.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-HH-01 - Create a household

**Test ID:** P1-HH-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A signed-in test account eligible to create a household; a synthetic household name.

**Steps:**
1. Open household creation.
2. Enter the required household information.
3. Submit and inspect the resulting household.

**Expected result:** A household is created.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): PASS (high confidence), through registration.** [SignUpForm](../accounts/forms.py) provides Create a new household and validates its name. [signup_view](../accounts/views.py) creates the Household with the new user as owner and an owner Profile; the [model](../accounts/models.py) generates an invitation code on save. Use a new account during signup: there is no separate household-creation route for an already signed-in account. If post-signup creation is required, that workflow is not covered by this predicted pass.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-HH-02 - Access household information as owner

**Test ID:** P1-HH-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An existing test household and its owner account.

**Steps:**
1. Sign in as the household owner.
2. Open the household information.
3. Compare it with the household created for this test.

**Expected result:** The owner can access the household information.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): PASS (high confidence) for the implemented household information.** The login-protected [dashboard_view](../accounts/views.py) retrieves the signed-in user's household, members, and owner role. The [dashboard template](../accounts/templates/accounts/dashboard.html) displays the household name and members and shows the invitation code to its owner. Confirm these values against the created household; inventory and grocery data are not displayed here.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-HH-03 - Join a household using an invitation code

**Test ID:** P1-HH-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An existing household, its valid invitation code, and a second eligible test account.

**Steps:**
1. Sign in as the second test account.
2. Open the join-household workflow.
3. Enter the invitation code and submit.
4. Inspect the resulting household membership.

**Expected result:** The user joins the existing household.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): PASS (high confidence), through registration.** [SignUpForm](../accounts/forms.py) validates a supplied invitation code after uppercasing it. [signup_view](../accounts/views.py) retrieves the matching household and creates a member Profile for the new user. The [dashboard](../accounts/templates/accounts/dashboard.html) lists members. Use a new second account: there is no separate join-household endpoint for an existing account.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-FAV-01 - Save a recipe to favorites

**Test ID:** P1-FAV-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in; an available recipe that is not already saved. Record its name or ID:

**Steps:**
1. Open the selected recipe.
2. Save it to favorites.
3. Open My Recipes and locate the recipe.

**Expected result:** The saved recipe appears in My Recipes.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL end-to-end (high confidence), with partial save behavior present.** The [Discover script](../recipes/templates/recipes/discover.html) intercepts the heart form, toggles a recipe in browser localStorage under `spenceSavedRecipes`, and updates the button. However, [recipe routes](../recipes/urls.py) contain no My Recipes page and the [models](../recipes/models.py) have no user-favorite relationship. A changed heart or localStorage entry alone does not satisfy appearance in My Recipes.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-FAV-02 - Remove a recipe from favorites

**Test ID:** P1-FAV-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in; a saved recipe. Record its name or ID:

**Steps:**
1. Open the saved recipe or its favorites entry.
2. Remove it from favorites.
3. Inspect the favorites list.

**Expected result:** The recipe no longer appears in the favorites list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL end-to-end (high confidence), with partial unsave behavior present.** The [Discover script](../recipes/templates/recipes/discover.html) removes an existing recipe from localStorage when its heart is clicked again. There is no routed favorites/My Recipes list in [recipe routes](../recipes/urls.py), so list removal cannot be verified through the required page. If a saved-list prerequisite cannot be established, record this execution as blocked rather than inventing an observation.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-FAV-03 - Preserve favorites across login sessions

**Test ID:** P1-FAV-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A test account with access to available recipes.

**Steps:**
1. Save a recipe and record the complete current favorites list.
2. Log out.
3. Log back in with the same account.
4. Open favorites and compare with the recorded list.

**Expected result:** The saved favorites remain unchanged after logging out and back in.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL end-to-end (high confidence), with possible same-browser persistence.** The [Discover script](../recipes/templates/recipes/discover.html) uses a single localStorage key without an account identifier. The [logout view](../accounts/views.py) does not explicitly clear that browser key, so the heart state is expected to survive logout in the same browser and origin. This is browser-local storage, not evidence of account-specific saved favorites, and the required favorites list has no [route](../recipes/urls.py). Verify persistence and account ownership separately; neither has been executed here.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-DISC-01 - View available recipes

**Test ID:** P1-DISC-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Access to Discover; recipes available in the tested environment. Record a known available recipe:

**Steps:**
1. Open Discover.
2. Inspect the available recipes.
3. Check that the known available recipe is displayed.

**Expected result:** The current available recipes are displayed.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL for a current-data recipe listing (medium confidence); a visual-only check may pass.** [discover](../recipes/views.py) queries Recipe objects and passes `recipes` into the template, but the [template](../recipes/templates/recipes/discover.html) renders fixed sample cards rather than looping over that context. Sample recipes should appear at `/discover/`, but database additions or removals will not change those cards. Compare a known database recipe with the displayed list before deciding the execution result; clarify whether static prototype samples satisfy this requirement.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P1-DISC-02 - View recipe details and ingredients

**Test ID:** P1-DISC-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An available recipe in Discover. Record its name or ID:

**Steps:**
1. Open Discover.
2. Select the recipe.
3. Inspect its details and required ingredients.

**Expected result:** The selected recipe's details and required ingredients are displayed.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** Recipe cards in the [Discover template](../recipes/templates/recipes/discover.html) all link to `recipe-details.html`; [recipe routes](../recipes/urls.py) define no details endpoint. [Recipe and RecipeIngredient models](../recipes/models.py) hold detail data, but no details view/template is wired to display it. Expect the card link to reach an unrouted URL.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

## Prototype 2

### P2-HH-01 - View shared household inventory

**Test ID:** P2-HH-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Two test accounts in the same household; a known item in its shared inventory.

**Steps:**
1. Sign in as the first member and note the inventory item.
2. Sign in as the second member using a separate session.
3. Open the same household's inventory.
4. Locate and compare the known item.

**Expected result:** Members of the same household can view the shared inventory.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at inventory setup during execution.** [Household/Profile models](../accounts/models.py) associate members with a household but define no inventory data. The [dashboard](../accounts/templates/accounts/dashboard.html) shows household membership only, and [root routes](../spence_space/urls.py) expose no inventory workflow. The standalone [stock page](../fridge-stock.html) contains fixed sample items, not household-backed inventory.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-HH-02 - View the shared household grocery list

**Test ID:** P2-HH-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Two test accounts in the same household; a known grocery-list item.

**Steps:**
1. Sign in as the first member and note the grocery-list item.
2. Sign in as the second member using a separate session.
3. Open the household grocery list.
4. Locate and compare the known item.

**Expected result:** Members of the same household can view the shared grocery list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at list setup during execution.** [Household/Profile models](../accounts/models.py) contain membership but no grocery-list records, and [root routes](../spence_space/urls.py) expose no grocery-list workflow. Sharing household membership does not establish a working shared list.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-HH-03 - Keep inventory isolated between households

**Test ID:** P2-HH-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Two tester-controlled households, A and B, with separate members and distinctive inventory items.

**Steps:**
1. As a member of household A, record its inventory page address and distinctive item.
2. In a separate session, sign in as a member of household B.
3. Inspect B's inventory for A's item.
4. Attempt to open A's recorded inventory page in B's session.

**Expected result:** The household B member cannot view household A's inventory through the list or direct page access.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): BLOCKED (high confidence).** There is no household inventory model in [account models](../accounts/models.py) or [recipe models](../recipes/models.py), and no inventory endpoint in [root routes](../spence_space/urls.py). Two distinct household inventories cannot be established for this test. A 404 for every user is not proof of correct household isolation; no access-control pass or data-exposure failure is inferred.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-HH-04 - Keep grocery lists isolated between households

**Test ID:** P2-HH-04  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Two tester-controlled households, A and B, with separate members and distinctive grocery-list items.

**Steps:**
1. As a member of household A, record its grocery-list page address and distinctive item.
2. In a separate session, sign in as a member of household B.
3. Inspect B's grocery list for A's item.
4. Attempt to open A's recorded grocery-list page in B's session.

**Expected result:** The household B member cannot view household A's grocery list through the list or direct page access.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): BLOCKED (high confidence).** The [account models](../accounts/models.py) contain no grocery-list data, and [root routes](../spence_space/urls.py) expose no grocery-list endpoint. Distinct household lists cannot be created for the comparison. A missing page does not verify isolation; repeat this test when a functioning household-backed list exists.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-INV-01 - Add a food item to inventory

**Test ID:** P2-INV-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Signed in with access to a test inventory. Record item name and required field values:

**Steps:**
1. Open the add-inventory-item workflow.
2. Enter the test item and required information.
3. Save and inspect the inventory list.

**Expected result:** The added item appears in the inventory list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The standalone [manual ingredient form](../manual-ingredients.html) contains inputs but no POST action or save script. [Root routes](../spence_space/urls.py) expose no inventory-add endpoint, and the [stock page](../fridge-stock.html) is fixed sample HTML. There is no wired path from submitting an item to a persisted inventory entry.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-INV-02 - Modify an inventory item

**Test ID:** P2-INV-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An existing test inventory item. Record original and replacement field values:

**Steps:**
1. Open the item's edit workflow.
2. Change its editable information.
3. Save.
4. Inspect the item's information.

**Expected result:** The item displays the updated information.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked if no real item can be created.** The [stock page](../fridge-stock.html) contains fixed rows and no edit controls. The [account views](../accounts/views.py) and [recipe views](../recipes/views.py) implement no inventory-update handler. Editing a form field in a mockup would not establish a saved update.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-INV-03 - Remove an inventory item

**Test ID:** P2-INV-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A disposable item in the test inventory. Record its name or ID:

**Steps:**
1. Locate the item in inventory.
2. Remove it and confirm if prompted.
3. Inspect the inventory list.

**Expected result:** The item no longer appears in the inventory list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at item setup.** The [stock page](../fridge-stock.html) has fixed rows with no remove operation, and [root routes](../spence_space/urls.py) expose no inventory-delete workflow. There is no implemented user action that deletes a persisted inventory item.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-INV-04 - Preserve inventory changes after refresh

**Test ID:** P2-INV-04  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A disposable inventory with items available for editing and removal.

**Steps:**
1. Add an item, save, refresh, and inspect its presence.
2. Edit an item, save, refresh, and inspect the updated values.
3. Remove an item, refresh, and check that it remains absent.
4. Record the outcome of each action separately in comments.

**Expected result:** Added, edited, and removed inventory data remain saved after refresh.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), with persistence checks blocked by missing CRUD.** The [manual form](../manual-ingredients.html) does not save to a backend or browser store; the [stock page](../fridge-stock.html) is hardcoded. No inventory persistence model or routed CRUD handler is implemented. Seeing the same sample rows after refresh is not proof that user changes were saved.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-INV-05 - Preserve inventory changes after leaving the page

**Test ID:** P2-INV-05  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A disposable inventory with items available for editing and removal.

**Steps:**
1. Add an item, save, navigate away, return, and inspect its presence.
2. Edit an item, save, navigate away, return, and inspect the updated values.
3. Remove an item, navigate away, return, and check that it remains absent.
4. Record the outcome of each action separately in comments.

**Expected result:** Added, edited, and removed inventory data remain saved after revisiting the page.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), with persistence checks blocked by missing CRUD.** [Root routes](../spence_space/urls.py) do not expose inventory management, and the standalone [manual form](../manual-ingredients.html) and [stock page](../fridge-stock.html) do not persist user changes. Returning to unchanged sample content does not satisfy persistence.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-BAR-01 - Recognize a supported food barcode

**Test ID:** P2-BAR-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS, camera/device:

**Preconditions / test data:** A project-supported food barcode, its reference product information, and camera access if required. Record the sample and support reference:

**Steps:**
1. Open the barcode scanner.
2. Provide the required camera access or supported input.
3. Scan the test barcode.
4. Compare the identified item with the reference product.

**Expected result:** The scanned item information is correct.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [barcode mockup](../scan-barcode.html) has a placeholder scanner area and a `type="button"` Scan Barcode button, but no camera/decoder script or lookup handler. [Root routes](../spence_space/urls.py) expose no barcode endpoint. Recognition cannot occur through the supplied workflow.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-BAR-02 - Add a barcode-scanned item to inventory

**Test ID:** P2-BAR-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS, camera/device:

**Preconditions / test data:** A supported food barcode; access to a test inventory. Record the expected product:

**Steps:**
1. Scan the supported barcode.
2. Use the scanned information in the add-inventory workflow.
3. Complete any required fields and save.
4. Inspect the inventory entry.

**Expected result:** Scanned food information can be used to add the corresponding item to inventory.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked by barcode recognition.** The [barcode mockup](../scan-barcode.html) has manually editable fields but no scan-to-field integration, POST save action, or save script. No corresponding scanner or inventory endpoint is registered in [root routes](../spence_space/urls.py).

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-01 - Add a grocery item with quantity

**Test ID:** P2-GROC-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Access to a test grocery list; an item name not already listed and a valid quantity.

**Steps:**
1. Open the grocery list.
2. Add the test item with the selected quantity.
3. Inspect the resulting entry.

**Expected result:** The item and its quantity appear on the list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [navigation](../recipes/templates/recipes/discover.html) points to `grocery.html`, but [root routes](../spence_space/urls.py) expose no such page. Neither [account models](../accounts/models.py) nor [recipe models](../recipes/models.py) defines grocery items, and the supplied views contain no add-list handler.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-02 - Combine duplicate items regardless of capitalization

**Test ID:** P2-GROC-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A test grocery list without milk; use the same unit for both additions if units are required.

**Steps:**
1. Add "Milk" with quantity 2.
2. Add "milk" with quantity 3.
3. Inspect the resulting entries and total quantity.

**Expected result:** One combined milk entry has quantity 5, regardless of capitalization.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked by the missing add-item workflow.** [Account views](../accounts/views.py) and [recipe views](../recipes/views.py) contain no grocery-item insertion, case normalization, duplicate merge, or quantity aggregation. The required list itself is not [routed](../spence_space/urls.py). The expected quantity 5 is a test target, not an observed result.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-03 - Change a grocery item quantity

**Test ID:** P2-GROC-03  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A grocery item with a known quantity. Record original and replacement quantities:

**Steps:**
1. Open the grocery list.
2. Change the item's quantity and save if required.
3. Inspect the entry.

**Expected result:** The item displays the updated quantity.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at grocery-item setup.** No grocery-list page or quantity-update endpoint is defined in [root routes](../spence_space/urls.py), [account views](../accounts/views.py), or [recipe views](../recipes/views.py). No saved grocery-item quantity is available to edit.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-04 - Remove a grocery item

**Test ID:** P2-GROC-04  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A disposable item in the grocery list. Record its name:

**Steps:**
1. Locate the item.
2. Remove it and confirm if prompted.
3. Inspect the grocery list.

**Expected result:** The item is removed from the list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at grocery-item setup.** The [root routes](../spence_space/urls.py) expose no grocery-list or remove-item workflow, and the supplied [account](../accounts/models.py) and [recipe](../recipes/models.py) models have no grocery-item records to delete.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-05 - Mark a grocery item as bought

**Test ID:** P2-GROC-05  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An item on the active grocery list. Record its name:

**Steps:**
1. Mark the item as bought.
2. Open or inspect the previously bought section.
3. Locate the item.

**Expected result:** The item appears in the previously bought section.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at grocery-item setup.** There is no grocery purchase-state field in the supplied [account](../accounts/models.py) or [recipe](../recipes/models.py) models and no bought-item handler or previously bought page in [root routes](../spence_space/urls.py). This transition is not implemented.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-06 - Add a previously bought item again

**Test ID:** P2-GROC-06  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** An item in the previously bought section. Record its name:

**Steps:**
1. Open the previously bought section.
2. Select Add Again for the item.
3. Inspect the active grocery list.

**Expected result:** The item returns to the grocery list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked because no previously bought entry can be established.** [Root routes](../spence_space/urls.py), [account views](../accounts/views.py), and [recipe views](../recipes/views.py) contain no previously bought list or Add Again operation.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-GROC-07 - Share grocery-list changes between members

**Test ID:** P2-GROC-07  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Two test accounts in the same household using separate sessions; a disposable grocery item.

**Steps:**
1. As member A, add the item with a known quantity.
2. As member B, open or refresh the grocery list and compare the item and quantity.
3. As member B, update the quantity.
4. As member A, open or refresh the list and compare the updated quantity.

**Expected result:** Both household members can view the same grocery list and saved changes.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked by the missing grocery list.** [Profile](../accounts/models.py) links users to households, but no household-backed grocery records or update views exist. The [dashboard](../accounts/templates/accounts/dashboard.html) displays members rather than grocery data. Membership alone does not implement sharing or synchronization.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-SEARCH-01 - Search recipes by keyword

**Test ID:** P2-SEARCH-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Recipes available for search; a keyword with a known matching recipe. Record both:

**Steps:**
1. Open recipe search.
2. Enter the keyword and submit.
3. Inspect the results for the known matching recipe.

**Expected result:** Recipes matching the keyword are returned.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [search form](../recipes/templates/recipes/discover.html) submits a `search` query parameter, but [discover](../recipes/views.py) ignores request parameters and queries all recipes. The template renders fixed cards instead of query results. A matching sample card being visible by coincidence is not proof that filtering worked; compare contrasting keywords.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-SEARCH-02 - Search recipes using available ingredients

**Test ID:** P2-SEARCH-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Available test ingredients and a known matching recipe; record the expected match and the project's matching rule if documented:

**Steps:**
1. Open recipe search.
2. Use the application's supported ingredient-search controls with the available ingredients.
3. Submit the search.
4. Inspect the returned recipes and their ingredients.

**Expected result:** Recipes matching the available ingredients are returned.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [Discover view](../recipes/views.py) has no ingredient filtering or household-inventory lookup. Its [template](../recipes/templates/recipes/discover.html) hardcodes the Recipes You Can Make cards and ingredient ratios. The [match_recipes management command](../recipes/management/commands/match_recipes.py) searches an external service by recipe name for import support; it is not an ingredient-search web workflow.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-PER-01 - Add a food item with expiration information

**Test ID:** P2-PER-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** Access to test inventory; a food item and valid expiration information. Record the values:

**Steps:**
1. Open the add-item workflow.
2. Enter the food item, expiration information, and other required fields.
3. Save.
4. Reopen or inspect the saved item's expiration information.

**Expected result:** The item's expiration information is saved.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [manual form](../manual-ingredients.html) contains an expiration-date input, but no save handler is connected. The [stock page](../fridge-stock.html) displays literal sample dates, and the supplied [account](../accounts/models.py) and [recipe](../recipes/models.py) models do not store inventory-item expiration dates. A date input accepting a value does not verify persistence.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-PER-02 - Update expiration information

**Test ID:** P2-PER-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS:

**Preconditions / test data:** A test item with expiration information. Record original and replacement values:

**Steps:**
1. Open the item's edit workflow.
2. Change its expiration information.
3. Save.
4. Reopen or inspect the saved item.

**Expected result:** The updated expiration information is saved.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked at item setup.** The [stock page](../fridge-stock.html) has no edit-expiration control; [root routes](../spence_space/urls.py) expose no inventory-update endpoint. The expiration input in the [manual mockup](../manual-ingredients.html) is not connected to persisted inventory data.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-RCPT-01 - Recognize food items from a receipt

**Test ID:** P2-RCPT-01  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS, camera/device if used:

**Preconditions / test data:** A project-supported receipt sample without personal information; a reference list of its food items. Record sample location and support reference:

**Steps:**
1. Open the receipt scanner.
2. Provide the supported receipt using the application's supported input method.
3. Run recognition.
4. Compare the identified food items with the reference list.
5. Note missing or incorrectly recognized items.

**Expected result:** The scanned food items are identified and recognized.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence).** The [receipt upload mockup](../scan-receipt.html) uses a GET form targeting `verify-receipt.html`, with no file-upload/OCR backend or recognition script. The [verification page](../verify-receipt.html) always contains fixed Milk, Eggs, and Bread inputs. Those samples are not recognition output, and neither page is registered in [root routes](../spence_space/urls.py).

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**

### P2-RCPT-02 - Add receipt-scanned items to inventory

**Test ID:** P2-RCPT-02  
**QA Name:**  
**Date of Test:**  
**PASS/FAIL:**  
**Resolved?:**

**Environment:** Commit/build, application URL, browser/version, OS, camera/device if used:

**Preconditions / test data:** A supported receipt sample without personal information; access to test inventory. Record the food items to add:

**Steps:**
1. Scan the supported receipt.
2. Review and select recognized food items for inventory.
3. Complete required fields and save using the available workflow.
4. Open inventory and compare the saved entries with the selected items.

**Expected result:** Scanned food items can be added to the inventory list.

**Actual result:**

**Additional Comments / annotations:**

- **Source-based prediction (not executed): FAIL (high confidence), or blocked by receipt recognition.** The [verification form](../verify-receipt.html) has editable sample rows and an Add All to Fridge submit button, but no POST action, save script, or inventory handler. [Root routes](../spence_space/urls.py) provide no receipt-to-inventory endpoint. Editing sample rows does not establish that receipt items were recognized or saved.

**Evidence (screenshots/logs):**

**Defect issue link:**

**Retest date / observations:**