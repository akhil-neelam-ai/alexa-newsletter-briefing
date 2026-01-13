# Contributing to Alexa Newsletter Briefing

Thank you for your interest in contributing! This document provides guidelines for contributing to this project.

## Ways to Contribute

### 1. Report Bugs 🐛

Found a bug? Please [open an issue](https://github.com/yourusername/alexa-newsletter-briefing/issues/new) with:

- Clear title describing the problem
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable
- Your environment (n8n version, OS, etc.)

### 2. Suggest Features 💡

Have an idea? [Open a feature request](https://github.com/yourusername/alexa-newsletter-briefing/issues/new) with:

- Clear description of the feature
- Use case / why it's valuable
- Proposed implementation (if you have ideas)

### 3. Improve Documentation 📖

Documentation improvements are always welcome:

- Fix typos or unclear instructions
- Add more examples
- Improve setup guides
- Translate to other languages

### 4. Share Customizations 🎨

Built something cool? Share it!

1. Add your customization to `customizations/` folder
2. Include a README explaining what it does
3. Submit a Pull Request

### 5. Code Contributions 🔧

Want to contribute code? Great!

## Pull Request Process

### Before You Start

1. **Check existing issues/PRs** to avoid duplicates
2. **Open an issue** to discuss major changes first
3. **Fork the repository** and create a branch

### Development Setup

```bash
# Clone your fork
git clone https://github.com/YOUR_USERNAME/alexa-newsletter-briefing.git
cd alexa-newsletter-briefing

# Create a branch
git checkout -b feature/your-feature-name
```

### Making Changes

1. **Make your changes**
   - Follow existing code style
   - Add comments where helpful
   - Test your changes thoroughly

2. **Update documentation**
   - Update README if needed
   - Update SETUP.md for new features
   - Add to CUSTOMIZATION.md if relevant

3. **Test thoroughly**
   - Test in n8n Cloud
   - Verify with actual newsletters
   - Check Alexa integration works

### Submitting Pull Request

1. **Commit your changes**
   ```bash
   git add .
   git commit -m "Add: Brief description of changes"
   ```

2. **Push to your fork**
   ```bash
   git push origin feature/your-feature-name
   ```

3. **Create Pull Request**
   - Go to the original repository
   - Click "New Pull Request"
   - Select your branch
   - Fill in the template

### PR Guidelines

**Title Format:**
- `Add:` for new features
- `Fix:` for bug fixes
- `Docs:` for documentation
- `Refactor:` for code improvements

**Description should include:**
- What changes were made
- Why these changes are needed
- How to test the changes
- Screenshots/videos if UI-related
- Related issue numbers

**Example:**
```
Fix: Alexa RSS feed XML escaping issue

- Added XML character escaping in RSS generation
- Prevents special characters from breaking feed
- Resolves #42

Testing:
1. Send newsletter with special characters (&, <, >)
2. Check RSS feed is valid XML
3. Verify Alexa can read the briefing
```

## Code Style Guidelines

### n8n Workflows

- Use descriptive node names
- Add comments to explain complex logic
- Group related nodes visually
- Use consistent naming conventions

### Documentation

- Use clear, concise language
- Include code examples
- Add screenshots where helpful
- Test all steps you document

### Code Nodes (JavaScript)

```javascript
// Use clear variable names
const emailBody = extractedContent;

// Add comments for complex logic
// Extract HTML content and strip tags
const cleanText = emailBody
  .replace(/<[^>]+>/g, ' ')
  .trim();

// Use error handling
try {
  // Your code
} catch (error) {
  console.error('Error:', error);
  throw error;
}
```

## Testing Checklist

Before submitting, ensure:

- [ ] Workflow imports successfully
- [ ] All nodes execute without errors
- [ ] Gmail integration works
- [ ] Claude generates proper summaries
- [ ] Airtable saves data correctly
- [ ] RSS feed is valid XML
- [ ] Alexa can read the briefing
- [ ] Documentation is updated
- [ ] No credentials or API keys in code

## Community Guidelines

### Be Respectful

- Be kind and courteous
- Respect different viewpoints
- Accept constructive criticism
- Focus on what's best for the project

### Be Helpful

- Help others in issues/discussions
- Share your knowledge
- Provide context in your responses
- Welcome newcomers

### Be Collaborative

- Work together toward common goals
- Give credit where due
- Be open to feedback
- Celebrate others' contributions

## Recognition

Contributors will be:
- Listed in README acknowledgments
- Credited in release notes
- Thanked publicly on LinkedIn/Twitter

## Questions?

Not sure about something? Just ask!

- [Open a discussion](https://github.com/yourusername/alexa-newsletter-briefing/discussions)
- [Contact me on LinkedIn](https://www.linkedin.com/in/akhilneelam/)
- Email: akhil_neelam@berkeley.edu

## License

By contributing, you agree that your contributions will be licensed under the MIT License.

---

**Thank you for contributing!** 🙏

Every contribution, no matter how small, makes this project better for everyone.
