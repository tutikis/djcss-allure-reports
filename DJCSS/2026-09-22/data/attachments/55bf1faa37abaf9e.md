# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: mct-form\mct-form-submission.spec.ts >> MCT Form Submission >> Analog >> DJCSS-T128: Verify Unrestricted Upload of File for MyCompanyTodayForm - Analog
- Location: tests\mct-form\mct-form-submission.spec.ts:227:13

# Error details

```
Error: expect(locator).toBeVisible() failed

Locator: getByText('An error occurred. Please try again.')
Expected: visible
Timeout: 15000ms
Error: element(s) not found

Call log:
  - Expect "toBeVisible" with timeout 15000ms
  - waiting for getByText('An error occurred. Please try again.')

```

```yaml
- link "Skip to Main Content":
  - /url: "#content"
- link "DJCSS Logo":
  - /url: /
  - img "DJCSS Logo"
- main:
  - 'heading "Article Submission: Analog Devices" [level=1]'
  - paragraph: Please submit by the agreed time for inclusion in the upcoming edition. If submitted later, the article will be included in the following edition.
  - text: Your Name*
  - textbox "Your Name*": Automated Test User
  - text: Your Email Address
  - textbox "Your Email Address":
    - /placeholder: If you want a copy of your submission
  - text: Headline Title*
  - textbox "Headline Title*":
    - /placeholder: Full title please.
    - text: Automated Test Headline
  - text: Publication Name*
  - textbox "Publication Name*": Automated Test Publication
  - text: Publication Date*
  - textbox "Publication Date*": 2026-08-01
  - text: URL to Link to
  - textbox "URL to Link to"
  - text: Upload File
  - button "Upload File"
  - text: 1 of 3 files selected
  - list:
    - listitem:
      - text: analog-malware.exe
      - button "Remove analog-malware.exe":
        - img "Remove"
  - text: Any Comments?
  - textbox "Any Comments?"
  - text: By submitting this form you confirm your organization has secured all necessary rights to distribute to its employees the content referenced herein via the Dow Jones service.*
  - checkbox "By submitting this form you confirm your organization has secured all necessary rights to distribute to its employees the content referenced herein via the Dow Jones service.*" [checked]
  - button "audio-loading" [disabled]:
    - img "audio-loading"
- paragraph: © 2026 Dow Jones
- list:
  - listitem:
    - link "Terms of Use":
      - /url: https://global.factiva.com/factivalogin/tou/default.aspx?fcpil=en
  - listitem:
    - link "Privacy Policy":
      - /url: https://global.factiva.com/factivalogin/privacypolicy/default.aspx?fcpil=en
  - listitem:
    - link "Cookie Policy":
      - /url: https://www.dowjones.com/cookies-policy
```

# Test source

