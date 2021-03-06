---
in_progress: true
---

Ideas for Future Deprecations
=============================

These are some ideas, extracted from [Shell Language
Deprecations](deprecations.html).

These breakages may never happen, as they require a significant new lexer mode.
If they do, you will want to avoid the following syntax:

- `r'` and `c'` at the beginning of a word
- the character sequences `$/`, `'''`, `"""` anywhere

I expect that those sequences are rare, so this change would break few
programs.

<!-- cmark.py expands this -->
<div id="toc">
</div>


## Raw and C String Literals in Word Mode

We might want to make string literals in the command/word context match Oil's
expressions.  This means we have to disallow implicit concatenation, or certain
instances of it.

No:

    var x = foo'bar'   # Not a valid Oil expression
    echo foo'bar'      # Valid in shell, but discouraged

Yes:

    var x = 'foobar'
    echo 'foobar'

We don't want to break these, so = could be special

    ls --foo='with spaces'
    ls --foo="with $var"

New raw and C strings (which technically conflict with shell):

    echo r'\'   # equivalent to '\'
    echo c'\n'  # equivalent to $'\t'


## Multiline Strings and Redirects

Instead of here docs:

    cat <<EOF
    hello
    there, $name
    EOF

    cat <<'EOF'
    $5.99  # no interpolation
    'EOF'

We could have multiline strings:

    cat << """
    hello
    there, $name
    """

    cat << '''
    $5.99  # no interpolation
    '''

Minor breakage: the `'''` and `"""` tokens become significant.  It may also be
nice to change the meaning of `<<` slightly.

## Inline Eggex

Instead of:

    var pat = / digit+ /
    egrep $pat *.txt

You can imagine:

    egrep $/ digit+ / *.txt

Minor breakage: making `$/` significant.

## Bare Function Call

Instead of

    call f()
    call g(x, y)

You could do:

    f()   # calls a func, doesn't begin a shell function declaration
    g(x, y)

- This change probably doesn't involve a lexer mode.
- It would be a big breakage!

