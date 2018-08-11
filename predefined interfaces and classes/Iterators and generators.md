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

This will return the position in the texts folder of the file containing 'foo' (on the assumption there is only one file containing that string). The problem is that every file is read before any examination of the contents takes place, so if the string was in the first file that would be a lot of wasted resource. So we use an iterator.

```php
class TextFileIterator implements Iterator {
    private $position = 0;
    private $texts;

    public function __construct() {
        $this->position = 0;
        $path = './texts/';
        $this->texts = glob($path . '*.txt');
    }

    public function current() {
        return current($this->texts);
    }

    public function key(){
        return key($this->texts);
    }
    public function next(){
        next($this->texts);
    }
    public function rewind(){
        reset($this->texts);
    }
    public function valid(){
        return $this->key() !== null;
    }
}

function findTextWithString(Iterator $texts) {
    foreach ($texts as $key=>$text) {
        if(strpos($text, 'foo') !== false){
            return $key;
        }
    }
    return -1;
}

function findString() {
    return findTextWithString(new TextFileIterator());
}

echo findString();
```

https://talesoft.codes/post/details/php-iterators



