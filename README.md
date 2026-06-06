# Autonomous Paper XTS

**Automated academic paper writing pipeline** — a Skill for AI coding agents that handles the full lifecycle of Chinese academic paper writing, from literature survey to format compliance.

Autonomous Paper XTS orchestrates six stages to produce a publication-ready Chinese academic review or research paper (~15,000 words) with minimal human intervention. It is designed for agents such as Claude Code, OpenAI Codex, QoderWork, and any platform that supports the `.claude/skills/` convention.

## Pipeline Overview

```
S1 Literature Survey → S2 Scientific Writing → S3 Self-Review → S4 Targeted Revision
                                                                        ↓
                                              S5 De-AI Humanizer → S6 Format Compliance → Final Output
```

| Stage | Description | Output |
|-------|-------------|--------|
| S1 | Parallel web search across 6 dimensions | Knowledge framework + 34 references |
| S2 | Two-stage scientific writing (outline → prose) | Paper v1.0 (~12,000 words) |
| S3 | 7-step structured self-review | Review report with P0/P1/P2 issues |
| S4 | Full revision based on review | Paper v2.0 (+ 3 comparison tables) |
| S5 | 24-pattern AI writing detection & rewrite | Paper v3.0 (natural style, score ≥ 35/50) |
| S6 | GB/T 7713.2-2022 format compliance check | Paper v4.0 (format-compliant final) |

**Total time**: ~75–95 minutes | **Human intervention**: topic definition + final review only.

## Quick Start

```
User: "帮我写一篇关于RFID技术在感知领域应用的论文，综述类型"

→ S1: Parallel search "RFID sensing technology" × 6
→ S2: Two-stage writing, 12,000-word v1.0
→ S3: 7-step review, 23 issues found
→ S4: Full revision, v2.0 (+ 3 tables)
→ S5: 24-pattern de-AI, v3.0 (score 39/50)
→ S6: GB/T 7713.2 check, v4.0 (0 errors)
→ Deliverable: RFID技术用于感知领域_学术报告.md
```

## Prerequisites — Install These First

This pipeline orchestrates four companion skills. **All four must be installed before using this skill.**

### 1. `scientific-writing` (used in S2)

Two-stage academic writing methodology — outline first, then flowing prose. No bullet points in the final manuscript.

