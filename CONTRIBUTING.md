# Contributing to Timeline Generator

First off, thank you for considering contributing to Timeline Generator! It's people like you that make open source projects great.

## Code of Conduct

By participating in this project, you agree to maintain a respectful and inclusive environment for everyone.

## How Can I Contribute?

### Reporting Bugs

Before creating bug reports, please check existing issues to avoid duplicates. When creating a bug report, include:

- **Clear title** describing the issue
- **Steps to reproduce** the behavior
- **Expected behavior** vs. actual behavior
- **Browser and OS** information
- **Screenshots** if applicable

### Suggesting Features

Feature suggestions are welcome! Please:

- Check if the feature has already been suggested
- Provide a clear description of the feature
- Explain the use case and benefits
- Consider if it fits the project's scope (simple, no-backend timeline tool)

### Pull Requests

1. **Fork** the repository
2. **Create a branch** for your feature (`git checkout -b feature/amazing-feature`)
3. **Make your changes** in `index.html`
4. **Test** your changes in multiple browsers
5. **Commit** with clear messages (`git commit -m 'Add amazing feature'`)
6. **Push** to your branch (`git push origin feature/amazing-feature`)
7. **Open a Pull Request**

## Development Guidelines

### Architecture

This project intentionally uses a **single-file architecture**. All code lives in `index.html`:

```
index.html
├── <style>     → CSS styles
├── <div id="app"> → Vue template
└── <script>    → Vue 3 application
```

**Please maintain this structure.** Do not split into multiple files.

### Tech Stack

- **Vue 3** (Composition API) — via CDN
- **Tailwind CSS** — via CDN
- **No build tools** — Keep it simple

### Code Style

- Use **2-space indentation**
- Use **camelCase** for JavaScript variables/functions
- Use **kebab-case** for CSS classes
- Add comments for complex logic
- Keep the German UI language (i18n is a welcome future contribution)

### Testing Checklist

Before submitting a PR, test:

- [ ] Creating new projects
- [ ] Adding/editing groups, phases, milestones
- [ ] Drag & drop reordering
- [ ] All export formats (PNG, SVG, JSON)
- [ ] Template save/load
- [ ] LocalStorage persistence (close/reopen browser)
- [ ] Different browsers (Chrome, Firefox, Safari)

## Feature Wishlist

Looking for something to work on? Here are some ideas:

### Good First Issues
- [ ] Add keyboard shortcuts for common actions
- [ ] Improve mobile/tablet responsiveness
- [ ] Add more milestone icons

### Medium Complexity
- [ ] Internationalization (i18n) support
- [ ] Dark mode theme
- [ ] Print stylesheet optimization

### Advanced
- [ ] Dependency arrows between phases (Gantt-style)
- [ ] Undo/redo functionality
- [ ] Import from CSV/Excel

## Questions?

Feel free to open an issue for questions. We're happy to help!

---

Thank you for contributing!
