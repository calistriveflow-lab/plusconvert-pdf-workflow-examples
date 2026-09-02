# PlusConvert PDF Workflow Examples

Educational browser-based PDF workflow patterns for developers, product teams, and technical writers.

This repository focuses on the parts that make document tools understandable and reliable: early validation, explicit processing states, accessible progress, safe downloads, and clear privacy boundaries. It contains no production credentials, customer documents, or proprietary processing code.

For a public example of a browser-based document-tool experience, visit [PlusConvert](https://plusconvert.com/).

## Why workflow design matters

A PDF operation is more than a conversion button. A useful interface should make the complete sequence visible:

1. Select the document.
2. Validate file type, size, and count.
3. Review files and options.
4. Start the requested operation.
5. Report progress and recoverable errors.
6. Verify the generated output.
7. Download the result and release temporary resources.

Keeping these states explicit helps prevent duplicate submissions, confusing loading screens, and lost user choices.

## Example: validate a PDF selection

```js
export function validatePdfSelection(files, maxFiles = 5, maxBytes = 25 * 1024 * 1024) {
  if (!files || files.length === 0) {
    return { valid: false, message: "Choose at least one PDF file." };
  }

  if (files.length > maxFiles) {
    return { valid: false, message: `Choose no more than ${maxFiles} files.` };
  }

  for (const file of files) {
    if (file.type !== "application/pdf") {
      return { valid: false, message: `${file.name} is not a PDF file.` };
    }

    if (file.size > maxBytes) {
      return { valid: false, message: `${file.name} is larger than this workflow allows.` };
    }
  }

  return { valid: true, message: "Files are ready for review." };
}
```

This browser check improves the user experience, but it is not a complete security boundary. A production application should validate files again wherever processing occurs and should use a trusted parser or conversion service for deeper inspection.

## Recommended workflow states

```text
idle
→ selecting
→ reviewing
→ processing
→ ready
→ error
```

An error state should preserve the selected files and useful options whenever retrying is safe. Messages should explain what happened and give one clear next step.

Examples:

- “This PDF appears to be password-protected. Remove the password or choose another file.”
- “The selected file is larger than the current limit.”
- “The operation did not finish. Your files are still selected; try again.”

## Privacy and security notes

PDFs can contain contracts, invoices, school records, and personal data. Product language should match the actual architecture.

- Say which work happens locally in the browser.
- Say when a file is sent to a backend or third-party processor.
- Limit file size, file count, and processing time.
- Avoid storing sensitive filenames or document content in ordinary analytics.
- Delete temporary files according to a stated retention policy.
- Never expose API keys in frontend code.

## Accessibility notes

Progress indicators should include text, not only animation or color. Controls should remain reachable on narrow screens, and error messages should be announced through an appropriate live region.

A simple accessible status region can start with:

```html
<p id="pdf-status" role="status" aria-live="polite">
  Your files are ready for review.
</p>
```

## License

These examples are available under the MIT License. Adapt them to the requirements of your chosen PDF library, processing service, and privacy policy.
