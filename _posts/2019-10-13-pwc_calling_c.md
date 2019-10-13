---
layout: post
title: "Perl Weekly Challenge W029 - Calling C"
tags: ["c", "inline", "nativecall", "perl"]
categories: ["pwc"]
---
The second task for this week's challenge is to write a script to demostrate calling a C function in perl, it can be a user-defined or a standard(built-in) C function. 

I have seen implementation of this using the [```Inline```](https://metacpan.org/pod/Inline) module but I have to be honest that I have never really tried it in perl. Never had the need to use it or atleast that's what I thought. At the back of my mind, I knew a piece of code compiled in C would perform better compared to pure perl solution. 

<!-- more -->
### **Solution:**
So for this task, I created a [```fibonacci```](https://en.wikipedia.org/wiki/Fibonacci_number) function (both in C and perl) that would take a number ```$n``` as input and would return the n-th number in the [```fibonacci```](https://en.wikipedia.org/wiki/Fibonacci_number) series.

I used the [```Inline```](https://metacpan.org/pod/Inline) module and wrote the C function after the special literals ```__END__``` and ```__C__```.

* ```__END__``` indicates the logical end of the script before the actual end of file. Any following text is ignored.
* ```__C__```  indicates the start of the C code

The ```perl_fib``` function is the pure perl version of the [```fibonacci```](https://en.wikipedia.org/wiki/Fibonacci_number) function and ```fib``` is the C version.

I knew the perl implementation would take longer to finish (vs C) on bigger input range; so I took the oppurtunity to use the [```Memoize```](https://perldoc.perl.org/Memoize.html) module to compare the performaces of:
* ```perl_fib``` - function writtern in perl without ```Memoize```
* ```perl_fib(Memoized)``` - same function but ```Memoized```
* ```fib``` - function written in C


### **Code:**
```perl
#!/usr/bin/env perl
# Write a script to demonstrate calling a C function. 
# It could be any user defined or standard C function.
use strict;
use warnings;
use feature 'say';
use Memoize;

use Inline 'C';

my $start_run;
my $run_time;


$start_run = time();
print (perl_fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Perl): $run_time sec\n";

memoize('perl_fib');
$start_run = time();
print (perl_fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Perl-Memoized): $run_time sec\n";

$start_run = time();
print (fib($_)." ") for 1..36;
$run_time  = time() - $start_run;
say "\nRun Time (Inline-C): $run_time sec\n";

sub perl_fib { (
    $_[0] == 0) ? 
        0: ($_[0] == 1)?
            1:perl_fib($_[0]-1)+perl_fib($_[0]-2)
}

__END__
__C__
unsigned long fib(int n) {
    if ( n == 0 )
        return 0;
    else if ( n == 1 )
        return 1;
    else
        return ( fib(n-1) + fib(n-2) );
}
```
### **Output:**
``` 
perl ch-2.pl 
1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 4181 6765 10946 17711 28657 46368 75025 
121393 196418 317811 514229 832040 1346269 2178309 3524578 5702887 9227465 14930352 
Run Time (Perl): 49 sec

1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 4181 6765 10946 17711 28657 46368 75025 
121393 196418 317811 514229 832040 1346269 2178309 3524578 5702887 9227465 14930352 
Run Time (Perl-Memoized): 0 sec

1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597 2584 4181 6765 10946 17711 28657 46368 75025 
121393 196418 317811 514229 832040 1346269 2178309 3524578 5702887 9227465 14930352 
Run Time (Inline-C): 1 sec
```

As expected, the perl implementation took the longer compared to the C version.
But it is surprising how [```Memoize```](https://perldoc.perl.org/Memoize.html) cut down the execution time to just a fraction of a second. 

And that's it for this week's challenge. If you'd like to join the fun and contribute, please visit the [site](https://perlweeklychallenge.org/blog/) managed by [Mohammad S Anwar](https://github.com/manwar). 

