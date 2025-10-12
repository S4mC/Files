# Common uses


## File list
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


## Nested Grids

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

