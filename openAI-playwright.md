# Problem:

Kubewarden has 27 policies. I want to check they can all be installed in UI. Some policies can be started just by providing policy name, but some of them require extra settings.


I decided to generate installation test for every policy in playwright. There is some basic examples on playwright web, but since I am new to javascript I tried to use chatGPT to give me skeleton of the code. This is the best of 3 attempts, 2 of them went quite wrong.

## Martin: Write playwright test

Sure, I'd be happy to help you write a Playwright test! Here's an example test that navigates to the Google homepage and performs a search for "Playwright":

```javascript
const { test, expect } = require('@playwright/test');

test('search on Google', async ({ page }) => {
  // Navigate to Google homepage
  await page.goto('https://www.google.com/');

  // Type "Playwright" into the search box and press Enter
  await page.fill('input[name="q"]', 'Playwright');
  await page.press('input[name="q"]', 'Enter');

  // Wait for the search results to load
  await page.waitForSelector('#search');

  // Ensure the search results contain the text "Playwright"
  const searchResults = await page.textContent('#search');
  expect(searchResults).toContain('Playwright');
});
```

## Martin: Write a loop in playwright that for every item on the list generates test. List is  Allow Privilege Escalation PSP, Allowed Fs Groups PSP, Allowed Proc Mount Types PSP, Apparmor PSP

Sure, here's an example of how you could write a loop in Playwright to generate a test for each item on the list:

```javascript
const { test } = require('@playwright/test');

const items = [
  'Allow Privilege Escalation PSP',
  'Allowed Fs Groups PSP',
  'Allowed Proc Mount Types PSP',
  'Apparmor PSP',
];

for (const item of items) {
  test(`PSP ${item}`, async ({ page }) => {
    // Navigate to the PSP page
    await page.goto('https://example.com/psp');

    // Click on the PSP item
    await page.click(`#psp-${item}`);

    // Verify that the PSP item is selected
    const isSelected = await page.$eval(`#psp-${item}`, el => el.classList.contains('selected'));
    expect(isSelected).toBe(true);
  });
}
```

This code defines an array of `items` and then loops over each item to generate a test using Playwright's `test` function. Each test navigates to a hypothetical PSP page, clicks on the PSP item corresponding to the current iteration of the loop, and verifies that the item is selected by checking its CSS class.

Note that this code assumes the presence of a hypothetical PSP page with items that can be selected by clicking on them. You may need to modify the code to work with your specific testing environment.

## Martin: Some tests need to perform additional actions

Certainly! Here's an example of how you could modify the previous code to perform additional actions in some tests:

```javascript
const { test } = require('@playwright/test');

const items = [
  { name: 'Allow Privilege Escalation PSP', hasAdditionalAction: false },
  { name: 'Allowed Fs Groups PSP', hasAdditionalAction: true },
  { name: 'Allowed Proc Mount Types PSP', hasAdditionalAction: false },
  { name: 'Apparmor PSP', hasAdditionalAction: true },
];

for (const item of items) {
  test(`PSP ${item.name}`, async ({ page }) => {
    // Navigate to the PSP page
    await page.goto('https://example.com/psp');

    // Click on the PSP item
    await page.click(`#psp-${item.name}`);

    // Verify that the PSP item is selected
    const isSelected = await page.$eval(`#psp-${item.name}`, el => el.classList.contains('selected'));
    expect(isSelected).toBe(true);

    // Perform additional action if necessary
    if (item.hasAdditionalAction) {
      // Click on the "More Info" button
      await page.click(`#psp-${item.name} .more-info`);

      // Verify that the additional info is displayed
      const isInfoDisplayed = await page.isVisible(`#psp-${item.name} .info`);
      expect(isInfoDisplayed).toBe(true);
    }
  });
}
```

In this code, we've modified the `items` array to include a `hasAdditionalAction` property for each item, which indicates whether the test should perform an additional action after clicking on the item. We've also added an `if` statement inside the test to check whether the item has an additional action, and perform it if necessary.

In this example, the additional action is to click on a "More Info" button and verify that additional information is displayed. You can modify this code to perform any additional actions that are necessary for your tests.


## Martin: Move additionalActions to functions

Sure, here's an example of how you could move the additional actions to separate functions:

```javascript
const { test } = require('@playwright/test');

const items = [
  { name: 'Allow Privilege Escalation PSP', hasAdditionalAction: false },
  { name: 'Allowed Fs Groups PSP', hasAdditionalAction: true },
  { name: 'Allowed Proc Mount Types PSP', hasAdditionalAction: false },
  { name: 'Apparmor PSP', hasAdditionalAction: true },
];

async function clickPSPItem(page, itemName) {
  // Click on the PSP item
  await page.click(`#psp-${itemName}`);

  // Verify that the PSP item is selected
  const isSelected = await page.$eval(`#psp-${itemName}`, el => el.classList.contains('selected'));
  expect(isSelected).toBe(true);
}

async function clickMoreInfo(page, itemName) {
  // Click on the "More Info" button
  await page.click(`#psp-${itemName} .more-info`);

  // Verify that the additional info is displayed
  const isInfoDisplayed = await page.isVisible(`#psp-${itemName} .info`);
  expect(isInfoDisplayed).toBe(true);
}

for (const item of items) {
  test(`PSP ${item.name}`, async ({ page }) => {
    // Navigate to the PSP page
    await page.goto('https://example.com/psp');

    // Click on the PSP item
    await clickPSPItem(page, item.name);

    // Perform additional action if necessary
    if (item.hasAdditionalAction) {
      await clickMoreInfo(page, item.name);
    }
  });
}
```

In this modified code, we've extracted the clicking of the PSP item and the clicking of the "More Info" button into separate functions: `clickPSPItem` and `clickMoreInfo`, respectively.

We've also modified the test loop to call these functions instead of inline code. This makes the code more modular and easier to read.

## Martin: Change hasAdditionalAction to the function name that would be called

Sure, here's an example of how you could change the `hasAdditionalAction` property to the name of the function that should be called:

```javascript
const { test } = require('@playwright/test');

const items = [
  { name: 'Allow Privilege Escalation PSP', action: null },
  { name: 'Allowed Fs Groups PSP', action: clickMoreInfo },
  { name: 'Allowed Proc Mount Types PSP', action: null },
  { name: 'Apparmor PSP', action: clickMoreInfo },
];

async function clickPSPItem(page, itemName) {
  // Click on the PSP item
  await page.click(`#psp-${itemName}`);

  // Verify that the PSP item is selected
  const isSelected = await page.$eval(`#psp-${itemName}`, el => el.classList.contains('selected'));
  expect(isSelected).toBe(true);
}

async function clickMoreInfo(page, itemName) {
  // Click on the "More Info" button
  await page.click(`#psp-${itemName} .more-info`);

  // Verify that the additional info is displayed
  const isInfoDisplayed = await page.isVisible(`#psp-${itemName} .info`);
  expect(isInfoDisplayed).toBe(true);
}

for (const item of items) {
  test(`PSP ${item.name}`, async ({ page }) => {
    // Navigate to the PSP page
    await page.goto('https://example.com/psp');

    // Click on the PSP item
    await clickPSPItem(page, item.name);

    // Perform additional action if necessary
    if (item.action) {
      await item.action(page, item.name);
    }
  });
}
```

In this modified code, we've changed the `hasAdditionalAction` property to `action`, which now contains the function that should be called if an additional action is required.

We've also modified the test loop to check whether `item.action` is truthy before calling it. If `item.action` is truthy, we call it with the `page` object and the PSP item name.
