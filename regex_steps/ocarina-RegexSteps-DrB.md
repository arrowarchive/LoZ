## Ocarina of Time Regex Search and Replace by Dr. B

After removing the weird `#$` stuff as we did on 15 March 2021 after class...Dr. B continued on and did the following:

1. Wrap the "acts" (or what seem like the larger outer sections). These seem to come with numbered info, parallel with the numbered stuff we used in acts.
Example: "[02'01] ---        Ganon's Castle        --- [02'01] "

    Find: `^\[(.+?)\] +?--- +?([A-z'][A-z' ]+)? +?---.+?$`
    
    Replace with: `</act>\n<act number="\1"><title>\2</title>`

2. Manually remove the `</act>` from the top of the file to the end before the closing `</xml>`.

3. Find the "chapters" which seem to be subsections within the "acts". ("Dot matches all turned off): 
  
  Find: `^[^\[A-z][A-z ']+ +- +.+?[^\]]$`
  
  Replace with: `</chapter>\n<chapter><title>\1</title>`

4. While cleaning up multiple extra `</chapter>` elements, find a way to match more "chapters" with:
    Find: `\n\n^([A-z][A-z0-9 ']+)$\n\n`

    Replace with: `</chapter>\n<chapter><title>\1</title>`

5. Continue manual cleanup of `</chapter>` tags. Use the Outline view in oXygen as a guide.

6. Remove ------ with
    Find: `-{4,}`
    Replace with nothing.

6. Find lots of "stage cues" With "Dot matches all" turned on. (I masked some stuff in the "Format" section so it wouldn't be marked with this, by removing the leading * from lines in that part of the document.) 

Find: `^(\*.+?)(\n\n)`

While reviewing these results, notice a pattern with 
`\* *- *Failure:.+?Success:`

Also scope out `\(Yes.+?\(No` (with Dot matches all turned on in all cases). This lets us scope areas in the game where the player either succeeds or fails, or is given choices. 

Try out some new tagging here to capture the Failure/Success situations as little connected units in the text:
For Failure through Success, maybe we can use the tag:
```<try>
      <interact type="Failure"> <!--Capture the scenario of failure here, including dialogue, inner stage directions, etc. --></interact>
      <interact type="Success"><!--Capture the scenario of failure here, including dialogue, inner stage directions, etc --></interact>
   </try>
```
There are three of these Failure / Success scenarios in Ocarina, so find them, and then tag them up by hand. Discover a third option implied here: just Continue:

Example section for Success / Failure: I'm making sure there are two `\n\n` around every line inside to make it easy to add regex markup later for internal dialogue, stage directions, and Yes / No choices:

```
<try><interact type="Failure">

SkullKid 2: Too bad...Heh heh! Do you want to play some more?

* - "Yes" repeats the event from the beginning. "No" ends the event.

</interact>
<interact type="Success">

SkullKid 2: That was quite a nice session. As a token of our friendship,
please take this.

* - Initially, they provide a Green Rupee. They reward Link with a Blue Rupee
for his second successful session, a Piece of Heart for his third success,
and a Red Rupee per each success thereafter.

</interact>
<interact type="Continue">

SkullKid 2: Do you want to play some more?

* - "Yes" repeats the event from the beginning. "No" ends the event.

</interact>
</try>
```
 
 Notice that other kinds of interactions seem to involve this pattern: ("Dot matches all" is turned on.)
 
 Find: `^\* - (".+?".*?"[^<]+?".+?)(\n\n)`
 Replace with: `
 
 ## stage type="info"
 
 Find: `^\* - (.+?)(\n\n)`
 Replace with: `<stage type="info">\1</stage>\2`
 
 
 ## stage type="location"
 
 Find: `(\n\n)(Near:.+?)$`
 Replace with: `\1<stage type="location">\2</stage>`
 
 Find: `\[([A-z ]+?-[A-z ]+?)\]`
 Replace with: `<stage type="location">\1</stage>
 


##Glossary Lists: 
Highlight selected area that has terms and definitions, and do regex in Only selected lines:

Find: `\[(.+?)\](.*?)\n(.+?)(\n\n)`
Replace with: `<entry><term>\1\2</term>\n<gloss>\3</gloss></entry>\4`

## Choices / Interactions: 
Yes / No interactions:

Find: `^-(\(Yes\).+?)(\(Nop?e?\)?.+?)(\n\n)`
Replace with: `<choice>\n<interact>\1</interact>\n<interact>\2</interact>\n</choice>`

All others in parentheses:

Find: `- *(\(.+?\))`
Replace with: `<interact>\1</interact>`

## Speeches 'n Speakers:

Find: `(\n\n)([^<]+?):([^<]+?)(<)`
Replace with: `\1<sp><speaker>\2</speaker>\3</sp>\4`

One more pass with:

Find: `(\n)([^<]+?):([^<]+?)(<)`
Replace with: `\1<sp><speaker>\2</speaker>\3</sp>\4`

