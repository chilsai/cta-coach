Trigger the knowledge updater agent to pull the latest Salesforce release notes and architect documentation.

Run the full workflow defined in agents/knowledge-updater-agent.md:

1. Fetch latest Salesforce release notes and summarise changes relevant to CTA domains
2. Check architect.salesforce.com for new or updated content since knowledge/release-notes/last-updated.txt
3. Identify any changes that require updates to agents/ or knowledge/domain-guides/
4. Save a dated summary to knowledge/release-notes/
5. Update knowledge/release-notes/last-updated.txt with today's date

After completing, report:
- What was checked
- What changed (if anything)
- Which domain guides need updating and why
