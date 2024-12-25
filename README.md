# Mustache template parser

This is a simple Mustache template implementation,
See https://mustache.github.io/ for the specification.

This implementation has the following advantages:
- no temporary files
- no require calls or includes
- no eval calls
- no weird ob_start calls


But this implementation is probably slower than "Bog the cow's" implementation:
https://github.com/bobthecow/mustache.php

But for some situations (i.e. for statifying CMS) this is not a problem ;)

## Features:
- Simple values
- Comments
- Blocks (normal and negating blocks)
- Lists with arrays and objects
- Wrapper functions
- Partials (you need to define a partial loader)
- Delimiter change
- no dot notation on property names


## Examples

### Template

    Hello {{planet}}, {{& planet }}.{{! Simple example with a simple property }}
    
    {{#test}}
    Yes, this is a {{test}}. {{! yes, it is}}
    {{/test}}
    {{^test}}
    No, this is not a {{test}}. {{ ! will not be displayed, because test is not false }}
    {{/test}}
    
    {{#car}}
    My Car is {{color}}. {{! this is a property of the array car }}
    It drives on {{& planet }}.{{! this property is inherited from the upper context }}
    {{/}}
    
    {{#house}}
    My House is {{size}}. {{! this property is read from an object }}
    {{/}} {{! short closing tags are allowed }}
    
    Some names:
    {{#names}}
    my name is {{ name }}.{{! yes, spaces are allowed}}
    {{/names}}
    
    {{#empty}}
    this is not displayed {{! because the list is empty }}
    {{/empty}}
    
    {{#upper}}
    Hello again, {{planet}}. {{!displayed in uppercase}}
    {{/}}
    
    <h1>Partials</h1>
    {{> mycoolpartial}}
    
    <h1>Changing Delimiters</h1>
    Default: {{name}}
    {{=$( )=}}
    Bash-Style: $(name)
    Default should not work here: {{name}}
    
    $(={{ }}=)
    Default again: {{name}}
    
    <h1>Dot notation</h1>
    this will not work: {{building}}
    but this is the color of the roof: {{building.roof.color}}


## Using

    $m = new Mustache();
    $m->partialLoader = function($name) {
     return "\nThis is a partial named ".$name.". It may include variables, like the name '{{name}}'.\n\n";
    };
    $m->parse( $source );
    
    echo 'Object: <pre><code>'; print_r($m); echo '</code></pre>';
    
    $data = array(
        'planet'  => '<b>world</b>',
        'test'  => 'Test',
        'name'  => 'Mallory',
        'car'   => array('color'=>'red'),
        'house' => (object) array('size'=>'big' ),
        'names' => array(
            array('name'=>'Alice'),
            array('name'=>'Bob')
        ),
        'empty' => array(),
        'upper' => static function($text) { return strtoupper($text); },
        'building' => array('roof'=>array('color'=>'gray'))
    
    );
    
    echo '<pre>'.$m->render( $data ).'</pre>';
