# Short-circuit evaluation

<https://stackoverflow.com/questions/2535515/does-the-condition-after-always-get-evaluated>

For example, in the following algorithm:

```php
function solution($P)
{
    $totalT = count($P) * (count($P)+1)/2;
    $run = 0;
    $totalF = 0;
    for($i = 0; $i < count($P); $i++){    
        if(!$P[$i]){
            $run++;
        }
        if(!$P[$i] && $P[$i+1]){
            $totalT -= $run * ($run + 1)/2;  
            $run = 0;          
        }   
    }
    return $totalT;
}

$P = [true,false,false,false,true,false];
echo solution($P);
```

You will only get the expected 'undefined offset' error from `if(!$P[$i] && $P[$i+1])` when the final array member is false. If it is true, then PHP will fail the condition at that point and not go on to evaluate the second part of the test. NB this is not unique to PHP but is true for most languages.

[the above algorithm is my not quite finished answer to this Codility task: <https://app.codility.com/programmers/task/pascal_triangle/>] 