```ts
  151 |         });
  152 | 
  153 |         await test.step('Leave Your Name and Headline Title empty, fill all other fields', async () => {
  154 |           await mctFormPage.fillForm({
  155 |             publicationName: 'Automated Test Publication',
  156 |             publicationDate: '2026-08-01',
  157 |             urlToLinkTo: 'https://example.com/article',
  158 |             comments: 'Automated test comment',
  159 |             optIn: true,
  160 |           });
  161 |         });
  162 | 
  163 |         await test.step('Click Submit', async () => {
  164 |           await mctFormPage.clickSubmit();
  165 |         });
  166 | 
  167 |         await test.step('Verify inline error messages appear for Your Name and Headline Title', async () => {
  168 |           const errors = await mctFormPage.getInlineErrorMessages();
  169 |           expect(errors.length).toBeGreaterThanOrEqual(2);
  170 |           await expect(mctFormPage.successMessage).not.toBeVisible();
  171 |         });
  172 |       });
  173 | 
  174 |       test(`${company.keys.mandatoryOnlySubmit}: Verify submission with only the mandatory fields filled, leaving all optional fields blank - ${company.displayName}`, async ({
  175 |         mctFormPage,
  176 |       }) => {
  177 |         await test.step('Open the New MCT Article Submission Form', async () => {
  178 |           await mctFormPage.openForCompany(company.slug);
  179 |         });
  180 | 
  181 |         await test.step('Fill in Your Name, Headline, Publication, Date, and Opt-In only', async () => {
  182 |           await mctFormPage.fillForm({
  183 |             yourName: 'Automated Test User',
  184 |             headlineTitle: 'Automated Test Headline',
  185 |             publicationName: 'Automated Test Publication',
  186 |             publicationDate: '2026-08-01',
  187 |             optIn: true,
  188 |           });
  189 |         });
  190 | 
  191 |         await test.step('Click Submit and verify success message', async () => {
  192 |           await mctFormPage.clickSubmit();
  193 |           await expect(mctFormPage.successMessage).toBeVisible({ timeout: 15000 });
  194 |         });
  195 |       });
  196 | 
  197 |       test(`${company.keys.exactlyThreeFiles}: Verify the form successfully accepts exactly 3 uploaded files - ${company.displayName}`, async ({
  198 |         mctFormPage,
  199 |       }) => {
  200 |         await test.step('Open the New MCT Article Submission Form', async () => {
  201 |           await mctFormPage.openForCompany(company.slug);
  202 |         });
  203 | 
  204 |         await test.step('Fill in all mandatory fields', async () => {
  205 |           await mctFormPage.fillForm({
  206 |             yourName: 'Automated Test User',
  207 |             headlineTitle: 'Automated Test Headline',
  208 |             publicationName: 'Automated Test Publication',
  209 |             publicationDate: '2026-08-01',
  210 |             optIn: true,
  211 |           });
  212 |         });
  213 | 
  214 |         await test.step('Upload File 1, File 2, and File 3', async () => {
  215 |           const files = [1, 2, 3].map((i) =>
  216 |             createTempFile(`${company.slug}-file${i}.txt`, `content ${i}`)
  217 |           );
  218 |           await mctFormPage.uploadFiles(files);
  219 |         });
  220 | 
  221 |         await test.step('Verify "Maximum 3 files allowed" instruction text is displayed', async () => {
  222 |           await expect(mctFormPage.uploadHelperText).toBeVisible();
  223 |         });
  224 |       });
  225 | 
  226 |       if (company.keys.unrestrictedFileType) {
  227 |         test(`${company.keys.unrestrictedFileType}: Verify Unrestricted Upload of File for MyCompanyTodayForm - ${company.displayName}`, async ({
  228 |           mctFormPage,
  229 |         }) => {
  230 |           await test.step('Open the New MCT Article Submission Form', async () => {
  231 |             await mctFormPage.openForCompany(company.slug);
  232 |           });
  233 | 
  234 |           await test.step('Fill in all mandatory fields', async () => {
  235 |             await mctFormPage.fillForm({
  236 |               yourName: 'Automated Test User',
  237 |               headlineTitle: 'Automated Test Headline',
  238 |               publicationName: 'Automated Test Publication',
  239 |               publicationDate: '2026-08-01',
  240 |               optIn: true,
  241 |             });
  242 |           });
  243 | 
  244 |           await test.step('Upload an unsupported file type and click Submit', async () => {
  245 |             const filePath = createTempFile(`${company.slug}-malware.exe`, 'MZ fake exe content');
  246 |             await mctFormPage.uploadFiles([filePath]);
  247 |             await mctFormPage.clickSubmit();
  248 |           });
  249 | 
  250 |           await test.step('Verify an error message is displayed', async () => {
> 251 |             await expect(mctFormPage.genericErrorMessage).toBeVisible({ timeout: 15000 });
      |                                                           ^ Error: expect(locator).toBeVisible() failed
  252 |           });
  253 |         });
  254 |       }
  255 |     });
  256 |   }
  257 | 
  258 |   // NOTE: TRS-Upload serves a reduced form template (Headline Title, Email, Upload File
  259 |   // only - no Your Name, Publication Name/Date, URL, Comments, or Opt-In checkbox), unlike
  260 |   // the shared Analog/Prosegur/Otsuka/Arconic/Constellium template. A file attachment is
  261 |   // also required server-side to submit successfully, despite no inline validation for it.
  262 |   // These tests are written to match the actual live QA behavior rather than the generic
  263 |   // Zephyr steps (which assume the full field set).
  264 |   test.describe('TRS', () => {
  265 |     test('DJCSS-T146: Verify that the form submits successfully when all fields (Mandatory + Optional) are populated correctly - TRS', async ({
  266 |       mctFormPage,
  267 |     }) => {
  268 |       await test.step('Open the New MCT Article Submission Form', async () => {
  269 |         await mctFormPage.openForCompany('TRS-Upload');
  270 |       });
  271 | 
  272 |       await test.step('Fill in Headline Title and Email, then upload a file', async () => {
  273 |         await mctFormPage.fillForm({
  274 |           headlineTitle: 'Automated Test Headline',
  275 |           email: 'automated.test@example.com',
  276 |         });
  277 |         const filePath = createTempFile('trs-full-submit.txt', 'sample content');
  278 |         await mctFormPage.uploadFiles([filePath]);
  279 |       });
  280 | 
  281 |       await test.step('Click Submit and verify success message', async () => {
  282 |         await mctFormPage.clickSubmit();
  283 |         await expect(mctFormPage.successMessage).toBeVisible({ timeout: 15000 });
  284 |       });
  285 |     });
  286 | 
  287 |     test('DJCSS-T147: Verify that the form prevents submission if mandatory fields are empty - TRS', async ({
  288 |       mctFormPage,
  289 |     }) => {
  290 |       await test.step('Open the New MCT Article Submission Form', async () => {
  291 |         await mctFormPage.openForCompany('TRS-Upload');
  292 |       });
  293 | 
  294 |       await test.step('Leave Headline Title empty and click Submit', async () => {
  295 |         await mctFormPage.clickSubmit();
  296 |       });
  297 | 
  298 |       await test.step('Verify inline error message appears for Headline Title', async () => {
  299 |         const errors = await mctFormPage.getInlineErrorMessages();
  300 |         expect(errors.length).toBeGreaterThanOrEqual(1);
  301 |         await expect(mctFormPage.successMessage).not.toBeVisible();
  302 |       });
  303 |     });
  304 | 
  305 |     test('DJCSS-T148: Verify submission with only the mandatory fields filled, leaving all optional fields blank - TRS', async ({
  306 |       mctFormPage,
  307 |     }) => {
  308 |       await test.step('Open the New MCT Article Submission Form', async () => {
  309 |         await mctFormPage.openForCompany('TRS-Upload');
  310 |       });
  311 | 
  312 |       await test.step('Fill in Headline Title and upload a file, leaving Email blank', async () => {
  313 |         await mctFormPage.fillForm({ headlineTitle: 'Automated Test Headline' });
  314 |         const filePath = createTempFile('trs-mandatory-only.txt', 'sample content');
  315 |         await mctFormPage.uploadFiles([filePath]);
  316 |       });
  317 | 
  318 |       // NOTE: on this QA build, submitting TRS-Upload without the Email field populated
  319 |       // returns "An error occurred. Please try again." even though Email has no inline
  320 |       // "required" validation and isn't marked mandatory on the form. This documents the
  321 |       // current (server-side required) behavior rather than the Zephyr step's assumption
  322 |       // that only Headline Title is mandatory.
  323 |       await test.step('Click Submit and verify the server rejects the submission', async () => {
  324 |         await mctFormPage.clickSubmit();
  325 |         await expect(mctFormPage.genericErrorMessage).toBeVisible({ timeout: 15000 });
  326 |       });
  327 |     });
  328 | 
  329 |     test('DJCSS-T149: Verify the form successfully accepts exactly 3 uploaded files - TRS', async ({
  330 |       mctFormPage,
  331 |     }) => {
  332 |       await test.step('Open the New MCT Article Submission Form', async () => {
  333 |         await mctFormPage.openForCompany('TRS-Upload');
  334 |       });
  335 | 
  336 |       await test.step('Fill in Headline Title', async () => {
  337 |         await mctFormPage.fillForm({ headlineTitle: 'Automated Test Headline' });
  338 |       });
  339 | 
  340 |       await test.step('Upload File 1, File 2, and File 3', async () => {
  341 |         const files = [1, 2, 3].map((i) => createTempFile(`trs-file${i}.txt`, `content ${i}`));
  342 |         await mctFormPage.uploadFiles(files);
  343 |       });
  344 | 
  345 |       await test.step('Verify "Maximum 3 files allowed" instruction text is displayed', async () => {
  346 |         await expect(mctFormPage.uploadHelperText).toBeVisible();
  347 |       });
  348 |     });
  349 | 
  350 |     test('DJCSS-T150: Verify Unrestricted Upload of File for MyCompanyTodayForm - TRS', async ({
  351 |       mctFormPage,
```