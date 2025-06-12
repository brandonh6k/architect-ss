# GitHub Copilot Instructions for Safety Shield Architecture Sandbox

## Workspace Context
- This is an architect sandbox workspace for the Safety Shield (SS) application
- Primary activities include design work, architecture planning, and documentation
- Work is captured as epics and stories in Markdown before mirroring to Jira

## Workflow Understanding
- Architecture and design decisions should follow industry best practices
- Markdown files are used to draft epics and stories before they are migrated to Jira
- Reference `/docs/jira_field_reference.md` for Jira field mappings and requirements

## Project Specifics
- All Jira items belong to the SAF project
- Confluence space is also SAF
- "SS" refers to Safety Shield throughout documentation

## Preferred Formats
- Use proper Markdown formatting for all documentation
- When creating epics/stories, follow existing templates in the workspace
- Include necessary Jira fields as YAML frontmatter in Markdown files

## AI Assistant Guidance
- Prioritize secure design patterns and architectural best practices
- Suggest improvements to architecture decisions when appropriate
- Help formulate clear acceptance criteria for epics and stories
- Assist with proper Jira field mapping for SAF project requirements

## Example Epics and Stories
- Use the provided examples in the workspace as templates for new epics and stories. `/templates/jira_epic.md` is also available as a template for creating new epics.
- Ensure all epics have a clear scope, user impact, and success metrics
- Break down epics into actionable stories with clear acceptance criteria
- Include metadata such as status, priority, reporter, and labels in the YAML frontmatter
- Use the `Epic Category` field to classify epics according to the provided categories
- Ensure all stories are linked to their parent epic using the `parent` field

## Jira Integration
- Use Jira MCP tools for creating, updating, and managing epics and stories
- When creating new epics or stories, ensure all required fields are populated (see jira_field_reference.md)
- Always update both Jira and local Markdown files to maintain consistency
- Use proper parent/epic linking when creating stories under epics
- Follow the SAF project conventions for issue types, priorities, and workflows

## Complex Problem Solving
- For architectural decisions, design analysis, or multi-step problem solving, use the Sequential Thinking MCP tool
- Break down complex requirements into manageable components
- Document decision rationale and trade-offs in the thinking process
- Use when analyzing security implications, performance considerations, or integration challenges

## Knowledge Management
- Maintain architectural decisions and rationale in documentation
- Update design documents when implementation details change
- Keep track of dependencies between components and epics
- Document lessons learned from implementation for future reference

## Documentation Standards
- All architectural decisions should include context, options considered, and rationale
- Security analysis should follow the established format in /docs/security/
- Design documents should include both current state and future state diagrams
- Epic and story documentation should be kept in sync between Jira and local files

## Workflow Management
- Draft epics and stories in Markdown before creating in Jira
- Use the established templates for consistency
- Maintain traceability between business requirements and technical implementation
- Coordinate epic splitting and story movement between phases as needed

## Git Practices
- Keep commit messages concise and descriptive (avoid verbose defaults)
- Use present tense, imperative mood: "Add feature" not "Added feature" or "Adding feature"
- Limit subject line to 50 characters when possible
- Focus on what the change does, not how it was implemented
- Examples: "Split LDAP stories to Phase 1.5 epic", "Update API documentation", "Fix authentication flow"