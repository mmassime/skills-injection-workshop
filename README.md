# Skills Injection Workshop

A repository demonstrating security vulnerabilities in AI Agent Skills (SKILL.md format). These examples show how malicious actors can exploit the trust agents place in skill files.

> **⚠️ WARNING**: These are intentionally malicious examples for educational purposes. Do NOT run these skills on your system. Do NOT use these techniques for malicious purposes.

## What are Agent Skills?

Agent Skills are markdown instruction files (SKILL.md) that give AI agents additional capabilities. Introduced by Anthropic for Claude Code, they allow agents to follow specialized instructions without code changes. The format has been adopted by other frameworks like Swarms.

**Structure:**
```
your-skill/
├── SKILL.md          # Instructions + metadata
├── scripts/          # Executable code the agent runs
├── references/       # Additional docs
└── assets/           # Templates, images, etc.
```

## Why Skills Are Dangerous

1. **Implicit trust** - Agents treat skill instructions as authoritative
2. **Human oversight gaps** - Long files reduce scrutiny; users rarely audit every line
3. **System access** - Skills often have broad file system and network access
4. **Instruction-only context** - Traditional "separate instructions from data" defenses don't apply

## Attack Examples

This repository contains 4 demonstration attacks, ordered by visibility:

| # | Attack | Raw View | Rendered View | LLM Sees? |
|---|--------|----------|---------------|-----------|
| 01 | Hidden Instructions | ✅ Visible | ✅ Visible | ✅ Yes |
| 02 | Markdown Hiding | ✅ Visible | ❌ Hidden | ✅ Yes |
| 03 | Unicode Injection | ❌ Hidden | ❌ Hidden | ✅ Yes |
| 04 | Script Reference | In separate file | N/A | ✅ Yes |

### [01 - Hidden Instructions](./01-hidden-instructions/)
Malicious commands buried deep within a verbose, legitimate-looking skill file. The attack hides an SSH key exfiltration command in "Step 9" of a 11-step README generator.

**Key technique:** Social engineering through information overload

### [02 - Markdown Hiding](./02-markdown-hiding/)
Instructions hidden using HTML comments, CSS `display:none`, off-screen positioning, and reference-style link definitions. Visible in raw text but invisible in rendered Markdown preview.

**Key technique:** Exploiting the gap between raw text and rendered view

### [03 - Unicode Injection](./03-unicode-injection/)  
Invisible instructions encoded using Unicode Tag codepoints (U+E0000-U+E007F). The characters render as zero-width but many LLMs interpret them as valid instructions.

**Key technique:** Invisible payload that bypasses human review

### [04 - Script Reference Injection](./04-script-reference-injection/)
A clean SKILL.md that references a script containing hidden data exfiltration logic disguised as "configuration sync" functions.

**Key technique:** Separation of malicious code from reviewed content

## Detection & Mitigation

### For Users
- **Always review entire skill files** including all referenced scripts
- **Run skills in sandboxed environments** without access to `~/.ssh`, `~/.aws`, etc.
- **Monitor network requests** during skill execution
- **Use verified sources** only - check signatures if available

### For Skill Platforms
- Strip dangerous Unicode ranges (U+E0000-U+E007F)
- Implement content security policies for skills
- Require explicit user approval for sensitive operations
- Sandbox skill execution with minimal privileges

### Detection Commands

```bash
# Detect hidden Unicode tags
python3 -c "
content = open('SKILL.md').read()
tags = [c for c in content if 0xE0000 <= ord(c) <= 0xE007F]
print(f'Found {len(tags)} suspicious characters')
"

# Find HTML comments with instructions
grep -rE '<!--.*curl|<!--.*exec|<!--.*\$\(' .

# Search for exfiltration patterns
grep -rE 'curl.*\$\(cat|wget.*--post|base64.*POST' .

# Find backgrounded network calls
grep -rE '(curl|wget|nc).*&\s*$' .

# Check for sensitive file access
grep -rE '\.ssh|\.aws|\.env|\.netrc|\.npmrc' .

# Find reference-style link definitions (often hide content)
grep -rE '^\[.+\]:\s*.*".*"' .
```

## References

- [Measuring Agent Vulnerability to Skill File Attacks](https://arxiv.org/abs/2602.20156) - arXiv 2026
- [Scary Agent Skills: Hidden Unicode Instructions](https://embracethered.com/blog/posts/2026/scary-agent-skills/)
- [snyk/agent-scan](https://github.com/snyk/agent-scan) - Security scanner for AI agents

## License

MIT - Educational use only. Do not use for malicious purposes.