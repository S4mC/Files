# Common uses

This document explains all the custom Markdown blocks and syntax extensions available in this blog system.


---


## Table of Contents

1. [Connector Block](#h2=connector block)
2. [Grid System](#h2=grid system)
3. [Float Elements](#h2=float elements)
4. [Callouts (Note & Warning)](#h2=callouts)
5. [Details/Summary](#h2=Details/Summary)
6. [Iframe Embedding](#h2=iframe embedding)
7. [Spacing Elements](#h2=spacing elements)
8. [Relative navigation link](#h2=Relative navigation link)


---


## Connector Block

The `:::connector` block creates a visual file/folder tree structure with connecting lines, perfect for displaying hierarchical content like file systems.

### Features:
- Nested lists with visual connectors
- Support for emojis as icons
- Escape icons with backslash if needed
- Maintains indentation for nested items
- Custom attributes support

### Example:

:::connector
    - 📄 file.txt
    - 📄 File with
        super long name.bat
    - 📂 Open folder
        :::connector
            - 📄 file inside.txt  
            - 📄 other file.txt
        :::
    - 📁 folder
    - \📃 escaped icon file.txt
:::

### Syntax:

```markdown
:::connector
    - 📄 file.txt
    - 📄 File with
        super long name.bat
    - 📂 Open folder
        :::connector
            - 📄 file inside.txt  
            - 📄 other file.txt
        :::
    - 📁 folder
    - \📃 escaped icon file.txt
:::
```

### With Custom Attributes:

```markdown
:::connector style="max-width: 500px;"
    - 📄 Your content here
:::
```


---


## Grid System

The `:::grid` block creates responsive grid layouts for organizing content in columns.

### Features:
- Customizable number of columns
- Adjustable gap spacing
- Responsive design options
- Auto-fit capabilities
- Equal height items option
- Custom styling support

### Basic Grid:

:::grid cols-2 gap-2
	### Element 1

	Content for element 1. Replace with your own content.
	---
	### Element 2

	Content for element 2. Replace with your own content.

    :::grid cols-2 gap-2
        ### Nested Element 1

        Content for nested element 1.
        ---
        ### Nested Element 2

        Content for nested element 2.
    :::
:::

### Syntax:

```markdown
:::grid cols-2 gap-2
    ### Element 1

    Content for element 1. Replace with your own content.
    ---
    ### Element 2

    Content for element 2. Replace with your own content.

    :::grid cols-2 gap-2
        ### Nested Element 1

        Content for nested element 1.
        ---
        ### Nested Element 2

        Content for nested element 2.
    :::
:::
```

### Configuration Options:

- **`cols-N`**: Number of columns (e.g., `cols-2`, `cols-3`, `cols-4`)
- **`gap-N`**: Gap size between items (e.g., `gap-2`, `gap-4`)
- **`responsive`**: Makes grid responsive to screen size
- **`auto-fit`**: Automatically fits columns based on content
- **`min-N`**: Minimum width for auto-fit (e.g., `min-250`)
- **`equal-height`**: Makes all grid items equal height
- **`style="..."`**: Add custom CSS styles

### Examples:

```markdown
# Responsive grid with auto-fit
:::grid auto-fit min-300 gap-3 responsive
    Content 1
    ---
    Content 2
    ---
    Content 3
:::

# 3-column grid with custom styles
:::grid cols-3 gap-4 style="padding: 20px; background: #f0f0f0;"
    Column 1
    ---
    Column 2
    ---
    Column 3
:::

# Equal height items
:::grid cols-2 gap-2 equal-height
    Short content
    ---
    Much longer content that would normally make this item taller
:::
```


---


## Float Elements

Float elements are floating panels that can be triggered anywhere in the content, perfect for tooltips, additional information, or expandable content.

### Creating a Float Element:

```markdown
:::float-myid
    This is the content that will appear in the floating panel.

    You can include **any markdown** here, including:
    - Lists
    - Images
    - Code blocks
    - etc.
:::
```

### Triggering the Float Element:

Use the syntax `(?=id)` anywhere in your content to create a trigger button:

```markdown
This is some text with a question mark (?=myid) that opens more info.
```

### Features:
- Click trigger to open/close
- Click close button (×) to dismiss
- Click outside to close
- Multiple float elements per page
- Supports full markdown inside

### Complete Example:

```markdown
    # My Article

    This is a paragraph with more information available (?=details).

    :::float-details
        ### Additional Details

        Here's some extra information that doesn't fit in the main text:
        - Point 1
        - Point 2
        - Point 3
    :::
```


---


## Callouts

Callouts are styled boxes to highlight important information. Two types are available: **Note** and **Warning**.

### Note Callout:

:::note
    This is important information that readers should be aware of.
    You can include any markdown content here.
:::

```markdown
    :::note
        This is important information that readers should be aware of.
        You can include any markdown content here.
    :::
```

### Warning Callout:

:::warning
    This is a critical warning that requires attention.
    Be careful with this information!
:::

```markdown
    :::warning
        This is a critical warning that requires attention.
        Be careful with this information!
    :::
```

### Features:
- Icon and header automatically added
- Supports nested markdown
- Distinct styling for each type
- Can contain any markdown content


---


## Details/Summary

Collapsible sections that hide content until clicked.

### Basic Details:

```markdown
    :::details Click to expand
        This content is hidden by default and will show when clicked.

        You can include:
        - Lists
        - **Formatted text**
        - Code blocks
        - Anything else
    :::
```

### Open by Default:

```markdown
    :::details -open Already expanded
        This content is visible by default but can be collapsed.
    :::
```

### Features:
- Collapsible content sections
- Custom summary text
- Option to start expanded with `-open`
- Supports full markdown inside
- Nested details supported


---


## Iframe Embedding

Embed external content with an expandable viewer.

### Syntax:

```markdown
    :::iframe width="100%" height="500px"
    https://example.com
    :::
```

### Features:
- Custom width and height attributes
- Expand/contract button automatically added
- Fullscreen capability
- Any valid iframe attributes supported

### Example with Multiple Attributes:

```markdown
    :::iframe width="800px" height="600px" allow="fullscreen"
    https://www.youtube.com/embed/VIDEO_ID
    :::
```


---


## Spacing Elements

Add precise spacing to your content.

### Width Spacing:

Adds horizontal spacing:

```markdown
    Text before (w=2em) text after
```

Creates a `<div>` with the specified width.

### Height Spacing:

Adds vertical spacing:

```markdown
    Paragraph 1

    (h=3rem)

    Paragraph 2
```

Creates a `<div>` with the specified height.

### Supported Units:
- `px` - pixels
- `em` - relative to font size
- `rem` - relative to root font size
- `lh` - line height

### Examples:

```markdown
    # Add 50px horizontal space
    Before (w=50px) After

    # Add 2em vertical space
    Top paragraph

    (h=2em)

    Bottom paragraph

    # Add 1 line-height space
    Content

    (h=1lh)

    More content
```


---


## Relative navigation link

Special navigation elements that create relative links with directional movement.

### Syntax:

Put the link using:
```markdown
    [Link Name](#goto=<DIRECTION>)
```

Put where it leads (makes focus) using:
```markdown
    (go-<DIRECTION>=element-name)
```

### Available Directions:

- **`out`**: Navigate to parent element
- **`above`**: Navigate to previous sibling
- **`below`**: Navigate to next sibling
- **`in`**: Navigate to first child
- **`inl`**: Navigate to last child

### Stacking Directions:

You can chain multiple directions together:

```markdown
    # Go out twice then down
    (go-out-out-bellow=section-name)

    # Go out, then to previous sibling
    (go-out-above=header-title)

    # Enter first child
    (go-in=first-item)
```

### Examples:

```markdown
    [Navigate to parent's next sibling with](#goto=next-topic)
    See the next section (go-out-bellow=next-topic)

    [Navigate up one level then to previous](#goto=previous-section)
    Go back to previous (go-out-above=previous-section)

    [Navigate to first child of next sibling](#goto=item-list)
    Check the first item (go-bellow-in=item-list)
```


---


## Additional Notes

### Nesting Blocks:

Most blocks support nesting. Common patterns:

```markdown
# Grid inside Note
:::note
    Important grid layout:
    :::grid cols-2 gap-2
        Item 1
        ---
        Item 2
    :::
:::

# Connector inside Grid
:::grid cols-2
    :::connector
        - File 1
        - File 2
    :::
    ---
    Other content
:::

# Details with Float triggers
:::details More Information
    Click here for details (?=extra-info)

    :::float-extra-info
        Even more detailed information
    :::
:::
```

### Indentation:

The system automatically removes common indentation from block content, so you can indent for readability in your source files without affecting the output.
In some cases, such as lists and connectors within lists, indentation is mandatory for correct operation.

### Markdown Processing:

All block contents are processed for markdown, so you can use:
- **Bold**, *italic*, `code`
- Lists and sublists
- Links and images
- Code blocks
- Other custom blocks


---


## Tips and Best Practices

1. **Use semantic names**: When creating float IDs or navigation targets, use descriptive names like `detailed-explanation` instead of `float1`.

2. **Grid responsiveness**: For mobile-friendly layouts, use `responsive` and `auto-fit` options with reasonable `min-` values.

3. **Callout usage**: Use `:::note` for helpful information and `:::warning` for critical warnings or potential issues.

4. **Spacing consistency**: Use `rem` or `em` units for spacing to maintain consistency with your design system.

5. **Navigation links**: Test your navigation links thoroughly as they depend on the rendered HTML structure.

6. **Float elements position**: Float elements work best with short, clickable trigger text. Don't overuse them in a single paragraph.


---


## Quick Reference

| Block Type | Syntax | Purpose |
|------------|--------|---------|
| Connector | `:::connector` | File/folder trees |
| Grid | `:::grid cols-N gap-N` | Multi-column layouts |
| Float | `:::float-<id>` + `(?=<id>)` | Floating panels/tooltips |
| Note | `:::note` | Information callouts |
| Warning | `:::warning` | Warning callouts |
| Details | `:::details <Title>` | Collapsible sections |
| Iframe | `:::iframe` | Embedded content |
| Width spacing | `(w=<AMOUNT><UNIT>)` | Horizontal space |
| Height spacing | `(h=<AMOUNT><UNIT>)` | Vertical space |
| Navigation | `(go-<DIR>=<name>)` | Relative navigation |
| Internal links | `[<text>](<#link>)` | Auto-encoded links |