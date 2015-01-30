Useless information on what you can raise in Ruby
====

You can't raise just anything:

```
> raise 3
TypeError: exception class/object expected
> raise Module
TypeError: exception class/object expected
```

But you can raise exception classes, not just exception instances:
```
> raise Exception
Exception: Exception
> raise Exception.new
Exception: Exception
> raise Exception.new.new
NoMethodError: undefined method `new' for #<Exception: Exception>
```

To be extra clear,

```
> Exception
=> Exception
> Exception.new
=> #<Exception: Exception>
```

