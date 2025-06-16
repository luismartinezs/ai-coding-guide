Your Goal: Create a Draft Pull Request with empty body

Follow these steps:
1. Verify upstream repo. If there is no upstream repo, STOP and ask user if origin should be used as upstream. If they answer "yes", add origin as upstream and continue.
2. Retrieve the currently checked out git branch name. If the user is checked out to main, master, or a branch that does not look like a feature branch, STOP, ask the user to switch to a feature branch and await for confirmation.
3. Create a new PR draft, using upstream as target and the currently checked out git branch as source. Use the current branch name as PR title. Leave the body empty.
4. Output the PR URL.