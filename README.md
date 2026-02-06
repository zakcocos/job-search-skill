# Job Search Skill for Claude Code

An automated job search skill that uses browser automation to find and evaluate job listings from [hiring.cafe](https://hiring.cafe).

## Features

- **Automated daily search** via cron job
- **Smart filtering** based on your preferences (salary, location, dealbreakers)
- **Job history tracking** to avoid showing duplicates
- **Learning from feedback** - refine preferences over time
- **Browser automation** via Claude in Chrome MCP

## Prerequisites

1. [Claude Code CLI](https://claude.ai/code) installed
2. [Claude in Chrome](https://chromewebstore.google.com/detail/claude-in-chrome) extension installed
3. Chrome browser running with the extension active

## Installation

### 1. Clone to your skills directory

```bash
git clone https://github.com/YOUR_USERNAME/job-search-skill.git ~/.claude/skills/job-search
```

### 2. Add your resume

Place your resume (PDF, DOCX, or TXT) in the assets folder:
```bash
cp /path/to/your/resume.pdf ~/.claude/skills/job-search/assets/resume/
```

Or run `/job-search setup` and it will prompt you.

### 3. Configure permissions

Add to `~/.claude/settings.json`:

```json
{
  "permissions": {
    "allow": [
      "Read(~/.claude/skills/job-search/**)",
      "Write(~/.claude/skills/job-search/assets/**)",
      "Edit(~/.claude/skills/job-search/assets/**)",
      "Bash(crontab *)",
      "mcp__claude-in-chrome__*"
    ]
  }
}
```

### 4. Run setup

```bash
claude "/job-search setup"
```

This will:
- Verify your resume is found
- Ask about your job preferences
- Optionally configure a daily cron job

## Usage

### Daily search (manual)
```bash
claude "/job-search"
```

### Search with specific keywords
```bash
claude "/job-search AI infrastructure"
claude "/job-search remote startup"
```

### Re-run setup
```bash
claude "/job-search setup"
```

### Run headless (for cron)
```bash
claude -p "/job-search"
```

## File Structure

```
~/.claude/skills/job-search/
├── SKILL.md                      # Main skill definition
├── README.md                     # This file
├── .gitignore                    # Excludes personal data
├── assets/
│   ├── resume/                   # Your resume (gitignored)
│   ├── matching-rules.md         # Your preferences (gitignored)
│   ├── job-history.md            # Log of all jobs found (gitignored)
│   └── templates/                # Templates for new users (committed)
│       ├── job-entry.md          # Format for history entries
│       ├── job-history-init.md   # Fresh job history
│       └── matching-rules.md     # Blank preferences template
├── scripts/
│   └── evaluate-jobs.md          # Job evaluation subagent
└── logs/
    └── cron.log                  # Output from automated runs (gitignored)
```

## Configuration

### Matching Rules (`assets/matching-rules.md`)

Customize your job preferences:

```markdown
## Target Roles
- VP Growth
- Head of Growth
- Director of Marketing

## Must-Have Criteria
- Remote or hybrid OK
- Minimum $250k+ total comp

## Dealbreakers
- Marketing agencies
- Crypto/blockchain
- >25% travel required

## Nice-to-Have
- Series B+ startup
- AI/ML focus
- B2B SaaS
```

### Updating Preferences

Just tell Claude what you want:
- *"Add fintech to my nice-to-haves"*
- *"I don't want any roles requiring relocation"*
- *"Bump my minimum salary to $300k"*

The skill will update `matching-rules.md` automatically.

## Job History

All jobs found are logged to `assets/job-history.md` with:
- Date and search terms
- Job details (title, company, location, salary)
- Fit score (High/Medium/Low/Skip)
- Notes explaining the rating

This prevents showing you the same jobs twice and creates a searchable archive.

## Cron Setup

To run daily at 9am:

```bash
# Add to crontab
(crontab -l 2>/dev/null; echo "0 9 * * * cd ~ && claude -p '/job-search' >> ~/.claude/skills/job-search/logs/cron.log 2>&1") | crontab -
```

**Note**: Requires Chrome to be running with Claude in Chrome extension active.

## Troubleshooting

### Permission prompts interrupting cron
Ensure all permissions are in `~/.claude/settings.json` (see Installation step 3).

### Browser not responding
Make sure Chrome is running and Claude in Chrome extension is active.

### No jobs found
- Check that hiring.cafe is accessible
- Try different search terms
- Verify your matching rules aren't too restrictive

## Roadmap

### v0.1 - Current
- [x] Automated job search on hiring.cafe
- [x] Smart filtering (salary, location, dealbreakers)
- [x] Job history tracking
- [x] Daily cron automation
- [x] Self-configuring setup flow

### v0.2 - Work History Interview
- [ ] Interactive interview to capture detailed work history
- [ ] Deep-dive on accomplishments, metrics, and impact
- [ ] Store structured work history in `assets/work-history.md`
- [ ] Use as foundation for all application materials

### v0.3 - Tailored Applications
- [ ] Generate customized resume for each target job
- [ ] Generate tailored cover letters highlighting relevant experience
- [ ] Match work history accomplishments to job requirements
- [ ] Store generated materials in `assets/applications/`

### v0.4 - Proactive Company Research
- [ ] Research companies beyond job boards
- [ ] Identify target companies based on preferences (industry, stage, culture)
- [ ] Track companies not currently hiring but worth monitoring
- [ ] Alert when target companies post new roles

### Future Ideas
- Support for additional job boards (LinkedIn, Indeed, etc.)
- Email digest of daily results
- Integration with ATS/application tracking
- Interview prep based on job description + work history

## Contributing

PRs welcome! Check the roadmap above for planned features, or open an issue to discuss new ideas.

## License

MIT
