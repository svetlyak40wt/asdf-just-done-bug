This repository demonstrates a bug in ASDF, related to a situation
when you have one system having defsystem-depends-on dependency from another
and this another system is package-inferred.

This is the smallest possible example how to reproduce this problem:

* `bar` defsystem-depends-on `foo` and `foo` is a package-inferred system.
* `minor` defsystem-depends-on `blah` and `blah` is not package-inferred.

When you'll call `(asdf:load-system "bar")` in the REPL a few times, you'll see these two warnings:

```
WARNING:
   Computing just-done stamp  for action (ASDF/LISP-ACTION:LOAD-OP "foo/foo"
                                          "file-type"), but dependency (ASDF/LISP-ACTION:COMPILE-OP
                                                                        "foo/foo"
                                                                        "file-type") wasn't done yet!
WARNING:
   Computing just-done stamp  for action (ASDF/LISP-ACTION:COMPILE-OP
                                          "foo/foo"), but dependency (ASDF/LISP-ACTION:COMPILE-OP
                                                                      "foo/foo"
                                                                      "file-type") wasn't done yet!
```

but nothing like that happens when you are doing `(asdf:load-system "minor")`.

The similar warnings happen when you have:  
`bar` defsystem-depends-on `foo` and `foo` is usual system but depends on some other package-inferred system.

This behavior was experienced on ASDF version 3.3.5.3.