- **Author**: K-Dense Inc. (MIT)
- **Install**: This skill is bundled with the [K-Dense AI co-scientist](https://github.com/K-Dense-AI/k-dense-byok) package. Copy the `scientific-writing/` skill folder into your `~/.claude/skills/` directory.

```bash
# If you have the K-Dense package:
cp -r path/to/k-dense/skills/scientific-writing ~/.claude/skills/
```

### 2. `humanizer-zh` (used in S5)

Chinese text humanizer that detects and rewrites 24 AI writing patterns to produce natural, human-sounding prose.

- **Source**: [op7418/Humanizer-zh](https://github.com/op7418/Humanizer-zh)

```bash
git clone https://github.com/op7418/Humanizer-zh.git /tmp/humanizer-zh
cp -r /tmp/humanizer-zh ~/.claude/skills/humanizer-zh
```

Or with GitHub CLI:

```bash
gh repo clone op7418/Humanizer-zh ~/.claude/skills/humanizer-zh
```

### 3. `paper-self-review` (used in S3)

7-step structured paper review with Claim Audit and P0/P1/P2 issue grading. Bundled inside the `claude-scholar` skill suite.

- **Source**: [Galaxy-Dawn/claude-scholar](https://github.com/Galaxy-Dawn/claude-scholar)

```bash
git clone https://github.com/Galaxy-Dawn/claude-scholar.git /tmp/claude-scholar
cp -r /tmp/claude-scholar/skills/paper-self-review ~/.claude/skills/paper-self-review
```

Or with GitHub CLI:

```bash
gh repo clone Galaxy-Dawn/claude-scholar /tmp/claude-scholar
cp -r /tmp/claude-scholar/skills/paper-self-review ~/.claude/skills/paper-self-review
```

### 4. `paper-format-checker` (used in S6)

GB/T 7713.2-2022 format compliance checker with 19 categories and 60+ check items for Chinese academic papers.

- **Install**: Obtain the `paper-format-checker` skill and place it in `~/.claude/skills/paper-format-checker/`. If you received this skill from another user, simply copy the folder:

```bash
cp -r path/to/paper-format-checker ~/.claude/skills/paper-format-checker
```

### Quick Verification

After installing all prerequisites, your `~/.claude/skills/` directory should contain:

```
~/.claude/skills/
├── autonomous-paper-xts/    ← this skill
├── scientific-writing/      ← S2
├── humanizer-zh/            ← S5
├── paper-self-review/       ← S3
└── paper-format-checker/    ← S6
```

You can verify with:

```bash
ls ~/.claude/skills/ | grep -E "autonomous-paper-xts|scientific-writing|humanizer-zh|paper-self-review|paper-format-checker"
```

All five directories should appear in the output.

## Installation

### Option A: Git Clone (Recommended)

Clone this repository and copy the skill directory into your agent's skill path:

```bash
# Clone the repository
git clone https://github.com/Marvisatron/autonomous-paper-xts.git /tmp/autonomous-paper-xts

# Copy the skill to your agent's skill directory
cp -r /tmp/autonomous-paper-xts/.claude/skills/autonomous-paper-xts ~/.claude/skills/
```

On Windows (PowerShell):

```powershell
git clone https://github.com/Marvisatron/autonomous-paper-xts.git $env:TEMP\autonomous-paper-xts
Copy-Item -Recurse "$env:TEMP\autonomous-paper-xts\.claude\skills\autonomous-paper-xts" "$HOME\.claude\skills\"
```

### Option B: GitHub CLI

If you have the [GitHub CLI](https://cli.github.com/) installed:

```bash
gh repo clone Marvisatron/autonomous-paper-xts /tmp/autonomous-paper-xts
cp -r /tmp/autonomous-paper-xts/.claude/skills/autonomous-paper-xts ~/.claude/skills/
```

### Option C: Manual Download

1. Download the [ZIP archive](https://github.com/Marvisatron/autonomous-paper-xts/archive/refs/heads/main.zip) from GitHub.
2. Extract it.
3. Copy the `.claude/skills/autonomous-paper-xts/` folder into your `~/.claude/skills/` directory.

### Platform-Specific Notes

| Platform | Skill directory | Command |
|----------|----------------|---------|
| Claude Code | `~/.claude/skills/` | Default path, no extra setup |
| OpenAI Codex | `~/.claude/skills/` or project `.claude/skills/` | Copy to either location |
| QoderWork | Managed via QoderWork skill installer | Import the `.claude/skills/autonomous-paper-xts/` directory |
| Other agents | Varies | Place `SKILL.md` + `references/` where your agent reads skills |

## File Structure

```
.claude/skills/autonomous-paper-xts/
├── SKILL.md                              # Main skill definition & pipeline instructions
└── references/
    ├── ai-writing-patterns.md            # 24 AI writing patterns for S5 detection
    ├── FINAL-VERDICT.md                  # Submission readiness verdict criteria for S3
    ├── gbt7713-2-2022-rules.md           # GB/T 7713.2 format rules (19 categories) for S6
    ├── quality-gates.md                  # Quality gate definitions for each stage
    └── section-checklist.md              # Per-section review checklist for S3
```

## Usage

Once installed, simply tell your agent:

```
写一篇关于 [主题] 的论文
```

or in English:

```
Write a paper on [topic]
```

The pipeline will run autonomously through all six stages and deliver a format-compliant `.md` paper.

### Customization

You can specify:

- **Paper type**: 综述 (review, default) or 研究论文 (research, IMRaD structure)
- **Target style**: e.g. 《传感技术学报》 style (default)
- **Output path**: e.g. `~/Desktop/my-paper/` (default)

## Contributing

Contributions are welcome. Please open an issue or submit a pull request on GitHub.

## License

This project is licensed under the [MIT License](LICENSE).
