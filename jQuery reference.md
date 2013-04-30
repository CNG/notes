# jQuery reference

Notes taken around 2010 or 2011 while browsing a jQuery book of which I no longer remember the title

## Selecting

Tags look like `$('.classname, #idname')`, and commas act like OR.  
`:not` for negation  
`:filter` -> odd, even, first, last  
`eq(0)` to specify element by its index (accepts negatives)  
`:contains()` accepts text without quotes, case sensitive  
`:has()` accepts element name like `span`  
`:empty`, opposite is `:parent`  
`:visible`, `:hidden`  
`[attribute=value]` `[attribute!=value]`  

**Child filters** (start indexing at 1)    
`:nth-child()` accepts even, odd, index or equation  
`:first-child`, `:last-child` each return multiple matches (all first child or last child)  

**Form filters**  
`input:radio`, `:disabled`, `:checked`

## Traversing

`:filter()` to use a whole new selector within set  
inverse of `.find()` is `.not()`  
`:first()` equals `:eq(0)`; `:last()` equals `:eq(-1)`  

    $('p').last();
    
`.has()` and `.is()` do not return jQuery object  
`.slice()` (can use negatives) is like `substr`: first param starting index, second is optional and not included  
`.children()` accepts optional selector
`.closest()` find elements up the DOM tree
`.find()` like `.children()` but all descendents, not just next level  
`.next()`, `.nextAll()`, `.nextUntil()`  
`.prev()`, `.prevAll()`, `.prevUntil()`  
`.siblings()` `.parents()` returns all parents, `.parentsUntil()`  
`.add()` adds query to current set **or** lets you create elements on the fly:

    $('p').add('<span id="bat">This is new.</span>');
    
`.andSelf()` recalls original query and adds to selection  
`.contents()` like `.children()` but returns text nodes, too  
`.end()` reverts to set immediately preceding last set  

## Creating new DOM elements

## Inserting into DOM

## Accessing/modifying CSS and attributes

## Affecting result sets

## Animation, other effects

## Event handling

## Event attachment

## AJAX