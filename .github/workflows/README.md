# Weekly README Verification Workflow

This workflow automatically verifies and maintains the quality of the README.md file in this repository.

## What it does

The workflow runs weekly (every Monday at 9:00 AM UTC) and performs the following tasks:

### 1. Link Verification
- Checks all links in README.md to ensure they are working and accessible
- Reports any broken or inaccessible links
- Uses `markdown-link-check` with retry logic and timeout handling

### 2. Project Discovery
- Searches GitHub for new EV-related projects using relevant keywords:
  - OCPP charging station
  - EV charging management system
  - ISO 15118 implementation
  - OCPI roaming platform
  - Electric vehicle charging software
  - Charge point operator software
  - EV charging API
  - Open source charging station
- Filters results for quality (minimum stars, not archived, has description)
- Identifies projects not already in the README

### 3. Automated Pull Request
- If broken links are found or new projects are discovered, automatically creates a PR
- PR contains a report file (`update-suggestions.md`) with:
  - List of broken links that need attention
  - Suggestions for new projects with descriptions and metadata
- **Note**: The workflow generates suggestions but does not automatically modify README.md - maintainers review and apply changes manually
- Uses a stable branch name (`weekly-readme-update`) so subsequent runs update the same PR instead of creating duplicates
- Proper labeling and assignment
- If PR creation fails, creates an issue instead

## Manual Trigger

You can manually trigger this workflow from the Actions tab:
1. Go to the "Actions" tab
2. Select "Weekly README Verification and Update"
3. Click "Run workflow"
4. Select the branch and click "Run workflow"

## Artifacts

The workflow saves the following artifacts for 30 days:
- `link-check-results.txt` - Full link check output
- `broken-links.txt` - List of broken links
- `new-projects.json` - Discovered projects data
- `update-suggestions.md` - Generated suggestions report

## Configuration

### Link Check Settings
Link checking is configured via `.github/workflows/link-check-config.json`:
- 20-second timeout per link
- Retry on 429 (rate limit) errors
- 3 retry attempts with 30-second delays
- Accepts various success HTTP status codes

### Schedule
Modify the cron expression in the workflow file to change the schedule:
```yaml
schedule:
  - cron: '0 9 * * 1'  # Every Monday at 9:00 AM UTC
```

## Permissions Required

The workflow needs the following permissions (already configured):
- `contents: write` - To create branches
- `pull-requests: write` - To create PRs
- `issues: write` - To create issues when PR creation fails

## Customization

### Adding More Search Keywords
Edit the `keywords` array in the "Search for new EV projects" step.

### Adjusting Filter Criteria
Modify the filtering logic in `search-projects.js` to change:
- Minimum star count
- Date range for updates
- Language preferences

### Changing Rate Limits
Adjust the `setTimeout` delay in the search script to comply with API rate limits.

## Troubleshooting

### Workflow Not Running
- Check that the repository has Actions enabled
- Verify the workflow file has no syntax errors
- Ensure the schedule cron expression is valid

### Too Many False Positives
- Adjust link check timeout in `link-check-config.json`
- Add patterns to `ignorePatterns` for known issues

### Rate Limiting
- The workflow includes delays between searches to respect API limits
- GitHub API authentication is automatically configured using `GITHUB_TOKEN`
  - Authenticated requests have a limit of 5,000 requests/hour
  - Unauthenticated requests are limited to 60 requests/hour
- The workflow automatically uses the `GITHUB_TOKEN` secret, which is available in all GitHub Actions
- No additional configuration is needed for authentication

## Contributing

To improve this workflow:
1. Test changes manually using workflow dispatch
2. Check artifacts to verify output quality
3. Monitor PR quality and adjust filters as needed
