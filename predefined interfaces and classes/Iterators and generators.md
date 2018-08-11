# Iterators and generators

Iterator extends Traversable, Generator implements Iterator. 

Traversable doesn't do much on its own. It is just an abstract base interface that detects if a class is traversable with foreach. As such, it is extended by Iterator (and also IteratorAggregator).

## Iterator

```php
Iterator extends Traversable {
    /* Methods */
    abstract public mixed current ( void )
    abstract public scalar key ( void )
    abstract public void next ( void )
    abstract public void rewind ( void )
    abstract public bool valid ( void )
}
```

In practice, it would be rare I think to use the native iterator interface. You would more likely find a suitable SPL iterator as your starting point, e.g. DirectoryIterator for reading from the filesystem. Having said that, a good and commonly used example for explaining the use of iterators is reading from the filesystem.

The naïve way of reading from the file system would be something like this (based on example at https://www.entropywins.wtf/blog/2017/10/16/introduction-to-iterators-and-generators-in-php/):

```php
function getContentsOfTextFiles(): array {
    $path = './texts/';
    $texts = [];
    foreach(glob($path . '*.txt') as $textFile) {
        $texts[] = file_get_contents($textFile);
    }
    return $texts;
}

function findTextWithString(array $texts) {
    foreach ($texts as $key=>$text) {
        if(strpos($text, 'foo') !== false){
            return $key;
        }
    }
    return -1;
}

function findString() {
    return findTextWithString(getContentsOfTextFiles());
}

echo findString();
```

This will return the position in the texts folder of the first file containing 'foo'.