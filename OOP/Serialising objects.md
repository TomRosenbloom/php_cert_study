# Serialising objects

In general terms serialising means creating a byte-stream representation of a value. For e.g. you can 'flatten' a multi-dimensional array by serialising it and later re-constitute it by unserialising it. The point of serialisation is to be able to pass complex items around over a network (or via URL, though this might be a bad practice) and/or between different programming languages. Also for database storage/other persistence. JSON is a serialisation format (interoperable). The PHP serialisation functions use PHP's own serialisation format (non-interoperable).

Objects can be unserialised only if the object class is defined.

Internally, object serialisation uses \_\_sleep and \_\_wakeup magic methods which can be overridden. There is also a Serializable interface http://php.net/manual/en/class.serializable.php



