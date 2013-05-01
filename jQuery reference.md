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
`.add()` adds query to current set *or* lets you create elements on the fly:

    $('p').add('<span id="bat">This is new.</span>');
    
`.andSelf()` recalls original query and adds to selection  
`.contents()` like `.children()` but returns text nodes, too  
`.end()` reverts to set immediately preceding last set  

## Creating new DOM elements
Just give tag: `$("<p>")` (even with attributes and content)  
Can add attribs with JSON key-value pairing as well

    $("<p>",{"class":"bat","text":"Sample!"});

## Inserting into DOM
`.append()`, `.prepend()` attaches *inside* matched elements  

    $("p").append("New text!");

    var para = $("<p>",{"text":"yo","css":{"background":"yellow"}});
    $("body").prepend(para);

`.appendTo()`, `prependTo()`  
`.after()`, `.before()` like `.append()` but *outside* elements  
`.insertAfter()`, `.insertBefore()` like `.appendTo()` but *outside*  
`.wrap("<strong/>")` or `"<strong>"` or `"<i><b></b></i>"`  
`.unwrap()` removes immediate parents, no arguments  
`.wrapAll()` rearranges elements to wrap them all  
`.wrapInner()`  
`.remove()` accepts filter, removes from DOM  
`.detach()` keeps jQuery data intact for later attaching  
`.data("test","This is data")` can use JSON like multidimensional array

## Accessing/modifying CSS and attributes
`.attr("id")` reads attrib, `.attr("id","title")` sets attrib (accepts JSON format)  
`.removeAttr()`  
`.css()` like `.attr()` but for styling rules  
`.text()`, `.html()` reads contents or rewrites contents with argument  
`.val()` returns or sets value of a `form` `input`  
`.addClass()`, `.removeClass()`, `.toggleClass()`  
`.hasClass()` like `.is()`, returns boolean

    var msg = $("p:eq(1)").hasClass("foo") ? "Yes" : "No";

`.height()`, `.width()` retrieve or set, integers only  
`.innerHeight()`, `.innerWidth()`, `.outerHeight()`, `.outerWidth()`  

## Affecting result sets
`.map()` not chainable, returns new object  
`.each()` chainable, returns original object with changes performed  
Appends "P #2", etc., to each matched element:

    $("p, .foo").map(function(index,ele){
      $(this).append(" " + ele.tagName + " #" + index);
    });

## Animation, other effects
`.show()` (accepts params: milliseconds, callback), `hide.()` adds/removes `display:none;`  
`.fadeIn()`, `.fadeOut()` default 400 ms, accept time, callback  
`.fadeTo()` requires ms, opacity 0 to 1, optional callback  
`.slideUp()`, `.slideDown()`, `.slideToggle()` same arguments  
`.animate()`  

    $("#bar").css({"background":"yellow","border":"1px solid black"})
      .animate({"width":"500px","height":"100px"},5000,"swing",callback);
    OR
      .animate({"width":"500px","height":"100px"},{"duration":5000,"easing":"swing","complete":callback});

Also has a `step` option taking callback, "queue", "special easing"  

`.delay()` milliseconds  
`.step()` two options: clear queue, jump to end of animation, both default to false

## Event handling
`.error()` takes handler
`.scroll()` takes handler (`$(window).scroll();`)  
`.ready()` waits for DOM ready before firing

    $(document).ready(function(){
      // all jQuery here
    });
    
Or to avoid conflict with other libraries:

    jQuery(function($){
      // all jQuery here
    });
    
`.unload()` when user exits page

## Event attachment
`.bind()` accepts event, handler or JSON  

* event can be multiple: "click mouseover"  
* can also pass in variables… see page 74  

`.unbind()` removes all event bindings by default, or can specify event  
`.live()`, `.die()` like `bind`/`unbind` but match future DOM elements too  
`.one()` like `bind` but unbinds automatically after one fire  
`.toggle()` takes event and optional array of arguments  
`.trigger()`  

Shortcut event methods call `trigger` if no argument:

* blur()
* focus()
* focusin()
* focusout()
* load()
* resize()
* scroll()
* unload()
* click()
* dblclick()
* mousedown()
* mouseup()
* mousemove()
* mouseover()
* mouseout()
* mouseenter()
* mouseleave()
* change()
* select()
* submit()
* keydown()
* keypress()
* keyup()
* error()

## AJAX
`$.ajax();`

* accepts object containing settings
* called without selector (global actions independent of DOM)

Example:

    $.ajax({"type":"POST","url":"ajax.php","data":"var=1&var2=2",
      "success":function(data){
        $("#bar").css("background":"yellow").html(data);
      }
    });

`$.ajaxSetup()` to pass defaults, such as above minus data  
`$.get()`, `$.post()` take four args: URL[, data, callback, dataType]  
`$.getJSON()` takes URL[, data, callback]  
`$.getScript()`  
`.load` method, not global function like `$.get()`