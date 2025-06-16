Create a folder with the same name as the provided GitHub issue number under memory-bank/issues. Then, add the following files to the folder:
- [issue-number]-ticket.md: Dump the issue content provided here. If the user did not provide the issue content, fetch it from GitHub.
- [issue-number]-outline.md: Leave empty.
- [issue-number]-plan.md: Leave empty.
- [issue-number]-pr.md: Leave empty.

For example, if the issue number is `1234`, then you should:
- create a folder called `memory-bank/issues/1234`
- create these files within that folder: `1234-ticket.md`, `1234-outline.md`, `1234-plan.md`, `1234-pr.md`

Ensure that the folder and files are organized and labeled accurately. If the issue content is not provided, make sure to retrieve it from GitHub to populate the ticket.md file. If no issue number is provided for folder and file naming, then generate a short slug for the issue and use it instead.