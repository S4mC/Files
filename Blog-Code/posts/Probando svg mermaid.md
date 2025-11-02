# Probando svg mermaid


:::svg -NoB -kanban
	Todo
		! Create Documentation
		!! Create Blog about the new diagram

	In progress
		!!! Create renderer so that it works in all cases. We also add some extra text here for testing purposes. And some more just for the extra flare.

	Ready for deploy
		!!!! Design grammar
	
	Ready for test
		Create parsing testsewfewfewfewfewfewfewf (K.Sveidqevist, )
		last item ( , gg)
	Done
		define getData
		Title of diagram is more than 100 chars when user duplicates diagram with 100 char
		Update DB function

	Can't reproduce
		Weird flickering in Firefox grgr reg re reg erg r egr egreg reg g ererggerregregger end fewedwqdwqqwdwqdd 5615
:::


:::svg -flowchartTB style="aspect-ratio: 1.25907 / 1;"
	(markdown) ✅ This **is** *Markdown*
		- <--> hola a trfeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeeodos 4 (newLines)
		- hola a todos 43r 43 (Holatodos)

	(newLines) ❌ newlines1 o yea
	Line 1221
	32241234563432
	123456
		- Por esta razin debe de e¿ir a (newLines2)

	(newLines2) 📄 Li1
	24654165
	56165e1f65e
		- hola a trfeeeeeeeeeeeerfeeeeeeeeeeeeeeeod4 43os (newLines3)

	(newLines3) ▶️ Li9ne1
	Line 2
	Line 3
		- hola a rfeeeeeeeeeeeeeeeeeeeeeetod 43o (newLines)
		- hola a refffffffffffffffffffffffftod 43os (newLines)
		- hola a tod 43os (newLines)
		- hola a 15666666666666666666666666666666666666666666666666666666666666666666tod 43os (newLines)
		- hola a tod 43os (newLines)
		
	(Holatodos) 🌉 Hola esdedewedwedwedwewdto es markdown
		- <-> hola a todo4 34tggtrgtgtrtrgtgr3 rs (newLines)
:::


:::svg -mermaid
	---
	config:
		look: handDrawn
	---
	flowchart LR
		markdown["`This **is** _Markdown_`"]
		newLines["`Line1
		Line 2
		Line 3`"]
		newLines2["`Line1
		Line 2
		Line 3`"]
		newLines3["`Line1
		Line 2
		Line 3`"]
		Holatodos["Hola esdedewedwedwedwewdto es markdown"]
		markdown --> |⠀hola a todos 4⠀| newLines
		markdown --> |⠀hola a todos 43r 43⠀| Holatodos
		Holatodos --> |⠀hola a todo4 34tggtrgtgtrtrgtgr3 rs⠀| newLines
		newLines2 --> |⠀hola a tod4 43os⠀| newLines3
		newLines3 --> |⠀hola a tod 43os⠀| newLines
		newLines3 --> |⠀hola a tod 43os⠀| newLines
		newLines3 --> |⠀hola a tod 43os⠀| newLines
		newLines3 --> |⠀hola a tod 43os⠀| newLines
		newLines3 --> |⠀hola a tod 43os⠀| newLines
:::


:::svg -pie style="height:20em;" Pets adopted by volunteers
	"Dogs jijiji wnqdionqwiodnowqind": 1
	"Cat": 90
	Rats: 9
:::


:::svg -xychart style="height:20em;" Sales Revenue
	x-axis "Months" [jan, feb, jiji]
	y-axis "Revenue (in $)" 4000 --> 8000
	bar [5000, 6000, 7900]
	line [5000, 6000, 7900]
:::