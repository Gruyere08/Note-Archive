# Useful tips

## Some useful highlights for code blocks

### console:
using ` ```console`
```console
git status
git log --oneline
```

### Config files:
using ` ```ini`
```ini
[user]
    name = Thiago Escalante
    email = thiago@example.com
```

### git output:
using ` ```vbnet`
```vbnet
On branch main
Your branch is up to date with 'origin/main'.
nothing to commit, working tree clean
```

---

## Other useful code block highlights

### json:
using ` ```json`
```json
{
  "name": "Thiago",
  "project": "notes",
  "languages": ["Java", "Python", "Markdown"]
}
```

### yaml:
using ` ```yaml`
```yaml
name: Thiago Escalante
project: notes
languages:
  - Java
  - Python
  - Markdown
```

### diff:
useful for showing changes between file versions  
using ` ```diff`
```diff
+ Added new feature to notes structure
- Removed unused test file
```

### python:
using ` ```python`
```python
def greet(name):
    print(f"Hello, {name}!")

greet("Thiago")
```

### javascript:
using ` ```javascript`
```javascript
function greet(name) {
  console.log(`Hello, ${name}!`);
}

greet("Thiago");
```

### plaintext:
for simple, unformatted blocks  
using ` ```plaintext`
```plaintext
This is plain text.
No highlighting or special formatting.
Good for command examples or notes.
```

---

## Text formatting examples

You can use **bold**, *italic*, or ***bold italic*** for emphasis.

> Quoted text can be used for notes, warnings, or explanations.

`Inline code` is great for showing single commands like `git push`.

Use checklists for tasks:
- [x] Learn Markdown basics  
- [x] Create a notes repository  
- [ ] Keep writing awesome notes!

---

### Bonus tip
You can also combine elements:
> **Tip:** Use fenced code blocks with language tags to make your notes more readable and easier to scan on GitHub.
