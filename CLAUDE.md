# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a comprehensive Korean-language technical knowledge base for AI engineers, covering everything from CS fundamentals to cutting-edge AI technologies. The repository is primarily documentation-focused using Markdown files organized in a hierarchical structure.

## Project Structure

```
docs/
├── 1-cs-전공-지식/         # CS fundamentals (OS, Network, DB, etc.)
├── 2-기초-딥러닝/          # Deep learning basics
├── 3-컴퓨터-비전/          # Computer vision
├── 4-대규모-언어-모델-llm/  # Large Language Models
├── 5-agentic-ai/          # Agentic AI
├── 6-mlops-and-배포/       # MLOps & deployment
├── 7-데이터-엔지니어링-and-파이프라인/  # Data engineering
├── 8-책임-ai-윤리-보안/     # Responsible AI & ethics
├── 9-엣지-ai-and-온디바이스-ml/  # Edge AI & on-device ML
├── 부록/                  # Appendix resources
└── assets/               # Images and other resources
```

## Documentation Standards

### Writing Guidelines
- **Language**: Korean for main content, English for code and widely-accepted technical terms
- **Format**: All documents in Markdown (`.md`)
- **Style**: Clear and concise Korean using '...함' or '...음' endings
- **File Naming**: Lowercase with hyphens (e.g., `process-vs-thread.md`)
- **Images**: Store in `docs/assets/images` and reference with relative paths

### Document Template Structure
Each technical document follows this structure:
1. **핵심 개념 (Core Concept)** - Brief 1-3 sentence summary
2. **상세 설명 (Detailed Explanation)** - In-depth explanation with subsections
3. **예시 (Example)** - Concrete examples, code, diagrams
4. **예상 면접 질문 (Potential Interview Questions)** - Q&A format
5. **더 읽어보기 (Further Reading)** - Reference links

### Visual Elements
- **Mermaid Diagrams**: Use for flowcharts and sequence diagrams
- **Tables**: For structured comparisons
- **Images**: When visual aids enhance understanding

## Common Commands

### Documentation Tasks
```bash
# View the main index
cat docs/index.md

# Search for specific topics
grep -r "트랜스포머" docs/

# Check document structure
find docs -type f -name "*.md" | wc -l
```

### Git Workflow
```bash
# Create feature branch for documentation updates
git checkout -b feature/update-[topic]

# Commit with descriptive emoji and message
git commit -m ':book: Update [topic] documentation'

# Common commit emojis used in this repo:
# :book: :green_book: :blue_book: :orange_book: - Documentation updates
# :wrench: - Fixes and improvements
# :sparkles: - New content
# :art: - Formatting improvements
```

## Content Management

### Obsidian Integration
This repository includes Obsidian configuration for enhanced editing experience. The `.obsidian/` directory contains plugins and themes optimized for technical documentation.

### Quality Checklist for New Content
- Follows the document template structure
- Includes at least one visual element (diagram, table, or image)
- Contains 3-5 potential interview questions
- References credible sources
- Uses consistent Korean terminology with English annotations where helpful
- Code examples are properly formatted with language tags

## Development Notes

### Focus Areas
- **Interview Preparation**: Content is optimized for AI engineer job interview preparation
- **Practical Examples**: Emphasis on real-world applications and use cases
- **Visual Learning**: Heavy use of diagrams and visual aids for complex concepts
- **Progressive Learning**: Content organized from fundamentals to advanced topics

### Contributing
When adding or modifying documentation:
1. Follow the established folder structure in `docs/`
2. Use the document template from `DOCS-GUIDE.md`
3. Ensure Korean language clarity and technical accuracy
4. Add relevant Mermaid diagrams or tables where appropriate
5. Include practical examples and potential interview questions
