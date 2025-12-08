# Project Guidelines

## 1. Build/Configuration Instructions

### Setup
1. Ensure Node.js is installed.
2. Install dependencies:
   ```bash
   npm install
   ```

### Building the Resume
The project uses `resumed` to generate the resume from `resume/resume.json` using a local theme.

*   **Generate PDF and HTML**:
    ```bash
    npm run generate
    ```
    This command:
    1.  Cleans previous builds.
    2.  Generates `resume/resume.html`.
    3.  Generates `resume/resume.pdf`.
    4.  Copies the PDF to `resume/VivekRanjanResume.pdf`.

*   **Generate HTML Only**:
    ```bash
    npm run serve
    ```
    *(Note: Despite the name `serve`, this npm script currently only renders the HTML file to disk.)*

### Development Server (Website)
To serve the static website files with live reloading (using BrowserSync):
```bash
npx gulp serve
```
This watches HTML, JS, and CSS files for changes.

## 2. Testing Information

Currently, there is no dedicated test suite configured in `package.json`. However, you can verify the resume generation using a simple Node.js script.

### Running a Verification Test
To ensure the resume generation works correctly, you can use the following script.

**Create `test_resume.js`:**
```javascript
import fs from 'fs';
import assert from 'assert';
import { exec } from 'child_process';

console.log('Running resume generation test...');

// Trigger the render command
exec('npm run serve', (error, stdout, stderr) => {
    if (error) {
        console.error(`exec error: ${error}`);
        process.exit(1);
    }

    // Check if file exists
    const filePath = './resume/resume.html';
    if (!fs.existsSync(filePath)) {
        console.error('File resume/resume.html was not generated.');
        process.exit(1);
    }

    // Check content
    const content = fs.readFileSync(filePath, 'utf-8');
    try {
        // Verify that expected content (e.g., the name) is present
        assert.match(content, /Vivek Ranjan/, 'Resume should contain the name "Vivek Ranjan"');
        console.log('Test Passed: resume.html generated and contains correct name.');
    } catch (e) {
        console.error('Test Failed:', e.message);
        process.exit(1);
    }
});
```

**Run the test:**
```bash
node test_resume.js
```

### Guidelines for Adding New Tests
*   Since the project is configured as an ES Module (`"type": "module"` in `package.json`), ensure test files use ESM syntax (`import`/`export`).
*   For more complex testing, consider adding a test runner like `mocha` or `jest`.

## 3. Additional Development Information

### Project Structure
*   **`resume/resume.json`**: The source data for the resume. Edit this file to update resume content.
*   **`jsonresume-theme-short-with-location/`**: Contains the custom theme logic.
    *   `index.js`: Theme entry point (Note: uses CommonJS syntax `require` but is consumed by `resumed`).
    *   `resume.template`: Handlebars template for the resume.
    *   `style.css`: Styles for the resume.
*   **`gulpfile.js`**: Gulp configuration for the static site server.

### Code Style
*   The project uses a mix of ES Modules (root level) and CommonJS (theme).
*   When editing the theme, ensure compatibility with the Handlebars helpers defined in `index.js`.
